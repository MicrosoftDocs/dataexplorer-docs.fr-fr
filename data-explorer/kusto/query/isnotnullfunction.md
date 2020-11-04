---
title: IsNotNull ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit IsNotNull () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 44161dcdc34232225f4b133fe0a569fb818c3f0f
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349373"
---
# <a name="isnotnull"></a>isnotnull()

Retourne `true` si l’argument n’a pas la valeur null.

## <a name="syntax"></a>Syntaxe

`isnotnull(`[ *valeur* ]`)`

`notnull(`[ *valeur* ] `)` -alias pour `isnotnull`

## <a name="example"></a>Exemple

```kusto
T | where isnotnull(PossiblyNull) | count
```
