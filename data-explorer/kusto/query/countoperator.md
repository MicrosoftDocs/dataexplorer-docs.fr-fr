---
title: opérateur de nombre-Azure Explorateur de données
description: Cet article décrit l’opérateur de nombre dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: 14895e17d77e3bbf1d98d2d68221930d3f291774
ms.sourcegitcommit: f7bebd245081a5cdc08e88fa4f9a769c18e13e5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94644618"
---
# <a name="count-operator"></a>opérateur count

Retourne le nombre d’enregistrements dans le jeu d’enregistrements d’entrée.

## <a name="syntax"></a>Syntaxe

`T | count`

## <a name="arguments"></a>Arguments

*T*: données tabulaires dont les enregistrements doivent être comptabilisés.

## <a name="returns"></a>Retours

Cette fonction retourne une table contenant un seul enregistrement et une colonne de type `long`. La valeur de la seule cellule correspond au nombre d’enregistrements dans *T*. 

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

## <a name="see-also"></a>Voir aussi

Pour plus d’informations sur la fonction d’agrégation Count (), consultez [Count () (fonction d’agrégation)](count-aggfunction.md).
