---
title: compte opérateur - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de comptage dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: c0d0286919a68b6e58065e0a6fe7e0d24b1cdd5f
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610235"
---
# <a name="count-operator"></a>opérateur count

Retourne le nombre d’enregistrements dans le record d’entrée établi.

**Syntaxe**

`T | count`

**Arguments**

*T*: données tabulaires dont les enregistrements doivent être comptabilisés.

**Retourne**

Cette fonction retourne une table contenant un seul enregistrement et une colonne de type `long`. La valeur de la seule cellule correspond au nombre d’enregistrements dans *T*. 

**Exemple**

```kusto
StormEvents | count
```
