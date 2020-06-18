---
title: Erreurs dans le code natif-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les erreurs en code natif dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/15/2020
ms.openlocfilehash: 5446b98283a6533ceacd1c84fa3043732fe79e6a
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84942724"
---
# <a name="errors-in-native-code"></a>Erreurs dans le code natif

Le moteur Kusto est écrit en code natif et signale les erreurs en utilisant des `HRESULT` valeurs négatives. Ces erreurs ne sont généralement pas visibles avec une API de programmation, mais peuvent se produire. Par exemple, les échecs d’opération peuvent indiquer un état « `Exception from HRESULT:` *HRESULT-code*».

Les codes d’erreur natifs Kusto sont définis à l’aide `MAKE-HRESULT` de la macro Windows avec :

* Gravité définie sur`1`
* Installation définie sur`0xDA`
  
Les codes d’erreur suivants sont définis :

|Manifeste, constante                  |Code |Valeur        |Signification                                                                                                        |
|-----------------------------------|-----|-------------|---------------------------------------------------------------------------------------------------------------|
|`E_EXTENT_LOAD_FAILED`             | `0`  |`0x80DA0000`|Le partition de données n’a pas pu être chargé                                                                                  |
|`E_RUNAWAY_QUERY`                  | `1`  |`0x80DA0001`|L’exécution de la requête a été abandonnée, car elle a dépassé ses ressources autorisées                                                   |
|`E_STREAM_FORMAT`                  | `2`  |`0x80DA0002`|Impossible d’analyser un flux de données, car il n’est pas au format correct                                    |
|`E_QUERY_RESULT_SET_TOO_LARGE`     | `3`  |`0x80DA0003`|Le jeu de résultats pour cette requête dépasse ses limites d’enregistrement/de taille autorisées                                            |
|`E_STREAM_ENCODING_VERSION`        | `4`  |`0x80DA0004`|Impossible d’analyser un flux de résultats, car sa version est inconnue                                                   |
|`E_KVDB_ERROR`                     | `5`  |`0x80DA0005`|Impossible d’effectuer une opération de base de données de clé/valeur                                                              |
|`E_QUERY_CANCELLED`                | `6`  |`0x80DA0006`|La requête a été annulée                 |
|`E_LOW_MEMORY_CONDITION`           | `7`  |`0x80DA0007`|L’opération a été abandonnée en raison d’une insuffisance de mémoire de processus                                              |
|`E_WRONG_NUMBER_OF_FIELDS`         | `8`  |`0x80DA0008`|Un document CSV soumis à l’ingestion a un enregistrement avec un nombre incorrect de champs (par rapport à d’autres enregistrements)|
|`E_INPUT_STREAM_TOO_LARGE`         | `9`  |`0x80DA0009`|Le document soumis pour réception a dépassé la longueur autorisée                                           |
|`E_ENTITY_NOT_FOUND`               | `10` |`0x80DA000A`|L’entité demandée est introuvable                                                                             |
|`E_CLOSING_QUOTE_MISSING`          | `11` |`0x80DA000B`|Un document CSV soumis pour réception contient un champ avec un guillemet manquant                                        |
|`E_OVERFLOW`                       | `12` |`0x80DA000C`|Représente une erreur de dépassement arithmétique (le résultat d’un calcul est trop grand pour le type de destination)    |
|`E_RS_BLOCKED_ROWSTOREKEY_ERROR`   | `101`|`0x80DA0065`|Une tentative a été effectuée pour accéder à une clé de RowStore bloquée                                                          |
|`E_RS_SHUTTINGDOWN_ERROR`          | `102`|`0x80DA0066`|RowStore s’arrête                                                                                     |
|`E_RS_LOCAL_STORAGE_FULL_ERROR`    | `103`|`0x80DA0067`|Le stockage sur disque local alloué pour RowStore est plein                                                             |
|`E_RS_CANNOT_READ_WRITE_AHEAD_LOG` | `104`|`0x80DA0068`|La lecture à partir du stockage RowStore a échoué                                                                      |
|`E_RS_CANNOT_RETRIEVE_VALUES_ERROR`| `105`|`0x80DA0069`|Échec de la récupération des valeurs à partir du stockage RowStore                                                              |
|`E_RS_NOT_READY_ERROR`             | `106`|`0x80DA006A`|RowStore est en cours d’initialisation                                                                                      |
|`E_RS_INSERTION_THROTTLED_ERROR`   | `107`|`0x80DA006B`|L’insertion de la valeur dans un RowStore a été limitée                                                                   |
|`E_RS_READONLY_ERROR`              | `108`|`0x80DA006C`|RowStore est joint en état lecture seule                                                                       |
|`E_RS_UNAVAILABLE_ERROR`           | `109`|`0x80DA006D`|RowStore n’est pas disponible actuellement                                                                             |
|`E_RS_DOES_NOT_EXIST_ERROR`           | `110`|`0x80DA006E`| RowStore n’existe pas.                        |
|`E_SB_QUERY_THROTTLED_ERROR`           | `200`|`0x80DA00C8`|La requête bac à sable (sandbox) a été limitée                                                                           |
|`E_SB_QUERY_CANCELLED`           | `201`|`0x80DA00C9`|La requête bac à sable (sandbox) a été annulée                                                                          |
|`E_UNSUPPORTED_INSTRUCTION_SET`           | `202`|`0x80DA00CA`|Le jeu d’instructions requis du moteur n’est pas pris en charge par ce processeur                                                                            |
