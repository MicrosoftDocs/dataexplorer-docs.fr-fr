---
title: Gestion de la stratégie de mise à jour Kusto-Azure Explorateur de données
description: Cet article décrit la stratégie de mise à jour dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 320824b4f614ea809141167c1284282b1ef641cf
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616556"
---
# <a name="update-policy"></a>Mettre à jour la stratégie

La [stratégie de mise à jour](updatepolicy.md) est un objet de stratégie au niveau de la table qui permet d’exécuter automatiquement une requête et de recevoir ses résultats lorsque les données sont ingérées dans une autre table.

## <a name="show-update-policy"></a>Afficher la stratégie de mise à jour

Cette commande renvoie la stratégie de mise à jour de la table spécifiée ou de toutes les tables de `*` la base de données par défaut si est utilisé comme nom de table.

**Syntaxe**

* `.show``table` *TableName* TableName `policy``update`
* `.show``table` *DatabaseName*DatabaseName`.`*TableName* , table `policy``update`
* `.show` `table` `*` `policy` `update`

**Retourne**

Cette commande retourne une table qui comporte un enregistrement par table, avec les colonnes suivantes :

|Colonne    |Type    |Description                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|Nom de l’entité sur laquelle la stratégie de mise à jour est définie                                                                                                                |
|Stratégies  |`string`|Tableau JSON qui indique toutes les stratégies de mise à jour définies pour l’entité, mises en forme en tant qu' [objet de stratégie de mise à jour](updatepolicy.md#the-update-policy-object)|

**Exemple**

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |Stratégies                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|[TestDB]. [DerivedTableX]|[{"IsEnabled" : true, "source" : "MyTableX", "Query" : "MyUpdateFunction ()", "IsTransactional" : false, "PropagateIngestionProperties" : false}]|

## <a name="alter-update-policy"></a>Modifier la stratégie de mise à jour

Cette commande définit la stratégie de mise à jour de la table spécifiée.

**Syntaxe**

* `.alter``table` *TableName* TableName `policy` *ArrayOfUpdatePolicyObjects* ArrayOfUpdatePolicyObjects `update`
* `.alter``table` *DatabaseName* `update` *ArrayOfUpdatePolicyObjects* *TableName* TableName ArrayOfUpdatePolicyObjects`.` `policy`

*ArrayOfUpdatePolicyObjects* est un tableau JSON qui ne contient aucun ou plusieurs objets de stratégie de mise à jour définis.

**Remarques**

1. Il est recommandé d’utiliser une fonction stockée pour la `Query` propriété de l’objet de stratégie de mise à jour.
   Cela facilite la modification de la définition de fonction uniquement au lieu de l’objet de stratégie entier.

2. Les validations suivantes sont effectuées sur la stratégie de mise à jour lorsqu’elle est définie ( `IsEnabled` en cas de `true`définition de la valeur) :
    1. `Source`: La table doit exister dans la base de données cible.
    2. `Query`: 
        * Le schéma défini par le schéma doit correspondre à celui de la table cible. 
        * La requête doit référencer `source` la table de la stratégie de mise à jour. Il est possible de définir une requête *not* de stratégie de mise à jour qui ne `AllowUnreferencedSourceTable=true` fait pas référence à la source en définissant dans les propriétés with (Voir l’exemple ci-dessous), mais elle n’est généralement pas recommandée pour des raisons de performances (cela implique que pour chaque ingestion de la table source, *tous les* enregistrements d’une table différente sont pris en compte pour l’exécution de la stratégie de
    3. La stratégie n’entraîne pas la création d’un cycle dans la chaîne de stratégies de mise à jour dans la base de données cible.
    4. Si `IsTransactional` a la valeur `true`, `TableAdmin` les autorisations sont également vérifiées par rapport `Source` (la table source).
  
3. Veillez à tester les performances de votre fonction/stratégie de mise à jour avant de l’appliquer à chaque ingestion de la table source. voir [ici](updatepolicy.md#testing-an-update-policys-performance-impact).

**Retourne**

La commande définit l’objet de stratégie de mise à jour de la table (en remplaçant toute stratégie actuelle définie, le cas échéant), puis retourne la sortie de la commande correspondante [. afficher la stratégie de mise à jour de table de table](#show-update-policy) .

**Exemple**

```kusto
// Creating function that will be used for update
.create function 
MyUpdateFunction()
{
    MyTableX
    | where ColumnA == 'some-string'
    | summarize MyCount=count() by ColumnB, Key=ColumnC
    | join (OtherTable | project OtherColumnZ, Key=OtherColumnC) on Key
    | project ColumnB, ColumnZ=OtherColumnZ, Key, MyCount
}

// Creating the target table (in case it doesn't already exist)
.set-or-append DerivedTableX <| MyUpdateFunction() | limit 0

// Use update policy on table DerivedTableX
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyUpdateFunction()", "IsTransactional": false, "PropagateIngestionProperties": false}]'
```

- Lorsqu’une ingestion de la table source (dans ce cas `MyTableX`) se produit, une ou plusieurs étendues (partitions de données) sont créées dans cette table.
- Le `Query` qui est défini dans l’objet de stratégie de mise à jour `MyUpdateFunction()`(dans le cas présent) s’exécute uniquement sur ces étendues et ne s’exécute pas sur la table entière.
  - Cette « portée » est effectuée en interne et automatiquement, elle ne doit pas être gérée lors `Query`de la définition de.
  - Seuls les enregistrements nouvellement ingérés (différents dans chaque opération d’ingestion) seront pris en considération lors de la réception de la table dérivée `DerivedTableX`(dans ce cas).


```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-table-policy-update"></a>. Alter-fusionner la table table mise à jour de la stratégie

Cette commande modifie la stratégie de mise à jour de la table spécifiée.

**Syntaxe**

* `.alter-merge``table` *TableName* TableName `policy` *ArrayOfUpdatePolicyObjects* ArrayOfUpdatePolicyObjects `update`
* `.alter-merge``table` *DatabaseName* `update` *ArrayOfUpdatePolicyObjects* *TableName* TableName ArrayOfUpdatePolicyObjects`.` `policy`

*ArrayOfUpdatePolicyObjects* est un tableau JSON qui ne contient aucun ou plusieurs objets de stratégie de mise à jour définis.

**Remarques**

1. Il est recommandé d’utiliser des fonctions stockées pour l’implémentation en bloc de la propriété Query de l’objet de stratégie de mise à jour. Cela facilite la modification de la définition de fonction uniquement au lieu de l’objet de stratégie entier.

2. Les mêmes validations effectuées sur la stratégie de mise à jour en `alter` cas d’exécution d’une `alter-merge` commande pour une commande.

**Retourne**

La commande ajoute à l’objet de stratégie de mise à jour de la table (en remplaçant toute stratégie actuelle définie, le cas échéant), puis retourne la sortie de la commande correspondante [. afficher la stratégie de mise à jour de table de table](#show-update-policy) .

**Exemple**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-table-policy-update"></a>. supprimer la table table mise à jour de la stratégie

Supprime la stratégie de mise à jour de la table spécifiée.

**Syntaxe**

* `.delete``table` *TableName* TableName `policy``update`
* `.delete``table` *DatabaseName*DatabaseName`.`*TableName* , table `policy``update`

**Retourne**

La commande supprime l’objet de stratégie de mise à jour de la table, puis retourne la sortie de la commande correspondante [. afficher la stratégie de mise à jour de table](#show-update-policy) de table.

**Exemple**

```kusto
.delete table DerivedTableX policy update 
```