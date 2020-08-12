---
title: Activer ou désactiver l’exportation de données en continu-Azure Explorateur de données
description: Cet article explique comment désactiver ou activer l’exportation continue de données dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: b69db144474557ab8d5a8e19a7bce9bbbd5df183
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149374"
---
# <a name="disable-or-enable-continuous-export"></a>Désactiver ou activer l’exportation continue

Désactive ou active le travail d’exportation continue. Une exportation continue désactivée ne sera pas exécutée, mais son état actuel est persistant et peut être repris lorsque l’exportation continue est activée. 

Lorsque vous activez une exportation continue qui a été désactivée depuis longtemps, l’exportation continue à partir de son dernier arrêt lorsque l’exportation est désactivée. Cette continuation peut entraîner une exportation longue, bloquant l’exécution d’autres exportations, si la capacité de cluster est insuffisante pour traiter tous les processus. Les exportations continues sont exécutées par heure de la dernière exécution dans l’ordre croissant (l’exportation la plus ancienne s’exécutera en premier, jusqu’à ce que l’opération de rattrapage soit terminée). 

## <a name="syntax"></a>Syntaxe

`.enable` `continuous-export` *ContinuousExportName* 

`.disable` `continuous-export` *ContinuousExportName* 

## <a name="properties"></a>Propriétés

| Propriété             | Type   | Description                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | Nom de l’exportation continue |

## <a name="output"></a>Output

Résultat de la [commande afficher l’exportation continue](show-continuous-export.md) de l’exportation continue modifiée. 
