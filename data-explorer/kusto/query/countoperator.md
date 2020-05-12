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
ms.openlocfilehash: 9a34734ebfee94646b2b2f15730f14f9d2709c6d
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227504"
---
# <a name="count-operator"></a>opérateur count

Retourne le nombre d’enregistrements dans le jeu d’enregistrements d’entrée.

**Syntaxe**

`T | count`

**Arguments**

*T*: données tabulaires dont les enregistrements doivent être comptabilisés.

**Retourne**

Cette fonction retourne une table contenant un seul enregistrement et une colonne de type `long`. La valeur de la seule cellule correspond au nombre d’enregistrements dans *T*. 

**Exemple**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```
