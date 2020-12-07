---
title: Créer une vue matérialisée-Azure Explorateur de données
description: Cet article explique comment créer des vues matérialisées dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 3cc5efa2d8b738c58d94d0db397218663fd76740
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96470211"
---
# <a name="create-materialized-view"></a>.create materialized-view

Une [vue matérialisée](materialized-view-overview.md) est une requête d’agrégation sur une table source, qui représente une seule instruction de synthèse.

Il existe deux façons de créer une vue matérialisée, notée par l’option de *renvoi* dans la commande :

 * **Créez en fonction des enregistrements existants dans la table source :** 
      * La création peut prendre un certain temps, en fonction du nombre d’enregistrements dans la table source. La vue ne sera pas disponible pour les requêtes tant que le renvoi n’est pas terminé.
      * Quand vous utilisez cette option, la commande CREATE doit être `async` et l’exécution peut être surveillée à l’aide de la commande [. Show Operations](../operations.md#show-operations) .

    * L’annulation du processus de renvoi est possible à l’aide de la commande [opération. Cancel](#cancel-materialized-view-creation) .

      > [!IMPORTANT]
      > L’utilisation de l’option de renvoi peut prendre beaucoup de temps pour les tables sources volumineuses. Si ce processus échoue de façon transitoire lors de son exécution, il ne sera pas automatiquement retenté et une nouvelle exécution de la commande Create est nécessaire. Pour plus d’informations, consultez la section [renvoi d’une vue matérialisée](#backfill-a-materialized-view) .
    
* **Créez la vue matérialisée à partir de maintenant :**
    * La vue matérialisée est créée vide et n’inclut que les enregistrements ingérés après la création de la vue. La création de ce type est immédiatement retournée, ne nécessite pas `async` , et la vue est immédiatement disponible pour la requête.

L’opération de création requiert des autorisations d' [administrateur de base de données](../access-control/role-based-authorization.md) . Le créateur de la vue matérialisée en devient l’administrateur.

## <a name="syntax"></a>Syntaxe

`.create` [`async`] `materialized-view` <br>
[ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] <br>
*ViewName* `on table` *SourceTableName* <br>
`{`<br>&nbsp;&nbsp;&nbsp;&nbsp;*Demande*<br>`}`

## <a name="arguments"></a>Arguments

|Argument|Type|Description
|----------------|-------|---|
|NomVue|String|Nom de la vue matérialisée. Le nom de la vue ne peut pas être en conflit avec des noms de table ou de fonction dans la même base de données et doit respecter les [règles d’attribution](../../query/schema-entities/entity-names.md#identifier-naming-rules)de noms d’identificateur. |
|SourceTableName|String|Nom de la table source sur laquelle la vue est définie.|
|Requête|String|Requête de vue matérialisée. Pour plus d’informations, consultez [query](#query-argument).|

### <a name="query-argument"></a>Argument de requête

La requête utilisée dans l’argument de vue matérialisée est limitée par les règles suivantes :

* L’argument de requête doit faire référence à une table de faits unique qui est la source de la vue matérialisée, inclure un seul opérateur de synthèse et une ou plusieurs fonctions d’agrégation agrégées par une ou plusieurs groupes par des expressions. L’opérateur de synthèse doit toujours être le dernier opérateur dans la requête.

* Une vue est soit une `arg_max` / `arg_min` / `any` vue (ces fonctions peuvent être utilisées ensemble dans la même vue), soit l’une des autres fonctions prises en charge, mais pas les deux à la fois dans la même vue matérialisée. 
    Par exemple, `SourceTable | summarize arg_max(Timestamp, *), count() by Id` n’est pas pris en charge. 

* La requête ne doit pas inclure d’opérateurs qui dépendent de `now()` ou de `ingestion_time()` . Par exemple, la requête ne doit pas avoir `where Timestamp > ago(5d)` . Une vue matérialisée avec une `arg_max` / `arg_min` / `any` agrégation ne peut pas inclure les autres fonctions d’agrégation prises en charge. Limitez la durée couverte par la vue à l’aide de la stratégie de rétention de la vue matérialisée.

* Les agrégations composites ne sont pas prises en charge dans la définition de vue matérialisée. Par exemple, au lieu de la vue suivante : `SourceTable | summarize Result=sum(Column1)/sum(Column2) by Id` , définissez la vue matérialisée comme suit : `SourceTable | summarize a=sum(Column1), b=sum(Column2) by Id` . Pendant l’affichage du temps de requête, exécutez- `ViewName | project Id, Result=a/b` . La sortie requise de la vue, y compris la colonne calculée ( `a/b` ), peut être encapsulée dans une [fonction stockée](../../query/functions/user-defined-functions.md). Accédez à la fonction stockée au lieu d’accéder directement à la vue matérialisée.

* Les requêtes entre clusters/bases de données croisées ne sont pas prises en charge.

* Les références aux [external_table ()](../../query/externaltablefunction.md) et [ExternalData](../../query/externaldata-operator.md) ne sont pas prises en charge.

* En plus de la table source de la vue, elle peut également faire référence à un ou plusieurs [`dimension tables`](../../concepts/fact-and-dimension-tables.md) . Les tables de dimension doivent être appelées explicitement dans les propriétés de la vue. Il est important de comprendre le comportement de la jointure avec les tables de dimension :

    * Les enregistrements de la table source de la vue (table de faits) sont matérialisés une seule fois. Une latence d’ingestion différente entre la table de faits et la table de dimension peut avoir un impact sur les résultats de la vue.

    * **Exemple**: une définition de vue comprend une jointure interne avec une table de dimension. Au moment de la matérialisation, l’enregistrement de dimension n’a pas été entièrement ingéré, mais il a déjà été ingéré dans la table de faits. Cet enregistrement est supprimé de la vue et n’est plus retraité. 

        De même, si la jointure est une jointure externe, l’enregistrement de la table de faits est traité et ajouté à la vue avec une valeur null pour les colonnes de la table de dimension. Les enregistrements qui ont déjà été ajoutés (avec des valeurs null) à la vue ne seront pas traités à nouveau. Leurs valeurs, dans les colonnes de la table de dimension, restent null.

## <a name="properties"></a>Propriétés

Les éléments suivants sont pris en charge dans la `with(propertyName=propertyValue)` clause. Toutes les propriétés sont facultatives.

|Propriété|Type|Description |
|----------------|-------|---|
|expiration|bool|Indique si la vue doit être créée en fonction de tous les enregistrements actuellement dans *SourceTable* ( `true` ) ou si elle doit être créée « à partir de maintenant-on » ( `false` ). La valeur par défaut est `false`.| 
|effectiveDateTime|DATETIME| S’il est spécifié avec `backfill=true` , la création de remplissages uniquement avec des enregistrements ingérés après la valeur DateTime. Le renvoi doit également avoir la valeur true. Attend un littéral DateTime, par exemple `effectiveDateTime=datetime(2019-05-01)`|
|dimensionTables|Array|Liste de tables de dimension séparées par des virgules dans la vue. Voir l' [argument de requête](#query-argument)
|autoUpdateSchema|bool|Indique s’il faut mettre à jour automatiquement la vue sur les modifications de la table source. La valeur par défaut est `false`. Cette option est valide uniquement pour les vues de type `arg_max(Timestamp, *)`  /  `arg_min(Timestamp, *)`  /  `any(*)` (uniquement lorsque l’argument Columns est `*` ). Si cette option a la valeur true, les modifications apportées à la table source sont automatiquement reflétées dans la vue matérialisée.
|dossier|string|Dossier de la vue matérialisée.|
|docString|string|Chaîne qui documente la vue matérialisée|

> [!WARNING]
> * L’utilisation de `autoUpdateSchema` peut entraîner une perte de données irréversible lorsque les colonnes de la table source sont supprimées. 
> * Si une modification est apportée à la table source, entraînant une modification de schéma dans la vue matérialisée, et `autoUpdateSchema` la valeur false, la vue sera automatiquement désactivée. 
>    * Cette erreur est courante lors de l’utilisation d’un `arg_max(Timestamp, *)` et de l’ajout de colonnes à la table source. 
>    * Évitez ce problème en définissant la requête de la vue en tant que `arg_max(Timestamp, Column1, Column2, ...)` ou en utilisant l' `autoUpdateSchema` option.
> * Si la vue est désactivée pour ces raisons, vous pouvez la réactiver après avoir corrigé le problème à l’aide de la commande [activer la vue matérialisée](materialized-view-enable-disable.md) .
>

## <a name="examples"></a>Exemples

1. Créez une vue de arg_max vide qui matérialise uniquement les enregistrements ingérés à partir de maintenant :

    <!-- csl -->
    ```
    .create materialized-view ArgMax on table T
    {
        T | summarize arg_max(Timestamp, *) by User
    }
    ```
    
1. Créer une vue matérialisée pour les agrégats quotidiens avec l’option de renvoi, à l’aide de `async` :

    <!-- csl -->
    ```
    .create async materialized-view with (backfill=true, docString="Customer telemetry") CustomerUsage on table T
    {
        T 
        | extend Day = bin(Timestamp, 1d)
        | summarize count(), dcount(User), max(Duration) by Customer, Day 
    } 
    ```
    
1. Créez une vue matérialisée avec le renvoi et `effectiveDateTime` . La vue est créée en fonction des enregistrements de la valeur DateTime uniquement :

    <!-- csl -->
    ```
    .create async materialized-view with (backfill=true, effectiveDateTime=datetime(2019-01-01)) CustomerUsage on table T 
    {
        T 
        | extend Day = bin(Timestamp, 1d)
        | summarize count(), dcount(User), max(Duration) by Customer, Day
    } 
    ```

1. Vue matérialisée qui déduplique la table source, en fonction de la colonne EventId :

    <!-- csl -->
    ```
    .create materialized-view DedupedT on table T
    {
        T
        | summarize any(*) by EventId
    }
    ```

1. La définition peut inclure des opérateurs supplémentaires avant l' `summarize` instruction, tant que `summarize` est le dernier :

    <!-- csl -->
    ```
    .create materialized-view CustomerUsage on table T 
    {
        T 
        | where Customer in ("Customer1", "Customer2", "CustomerN")
        | parse Url with "https://contoso.com/" Api "/" *
        | extend Month = startofmonth(Timestamp)
        | summarize count(), dcount(User), max(Duration) by Customer, Api, Month
    }
    ```

1. Vues matérialisées qui se joignent à une table de dimension :

    <!-- csl -->
    ```
    .create materialized-view EnrichedArgMax on table T with (dimensionTables = ['DimUsers'])
    {
        T
        | lookup DimUsers on User  
        | summarize arg_max(Timestamp, *) by User 
    }
    
    .create materialized-view EnrichedArgMax on table T with (dimensionTables = ['DimUsers'])
    {
        DimUsers | project User, Age, Address
        | join kind=rightouter hint.strategy=broadcast T on User
        | summarize arg_max(Timestamp, *) by User 
    }
    ```
    

## <a name="supported-aggregation-functions"></a>Fonctions d’agrégation prises en charge

Les fonctions d’agrégation suivantes sont prises en charge :

* [`count`](../../query/count-aggfunction.md)
* [`countif`](../../query/countif-aggfunction.md)
* [`dcount`](../../query/dcount-aggfunction.md)
* [`dcountif`](../../query/dcountif-aggfunction.md)
* [`min`](../../query/min-aggfunction.md)
* [`max`](../../query/max-aggfunction.md)
* [`avg`](../../query/avg-aggfunction.md)
* [`avgif`](../../query/avgif-aggfunction.md)
* [`sum`](../../query/sum-aggfunction.md)
* [`arg_max`](../../query/arg-max-aggfunction.md)
* [`arg_min`](../../query/arg-min-aggfunction.md)
* [`any`](../../query/any-aggfunction.md)
* [`hll`](../../query/hll-aggfunction.md)
* [`make_set`](../../query/makeset-aggfunction.md)
* [`make_list`](../../query/makelist-aggfunction.md)
* [`percentile`, `percentiles`](../../query/percentiles-aggfunction.md)

## <a name="performance-tips"></a>Conseils sur les performances

* Les filtres de requête de vue matérialisée sont optimisés lorsqu’ils sont filtrés par une des dimensions de vue matérialisée (agrégation par clause). Si vous savez que votre modèle de requête est souvent filtré par une colonne, qui peut être une dimension de la vue matérialisée, incluez-la dans la vue. Par exemple : pour une vue matérialisée qui expose un `arg_max` par `ResourceId` qui sera souvent filtré par `SubscriptionId` , la recommandation est la suivante :

    **Procédez** comme suit :
    
    ```kusto
    .create materialized-view ArgMaxResourceId on table FactResources
    {
        FactResources | summarize arg_max(Timestamp, *) by SubscriptionId, ResouceId 
    }
    ``` 
    
    **Ne pas faire**:
    
    ```kusto
    .create materialized-view ArgMaxResourceId on table FactResources
    {
        FactResources | summarize arg_max(Timestamp, *) by ResouceId 
    }
    ```

* N’incluez pas les transformations, les normalisations, les recherches dans les tables de dimension et les autres calculs lourds qui peuvent être déplacés vers une [stratégie de mise à jour](../updatepolicy.md) dans le cadre de la définition de la vue matérialisée. Au lieu de cela, effectuez tous ces processus dans une stratégie de mise à jour et effectuez l’agrégation uniquement dans la vue matérialisée.

    **Procédez** comme suit :
    
    * Stratégie de mise à jour :
    
    ```kusto
    .alter-merge table Target policy update 
    @'[{"IsEnabled": true, 
        "Source": "SourceTable", 
        "Query": 
            "SourceTable 
            | extend ResourceId = strcat('subscriptions/', toupper(SubscriptionId), '/', resourceId)", 
            | lookup DimResources on ResourceId
        "IsTransactional": false}]'  
    ```
        
    * Vue matérialisée :
    
    ```kusto
    .create materialized-view Usage on table Events
    {
        Target 
        | summarize count() by ResourceId 
    }
    ```
    
    **Ne pas faire**:
    
    ```kusto
    .create materialized-view Usage on table SourceTable
    {
        SourceTable
        | extend ResourceId = strcat('subscriptions/', toupper(SubscriptionId), '/', resourceId)
        | lookup DimResources on ResourceId
        | summarize count() by ResourceId
    }
    ```

> [!TIP]
> Si vous avez besoin des meilleures performances en matière de temps de requête, mais que vous pouvez tolérer une certaine latence des données, utilisez la [fonction materialized_view ()](../../query/materialized-view-function.md).

## <a name="backfill-a-materialized-view"></a>Renvoi d’une vue matérialisée

Lors de la création d’une vue matérialisée avec la `backfill` propriété, la vue matérialisée est créée en fonction des enregistrements disponibles dans la table source (ou d’un sous-ensemble de ces enregistrements, si `effectiveDateTime` est utilisé). Le renvoi peut prendre beaucoup de temps pour les tables sources volumineuses.

* L’utilisation de l’option de renvoi n’est pas prise en charge pour les données dans le cache à froid. Augmentez la période de cache à chaud, si nécessaire, pour la durée de la création de la vue. Cela peut nécessiter une montée en puissance parallèle.

* En arrière-plan, le processus de renvoi fractionne les données en renvoi à plusieurs lots et utilise plusieurs opérations de réception pour renvoyer la vue. Les échecs temporaires qui se produisent dans le cadre du processus de renvoi sont retentés, mais si toutes les nouvelles tentatives sont épuisées, une réexécution manuelle de la commande CREATE peut être nécessaire.

* Vous pouvez essayer de modifier quelques propriétés si vous rencontrez des problèmes lors de la création de la vue :

    * `MaxSourceRecordsForSingleIngest` -par défaut, le nombre d’enregistrements sources dans chaque opération de réception, pendant le renvoi, est de 2 millions enregistrements par nœud. Vous pouvez modifier ce paramètre par défaut en définissant cette propriété sur le nombre d’enregistrements souhaité (la valeur est le nombre _total_ d’enregistrements dans chaque opération de réception). La diminution de cette valeur peut être utile lorsque la création échoue sur les limites de la mémoire/les délais d’expiration des requêtes. L’amélioration de cette valeur peut accélérer la création d’une vue, en supposant que le cluster est en mesure d’exécuter la fonction d’agrégation sur plus d’enregistrements que la valeur par défaut.

    * `Concurrency` -les opérations de réception, exécutées dans le cadre du processus de renvoi, s’exécutent simultanément. Par défaut, l’accès concurrentiel est `min(number_of_nodes * 2, 5)` . Vous pouvez définir cette propriété pour augmenter/réduire la concurrence. L’extension de cette valeur est recommandée uniquement si l’UC du cluster est faible, car cela peut avoir un impact significatif sur la consommation du processeur du cluster.

  Par exemple, la commande suivante renverra la vue matérialisée de `2020-01-01` , avec un nombre maximal d’enregistrements dans chaque opération de réception des `3 million` enregistrements, et exécutera les opérations de réception avec concurrence `2` de : 
    
    <!-- csl -->
    ```
    .create async materialized-view with (
            backfill=true,
            effectiveDateTime=datetime(2019-01-01),
            MaxSourceRecordsForSingleIngest=3000000,
            Concurrency=2
        )
        CustomerUsage on table T
    {
        T
        | summarize count(), dcount(User), max(Duration) by Customer, bin(Timestamp, 1d)
    } 
    ```

## <a name="limitations-on-creating-materialized-views"></a>Limitations relatives à la création de vues matérialisées

* Impossible de créer une vue matérialisée :
    * Au-dessus d’une autre vue matérialisée.
    * Sur les [bases de données de suivi](../../../follower.md). Les bases de données de suivi sont en lecture seule et les vues matérialisées nécessitent des opérations d’écriture.  Les vues matérialisées définies sur les bases de données de leader peuvent être interrogées à partir de leurs abonnés, comme n’importe quelle autre table dans le responsable. 
* La table source d’une vue matérialisée :
    * Doit être une table qui est ingérée directement, soit à l’aide de l’une des [méthodes](../../../ingest-data-overview.md#ingestion-methods-and-tools)d’ingestion, à l’aide d’une [stratégie de mise à jour](../updatepolicy.md), soit en ingestion [des commandes de requête](../data-ingestion/ingest-from-query.md).
        * En particulier, l’utilisation d' [étendues de déplacement](../move-extents.md) d’autres tables dans la table source de la vue matérialisée n’est pas prise en charge. Les étendues de déplacement peuvent échouer avec l’erreur suivante : `Cannot drop/move extents from/to table 'TableName' since Materialized View 'ViewName' is currently processing some of these extents` . 
    * La [stratégie IngestionTime](../ingestiontimepolicy.md) doit être activée (la valeur par défaut est activé).
    * Ne peut pas être activé pour l’ingestion de diffusion en continu.
    * Il ne peut pas s’agir d’une table restreinte ou d’une table pour laquelle la sécurité au niveau des lignes est activée.
* Les [fonctions de curseur](../databasecursor.md#cursor-functions) ne peuvent pas être utilisées sur des vues matérialisées.
* L’exportation continue à partir d’une vue matérialisée n’est pas prise en charge.

## <a name="cancel-materialized-view-creation"></a>Annuler la création de la vue matérialisée

Annulez le processus de création d’une vue matérialisée lors de l’utilisation de l' `backfill` option. Cette action est utile lorsque la création prend trop de temps et que vous souhaitez l’abandonner lors de l’exécution.  

> [!WARNING]
> La vue matérialisée ne peut pas être restaurée après l’exécution de cette commande.

Le processus de création ne peut pas être abandonné immédiatement. La commande Cancel signale que la matérialisation est arrêtée et que la création vérifie régulièrement si l’annulation a été demandée. La commande Cancel attend une période maximale de 10 minutes jusqu’à ce que le processus de création de la vue matérialisée soit annulé et resignale si l’annulation a réussi. Même si l’annulation n’a pas réussi dans les 10 minutes, et que la commande Cancel signale un échec, la vue matérialisée s’abandonnera probablement plus tard dans le processus de création. La [`.show operations`](../operations.md#show-operations) commande indique si l’opération a été annulée. La `cancel operation` commande est uniquement prise en charge pour l’annulation de la création de vues matérialisées, et non pour l’annulation d’autres opérations.

### <a name="syntax"></a>Syntaxe

`.cancel` `operation` *operationId*

### <a name="properties"></a>Propriétés

|Propriété|Type|Description
|----------------|-------|---|
|operationId|Guid|L’ID d’opération retourné par la commande CREATE MATERIALIZED-VIEW.|

### <a name="output"></a>Output

|Paramètre de sortie |Type |Description
|---|---|---
|OperationId|Guid|ID d’opération de la commande créer une vue matérialisée.
|Opération|String|Type d’opération.
|StartedOn|DATETIME|Heure de début de l’opération de création.
|CancellationState|string|Un de- `Cancelled successfully` (la création a été annulée), ( `Cancellation failed` attente de l’annulation expirée), `Unknown` (la création de la vue n’est plus exécutée, mais n’a pas été annulée par cette opération).
|ReasonPhrase|string|Raison pour laquelle l’annulation n’a pas réussi.

### <a name="example"></a> Exemple

<!-- csl -->
```
.cancel operation c4b29441-4873-4e36-8310-c631c35c916e
```

|OperationId|Opération|StartedOn|CancellationState|ReasonPhrase|
|---|---|---|---|---|
|c4b29441-4873-4e36-8310-c631c35c916e|MaterializedViewCreateOrAlter|2020-05-08 19:45:03.9184142|Annulation réussie||

Si l’annulation ne s’est pas terminée dans les 10 minutes, `CancellationState` indique un échec. La création peut ensuite être abandonnée.
