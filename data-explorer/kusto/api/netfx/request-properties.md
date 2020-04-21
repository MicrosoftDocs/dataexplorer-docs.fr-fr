---
title: Propriétés de demande, ClientRequestProperties - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les propriétés De la demande, ClientRequestProperties dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/23/2019
ms.openlocfilehash: 0aa496e6011fce98db5a304b9748f27f07590b4f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524325"
---
# <a name="request-properties-clientrequestproperties"></a>Propriétés de demande, ClientRequestProperties

Lorsque vous faites une demande de Kusto par l’intermédiaire du .NET SDK, on fournit généralement les valeurs suivantes :

1. Une chaîne de connexion indiquant le point de terminaison de service pour se connecter, des paramètres d’authentification et des informations similaires liées à la connexion. Sur le plan programmatique, la `KustoConnectionStringBuilder` chaîne de connexion est représentée par la classe.

2. Le nom de la base de données qui est utilisé pour décrire la «portée» de la demande.

3. Le texte de la demande (requête ou commande) lui-même.

4. Propriétés supplémentaires que le client fournit le service et sont appliquées à la demande. Programmatiquement, ces propriétés sont `ClientRequestProperties`détenues par une classe appelée .

Les propriétés de demande de client ont beaucoup d’utilisations. Certains d’entre eux sont utilisés pour faciliter le débogage (par exemple, en fournissant des chaînes de corrélation qui peuvent être utilisées pour suivre les interactions client/service), d’autres sont utilisées pour influer sur les limites et les politiques appliquées à la demande, tandis qu’une troisième catégorie de ces [propriétés](../../query/queryparametersstatement.md)est les paramètres de requête . Une liste complète des propriétés prises en charge apparaît dans cette page.

La `Kusto.Data.Common.ClientRequestProperties` classe contient trois types de données :

* Propriétés nommées.

* Options (cartographie d’un nom d’option à une valeur d’option).

* Paramètres (une cartographie d’un nom de paramètre de requête à une valeur de paramètre de requête).

> [!NOTE]
> Certaines propriétés nommées sont marquées comme « ne pas utiliser ». Ces propriétés ne doivent pas être spécifiées par les clients et n’ont aucun effet sur le service.

## <a name="the-clientrequestid-x-ms-client-request-id-named-property"></a>Le ClientRequestId (x-ms-client-request-id) a nommé la propriété

Cette propriété nommée détient l’identité spécifiée par le client de la demande. Il est fortement recommandé que la valeur unique par demande soit spécifiée par les clients à chaque demande qu’ils envoient, car elle facilite les défaillances de débogage (et est nécessaire dans certains scénarios, comme l’annulation des requêtes).

Le nom programmatique de `ClientRequestId`cette propriété est, et `x-ms-client-request-id`il se traduit par l’en-tête HTTP .

Cette propriété sera définie à une valeur (aléatoire) par le SDK si le client ne spécifie pas sa propre valeur.

Le contenu de cette propriété peut être n’importe quelle chaîne unique imprimable, telle qu’un GUID.
Il est toutefois recommandé que les clients utilisent le modèle suivant :

*ApplicationName* `.` *ActivityName* `;` *UniqueId*

Lorsque *ApplicationName* identifie la demande du client qui fait la demande, *ActivityName* identifie le type d’activité pour laquelle l’application client émet la demande du client, et *UniqueId* identifie la demande spécifique.

## <a name="the-application-x-ms-app-named-property"></a>L’application (x-ms-app) a nommé la propriété

Cette propriété nommée détient le nom de la demande du client qui fait la demande, à des fins de traçage.

Le nom programmatique de `Application`cette propriété est, et `x-ms-app`il se traduit par l’en-tête HTTP . Il peut être spécifié dans la `Application Name for Tracing`chaîne de connexion Kusto comme .

Cette propriété sera définie au nom du processus d’hébergement du SDK si le client ne spécifie pas sa propre valeur.

## <a name="the-user-x-ms-user-named-property"></a>L’utilisateur (x-ms-utilisateur) nommé propriété

Cette propriété nommée détient l’identité de l’utilisateur qui fait la demande, à des fins de traçage.

Le nom programmatique de `User`cette propriété est, et `x-ms-user`il se traduit par l’en-tête HTTP . Il peut être spécifié dans la `User Name for Tracing`chaîne de connexion Kusto comme .

## <a name="controlling-request-properties-using-the-rest-api"></a>Contrôle des propriétés de demande à l’aide de l’API REST

Lors de l’émission d’une demande `properties` HTTP au service Kusto, utilisez la fente dans le document JSON qui est l’organisme de demande POST pour fournir des propriétés de demande. Notez que certaines des propriétés (telles que l’ID de demande client, qui est l’ID de corrélation que le client fournit au service pour identifier la demande) peuvent être fournies dans l’en-tête HTTP, et peuvent donc également être définies si HTTP GET est utilisé.
Consultez [l’objet de demande d’API Kusto REST](../rest/request.md) pour plus d’informations.

## <a name="providing-values-for-query-parametrization-as-request-properties"></a>Fournir des valeurs pour la paramétrisation des requêtes en tant que propriétés de demande

Les requêtes Kusto peuvent se référer aux paramètres de requête en utilisant une déclaration spécialisée [de paramètres de requête](../../query/queryparametersstatement.md) dans le texte de requête. Cela permet aux applications client de paramétiser les requêtes Kusto basées sur l’entrée de l’utilisateur d’une manière sécurisée (sans crainte d’attaques par injection).)

Programmatiquement, on peut définir des `ClearParameter` `SetParameter`valeurs `HasParameter` de propriété en utilisant le , , et les méthodes.

Dans l’API REST, les paramètres de requête apparaissent dans la même chaîne JSON-encoded que les autres propriétés de demande.

## <a name="sample-code-for-using-request-properties"></a>Exemple de code pour l’utilisation des propriétés de demande

Voici un exemple pour le code client :

```csharp
public static System.Data.IDataReader QueryKusto(
    Kusto.Data.Common.ICslQueryProvider queryProvider,
    string databaseName,
    string query)
{
    var queryParameters = new Dictionary<String, String>()
    {
        { "xIntValue", "111" },
        { "xStrValue", "abc" },
        { "xDoubleValue", "11.1" }
    };

    // Query parameters (and many other properties) are provided
    // by a ClientRequestProperties object handed alongside
    // the query:
    var clientRequestProperties = new Kusto.Data.Common.ClientRequestProperties(
        principalIdentity: null,
        options: null,
        parameters: queryParameters);

    // Having client code provide its own ClientRequestId is
    // highly recommended. It not only allows the caller to
    // cancel the query, but also makes it possible for the Kusto
    // team to investigate query failures end-to-end:
    clientRequestProperties.ClientRequestId
        = "MyApp.MyActivity;"
        + Guid.NewGuid().ToString();

    // This is an example for setting an option
    // ("notruncation", in this case). In most cases this is not
    // needed, but it's included here for completeness:
    clientRequestProperties.SetOption(
        Kusto.Data.Common.ClientRequestProperties.OptionNoTruncation,
        true);
 
    try
    {
        return queryProvider.ExecuteQuery(query, clientRequestProperties);
    }
    catch (Exception ex)
    {
        Console.WriteLine(
            "Failed invoking query '{0}' against Kusto."
            + " To have the Kusto team investigate this failure,"
            + " please open a ticket @ https://aka.ms/kustosupport,"
            + " and provide: ClientRequestId={1}",
            query, clientRequestProperties.ClientRequestId);
        return null;
    }
}
```

## <a name="list-of-clientrequestproperties"></a>Liste des clientsRequestProperties

<!-- The following is auto-generated by running the following command: -->
<!-- Kusto.Cli.exe -execute:"#crp -doc"                                -->

<!-- The following text can be re-produced by running the Kusto.Cli.exe directive '#crp -doc' -->

<!-- The following text can be re-produced by running the Kusto.Cli.exe directive '#crp -doc' -->

* `debug_query_externaldata_projection_fusion_disabled`(*OptionDebugQueryDisableExternalDataProjectionFusion*) : Si vous êtes défini, ne fusionnez pas la projection en opérateur ExternalData. [Boolean]
* `debug_query_fanout_threads_percent_external_data`(*OptionDebugQueryFanoutThreadsPercentExternalData*) : Le pourcentage de threads à l’exécution fanout pour les nœuds de données externes. [Int]
* `deferpartialqueryfailures`(*OptionDeferPartialQueryFailures*) : Si c’est vrai, désactive les défaillances partielles de requête dans le cadre de l’ensemble de résultats. [Boolean]
* `max_memory_consumption_per_query_per_node`(*OptionMaxMemoryConsumptionPerQueryPerNode*) : Remplace la quantité maximale de mémoire par défaut qu’une requête entière peut allouer par nœud. [UInt64]
* `maxmemoryconsumptionperiterator`(*OptionMaxMemoryConsumptionPerIterator*) : Remplace la quantité maximale de mémoire par défaut qu’un opérateur de requête peut allouer. [UInt64]
* `maxoutputcolumns`(*OptionMaxOutputColumns*) : Remplace le nombre maximum de colonnes par défaut qu’une requête est autorisé à produire. [Long]
* `norequesttimeout`(*OptionNoRequestTimeout*) : Permet de définir le délai de demande à sa valeur maximale. [Boolean]
* `notruncation`(*OptionNoTruncation*) : Permet de supprimer la troncation des résultats de la requête retournés à l’appelant. [Boolean]
* `push_selection_through_aggregation`(*OptionPushSelectionThroughAggregation*): Si c’est vrai, poussez la sélection simple par l’agrégation [Boolean]
* `query_admin_super_slacker_mode`(*OptionAdminSuperSlackerMode ):* Si c’est vrai, déléguer l’exécution de la requête à un autre nœud [Boolean]
* `query_bin_auto_at`(*QueryBinAutoAt*) : Lors de l’évaluation de la fonction bin_auto(), la valeur de démarrage à utiliser. [LittéraleMentexpression]
* `query_bin_auto_size`(*QueryBinAutoSize*) : Lors de l’évaluation de la fonction bin_auto(), la valeur de taille du bac à utiliser. [LittéraleMentexpression]
* `query_cursor_after_default`(*OptionQueryCursorAfterDefault*) : La valeur paramètre par défaut de la fonction cursor_after)lorsqu’elle est appelée sans paramètres. [string]
* `query_cursor_before_or_at_default`(*OptionQueryCursorBeforeOrAtDefault*): La valeur paramètre par défaut de la fonction cursor_before_or_at)lorsqu’elle est appelée sans paramètres. [string]
* `query_cursor_current`(*OptionQueryCursorCurrent*) : Remplace la valeur du curseur retournée par les fonctions cursor_current)) ou current_cursor). [string]
* `query_cursor_scoped_tables`(*OptionQueryCursorScopedTables*): Liste des noms de table qui devraient être portées à cursor_after_default .. cursor_before_or_at_default (la limite supérieure est facultative). [dynamique]
* `query_datascope`(*OptionQueryDataScope*) : Contrôle le datascope de la requête, que la requête s’applique à toutes les données ou en fasse partie. ['default', 'all', ou 'hotcache']
* `query_datetimescope_column`(*OptionQueryDateTimeScopeColumn*) : Contrôle le nom de la colonne pour la portée de date de la requête (query_datetimescope_to / query_datetimescope_from). [String]
* `query_datetimescope_from`(*OptionQueryDateTimeScopeDe )*: Contrôle la portée de date de la requête (plus tôt) -- utilisé comme filtre auto-appliqué sur query_datetimescope_column seulement (si défini). [DateTime]
* `query_datetimescope_to`(*OptionQueryDateTimeScopeTo*) : Contrôle la portée de date de la requête (dernière) -- utilisée comme filtre auto-appliqué sur query_datetimescope_column seulement (si défini). [DateTime]
* `query_distribution_nodes_span`(*OptionQueryDistributionNodesSpanSize*) : Si elle est définie, contrôle la façon dont la sous-requête se comporte : le nœud exécutant introduira un niveau supplémentaire dans la hiérarchie des requêtes pour chaque sous-groupe de nœuds ; la taille du sous-groupe est définie par cette option. [Int]
* `query_fanout_nodes_percent`(*OptionQueryFanoutNodesPercent*) : Le pourcentage de nœuds à l’exécution fanout. [Int]
* `query_fanout_threads_percent`(*OptionQueryFanoutThreadsPercent*) : Le pourcentage de threads à l’exécution fanout. [Int]
* `query_language`(*OptionQueryLanguage*): Contrôle la façon dont le texte de requête doit être interprété. ['csl','kql' ou 'sql']
* `query_max_entities_in_union`(*OptionMaxEntitiesToUnion*) : Remplace le nombre maximum de colonnes par défaut qu’une requête est autorisé à produire. [Long]
* `query_now`(*OptionQueryNow*) : Remplace la valeur de date retournée par la fonction maintenant (0s). [DateTime]
* `query_python_debug`(*OptionDebugPython*): Si ensemble, générer des requêtes en python débauche pour le nœud python énuméré (par défaut d’abord). [Boolean ou Int]
* `query_results_apply_getschema`(*OptionQueryResultsApplyGetSchema*): Si défini, récupère le schéma de chaque données tabulaires dans les résultats de la requête au lieu des données elles-mêmes. [Boolean]
* `query_results_cache_max_age`(*OptionQueryResultsCacheMaxAge*): Si positif, contrôle l’âge maximum des résultats de requête mis en cache que Kusto est autorisé à retourner [TimeSpan]
* `query_results_progressive_row_count`(*OptionProgressiveQueryMinRowCountPerUpdate*) : Indice pour Kusto du nombre d’enregistrements à envoyer dans chaque mise à jour (prend effet seulement si OptionResultsProgressiveEnabled est défini)
* `query_results_progressive_update_period`(*OptionProgressiveProgressReportPeriod*) : Indice pour Kusto quant à la fréquence à laquelle envoyer des cadres de progression (prend effet seulement si OptionResultsProgressiveEnabled est défini)
* `query_shuffle_broadcast_join`(*ShuffleBroadcastJoin*) : Permet de mélanger sur la diffusion joindre.
* `query_take_max_records`(*OptionTakeMaxRecords*) : Permet de limiter les résultats des requêtes à ce nombre d’enregistrements. [Long]
* `queryconsistency`(*OptionQueryConsistency*) : Contrôle la cohérence des requêtes. ['forte incohérence' ou 'normalconsistency' ou 'faiblesse']
* `request_callout_disabled`(*OptionRequestCalloutDisabled*) : Si spécifié, indique que la demande ne peut pas être appelée à un service fourni par l’utilisateur. [Boolean]
* `request_description`(*OptionRequestDescription*) : Texte arbitraire que l’auteur de la demande veut inclure comme description de la demande. [String]
* `request_external_table_disabled`(*OptionRequestExternalTableDisabled*) : Si spécifié, indique que la demande ne peut pas invoquer du code dans l’ExternalTable. [Boolean]
* `request_readonly`(*OptionRequestReadOnly*) : Si spécifié, indique que la demande ne doit pas être en mesure d’écrire quoi que ce soit. [Boolean]
* `request_remote_entities_disabled`(*OptionRequestRemoteEntitiesDisabled*) : Si spécifié, indique que la demande ne peut pas accéder aux bases de données et aux clusters distants. [Boolean]
* `request_sandboxed_execution_disabled`(*OptionRequestSandboxedExecutionDisabled*) : Si spécifié, indique que la demande ne peut pas invoquer du code dans le bac à sable. [Boolean]
* `results_progressive_enabled`(*OptionResultsProgressiveEnabled*) : Si l’ensemble, permet le flux progressif de requête
* `servertimeout`(*OptionServerTimeout*) : Remplace le délai d’attente de demande par défaut. [TimeSpan]
* `truncationmaxrecords`(*OptionTruncationMaxRecords*) : Remplace le nombre maximum d’enregistrements par défaut qu’une requête est autorisé à retourner à l’appelant (troncation). [Long]
* `truncationmaxsize`(*OptionTruncationMaxSize*) : Remplace la taille maximale de données dfefault qu’une requête est autorisée à retourner à l’appelant (troncation). [Long]
* `validate_permissions`(*OptionValidatePermissions*) : Valide les autorisations de l’utilisateur pour effectuer la requête et ne fonctionne pas la requête elle-même. [Boolean]

