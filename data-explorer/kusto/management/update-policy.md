---
title: Gestion de la stratégie de mise à jour Kusto-Azure Explorateur de données
description: Cet article décrit la stratégie de mise à jour dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/04/2020
ms.openlocfilehash: 111110ac69e726c8367af4a2741a79061df7531a
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803860"
---
# <a name="update-policy-commands"></a>mettre à jour les commandes de stratégie

La [stratégie de mise à jour](updatepolicy.md) est un objet de stratégie au niveau de la table qui exécute automatiquement une requête, puis ingère les résultats lorsque les données sont ingérées dans une autre table.

## <a name="show-update-policy"></a>Afficher la stratégie de mise à jour

Cette commande renvoie la stratégie de mise à jour de la table spécifiée ou de toutes les tables de la base de données par défaut si `*` est utilisé comme nom de table.

### <a name="syntax"></a>Syntaxe

* `.show``table` *TableName* TableName `policy``update`
* `.show``table` *DatabaseName*, `.` *TableName* table `policy``update`
* `.show` `table` `*` `policy` `update`

### <a name="returns"></a>Retours

Cette commande retourne une table qui comporte un enregistrement par table.

|Colonne    |Type    |Description                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|Nom de l’entité sur laquelle la stratégie de mise à jour est définie                                                                                                                |
|Stratégies  |`string`|Tableau JSON qui indique toutes les stratégies de mise à jour définies pour l’entité, mises en forme en tant qu' [objet de stratégie de mise à jour](updatepolicy.md#the-update-policy-object)|

### <a name="example"></a>Exemple

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |Stratégies                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|[TestDB]. [DerivedTableX]|[{"IsEnabled" : true, "source" : "MyTableX", "Query" : "MyUpdateFunction ()", "IsTransactional" : false, "PropagateIngestionProperties" : false}]|

## <a name="alter-update-policy"></a>Modifier la stratégie de mise à jour

Cette commande définit la stratégie de mise à jour de la table spécifiée.

### <a name="syntax"></a>Syntaxe

* `.alter``table` *TableName* `policy` TableName `update` *ArrayOfUpdatePolicyObjects*
* `.alter``table` *DatabaseName* `.` *TableName* `policy` `update` *ArrayOfUpdatePolicyObjects*

*ArrayOfUpdatePolicyObjects* est un tableau JSON qui ne contient aucun ou plusieurs objets de stratégie de mise à jour définis.

> [!NOTE]
> * Utilisez une fonction stockée pour la `Query` propriété de l’objet de stratégie de mise à jour.
   Il vous suffit de modifier la définition de la fonction, au lieu de l’intégralité de l’objet de stratégie.
> * Si `IsEnabled` a la valeur `true` , les validations suivantes sont effectuées sur la stratégie de mise à jour, car elle est définie :
>    * `Source`-Vérifie que la table existe dans la base de données cible.
>    * `Query` 
>        * Vérifie que le schéma défini par le schéma correspond à celui de la table cible.
>        * Vérifie que la requête fait référence `source` à la table de la stratégie de mise à jour. 
        Il est possible de définir une requête de stratégie de mise à jour qui ne fait pas référence à la source en définissant `AllowUnreferencedSourceTable=true` dans le *avec* les propriétés (Voir l’exemple ci-dessous), mais cela n’est pas recommandé en raison de problèmes de performances. Pour chaque ingestion de la table source, tous les enregistrements d’une table différente sont pris en compte pour l’exécution de la stratégie de mise à jour.
 >       * Vérifie que la stratégie n’entraîne pas la création d’un cycle dans la chaîne de stratégies de mise à jour dans la base de données cible.
 > * Si `IsTransactional` a la valeur `true` , vérifie que les `TableAdmin` autorisations sont également vérifiées par rapport `Source` (la table source).
 > * Testez les performances de votre stratégie ou de votre fonction de mise à jour avant de l’appliquer à chaque ingestion de la table source. Pour plus d’informations, consultez [test de l’impact sur les performances d’une stratégie de mise à jour](updatepolicy.md#performance-impact).

### <a name="returns"></a>Retours

La commande définit l’objet de stratégie de mise à jour de la table, en remplaçant toute stratégie actuelle, puis retourne la sortie de la commande correspondante [. afficher la stratégie de mise à jour de table](#show-update-policy) .

### <a name="example"></a>Exemple

```kusto
// Create a function that will be used for update
.create function 
MyUpdateFunction()
{
    MyTableX
    | where ColumnA == 'some-string'
    | summarize MyCount=count() by ColumnB, Key=ColumnC
    | join (OtherTable | project OtherColumnZ, Key=OtherColumnC) on Key
    | project ColumnB, ColumnZ=OtherColumnZ, Key, MyCount
}

// Create the target table (if it doesn't already exist)
.set-or-append DerivedTableX <| MyUpdateFunction() | limit 0

// Use update policy on table DerivedTableX
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyUpdateFunction()", "IsTransactional": false, "PropagateIngestionProperties": false}]'
```

* Lorsqu’une ingestion a lieu dans la table source, dans ce cas, `MyTableX` une ou plusieurs étendues (partitions de données) sont créées dans cette table.
* Le `Query` défini dans l’objet de stratégie de mise à jour, dans ce cas `MyUpdateFunction()` , s’exécute uniquement sur ces étendues et ne s’exécute pas sur la table entière.
  * La « portée » est effectuée en interne et automatiquement, et ne doit pas être gérée lors de la définition de `Query` .
  * Seuls les enregistrements nouvellement ingérés, qui sont différents dans chaque opération d’ingestion, sont pris en considération lors de la réception de la `DerivedTableX` table dérivée.

```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-tablename-policy-update"></a>. Alter-fusionner la table *nom_table* mettre à jour la stratégie

Cette commande modifie la stratégie de mise à jour de la table spécifiée.

**Syntaxe**

* `.alter-merge``table` *TableName* `policy` TableName `update` *ArrayOfUpdatePolicyObjects*
* `.alter-merge``table` *DatabaseName* `.` *TableName* `policy` `update` *ArrayOfUpdatePolicyObjects*

*ArrayOfUpdatePolicyObjects* est un tableau JSON qui ne contient aucun ou plusieurs objets de stratégie de mise à jour définis.

> [!NOTE]
> * Utilisez des fonctions stockées pour l’implémentation en bloc de la propriété Query de l’objet de stratégie de mise à jour. 
     Il vous suffit de modifier la définition de fonction au lieu de l’objet de stratégie entier.
> * Les validations sont les mêmes que celles effectuées sur une `alter` commande.

**Retourne**

La commande ajoute à l’objet de stratégie de mise à jour de la table, en remplaçant toute stratégie actuelle, puis retourne la sortie de la commande de [mise à jour de la table *TableName* correspondante.](#show-update-policy)

**Exemple**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-tablename-policy-update"></a>. supprimer la table de mise à jour de la stratégie *TableName*

Supprime la stratégie de mise à jour de la table spécifiée.

**Syntaxe**

* `.delete``table` *TableName* TableName `policy``update`
* `.delete``table` *DatabaseName*, `.` *TableName* table `policy``update`

**Retourne**

La commande supprime l’objet de stratégie de mise à jour de la table, puis retourne la sortie de la commande correspondante [. afficher la stratégie de mise à jour de table *TableName* ](#show-update-policy) .

**Exemple**

```kusto
.delete table DerivedTableX policy update 
```
