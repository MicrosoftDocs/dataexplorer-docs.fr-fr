---
title: Erreurs dans le code natif - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les erreurs dans le code natif dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/12/2019
ms.openlocfilehash: fa36bec3586a5e316b08ebc2949617268f0270ab
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523254"
---
# <a name="errors-in-native-code"></a>Erreurs dans le code natif

Le moteur Kusto est écrit dans le `HRESULT` code natif et signale des erreurs en utilisant des valeurs négatives. Ces erreurs ne sont généralement pas visibles avec une API programmatique, mais peuvent se produire. Par exemple, les défaillances d’opération peuvent représenter un statut de «`Exception from HRESULT:` *HRESULT-CODE*».

Les codes d’erreur natifs `MAKE-HRESULT` de Kusto sont définis à l’aide de Windows macro avec :

* Sévérité réglée à`1`
* Facilité définie à`0xDA`
  
Les codes d’erreur suivants sont définis :

|Manifeste constante                  |Code |Valeur        |Signification                                                                                                        |
|-----------------------------------|-----|-------------|---------------------------------------------------------------------------------------------------------------|
|`E_EXTENT_LOAD_FAILED`             | `0`  |`0x80DA0000`|L’éclat de données ne pouvait pas être chargé                                                                                  |
|`E_RUNAWAY_QUERY`                  | `1`  |`0x80DA0001`|Exécution de requête avorté car elle a dépassé ses ressources autorisées                                                   |
|`E_STREAM_FORMAT`                  | `2`  |`0x80DA0002`|Un flux de données ne peut pas être analysé car il est mal formaté                                                      |
|`E_QUERY_RESULT_SET_TOO_LARGE`     | `3`  |`0x80DA0003`|Le résultat fixé pour cette requête dépasse ses limites d’enregistrement/taille autorisées                                            |
|`E_STREAM_ENCODING_VERSION`        | `4`  |`0x80DA0004`|Un flux de résultats ne peut pas être analysé puisque sa version est inconnue                                                   |
|`E_KVDB_ERROR`                     | `5`  |`0x80DA0005`|Défaut d’effectuer une opération de base de données de clés/valeurs                                                              |
|`E_QUERY_CANCELLED`                | `6`  |`0x80DA0006`|La requête a été annulée                                                                                            |
|`E_LOW_MEMORY_CONDITION`           | `7`  |`0x80DA0007`|L’opération a été interrompue en raison de la mémoire de processus disponible à court                                              |
|`E_WRONG_NUMBER_OF_FIELDS`         | `8`  |`0x80DA0008`|Un document csv soumis à l’ingestion a un dossier avec le mauvais nombre de champs (par rapport à d’autres dossiers)|
|`E_INPUT_STREAM_TOO_LARGE`         | `9`  |`0x80DA0009`|Le document soumis à l’ingestion a dépassé la durée autorisée                                           |
|`E_ENTITY_NOT_FOUND`               | `10` |`0x80DA000A`|L’entité demandée n’a pas été trouvée                                                                             |
|`E_CLOSING_QUOTE_MISSING`          | `11` |`0x80DA000B`|Un document csv soumis pour ingestion a un champ avec un devis manquant                                        |
|`E_OVERFLOW`                       | `12` |`0x80DA000C`|Représente une erreur de débordement arithmétique (le résultat d’un calcul est trop grand pour le type de destination)    |
|`E_RS_BLOCKED_ROWSTOREKEY_ERROR`   | `101`|`0x80DA0065`|Une tentative a été faite pour accéder à une clé de magasin en rangée bloquée                                                          |
|`E_RS_SHUTTINGDOWN_ERROR`          | `102`|`0x80DA0066`|magasin de rangées est en arrêtant                                                                                     |
|`E_RS_LOCAL_STORAGE_FULL_ERROR`    | `103`|`0x80DA0067`|L’espace de disque alloué pour le stockage de magasin de ligne est plein                                                             |
|`E_RS_CANNOT_READ_WRITE_AHEAD_LOG` | `104`|`0x80DA0068`|La lecture du stockage des magasins en rangée a échoué                                                                      |
|`E_RS_CANNOT_RETRIEVE_VALUES_ERROR`| `105`|`0x80DA0069`|Défaut de récupérer les valeurs du stockage des magasins en rangée                                                              |
|`E_RS_NOT_READY_ERROR`             | `106`|`0x80DA006A`|magasin de rangées est l’initialisation                                                                                      |
|`E_RS_INSERTION_THROTTLED_ERROR`   | `107`|`0x80DA006B`|L’insertion de valeur à un magasin de rangées a été étranglée                                                                   |
|`E_RS_READONLY_ERROR`              | `108`|`0x80DA006C`|magasin de ligne est attaché dans l’état de lecture seulement                                                                       |
|`E_RS_UNAVAILABLE_ERROR`           | `109`|`0x80DA006D`|magasin de rangées est actuellement indisponible                                                                             |