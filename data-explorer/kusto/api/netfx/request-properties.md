---
title: Propriétés de la demande et ClientRequestProperties-Azure Explorateur de données
description: Cet article décrit les propriétés des requêtes et les ClientRequestProperties dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/23/2019
ms.openlocfilehash: a7474dc04e85f439fc611c44213694041fedc1a4
ms.sourcegitcommit: f134d51e52504d3ca722bdf6d33baee05118173a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96563289"
---
# <a name="request-properties-and-clientrequestproperties"></a>Propriétés de la demande et ClientRequestProperties

Quand une demande est effectuée à partir de Kusto via le kit de développement logiciel (SDK) .NET, fournissez :

* Chaîne de connexion qui indique le point de terminaison de service auquel se connecter, les paramètres d’authentification et les informations similaires relatives à la connexion. Par programme, la chaîne de connexion est représentée par la `KustoConnectionStringBuilder` classe.
* Nom de la base de données utilisée pour décrire l’étendue de la demande.
* Texte de la requête (requête ou commande) elle-même.
* Propriétés supplémentaires fournies par le client au service et appliquées à la demande. Par programmation, ces propriétés sont détenues par une classe appelée [`ClientRequestProperties`](#clientrequestproperties) .

## <a name="clientrequestproperties"></a>ClientRequestProperties

Les propriétés des demandes clientes peuvent affecter les limites et les stratégies qui sont appliquées à la requête.

La `Kusto.Data.Common.ClientRequestProperties` classe contient trois types de données :

* [Propriétés nommées](#named-properties) : ces propriétés facilitent le débogage. Par exemple, les propriétés peuvent fournir des chaînes de corrélation utilisées pour suivre les interactions entre le client et le service. 
* [Options ClientRequestProperties](#clientrequestproperties-options) : mappage d’un nom d’option à une valeur d’option.
* [Paramètres de requête](../../query/queryparametersstatement.md)  : mappage d’un nom de paramètre de requête à une valeur de paramètre de requête. Ces paramètres permettent aux applications clientes de paramétrer les requêtes Kusto en fonction de l’entrée utilisateur.

## <a name="named-properties"></a>Propriétés nommées

> [!NOTE]
> Certaines propriétés nommées sont marquées « ne pas utiliser ». Ces propriétés ne doivent pas être spécifiées par les clients et n’ont aucun effet sur le service.

### <a name="clientrequestid-x-ms-client-request-id"></a>ClientRequestId (x-ms-client-Request-ID)

Cette propriété nommée a l’identité spécifiée par le client de la demande. Les clients doivent spécifier une valeur unique par demande pour chaque demande qu’ils envoient. Cette valeur rend les échecs de débogage plus faciles à effectuer, et est nécessaire dans certains scénarios, par exemple pour l’annulation de requête.

Le nom de programmation de la propriété est `ClientRequestId` , et il se traduit par l’en-tête http `x-ms-client-request-id` .

Cette propriété est définie sur une valeur (aléatoire) par le kit de développement logiciel (SDK) si le client ne spécifie pas de valeur.

Le contenu de cette propriété peut être n’importe quelle chaîne unique imprimable, telle qu’un GUID.
Toutefois, nous recommandons aux clients d’utiliser : *applicationName* `.` *ActivityName* `;` *UniqueID*

* *ApplicationName* identifie l’application cliente qui effectue la demande.
* *ActivityName* identifie le type d’activité pour lequel l’application cliente émet la requête du client.
* *UniqueID* identifie la requête spécifique.

### <a name="application-x-ms-app"></a>Application (x-ms-App)

La propriété de l’application (x-ms-App) nommée a le nom de l’application cliente qui effectue la demande et est utilisée pour le suivi.

Le nom de programmation de cette propriété est `Application` , et il se traduit par l’en-tête http `x-ms-app` . Il peut être spécifié dans la chaîne de connexion Kusto sous la forme `Application Name for Tracing` .

Cette propriété sera définie sur le nom du processus hébergeant le kit de développement logiciel (SDK) si le client ne spécifie pas sa propre valeur.

### <a name="user-x-ms-user"></a>Utilisateur (x-ms-User)

La propriété de l’utilisateur (x-ms-User) nommé a l’identité de l’utilisateur qui effectue la demande et est utilisée pour le suivi.

Le nom de programmation de cette propriété est `User` , et il se traduit par l’en-tête http `x-ms-user` . Il peut être spécifié dans la chaîne de connexion Kusto sous la forme `User Name for Tracing` .

## <a name="use-request-properties"></a>Utiliser les propriétés de la demande

Utilisez les instructions suivantes pour contrôler les propriétés de la demande et fournir des valeurs pour le paramétrage de la requête. 

### <a name="control-request-properties-using-the-rest-api"></a>Contrôler les propriétés de demande à l’aide de l’API REST

Lors de l’émission d’une requête HTTP auprès du service Kusto, utilisez l' `properties` emplacement dans le document JSON qui est le corps de la demande de publication pour fournir les propriétés de la demande. 

> [!NOTE]
> Certaines des propriétés (telles que l' « ID de demande client », qui est l’ID de corrélation fourni par le client au service pour identifier la demande) peuvent être fournies dans l’en-tête HTTP et peuvent également être définies si HTTP est utilisé.
Pour plus d’informations, consultez [l’objet demande de l’API REST Kusto](../rest/request.md).

### <a name="provide-values-for-query-parameterization-as-request-properties"></a>Fournir des valeurs pour le paramétrage de requête en tant que propriétés de la demande

Les requêtes Kusto peuvent faire référence à des paramètres de requête à l’aide d’une instruction [Declare Statement-Parameters](../../query/queryparametersstatement.md) spécialisée dans le texte de la requête. Cette instruction permet aux applications clientes de paramétrer les requêtes Kusto en fonction de l’entrée de l’utilisateur, de manière sécurisée et sans crainte d’attaques par injection.

Par programme, définissez les valeurs des propriétés à l’aide des `ClearParameter` `SetParameter` méthodes, et `HasParameter` .

Dans l’API REST, les paramètres de requête s’affichent dans la même chaîne encodée au format JSON que les autres propriétés de la requête.

## <a name="example"></a> Exemple

L’exemple suivant montre un exemple de code client pour l’utilisation des propriétés de la demande :

```csharp
public static System.Data.IDataReader QueryKusto(
    Kusto.Data.Common.ICslQueryProvider queryProvider,
    string databaseName,
    string query)
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
    var clientRequestProperties = new Kusto.Data.Common.ClientRequestProperties(
        principalIdentity: null,
        options: null,
        parameters: queryParameters);

    // Having client code provide its own ClientRequestId is
    // highly recommended. It not only allows the caller to
    // cancel the query, but also makes it possible for the Kusto
    // team to investigate query failures end-to-end:
    clientRequestProperties.ClientRequestId
        = "MyApp.MyActivity;"
        + Guid.NewGuid().ToString();

    // This is an example for setting an option
    // ("notruncation", in this case). In most cases this is not
    // needed, but it's included here for completeness:
    clientRequestProperties.SetOption(
        Kusto.Data.Common.ClientRequestProperties.OptionNoTruncation,
        true);
 
    try
    {
        return queryProvider.ExecuteQuery(query, clientRequestProperties);
    }
    catch (Exception ex)
    {
        Console.WriteLine(
            "Failed invoking query '{0}' against Kusto."
            + " To have the Kusto team investigate this failure,"
            + " please open a ticket @ https://aka.ms/kustosupport,"
            + " and provide: ClientRequestId={1}",
            query, clientRequestProperties.ClientRequestId);
        return null;
    }
}
```

## <a name="clientrequestproperties-options"></a>Options de ClientRequestProperties

<!-- The following is auto-generated by running  Kusto.Cli.exe -execute:"#crp -doc"           -->
<!-- The following text can be re-produced by running the Kusto.Cli.exe directive '#crp -doc' -->

* `deferpartialqueryfailures` (*OptionDeferPartialQueryFailures*) : si la valeur est true, désactive la création de rapports sur les échecs de requête partiels dans le cadre du jeu de résultats. Expression
* `materialized_view_shuffle` (*OptionMaterializedViewShuffleQuery*) : Conseil permettant d’utiliser la stratégie de lecture aléatoire pour les vues matérialisées référencées dans la requête.
La propriété est un tableau de noms de vues matérialisées et les clés de lecture aléatoire à utiliser.
exemples : 'Dynamic ([{"Name" : "V1", "Keys" : ["K1", "K2"]}]) ' (lecture aléatoire de la vue v1 par K1, K2) ou’Dynamic ([{"Name" : "V1"}]) ' (affichage aléatoire de la vue v1 par toutes les clés) [Dynamic]
* `max_memory_consumption_per_query_per_node` (*OptionMaxMemoryConsumptionPerQueryPerNode*) : remplace la quantité maximale de mémoire par défaut qu’une requête entière peut allouer par nœud. UInt64
* `maxmemoryconsumptionperiterator` (*OptionMaxMemoryConsumptionPerIterator*) : remplace la quantité maximale par défaut de mémoire qu’un opérateur de requête peut allouer. UInt64
* `maxoutputcolumns` (*OptionMaxOutputColumns*) : remplace le nombre maximal par défaut de colonnes qu’une requête est autorisée à produire. Long
* `norequesttimeout` (*OptionNoRequestTimeout*) : permet de définir le délai d’expiration de la requête sur sa valeur maximale. Expression
* `notruncation` (*OptionNoTruncation*) : permet de supprimer la troncation des résultats de la requête retournés à l’appelant. Expression
* `push_selection_through_aggregation` (*OptionPushSelectionThroughAggregation*) : si la valeur est true, envoyer une sélection simple par le biais de l’agrégation [Boolean]
* `query_bin_auto_at` (*QueryBinAutoAt*) : lors de l’évaluation de la fonction bin_auto (), valeur de début à utiliser. [LiteralExpression]
* `query_bin_auto_size` (*QueryBinAutoSize*) : lors de l’évaluation de la fonction bin_auto (), valeur de taille de l’emplacement à utiliser. [LiteralExpression]
* `query_cursor_after_default` (*OptionQueryCursorAfterDefault*) : valeur de paramètre par défaut de la fonction cursor_after () lorsqu’elle est appelée sans paramètres. [string]
* `query_cursor_before_or_at_default` (*OptionQueryCursorBeforeOrAtDefault*) : valeur de paramètre par défaut de la fonction cursor_before_or_at () lorsqu’elle est appelée sans paramètres. [string]
* `query_cursor_current` (*OptionQueryCursorCurrent*) : remplace la valeur de curseur retournée par les fonctions cursor_current () ou current_cursor (). [string]
* `query_cursor_disabled` (*OptionQueryCursorDisabled*) : désactive l’utilisation des fonctions de curseur dans le contexte de la requête. expression
* `query_cursor_scoped_tables` (*OptionQueryCursorScopedTables*) : liste des noms de tables dont l’étendue doit être comcursor_after_default.. cursor_before_or_at_default (la limite supérieure est facultative). dynamique
* `query_datascope` (*OptionQueryDataScope*) : contrôle le Datascope de la requête, que la requête s’applique à toutes les données ou à une partie seulement. ['default', 'all’ou’hotcache']
* `query_datetimescope_column` (*OptionQueryDateTimeScopeColumn*) : contrôle le nom de colonne pour l’étendue DateTime de la requête (query_datetimescope_to/query_datetimescope_from). Chaîne
* `query_datetimescope_from` (*OptionQueryDateTimeScopeFrom*) : contrôle l’étendue DateTime de la requête (au plus tôt), utilisée comme filtre appliqué automatiquement sur query_datetimescope_column uniquement (si elle est définie). [DateTime]
* `query_datetimescope_to` (*OptionQueryDateTimeScopeTo*) : contrôle l’étendue DateTime de la requête (dernière version), utilisée comme filtre appliqué automatiquement sur query_datetimescope_column uniquement (si elle est définie). [DateTime]
* `query_distribution_nodes_span` (*OptionQueryDistributionNodesSpanSize*) : s’il est défini, contrôle le comportement de la fusion des sous-requêtes : le nœud en cours d’exécution introduira un niveau supplémentaire dans la hiérarchie des requêtes pour chaque sous-groupe de nœuds ; la taille du sous-groupe est définie par cette option. Tiers
* `query_fanout_nodes_percent` (*OptionQueryFanoutNodesPercent*) : pourcentage de nœuds sur lesquels l’exécution doit être ventilée. Tiers
* `query_fanout_threads_percent` (*OptionQueryFanoutThreadsPercent*) : pourcentage de threads à partir duquel l’exécution doit être ventilée. Tiers
* `query_force_row_level_security` (*OptionQueryForceRowLevelSecurity*) : si ce paramètre est spécifié, force sécurité au niveau des lignes règles, même si row_level_security stratégie est désactivée [booléen]
* `query_language` (*OptionQueryLanguage*) : contrôle la manière dont le texte de la requête doit être interprété. [« CSL », « kql » ou « SQL »]
* `query_max_entities_in_union` (*OptionMaxEntitiesToUnion*) : remplace le nombre maximal par défaut de colonnes qu’une requête est autorisée à produire. Long
* `query_now` (*OptionQueryNow*) : remplace la valeur DateTime retournée par la fonction Now (0). [DateTime]
* `query_python_debug` (*OptionDebugPython*) : s’il est défini, générer une requête de débogage Python pour le nœud python énuméré (premier par défaut). [Booléen ou entier]
* `query_results_apply_getschema` (*OptionQueryResultsApplyGetSchema*) : si cette valeur est définie, récupère le schéma de chaque données tabulaire dans les résultats de la requête au lieu des données elles-mêmes. Expression
* `query_results_cache_max_age` (*OptionQueryResultsCacheMaxAge*) : si positif, contrôle l’âge maximal des résultats de la requête mis en cache que Kusto est autorisé à renvoyer [TimeSpan]
* `query_results_progressive_row_count` (*OptionProgressiveQueryMinRowCountPerUpdate*) : Conseil pour Kusto en fonction du nombre d’enregistrements à envoyer dans chaque mise à jour (prend effet uniquement si OptionResultsProgressiveEnabled est défini)
* `query_results_progressive_update_period` (*OptionProgressiveProgressReportPeriod*) : Conseil pour Kusto quant à la fréquence d’envoi des frames de progression (prend effet uniquement si OptionResultsProgressiveEnabled est défini)
* `query_take_max_records` (*OptionTakeMaxRecords*) : permet de limiter les résultats de la requête à ce nombre d’enregistrements. Long
* `queryconsistency` (*OptionQueryConsistency*) : contrôle la cohérence des requêtes. [« strongconsistency » ou « normalconsistency » ou « weakconsistency »]
* `request_block_row_level_security` (*OptionRequestBlockRowLevelSecurity*) : s’il est spécifié, bloque l’accès aux tables pour lesquelles row_level_security stratégie est activée [Boolean]
* `request_callout_disabled` (*OptionRequestCalloutDisabled*) : s’il est spécifié, indique que la demande ne peut pas appeler un service fourni par l’utilisateur. Expression
* `request_description` (*OptionRequestDescription*) : texte arbitraire que l’auteur de la demande souhaite inclure comme description de la demande. Chaîne
* `request_external_table_disabled` (*OptionRequestExternalTableDisabled*) : s’il est spécifié, indique que la demande ne peut pas appeler de code dans le ExternalTable. Expression
* `request_impersonation_disabled` (*OptionDoNotImpersonate*) : s’il est spécifié, indique que le service ne doit pas emprunter l’identité de l’appelant. Expression
* `request_readonly` (*OptionRequestReadOnly*) : si spécifié, indique que la demande ne peut rien écrire. Expression
* `request_remote_entities_disabled` (*OptionRequestRemoteEntitiesDisabled*) : si spécifié, indique que la demande ne peut pas accéder aux bases de données et aux clusters distants. Expression
* `request_sandboxed_execution_disabled` (*OptionRequestSandboxedExecutionDisabled*) : si spécifié, indique que la demande ne peut pas appeler de code dans le bac à sable (sandbox). Expression
* `results_progressive_enabled` (*OptionResultsProgressiveEnabled*) : s’il est défini, active le flux de requête progressif
* `servertimeout` (*OptionServerTimeout*) : remplace le délai d’expiration de la demande par défaut. TimeSpan
* `truncationmaxrecords` (*OptionTruncationMaxRecords*) : remplace le nombre maximal d’enregistrements par défaut qu’une requête est autorisée à renvoyer à l’appelant (troncation). Long
* `truncationmaxsize` (*OptionTruncationMaxSize*) : remplace la taille de données maximale par défaut qu’une requête est autorisée à retourner à l’appelant (troncation). Long
* `validate_permissions` (*OptionValidatePermissions*) : valide les autorisations de l’utilisateur pour exécuter la requête et n’exécute pas la requête elle-même. Expression