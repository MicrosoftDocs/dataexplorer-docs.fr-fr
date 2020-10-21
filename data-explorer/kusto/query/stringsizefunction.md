---
title: string_size ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit string_size () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ea06e7e41ebe48e09839109745f3fe7ba5a96e7a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251465"
---
# <a name="string_size"></a>string_size()

Retourne la taille, en octets, de la chaîne d’entrée.

## <a name="syntax"></a>Syntaxe

`string_size(`*code*`)`

## <a name="arguments"></a>Arguments

* *source*: chaîne source qui sera mesurée pour la taille de chaîne.

## <a name="returns"></a>Retours

Retourne la longueur, en octets, de la chaîne d’entrée.

## <a name="examples"></a>Exemples

```kusto
print size = string_size("hello")
```

|taille|
|---|
|5|

```kusto
print size = string_size("⒦⒰⒮⒯⒪")
```

|taille|
|---|
|15|