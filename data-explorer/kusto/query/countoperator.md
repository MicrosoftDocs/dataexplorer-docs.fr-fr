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
ms.openlocfilehash: bf595e27d7b4881dca7b5e2a370c90a8407a8c78
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252585"
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
