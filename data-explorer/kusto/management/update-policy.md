---
title: Stratégie de mise à jour - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique de mise à jour dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 95952a6f4e7a8c0d1a5b4207742e15873eb44c91
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519531"
---
# <a name="update-policy"></a>Mettre à jour la stratégie

La [stratégie de mise à jour](updatepolicy.md) est un objet de stratégie au niveau de la table pour exécuter automatiquement une requête et ingérer ses résultats lorsque les données sont ingérées dans un autre tableau.

## <a name="show-update-policy"></a>Afficher la politique de mise à jour

Cette commande renvoie la stratégie de mise à jour `*` du tableau spécifié, ou toutes les tables de la base de données par défaut si elle est utilisée comme nom de table.

**Syntaxe**

* `.show``table` *Nom de la table* `policy``update`
* `.show``table` *DatabaseName*`.`*TableName* `policy``update`
* `.show` `table` `*` `policy` `update`

**Retourne**

Cette commande renvoie un tableau qui a un enregistrement par tableau, avec les colonnes suivantes :

|Colonne    |Type    |Description                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|Le nom de l’entité la stratégie de mise à jour est défini sur                                                                                                                |
|Stratégies  |`string`|Un tableau JSON indiquant toutes les stratégies de mise à jour définies pour l’entité, formatées comme [objet de stratégie de mise à jour](updatepolicy.md#the-update-policy-object)|

**Exemple**

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |Stratégies                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|[TestDB]. [DerivedTableX]|["IsEnabled": true, "Source": "MyTableX","Query": "MyUpdateFunction()","IsTransactional": false,"PropagateIngestionProperties": faux'|

## <a name="alter-update-policy"></a>Modifier la politique de mise à jour

Cette commande définit la stratégie de mise à jour du tableau spécifié.

**Syntaxe**

* `.alter``table` *TableName* `policy` `update` *ArrayOfUpdatePolicyObjects*
* `.alter``table` *DatabaseName*`.`*TableName* `policy` `update` *ArrayOfUpdatePolicyObjects*

*ArrayOfUpdatePolicyObjects* est un tableau JSON qui a zéro ou plus d’objets de stratégie de mise à jour définis.

**Remarques**

1. Il est recommandé d’utiliser une `Query` fonction stockée pour la propriété de l’objet de la stratégie de mise à jour.
   Il est donc facile de modifier seulement la définition de fonction au lieu de l’objet de stratégie entier.

2. Les validations suivantes sont effectuées sur la stratégie de `IsEnabled` mise `true`à jour lorsqu’elle est définie (au cas où elle est réglée pour ):
    1. `Source`: Le tableau doit exister dans la base de données cible.
    2. `Query`: 
        * Le schéma défini par le schéma doit correspondre à celui de la table cible. 
        * La requête doit `source` faire référence au tableau de la stratégie de mise à jour. Définir une requête en *not* politique de mise à `AllowUnreferencedSourceTable=true` jour qui ne fait pas référence à la source est possible en définissant les propriétés avec (voir l’exemple ci-dessous), mais n’est généralement pas recommandé pour des raisons de rendement (il implique que pour chaque ingestion à la table source, *tous les* enregistrements dans un tableau différent sont considérés pour l’exécution de la stratégie de mise à jour).
    3. La stratégie n’a pas lieu avec la création d’un cycle dans la chaîne des politiques de mise à jour dans la base de données cible.
    4. Dans `IsTransactional` le cas `true` `TableAdmin` où est réglé `Source` à , les autorisations sont vérifiées contre (la table source) ainsi.
  
3. Assurez-vous de tester votre stratégie de mise à jour / fonction pour les performances avant de l’appliquer pour exécuter sur chaque ingestion à la table source - voir [ici](updatepolicy.md#testing-an-update-policys-performance-impact).

**Retourne**

La commande définit l’objet de stratégie de mise à jour de la table (en prépondant toute stratégie actuelle définie, le cas échéant) et renvoie ensuite la sortie de la commande de stratégie de [mise à jour TABLE](#show-update-policy) de table correspondante .

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

- Lorsqu’une ingestion au tableau source `MyTableX`(dans ce cas) se produit, une ou plusieurs étendues (éclats de données) sont créées dans ce tableau.
- Le `Query` qui est défini dans l’objet `MyUpdateFunction()`de la stratégie de mise à jour (dans ce cas) ne s’exécutera que sur ces étendues, et ne s’exécutera pas sur la table entière.
  - Cette "scoping" se fait en interne et automatiquement, `Query`elle ne doit pas être traitée lors de la définition de la .
  - Seuls les enregistrements nouvellement ingérés (différents dans chaque opération d’ingestion) seront `DerivedTableX`pris en considération lors de l’ingestion à la table dérivée (dans ce cas).


```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-table-policy-update"></a>.alter-fusion table TABLE mise à jour de la politique

Cette commande modifie la stratégie de mise à jour du tableau spécifié.

**Syntaxe**

* `.alter-merge``table` *TableName* `policy` `update` *ArrayOfUpdatePolicyObjects*
* `.alter-merge``table` *DatabaseName*`.`*TableName* `policy` `update` *ArrayOfUpdatePolicyObjects*

*ArrayOfUpdatePolicyObjects* est un tableau JSON qui a zéro ou plus d’objets de stratégie de mise à jour définis.

**Remarques**

1. Il est recommandé d’utiliser des fonctions stockées pour la mise en œuvre en vrac de la propriété de requête de l’objet de la stratégie de mise à jour. Il est donc facile de modifier seulement la définition de fonction au lieu de l’objet de stratégie entier.

2. Les mêmes validations effectuées sur la `alter` stratégie de mise `alter-merge` à jour en cas de commande sont effectuées pour une commande.

**Retourne**

La commande annexe à l’objet de stratégie de mise à jour de la table (en prépondant toute stratégie actuelle définie, le cas échéant) et renvoie ensuite la sortie de la commande de stratégie de mise à jour TABLE de [table correspondante .show](#show-update-policy) table.

**Exemple**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-table-policy-update"></a>.supprimer la mise à jour de la stratégie TABLE TABLE

Supprime la stratégie de mise à jour du tableau spécifié.

**Syntaxe**

* `.delete``table` *Nom de la table* `policy``update`
* `.delete``table` *DatabaseName*`.`*TableName* `policy``update`

**Retourne**

La commande supprime l’objet de stratégie de mise à jour du tableau, puis renvoie la sortie de la commande de stratégie de [mise à jour TABLE de table .show](#show-update-policy) correspondante.

**Exemple**

```kusto
.delete table DerivedTableX policy update 
```