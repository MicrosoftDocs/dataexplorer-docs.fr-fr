---
title: Erreurs dans le code natif-Azure Explorateur de données
description: Cet article décrit les erreurs en code natif dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/15/2020
ms.openlocfilehash: f1c076ba2c85ee18474372e4e19b285c133633d7
ms.sourcegitcommit: 0d15903613ad6466d49888ea4dff7bab32dc5b23
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "86013836"
---
# <a name="errors-in-native-code"></a>Erreurs dans le code natif

Le moteur Kusto est écrit en code natif et signale les erreurs en utilisant des `HRESULT` valeurs négatives. Ces erreurs sont inhabituelles avec une API de programmation, mais peuvent se produire. Par exemple, les échecs d’opération peuvent indiquer un état « `Exception from HRESULT:` *HRESULT-code*».

Les codes d’erreur natifs Kusto sont définis à l’aide `MAKE-HRESULT` de la macro Windows avec :

* Gravité définie sur`1`
* Installation définie sur`0xDA`
  
Les codes d’erreur suivants sont définis.

|Manifeste, constante                  |Code  |Valeur       |Signification                                                                                                        |
|-----------------------------------|------|------------|---------------------------------------------------------------------------------------------------------------|
|`E_EXTENT_LOAD_FAILED`             | `0`  |`0x80DA0000`|Impossible de charger l’étendue des données                                                                                 |
|`E_RUNAWAY_QUERY`                  | `1`  |`0x80DA0001`|L’exécution de la requête a été abandonnée car elle a dépassé ses ressources autorisées                                              |
|`E_STREAM_FORMAT`                  | `2`  |`0x80DA0002`|Impossible d’analyser un flux de données, car il n’est pas au format correct                                                     |
|`E_QUERY_RESULT_SET_TOO_LARGE`     | `3`  |`0x80DA0003`|Le jeu de résultats pour cette requête dépasse ses limites d’enregistrement/de taille autorisées.                                         |
|`E_STREAM_ENCODING_VERSION`        | `4`  |`0x80DA0004`|Impossible d’analyser un flux de résultats, car sa version est inconnue                                                 |
|`E_KVDB_ERROR`                     | `5`  |`0x80DA0005`|Échec dans le composant de magasin de clés/valeurs incorporé                                                              |
|`E_QUERY_CANCELLED`                | `6`  |`0x80DA0006`|La requête a été annulée                                                                                             |
|`E_LOW_MEMORY_CONDITION`           | `7`  |`0x80DA0007`|Une condition de mémoire insuffisante s’est produite                                                                                  |
|`E_WRONG_NUMBER_OF_FIELDS`         | `8`  |`0x80DA0008`|Nombre de champs incorrect dans le flux d’entrée                                                                 |
|`E_INPUT_STREAM_TOO_LARGE`         | `9`  |`0x80DA0009`|La taille d’entrée d’un champ/enregistrement/flux a dépassé la longueur autorisée                                          |
|`E_ENTITY_NOT_FOUND`               | `10` |`0x80DA000A`|L’entité demandée est introuvable                                                                              |
|`E_CLOSING_QUOTE_MISSING`          | `11` |`0x80DA000B`|Un flux de fichier CSV envoyé pour réception contient un champ avec un guillemet manquant                                          |
|`E_OVERFLOW`                       | `12` |`0x80DA000C`|Représente une erreur de dépassement arithmétique. Le résultat d’un calcul est trop grand pour le type de destination     |
|`E_INCOMPATIBLE_TOKENIZERS`        | `13` |`0x80DA000D`|Échec de la fusion des extensions en raison d’une incompatibilité de générateurs des index de fusion                                    |
|`E_DYNAMIC_PROPERTY_BAG_TOO_LARGE` | `14` |`0x80DA000E`|La taille combinée des clés distinctes du conteneur des propriétés est trop grande                                             |
|`E_RS_BLOCKED_ROWSTOREKEY_ERROR`   | `101`|`0x80DA0065`|Une tentative a été effectuée pour accéder à une clé de RowStore bloquée                                                           |
|`E_RS_SHUTTINGDOWN_ERROR`          | `102`|`0x80DA0066`|RowStore s’arrête                                                                                      |
|`E_RS_LOCAL_STORAGE_FULL_ERROR`    | `103`|`0x80DA0067`|Le stockage sur disque local pour RowStore est plein                                                                        |
|`E_RS_CANNOT_READ_WRITE_AHEAD_LOG` | `104`|`0x80DA0068`|Échec de la lecture à partir du stockage d’écriture anticipée RowStore                                                           |
|`E_RS_CANNOT_RETRIEVE_VALUES_ERROR`| `105`|`0x80DA0069`|Échec de la récupération des valeurs à partir du stockage RowStore                                                               |
|`E_RS_NOT_READY_ERROR`             | `106`|`0x80DA006A`|RowStore est en cours d’initialisation                                                                                       |
|`E_RS_INSERTION_THROTTLED_ERROR`   | `107`|`0x80DA006B`|L’insertion de la valeur dans un RowStore a été limitée                                                                    |
|`E_RS_READONLY_ERROR`              | `108`|`0x80DA006C`|RowStore est joint en état lecture seule                                                                        |
|`E_RS_UNAVAILABLE_ERROR`           | `109`|`0x80DA006D`|RowStore n’est pas disponible actuellement                                                                              |
|`E_RS_DOES_NOT_EXIST_ERROR`        | `110`|`0x80DA006E`|RowStore n’existe pas                                                                                         |
|`E_SB_QUERY_THROTTLED_ERROR`       | `200`|`0x80DA00C8`|La requête bac à sable (sandbox) a été limitée                                                                                  |
|`E_SB_QUERY_CANCELLED`             | `201`|`0x80DA00C9`|La requête bac à sable (sandbox) a été annulée                                                                                   |
|`E_UNSUPPORTED_INSTRUCTION_SET`    | `202`|`0x80DA00CA`|Le jeu d’instructions requis du moteur n’est pas pris en charge par ce processeur                                                   |
