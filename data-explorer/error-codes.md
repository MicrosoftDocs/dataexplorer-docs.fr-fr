---
title: Codes d’erreur d’ingestion dans Azure Data Explorer
description: Cette rubrique liste les codes d’erreur d’ingestion dans Azure Data Explorer
author: orspod
ms.author: orspodek
ms.reviewer: vladikbr
ms.service: data-explorer
ms.topic: reference
ms.date: 11/11/2020
ms.openlocfilehash: aeef0c9295fbb22c225068fb240670fb7d637a98
ms.sourcegitcommit: c6cb2b1071048daa872e2fe5a1ac7024762c180e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96776551"
---
# <a name="ingestion-error-codes-in-azure-data-explorer"></a>Codes d’erreur d’ingestion dans Azure Data Explorer

La liste suivante contient les codes d’erreur que vous pouvez rencontrer pendant l’[ingestion](ingest-data-overview.md). Quand vous activez les [journaux de diagnostic](using-diagnostic-logs.md#ingestion-logs-schema) des échecs d’ingestion sur votre cluster, vous pouvez voir les codes d’erreur dans le journal des opérations d’**ingestion en échec**. Vous pouvez aussi superviser la [métrique](using-metrics.md#ingestion-metrics) de **résultat d’ingestion**  pour identifier la **catégorie** des erreurs d’ingestion, mais pas le code d’erreur spécifique. Les erreurs ci-dessous sont organisées par catégories. 

> [!NOTE]
> Si l’erreur est temporaire, une nouvelle tentative d’ingestion peut aboutir.

## <a name="category-badformat"></a>Catégorie : BadFormat

|Message d’erreur                                 |Description                                           |Permanent/Temporaire|
|---|---|---|
|Stream_WrongNumberOfFields                        |Nombre incohérent de champs dans les enregistrements d’entrée. HRESULT: 0x80DA0008      |Permanent           |
|Stream_ClosingQuoteMissing                        |Format CSV non valide. Le guillemet fermant est manquant. HRESULT: 0x80DA000b            |Permanent           |
|BadRequest_InvalidBlob                            |L’objet blob n’est pas valide.                                                              |Permanent           |
|BadRequest_EmptyArchive                           |L’archive est vide.                                                             |Permanent           |
|BadRequest_InvalidArchive                         |L’archive n’est pas valide.                                                           |Permanent           |
|BadRequest_InvalidMapping                         |Échec de l’analyse du mappage d’ingestion.<br>Pour plus d’informations sur l’écriture d’un mappage d’ingestion, consultez [Mappages de données](./kusto/management/mappings.md).   |Permanent           |
|BadRequest_InvalidMappingReference                |Référence de mappage non valide.            |Permanent           |
|BadRequest_FormatNotSupported                     |Le format n’est pas pris en charge. Cela peut être dû au fait que vous utilisez un format qui n’est pas pris en charge par une connexion de données particulière. <br>Pour plus d’informations sur les formats de données pris en charge par Azure Data Explorer pour l’ingestion, consultez [Formats de données pris en charge](ingestion-supported-formats.md). |Permanent          |
|BadRequest_InconsistentMapping                    |Le mappage d’ingestion pris en charge n’est pas cohérent par rapport au schéma de table existant. |Permanent           |
|BadRequest_UnexpectedCharacterInInputStream       |Caractère inattendu dans le flux d’entrée.                                     |Permanent           |

## <a name="category-badrequest"></a>Catégorie : BadRequest

|Message d’erreur                                 |Description                                           |Permanent/Temporaire|
|---|---|---|
|BadRequest_EmptyBlob                              |L’objet blob est vide.                                                               |Permanent           |
|BadRequest_EmptyBlobUri                           |L’URI de l’objet blob est vide.                                                           |Permanent           |
|BadRequest_DuplicateMapping                       |Les propriétés d’ingestion incluent à la fois ingestionMapping et ingestionMappingReference, ce qui n’est pas valide.              |Permanent          |
|BadRequest_InvalidOrEmptyTableName                |Le nom de la table est vide ou non valide.<br>Pour plus d’informations sur la convention d’affectation de noms d’Azure Data Explorer, consultez [Noms d’entités](./kusto/query/schema-entities/entity-names.md).    |Permanent          |
|BadRequest_EmptyDatabaseName                      |Le nom de la base de données est vide.             |Permanent          |
|BadRequest_EmptyMappingReference                  |Le mappage d’ingestion devrait être ingéré pour certains formats, or la référence de mappage est vide.<br>Pour plus d’informations sur le mappage, consultez [Mappage de données](./kusto/management/mappings.md).        |Permanent           |
|Download_BadRequest                               |Échec du téléchargement de la source à partir de Stockage Azure en raison d’une demande incorrecte.    |Permanent           |
|BadRequest_MissingMappingtFailure                 |Les formats Avro et Json doivent être ingérés avec le paramètre ingestionMapping ou ingestionMappingReference.         |Permanent           |
|Stream_NoDataToIngest                             |Donnée à ingérer introuvables.<br>Pour les données au format JSON, cette erreur peut indiquer que le format JSON n’était pas valide.        |Permanent          |
|Stream_DynamicPropertyBagTooLarge                 |Les données contiennent des valeurs trop élevées dans une colonne dynamique. HRESULT: 0x80DA000E         |Permanent          |
|General_BadRequest                                |Demande incorrecte.            |Permanent           |
|BadRequest_CorruptedMessage                       |Le message est corrompu.    |Permanent           |
|BadRequest_SyntaxError                            |Erreur de syntaxe de la demande.     |Permanent           |
|BadRequest_ZeroRetentionPolicyWithNoUpdatePolicy  |La table n’a aucune stratégie de rétention et n’est la table source d’aucune stratégie de mise à jour.    |Permanent           |
|BadRequest_CreationTimeEarlierThanSoftDeletePeriod|L’heure de création qui a été spécifiée pour l’ingestion n’est pas comprise dans `SoftDeletePeriod`.<br>Pour plus d’informations sur `SoftDeletePeriod`, consultez [Objet de stratégie](./kusto/management/retentionpolicy.md#the-policy-object).  |Permanent   |
|BadRequest_NotSupported                           |Demande non prise en charge.    |Permanent           |
|Download_SourceNotFound                           |Échec du téléchargement de la source à partir de Stockage Azure. Source introuvable.       |Permanent       |
|BadRequest_EntityNameIsNotValid                   |Le nom d’entité n’est pas valide.<br>Pour plus d’informations sur la convention d’affectation de noms d’Azure Data Explorer, consultez [Noms d’entités](./kusto/query/schema-entities/entity-names.md).    |Permanent           |
|BadRequest_MalformedIngestionProperty              |La propriété d’ingestion est incorrecte.    |Permanent           |
| BadRequest_IngestionPropertyNotSupportedInThisContext | La propriété d’ingestion n’est pas prise en charge dans ce contexte.| Permanent

## <a name="category-dataaccessnotauthorized"></a>Catégorie : DataAccessNotAuthorized

|Message d’erreur                                 |Description                                           |Permanent/Temporaire|
|---|---|---|
|Download_AccessConditionNotSatisfied              |Échec du téléchargement de la source à partir de Stockage Azure. Condition d’accès non satisfaite.     |Permanent           |
|Download_Forbidden                                |Échec du téléchargement de la source à partir de Stockage Azure. Accès interdit.    |Permanent           |
|Download_AccountNotFound                          |Échec du téléchargement de la source à partir de Stockage Azure. Compte introuvable.    |Permanent           |
|BadRequest_TableAccessDenied                      |L’accès à la table est refusé.<br>Pour plus d’informations, consultez [Autorisation basée sur les rôles dans Kusto](./kusto/management/access-control/role-based-authorization.md).     |Permanent           |
|BadRequest_DatabaseAccessDenied                   |L’accès à la base de données est refusé.<br>Pour plus d’informations, consultez [Autorisation basée sur les rôles dans Kusto](./kusto/management/access-control/role-based-authorization.md).                        |Permanent           |

## <a name="category-downloadfailed"></a>Catégorie : DownloadFailed

|Message d’erreur                                 |Description                                           |Permanent/Temporaire|
|---|---|---|
|Download_NotTransient                             |Échec du téléchargement de la source à partir de Stockage Azure. Une erreur non temporaire s’est produite.                   |Permanent           |
|Download_UnknownError                             |Échec du téléchargement de la source à partir de Stockage Azure. Une erreur inconnue s'est produite              |Temporaire           |


## <a name="category-entitynotfound"></a>Catégorie : EntityNotFound

|Message d’erreur                                 |Description                                           |Permanent/Temporaire|
|---|---|---|
|BadRequest_MappingReferenceWasNotFound            |La référence de mappage était introuvable.   |Permanent           |
|BadRequest_DatabaseNotExist                       |La base de données n’existe pas.          |Permanent           |
|BadRequest_TableNotExist                          |La table n’existe pas.          |Permanent           |
|BadRequest_EntityNotFound                         |L’entité Azure Data Explorer (telle qu’un mappage, une base de données ou une table) était introuvable.           |Permanent           |

## <a name="category-filetoolarge"></a>Catégorie : FileTooLarge

|Message d’erreur                                 |Description                                           |Permanent/Temporaire|
|---|---|---|
|Stream_InputStreamTooLarge                        |La taille totale des données d’entrée ou la taille d’un champ unique dans les données est trop importante. HRESULT: 0x80DA0009                 |Permanent          |
|BadRequest_FileTooLarge                           |La taille de l’objet blob a dépassé la taille limite autorisée pour l’ingestion.<br>Pour plus d’informations sur la taille limite d’ingestion, consultez [Vue d’ensemble de l’ingestion de données Azure Data Explorer](ingest-data-overview.md#comparing-ingestion-methods-and-tools). |Permanent           |

## <a name="category-internalserviceerror"></a>Catégorie : InternalServiceError

|Message d’erreur                                 |Description                                           |Permanent/Temporaire|
|---|---|---|
|General_InternalServerError                       |Une erreur de serveur interne s'est produite.                     |Temporaire          |
|General_TransientSchemaMismatch                   |Le schéma de la table cible au début de l’ingestion ne correspond pas au schéma au moment de la validation de l’ingestion.         |Temporaire           |
|Délai d'expiration                                            |L’opération a été abandonnée en raison du dépassement du délai d’attente.     |Temporaire           |
|BadRequest_MessageExhausted                       |Échec de l’ingestion de données, car l’ingestion a atteint le nombre maximal de nouvelles tentatives ou la période maximale de nouvelles tentatives.<br>Une nouvelle tentative d’ingestion peut aboutir.   |Temporaire          |

## <a name="category-updatepolicyfailure"></a>Catégorie : UpdatePolicyFailure

|Message d’erreur                                 |Description                                           |Permanent/Temporaire|
|---|---|---|
|UpdatePolicy_QuerySchemaDoesNotMatchTableSchema   |Échec de l’appel de la stratégie de mise à jour. Le schéma de requête ne correspond pas au schéma de table.     |Permanent           |
|UpdatePolicy_FailedDescendantTransaction          |Échec de l’appel de la stratégie de mise à jour. Échec de la stratégie de mise à jour transactionnelle descendante.    |Temporaire           |
|UpdatePolicy_IngestionError                       |Échec de l’appel de la stratégie de mise à jour. Une erreur d’ingestion s’est produite.<br>L’erreur est signalée dans la table source de la stratégie de mise à jour.     |Temporaire          |
|UpdatePolicy_UnknownError                         |Échec de l’appel de la stratégie de mise à jour. Une erreur inconnue s’est produite.<br>L’erreur est signalée dans la table cible de la stratégie de mise à jour.    |Temporaire           |
|UpdatePolicy_Cyclic_Update_Not_Allowed         |Échec de l’appel de la stratégie de mise à jour. La mise à jour cyclique n’est pas autorisée.      |Permanent           |

## <a name="category-useraccessnotauthorized"></a>Catégorie : UserAccessNotAuthorized

|Message d’erreur                                 |Description                                           |Permanent/Temporaire|
|---|---|---|
|BadRequest_InvalidKustoIdentityToken              |Jeton d’identité Kusto non valide.                           |Permanent           |
|Interdit                                          |Autorisations de sécurité insuffisantes pour exécuter la demande. | Permanent|

## <a name="category-unknown"></a>Catégorie : Unknown

|Message d’erreur                                 |Description                                           |Permanent/Temporaire|
|---|---|---|
|Unknown                                            |Une erreur inconnue s’est produite.                             |Temporaire          |
