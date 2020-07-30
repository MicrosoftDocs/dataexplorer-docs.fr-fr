---
title: opérateur de nombre-Azure Explorateur de données
description: Cet article décrit l’opérateur de nombre dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: 969efc142f1cd823b319a5c98494542fb2603f24
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348732"
---
# <a name="count-operator"></a>opérateur count

Retourne le nombre d’enregistrements dans le jeu d’enregistrements d’entrée.

## <a name="syntax"></a>Syntaxe

`T | count`

## <a name="arguments"></a>Arguments

*T*: données tabulaires dont les enregistrements doivent être comptabilisés.

## <a name="returns"></a>Retourne

Cette fonction retourne une table contenant un seul enregistrement et une colonne de type `long`. La valeur de la seule cellule correspond au nombre d’enregistrements dans *T*. 

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```
