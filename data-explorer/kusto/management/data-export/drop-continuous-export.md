---
title: Abandonner l’exportation continue des données-Azure Explorateur de données
description: Cet article explique comment supprimer l’exportation de données en continu dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: f08d8c99c242aa63e3847d3965bf9511967b60e1
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149462"
---
# <a name="drop-continuous-export"></a>Supprimer l’exportation continue

Supprime une tâche d’exportation continue.

## <a name="syntax"></a>Syntaxe

`.drop` `continuous-export` *ContinuousExportName*

## <a name="properties"></a>Propriétés

| Propriété             | Type   | Description                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | Nom de l’exportation continue |

## <a name="output"></a>Output

Les exportations continues restantes dans la base de données (après suppression). Schéma de sortie comme dans la [commande afficher l’exportation continue](show-continuous-export.md).
