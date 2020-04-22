---
title: Purge des données - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la purge de données dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 460ad9cfca4f97e6735d30a4d47d6384581e7af7
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82029986"
---
# <a name="data-purge"></a>Vidage des données

>[!Note]
> Cet article explique comment supprimer les données personnelles de l’appareil ou du service et il peut être utilisé dans le cadre de vos obligations en vertu du Règlement général sur la protection des données. Si vous êtes à la recherche d’informations générales sur GDPR, consultez la [section GDPR du portail Service Trust](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).



En tant que plate-forme de données, Azure Data Explorer (Kusto) prend en charge la possibilité de supprimer des enregistrements individuels, grâce à l’utilisation de `.purge` commandes et des commandes connexes. Vous pouvez également [purger une table entière](#purging-an-entire-table).  

> [!WARNING]
> La suppression des `.purge` données par le biais de la commande est conçue pour être utilisée pour protéger les données personnelles et ne doit pas être utilisée dans d’autres scénarios. Il n’est pas conçu pour prendre en charge les demandes de suppression fréquentes, ou la suppression de quantités massives de données, et peut avoir un impact significatif sur les performances sur le service.

## <a name="purge-guidelines"></a>Lignes directrices sur la purge

Il est **fortement recommandé** que les équipes qui stockent des données personnelles dans Azure Data Explorer ne le fassent qu’après une conception minutieuse du schéma de données et l’enquête sur les politiques pertinentes.

1. Dans le meilleur des cas, la période de conservation de ces données est suffisamment courte et les données sont automatiquement supprimées.
2. Si l’utilisation de la période de conservation n’est pas possible, l’approche recommandée consiste à isoler toutes les données soumises aux règles de confidentialité dans un petit nombre de tables Kusto (optimalement, une seule table) et à y établir un lien de toutes les autres tables. Cela permet d’exécuter le processus de [purge](#purge-process) de données sur un petit nombre de tables contenant des données sensibles, et d’éviter toutes les autres tables.
3. L’appelant doit faire toutes les `.purge` tentatives pour passer par lots l’exécution des commandes à 1-2 commandes par table par jour.
   Ne publiez pas de commandes multiples, chacune ayant sa propre identité d’utilisateur prédicate; plutôt envoyer une seule commande dont le prédicat comprend toutes les identités des utilisateurs qui nécessitent la purge.

## <a name="purge-process"></a>Processus de purge

Le processus de purge sélective des données d’Azure Data Explorer se déroule dans les étapes suivantes :

1. **Phase 1 :** Compte tenu d’un nom de table Kusto et d’un prédicat par enregistrement indiquant quels enregistrements supprimer, Kusto scanne le tableau à la recherche d’identifier les fragments de données qui participeraient à la purge des données (avoir un ou plusieurs enregistrements pour lesquels les retours prédicat vrai).
2. **Phase 2 : (Soft Delete)** Remplacez chaque éclat de données dans le tableau (identifié en étape (1)) par une version re-ingérée qui n’a pas les enregistrements pour lesquels le prédicat renvoie vrai.
   Tant qu’aucune nouvelle donnée n’est ingérée dans la table, d’ici la fin de cette phase, les requêtes ne retourneront plus les données pour lesquelles le prédicat retourne vrai. 
   La durée de la phase de suppression douce de purge dépend du nombre d’enregistrements qui doivent être purgés, de leur distribution à travers les éclats de données dans le cluster, du nombre de nœuds dans le cluster, de la capacité de réserve qu’il a pour les opérations de purge et de plusieurs autres facteurs. La durée de la phase 2 peut varier de quelques secondes à plusieurs heures.
3. **Phase 3 : (Hard Delete)** Travaillez en arrière tous les artefacts de stockage qui peuvent avoir les données « empoisonnées », et supprimez-les du stockage. Cette phase est effectuée au moins 5 jours *après* l’achèvement de la phase précédente, mais pas plus de 30 jours après la commande initiale, pour se conformer aux exigences en matière de confidentialité des données.

L’émission d’une `.purge` commande déclenche ce processus, qui prend quelques jours. Notez que si la « densité » d’enregistrements pour lesquels le prédicat s’applique est suffisamment importante, le processus réingérera effectivement toutes les données du tableau, ce qui aura un impact significatif sur le rendement et le COGS.


## <a name="purge-limitations-and-considerations"></a>Limites et considérations de purge

* **Le processus de purge est définitif et irréversible**. Il n’est pas possible de « défaire » ce processus ou de récupérer les données qui ont été purgées. Par conséquent, les commandes telles que [la baisse de table annuler](../management/undo-drop-table-command.md) ne peut pas récupérer les données purgées, et la réduction des données à une version précédente ne peut pas aller à "avant" la dernière commande de purge.

* Pour éviter les erreurs, il est recommandé de vérifier le prédicat en exécutant une requête avant la purge pour s’assurer que les résultats correspondent au résultat prévu, ou utiliser le processus en deux étapes qui renvoie le nombre prévu d’enregistrements qui seront purgés. 

* Par mesure de précaution, le processus de purge est désactivé, par défaut, sur tous les clusters.
   Permettre le processus de purge est une opération ponctuelle qui nécessite l’ouverture d’un [billet de soutien;](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) s’il vous `EnabledForPurge` plaît spécifier que vous voulez que la fonctionnalité soit activée.

* La `.purge` commande est exécutée à l’encontre du critère de gestion des données : `https://ingest-[YourClusterName].kusto.windows.net`.
   La commande nécessite des autorisations [d’administration](../management/access-control/role-based-authorization.md) de base de données sur les bases de données pertinentes. 
* En raison de l’impact sur le rendement du processus de purge et pour garantir que les lignes directrices sur la purge ont été [suivies,](#purge-guidelines) on s’attend à ce que l’appelant modifie le schéma de données afin que les tableaux minimaux incluent les données pertinentes, et les commandes par lot par tableau pour réduire l’impact significatif du COGS du processus de purge.
* Le `predicate` paramètre de la commande [.purge](#purge-table-tablename-records-command) est utilisé pour spécifier quels enregistrements purger.
`Predicate`la taille est limitée à 63 KB. Lors de `predicate`la construction de la :
    * Utilisez [l’opérateur 'in',](../query/inoperator.md) `where [ColumnName] in ('Id1', 'Id2', .. , 'Id1000')`par exemple . 
        * Notez les limites de [l’opérateur 'in'](../query/inoperator.md) (la liste peut contenir jusqu’à `1,000,000` des valeurs).
    * Si la taille de la requête est grande, utilisez [l’opérateur de « données externes »,](../query/externaldata-operator.md)par exemple. `where UserId in (externaldata(UserId:string) ["https://...blob.core.windows.net/path/to/file?..."])` Le fichier stocke la liste des DIU à purger.
        * La taille totale des requêtes, après avoir agrandi tous les blobs externes (taille totale de tous les blobs) ne peut pas dépasser 64 Mo. 

## <a name="purge-performance"></a>Exécution de purge

Une seule demande de purge peut être exécutée sur le cluster, à un moment donné. Toutes les autres demandes sont en file d’attente dans l’état "programmé". La taille de la file d’attente de demande de purge doit être surveillée et maintenue dans des limites adéquates pour répondre aux exigences applicables à vos données.

Pour réduire le temps d’exécution de purge :
* Diminuer la quantité de données purgées en suivant les [directives de purge](#purge-guidelines)
* Ajuster la [politique de mise en cache](../management/cachepolicy.md) puisque la purge prend plus de temps sur les données froides.
* Échelle du cluster

* Augmenter la capacité de purge des [grappes,](../management/capacitypolicy.md#extents-purge-rebuild-capacity)après un examen attentif, comme détaillé dans Extents purge capacité de reconstruction . La modification de ce paramètre nécessite l’ouverture d’un [billet de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="trigger-the-purge-process"></a>Déclencher le processus de purge

> [!Note]
> L’exécution de purge est invoquée en exécutant la [table de purge *TableName* enregistre](#purge-table-tablename-records-command) la commande sur le point de terminaison de gestion des données (**https://ingest-[YourClusterName].kusto.windows.net**).

### <a name="purge-table-tablename-records-command"></a>Commande *de* dossiers de table de purge De table

La commande de purge peut être invoquée de deux façons pour des scénarios d’utilisation différents :
1. Invocation programmatique : Une seule étape qui est destinée à être invoquée par les demandes. L’appel de cette commande déclenche directement la séquence d’exécution de purge.

    **Syntaxe**

     ```kusto
     .purge table [TableName] records in database [DatabaseName] with (noregrets='true') <| [Predicate]
     ```

    > [!NOTE]
    > Générez cette commande en utilisant l’API CslCommandGenerator, disponible dans le cadre du forfait NuGet de la [bibliothèque cliente Kusto.](../api/netfx/about-kusto-data.md)

1. Invocation humaine : Un processus en deux étapes qui nécessite une confirmation explicite comme étape distincte. Première invocation de la commande renvoie un jeton de vérification, qui devrait être fourni pour exécuter la purge réelle. Cette séquence réduit le risque de suppression par inadvertance de données incorrectes. L’utilisation de cette option peut prendre beaucoup de temps à compléter sur de grandes tables avec des données significatives cache froide.
    <!-- If query times-out on DM endpoint (default timeout is 10 minutes), it is recommended to use the [engine `whatif` command](#purge-whatif-command) directly againt the engine endpoint while increasing the [server timeout limit](../concepts/querylimits.md#limit-on-request-execution-time-timeout). Only after you have verified the expected results using the engine whatif command, issue the purge command via the DM endpoint using the 'noregrets' option. -->

     **Syntaxe**

     ```kusto
     // Step #1 - retrieve a verification token (no records will be purged until step #2 is executed)
     .purge table [TableName] records in database [DatabaseName] <| [Predicate]

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] records in database [DatabaseName] with (verificationtoken='<verification token from step #1>') <| [Predicate]
     ```
    
    |Paramètres  |Description  |
    |---------|---------|
    | nom_base_de_données   |   Nom de la base de données      |
    | TableName     |     Nom de la table    |
    | Predicate    |    Identifie les dossiers pour purger. Voir Purge prédicate limitations ci-dessous. | 
    | noregrets noregrets    |     Si défini, déclenche une activation en une seule étape.    |
    | vérificationtoken     |  Dans le scénario d’activation en deux étapes **(noregrets** n’est pas défini), ce jeton peut être utilisé pour exécuter la deuxième étape et de commettre l’action. Si **le jet de vérification** n’est pas spécifié, il déclenchera la première étape de la commande, dans laquelle les informations sur la purge sont retournées et un jeton, qui doivent être transmises à l’ordre pour effectuer l’étape #2.   |

    **Purger les limitations**
    * Le prédicat doit être une simple sélection (p. ex. *lorsque [ColumnName] 'X'* / *où [ColumnName] en ('X', 'Y', 'Z') et [OtherColumn] 'A').*
    * Les filtres multiples doivent être combinés avec `where` un «et», `where [ColumnName] == 'X' and  OtherColumn] == 'Y'` plutôt `where [ColumnName] == 'X' | where [OtherColumn] == 'Y'`que des clauses distinctes (par exemple et non ).
    * Le prédicat ne peut pas faire référence à d’autres que la table étant purgée (*TableName*). Le prédicat ne peut inclure`where`que l’énoncé de sélection (). Il ne peut pas projeter des colonnes spécifiques à partir de la table (schéma de sortie lors de l’exécution '* `table` Predicate*' doit correspondre schéma de table).
    * Les fonctions du `ingestion_time()`système `extent_id()`(telles que, , ) ne sont pas prises en charge dans le cadre du prédicat.

#### <a name="example-two-step-purge"></a>Exemple : Purge en deux étapes

1. Pour initier la purge dans un scénario d’activation en deux étapes, exécutez l’étape #1 de la commande :

    ```kusto
    .purge table MyTable records in database MyDatabase <| where CustomerId in ('X', 'Y')
    ```

    **Sortie**

    |NumRecordsToPurge |EstimationPurgeExecutionTime| VérificationToken
    |--|--|--
    |1,596 |00:00:02 |e43c7184ed2f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b

    Validez le NumRecordsToPurge avant de courir #2 étape. 
2. Pour effectuer une purge dans un scénario d’activation en deux étapes, utilisez le jeton de vérification retourné de l’étape #1 pour exécuter l’étape #2 :

    ```kusto
    .purge table MyTable records in database MyDatabase
    with (verificationtoken='e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b')
    <| where CustomerId in ('X', 'Y')
    ```

    **Sortie**

    |OpérationId |nom_base_de_données |TableName |Temps prévu |Duration |LastUpdatedOn |EngineOperationId |State |StateDetails |EngineStartTime (en) |EngineDuration |Nouvelle tentatives |ClientRequestId |Principal
    |--|--|--|--|--|--|--|--|--|--|--|--|--|--
    |c9651d74-3b80-4183-90bb-bbe9e42eadc4 |Mydatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Planifiée | | | |0 |Ke. RunCommand;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD app id...

#### <a name="example-single-step-purge"></a>Exemple : Purge en une seule étape

Pour déclencher une purge dans un scénario d’activation en une seule étape, exécutez la commande suivante :

```kusto
.purge table MyTable records in database MyDatabase with (noregrets='true') <| where CustomerId in ('X', 'Y')
```

**Sortie**

|OpérationId |nom_base_de_données |TableName |Temps prévu |Duration |LastUpdatedOn |EngineOperationId |State |StateDetails |EngineStartTime (en) |EngineDuration |Nouvelle tentatives |ClientRequestId |Principal
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |Mydatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Planifiée | | | |0 |Ke. RunCommand;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD app id...

### <a name="cancel-purge-operation-command"></a>Annuler le commandement de l’opération de purge

Si nécessaire, vous pouvez annuler les demandes de purge en attente.

> [!NOTE]
> Cette opération est destinée aux scénarios de récupération d’erreurs. Il n’est pas garanti de réussir, et ne devrait pas faire partie d’un flux opérationnel normal. Il ne peut être appliqué qu’aux demandes en file d’attente (pas encore expédiés au nœud moteur pour exécution). La commande est exécutée sur le point de terminaison de gestion des données.

**Syntaxe**

```kusto
 .cancel purge <OperationId>
 ```

**Exemple**

```kusto
 .cancel purge aa894210-1c60-4657-9d21-adb2887993e1
 ```

**Sortie**

La sortie de cette commande est la même que la sortie de commande de 'show purges *OperationId',* montrant l’état mis à jour de l’opération de purge annulée. Si la tentative est réussie, l’état d’opération est mis à jour à «abandonné», sinon l’état d’opération n’est pas modifié. 

|OpérationId |nom_base_de_données |TableName |Temps prévu |Duration |LastUpdatedOn |EngineOperationId |State |StateDetails |EngineStartTime (en) |EngineDuration |Nouvelle tentatives |ClientRequestId |Principal
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |Mydatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Abandonné | | | |0 |Ke. RunCommand;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD app id...

## <a name="track-purge-operation-status"></a>Suivi de l’état de l’opération de purge 

> [!Note]
> Les opérations de purge peuvent être suivies avec le [show purges](#show-purges-command) commande, exécuté contre le point de terminaison de gestion des données (**https://ingest-[YourClusterName].kusto.windows.net**).

Statut - 'Terminé' indique l’achèvement réussi de la première phase de l’opération de purge, qui est les dossiers sont soft-supprimés et ne sont plus disponibles pour la requête. On **ne s’attend pas** à ce que les clients suivent et vérifient l’achèvement de la deuxième phase (hard-delete). Cette phase est surveillée en interne par Kusto.

### <a name="show-purges-command"></a>afficher purges commande

`Show purges`commande montre l’état de l’opération de purge en spécifiant l’opération Id dans la période de temps demandée. 

```kusto
.show purges <OperationId>
.show purges [in database <DatabaseName>]
.show purges from '<StartDate>' [in database <DatabaseName>]
.show purges from '<StartDate>' to '<EndDate>' [in database <DatabaseName>]
```

|Propriétés  |Description  |Obligatoire/facultatif
|---------|---------|
|OpérationId    |      L’opération de gestion des données Id sortie après l’exécution d’une seule phase ou deuxième phase.   |Obligatoire
|StartDate    |   Limite de temps plus faible pour les opérations de filtrage. Si omis, par défaut à 24 heures avant l’heure actuelle.      |Facultatif
|EndDate    |  Limite de temps supérieure pour les opérations de filtrage. Si omis, par défaut à l’heure actuelle.       |Facultatif
|nom_base_de_données    |     Nom de base de données pour filtrer les résultats.    |Facultatif

> [!NOTE]
> L’état sera fourni uniquement sur les bases de données que le client a des autorisations [d’administration de base de données.](../management/access-control/role-based-authorization.md)

**Exemples**

```kusto
.show purges
.show purges c9651d74-3b80-4183-90bb-bbe9e42eadc4
.show purges from '2018-01-30 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00' in database MyDatabase
```

**Sortie** 

|OpérationId |nom_base_de_données |TableName |Temps prévu |Duration |LastUpdatedOn |EngineOperationId |State |StateDetails |EngineStartTime (en) |EngineDuration |Nouvelle tentatives |ClientRequestId |Principal
|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |Mydatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:33.6782130 |2019-01-20 11:42:34.6169153 |a0825d4d-6b0f-47f3-a499-54ac5681ab78 |Completed |Purge terminée avec succès (artefacts de stockage en attente de suppression) |2019-01-20 11:41:34.6486506 |00:00:04.4687310 |0 |Ke. RunCommand;1d0ad28b-f791-4f5a-a60f-0e32318367b7 |AAD app id...

* **OperationId** - l’ID d’opération DM est revenu lors de l’exécution de la purge. 
* **DatabaseName** - nom de base de données (sensible aux cas). 
* **TableName** - nom de table (sensible aux cas). 
* **ScheduledTime** - temps d’exécution de la commande de purge au service DM. 
* **Durée** - durée totale de l’opération de purge, y compris le temps d’attente d’exécution de file d’attente DM. 
* **EngineOperationId** - l’ID d’opération de la purge réelle exécutant dans le moteur. 
* **État** - état de purge, peut être l’un des suivants: 
    * Opération de purge prévue - est prévue pour l’exécution. Si le travail reste **prévu,** il ya probablement un arriéré d’opérations de purge. Voir [les performances de purge](#purge-performance) pour effacer cet arriéré. Si une opération de purge échoue sur une erreur transitoire, elle sera rejugée par le DM et réglée à **scheduled** à nouveau (de sorte que vous pouvez voir une transition d’opération de **Scheduled** à **InProgress** et retour à **Scheduled**).
    * InProgress - l’opéra de purge est en cours dans le moteur. 
    * Terminé - purge terminée avec succès.
    * BadInput - purge a échoué sur les mauvaises entrées et ne sera pas rejugé. Cela peut être dû à diverses questions telles qu’une erreur de syntaxe dans le prédicat, un prédicat illégal pour les commandes de purge, une requête qui dépasse les limites (par exemple, plus d’entités 1M dans un opérateur de données externes ou plus de 64 Mo de la taille totale de requête élargie), et 404 ou 403 erreurs pour les blobs externaldata.
    * Échec - purge a échoué et ne sera pas rejugé. Cela peut se produire si l’opération a été en attente dans la file d’attente pendant trop longtemps (plus de 14 jours), en raison d’un arriéré d’autres opérations de purge ou un certain nombre de défaillances qui dépasse la limite de nouvelle tentative. Ce dernier lèvera une alerte de surveillance interne et fera l’objet d’une enquête de l’équipe Azure Data Explorer. 
* StateDetails - une description de l’État.
* EngineStartTime - l’heure à la commande a été émise au moteur. S’il y a une grande différence entre cette époque et ScheduledTime, il y a habituellement un gros arriéré d’opérations de purge et le cluster ne suit pas le rythme. 
* EngineDuration - temps d’exécution de purge réelle dans le moteur. Si la purge a été rejugée plusieurs fois, c’est la somme de toutes les durées d’exécution. 
* Retries - nombre de fois l’opération a été rejugée par le service DM en raison d’une erreur transitoire.
* ClientRequestId - ID d’activité client de la demande de purge DM. 
* Principal - identité de l’émetteur de commandement de purge.

## <a name="purging-an-entire-table"></a>Purger une table entière
Purger une table comprend laisser tomber la table, et le marquer comme purgé de sorte que le processus de suppression dure décrit dans le [processus de purge](#purge-process) fonctionne sur elle. Laisser tomber une table sans purger ne supprime pas tous ses artefacts de stockage (supprimé selon la stratégie de rétention dure initialement mis sur la table). La `purge table allrecords` commande est rapide et efficace et est de loin préférable au processus d’enregistrement de purge, le cas échéant pour votre scénario. 

> [!Note]
> La commande est invoquée en exécutant la [table de purge *TableName* allrecords](#purge-table-tablename-allrecords-command) commande sur le point de terminaison de gestion des données (**https://ingest-[YourClusterName].kusto.windows.net**).

### <a name="purge-table-tablename-allrecords-command"></a>tableau de purge *TableName* allrecords commande

Semblable à['.purge table records ](#purge-table-tablename-records-command)' commande, cette commande peut être invoquée dans un programme (une étape unique) ou dans un manuel (deux étapes) mode.
1. Invocation programmatique (étape unique) :

     **Syntaxe**

     ```kusto
     .purge table [TableName] in database [DatabaseName] allrecords with (noregrets='true')
     ```

2. Invocation humaine (deux étapes):

     **Syntaxe**

     ```kusto
     // Step #1 - retrieve a verification token (the table will not be purged until step #2 is executed)
     .purge table [TableName] in database [DatabaseName] allrecords

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] in database [DatabaseName] allrecords with (verificationtoken='<verification token from step #1>')
     ```

    |Paramètres  |Description  |
    |---------|---------|
    |**DatabaseName**   |   Nom de la base de données.      |
    |**TableName**     |     Nom de la table.    |
    |**noregrets noregrets**    |     Si défini, déclenche une activation en une seule étape.    |
    |**vérificationtoken**     |  Dans le scénario d’activation en deux étapes **(noregrets** n’est pas défini), ce jeton peut être utilisé pour exécuter la deuxième étape et de commettre l’action. Si **le jet de vérification** n’est pas spécifié, il déclenchera la première étape de la commande, dans laquelle un jeton est retourné, de passer à la commande et d’effectuer l’étape de la commande #2.|

#### <a name="example-two-step-purge"></a>Exemple : Purge en deux étapes

1. Pour initier la purge dans un scénario d’activation en deux étapes, exécutez l’étape #1 de la commande : 

    ```kusto
    .purge table MyTable in database MyDatabase allrecords
    ```

    **Sortie**

    | VérificationToken
    |--
    |e43c7184ed2f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b

1.  Pour effectuer une purge dans un scénario d’activation en deux étapes, utilisez le jeton de vérification retourné de l’étape #1 pour exécuter l’étape #2 :

    ```kusto
    .purge table MyTable in database MyDatabase allrecords 
    with (verificationtoken='eyJTZXJ2aWNlTmFtZSI6IkVuZ2luZS1pdHNhZ3VpIiwiRGF0YWJhc2VOYW1lIjoiQXp1cmVTdG9yYWdlTG9ncyIsIlRhYmxlTmFtZSI6IkF6dXJlU3RvcmFnZUxvZ3MiLCJQcmVkaWNhdGUiOiIgd2hlcmUgU2VydmVyTGF0ZW5jeSA9PSAyNSJ9')
    ```
    
    La sortie est la même que la sortie de commande des tables de démonstration (retournée sans la table purgée).

    **Sortie**

    |TableName|nom_base_de_données|Dossier|DocString (en)
    |---|---|---|---
    |OtherTable|Mydatabase|---|---


#### <a name="example-single-step-purge"></a>Exemple : Purge en une seule étape

Pour déclencher une purge dans un scénario d’activation en une seule étape, exécutez la commande suivante :

```kusto
.purge table MyTable in database MyDatabase allrecords with (noregrets='true')
```

La sortie est la même que la sortie de commande des tables de démonstration (retournée sans la table purgée).

**Sortie**

|TableName|nom_base_de_données|Dossier|DocString (en)
|---|---|---|---
|OtherTable|Mydatabase|---|---


