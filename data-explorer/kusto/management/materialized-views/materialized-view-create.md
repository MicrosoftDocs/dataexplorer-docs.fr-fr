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
ms.openlocfilehash: 807ca8a9bfd8d3f356fd849b6adc2102201513cf
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057159"
---
# <a name="create-materialized-view"></a>. créer une vue matérialisée

Une [vue matérialisée](materialized-view-overview.md) est une requête d’agrégation sur une table source, qui représente une seule instruction de synthèse.
Pour obtenir des informations générales et des instructions sur la création d’une vue matérialisée, consultez [créer une vue matérialisée](materialized-view-overview.md#create-a-materialized-view).

Requiert des autorisations d' [administrateur de base de données](../access-control/role-based-authorization.md) .

La vue matérialisée est toujours basée sur un unique `fact table` et peut également faire référence à un ou plusieurs [`dimension tables`](../../concepts/fact-and-dimension-tables.md) . Pour plus d’informations sur les limitations de jointure avec les tables de dimension dans les vues matérialisées, consultez [Propriétés](#properties). Pour connaître les limitations générales, consultez [limitations relatives à la création de vues matérialisées](materialized-view-overview.md#limitations-on-creating-materialized-views). 

* Suivez le processus de création avec la commande [. Show Operations](../operations.md#show-operations) .
* Annulez le processus de création à l’aide de la commande d' [opération. Cancel](#cancel-materialized-view-creation) .

## <a name="syntax"></a>Syntaxe

`.create` [`async`] `materialized-view` <br>
[ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] <br>
*ViewName* `on table` *SourceTableName* <br>
`{`<br>&nbsp;&nbsp;&nbsp;&nbsp;*Demande*<br>`}`

## <a name="arguments"></a>Arguments

|Argument|Type|Description
|----------------|-------|---|
|NomVue|Chaîne|Nom de la vue matérialisée. Le nom de la vue ne peut pas être en conflit avec des noms de table ou de fonction dans la même base de données et doit respecter les [règles d’attribution](../../query/schema-entities/entity-names.md#identifier-naming-rules)de noms d’identificateur. |
|SourceTableName|Chaîne|Nom de la table source sur laquelle la vue est définie.|
|Requête|Chaîne|Requête de vue matérialisée. Pour plus d’informations, consultez [query](#query-argument).|

### <a name="query-argument"></a>Argument de requête

La requête utilisée dans l’argument de vue matérialisée est limitée par les règles suivantes :

* L’argument de requête doit faire référence à une table de faits unique qui est la source de la vue matérialisée, inclure un seul opérateur de synthèse et une ou plusieurs fonctions d’agrégation agrégées par une ou plusieurs groupes par des expressions. L’opérateur de synthèse doit toujours être le dernier opérateur dans la requête.

* La requête ne doit pas inclure d’opérateurs qui dépendent de `now()` ou de `ingestion_time()` . Par exemple, la requête ne doit pas avoir `where Timestamp > ago(5d)` . Une vue matérialisée avec une `arg_max` / `arg_min` / `any` agrégation ne peut pas inclure les autres fonctions d’agrégation prises en charge. Limitez la durée couverte par la vue à l’aide de la stratégie de rétention de la vue matérialisée.

* Une vue est soit une `arg_max` / `arg_min` / `any` vue (ces fonctions peuvent être utilisées ensemble dans la même vue), soit l’une des autres fonctions prises en charge, mais pas les deux à la fois dans la même vue matérialisée. 
    Par exemple, `SourceTable | summarize arg_max(Timestamp, *), count() by Id` n’est pas pris en charge. 

* Les agrégations composites ne sont pas prises en charge dans la définition de vue matérialisée. Par exemple, au lieu de la vue suivante : `SourceTable | summarize Result=sum(Column1)/sum(Column2) by Id` , définissez la vue matérialisée comme suit : `SourceTable | summarize a=sum(Column1), b=sum(Column2) by Id` . Pendant l’affichage du temps de requête, exécutez- `ViewName | project Id, Result=a/b` . La sortie requise de la vue, y compris la colonne calculée ( `a/b` ), peut être encapsulée dans une [fonction stockée](../../query/functions/user-defined-functions.md). Accédez à la fonction stockée au lieu d’accéder directement à la vue matérialisée.

* Les requêtes entre clusters/bases de données croisées ne sont pas prises en charge.

* Les références aux [external_table ()](../../query/externaltablefunction.md) et [ExternalData](../../query/externaldata-operator.md) ne sont pas prises en charge.

## <a name="properties"></a>Propriétés

Les éléments suivants sont pris en charge dans la `with(propertyName=propertyValue)` clause. Toutes les propriétés sont facultatives.

|Propriété|Type|Description | Notes |
|----------------|-------|---|---|
|expiration|bool|Indique si la vue doit être créée en fonction de tous les enregistrements actuellement dans *SourceTable* ( `true` ) ou si elle doit être créée « à partir de maintenant-on » ( `false` ). La valeur par défaut est `false`.| La commande doit être `async` , et la vue ne sera pas disponible pour les requêtes tant que la création n’est pas terminée. Selon la quantité de données à renvoyer, la création avec le renvoi peut prendre beaucoup de temps. Il est intentionnellement « lent » pour s’assurer qu’il ne consomme pas trop de ressources du cluster. Voir l' [explication du renvoi](materialized-view-overview.md#create-a-materialized-view) |
|effectiveDateTime|DATETIME| S’il est spécifié avec `backfill=true` , la création de remplissages uniquement avec des enregistrements ingérés après la valeur DateTime. Le renvoi doit également avoir la valeur true. Attend un littéral DateTime, par exemple `effectiveDateTime=datetime(2019-05-01)`|
|dimensionTables|Array|Liste de tables de dimension séparées par des virgules dans la vue.|  Les tables de dimension doivent être appelées explicitement dans les propriétés de la vue. Les jointures/recherches avec les tables de dimension doivent utiliser les [meilleures pratiques de requête](../../query/best-practices.md). Consultez [jointure avec la table de dimension](#join-with-dimension-table).
|autoUpdateSchema|bool|Indique s’il faut mettre à jour automatiquement la vue sur les modifications de la table source. La valeur par défaut est `false`.| L' `autoUpdateSchema` option est valide uniquement pour les vues de type `arg_max(Timestamp, *)`  /  `arg_min(Timestamp, *)`  /  `any(*)` (uniquement lorsque l’argument Columns est `*` ). Si cette option a la valeur true, les modifications apportées à la table source sont automatiquement reflétées dans la vue matérialisée. Toutes les modifications apportées à la table source ne sont pas prises en charge lors de l’utilisation de cette option. Pour plus d’informations, consultez [. ALTER MATERIALIZED-VIEW](materialized-view-alter.md). |
|dossier|string|Dossier de la vue matérialisée.|
|docString|string|Chaîne qui documente la vue matérialisée|

> [!WARNING]
> L’utilisation de `autoUpdateSchema` peut entraîner une perte de données irréversible lorsque les colonnes de la table source sont supprimées. La vue est désactivée si elle n’a pas la valeur `autoUpdateSchema` et qu’une modification est apportée à la table source, ce qui entraîne une modification du schéma de la vue matérialisée. Si le problème est résolu, réactivez la vue matérialisée à l’aide de la commande [activer la vue matérialisée](materialized-view-enable-disable.md) . 
>
>Ce processus est courant lors de l’utilisation d’un `arg_max(Timestamp, *)` et de l’ajout de colonnes à la table source. Évitez l’échec en définissant la requête de la vue en tant que `arg_max(Timestamp, Column1, Column2, ...)` ou en utilisant l' `autoUpdateSchema` option. 

### <a name="join-with-dimension-table"></a>Jointure avec la table de dimension

Les enregistrements de la table source de la vue (table de faits) sont matérialisés une seule fois. Une latence d’ingestion différente entre la table de faits et la table de dimension peut avoir un impact sur les résultats de la vue.

**Exemple**: une définition de vue comprend une jointure interne avec une table de dimension. Au moment de la matérialisation, l’enregistrement de dimension n’a pas été entièrement ingéré, mais il a déjà été ingéré dans la table de faits. Cet enregistrement est supprimé de la vue et n’est plus retraité. 

Pour remédier à ce recours, supposez que la jointure est une jointure externe. L’enregistrement de la table de faits sera traité et ajouté à la vue avec une valeur null pour les colonnes de la table de dimension. Les enregistrements qui ont déjà été ajoutés (avec des valeurs null) à la vue ne seront pas traités à nouveau. Leurs valeurs, dans les colonnes de la table de dimension, restent null.


## <a name="examples"></a>Exemples

1. Créez un affichage vide qui matérialise uniquement les enregistrements qui sont ingérés à partir de maintenant : 

    <!-- csl -->
    ```
    .create materialized-view ArgMax on table T
    {
        T | summarize arg_max(Timestamp, *) by User
    }
    ```
    
1. Créer une vue matérialisée avec l’option de renvoi, à l’aide de `async` :

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
    .create materialized-view EnrichedArgMax on table T with (dimensionTable = ['DimUsers'])
    {
        T
        | lookup DimUsers on User  
        | summarize arg_max(Timestamp, *) by User 
    }
    
    .create materialized-view EnrichedArgMax on table T with (dimensionTable = ['DimUsers'])
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

    **Procédez**comme suit :
    
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

* N’incluez pas les transformations, les normalisations et d’autres calculs lourds qui peuvent être déplacés vers une [stratégie de mise à jour](../updatepolicy.md) dans le cadre de la définition de la vue matérialisée. Au lieu de cela, effectuez tous ces processus dans une stratégie de mise à jour et effectuez l’agrégation uniquement dans la vue matérialisée. Utilisez ce processus pour la recherche dans les tables de dimension, le cas échéant.

    **Procédez**comme suit :
    
    * Stratégie de mise à jour :
    
    ```kusto
    .alter-merge table Target policy update 
    @'[{"IsEnabled": true, 
        "Source": "SourceTable", 
        "Query": 
            "SourceTable 
            | extend ResourceId = strcat('subscriptions/', toupper(SubscriptionId), '/', resourceId)", 
        "IsTransactional": false}]'  
    ```
        
    * Vue matérialisée :
    
    ```kusto
    .create materialized-view Usage on table Events
    {
    &nbsp;     Target 
    &nbsp;     | summarize count() by ResourceId 
    }
    ```
    
    **Ne pas faire**:
    
    ```kusto
    .create materialized-view Usage on table SourceTable
    {
    &nbsp;     SourceTable 
    &nbsp;     | extend ResourceId = strcat('subscriptions/', toupper(SubscriptionId), '/', resourceId)
    &nbsp;     | summarize count() by ResourceId
    }
    ```

> [!NOTE]
> Si vous avez besoin des meilleures performances en matière de temps de requête, mais que vous pouvez sacrifier l’actualisation des données, utilisez la [fonction materialized_view ()](../../query/materialized-view-function.md).

## <a name="cancel-materialized-view-creation"></a>Annuler la création de la vue matérialisée

Annulez le processus de création d’une vue matérialisée lors de l’utilisation de l' `backfill` option. Cette action est utile lorsque la création prend trop de temps et que vous souhaitez l’abandonner lors de l’exécution.  

> [!WARNING]
> La vue matérialisée ne peut pas être restaurée après l’exécution de cette commande.

Le processus de création ne peut pas être abandonné immédiatement. La commande Cancel signale que la matérialisation est arrêtée et que la création vérifie régulièrement si l’annulation a été demandée. La commande Cancel attend une période maximale de 10 minutes jusqu’à ce que le processus de création de la vue matérialisée soit annulé et resignale si l’annulation a réussi. Même si l’annulation n’a pas réussi dans les 10 minutes, et que la commande Cancel signale un échec, la vue matérialisée s’abandonnera probablement plus tard dans le processus de création. La commande [. Show Operations](../operations.md#show-operations) indique si l’opération a été annulée. La `cancel operation` commande est uniquement prise en charge pour l’annulation de la création de vues matérialisées, et non pour l’annulation d’autres opérations.

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
|Opération|Chaîne|Type d’opération.
|StartedOn|DATETIME|Heure de début de l’opération de création.
|CancellationState|string|Un de- `Cancelled successfully` (la création a été annulée), ( `Cancellation failed` attente de l’annulation expirée), `Unknown` (la création de la vue n’est plus exécutée, mais n’a pas été annulée par cette opération).
|ReasonPhrase|string|Raison pour laquelle l’annulation n’a pas réussi.

### <a name="example"></a>Exemple

<!-- csl -->
```
.cancel operation c4b29441-4873-4e36-8310-c631c35c916e
```

|OperationId|Opération|StartedOn|CancellationState|ReasonPhrase|
|---|---|---|---|---|
|c4b29441-4873-4e36-8310-c631c35c916e|MaterializedViewCreateOrAlter|2020-05-08 19:45:03.9184142|Annulation réussie||

Si l’annulation ne s’est pas terminée dans les 10 minutes, `CancellationState` indique un échec. La création peut ensuite être abandonnée.
