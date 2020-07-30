---
title: string_size ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit string_size () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4832d00703ab9e6d1478af5b3f45ec294dfb79ce
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350908"
---
# <a name="string_size"></a>string_size()

Retourne la taille, en octets, de la chaîne d’entrée.

## <a name="syntax"></a>Syntaxe

`string_size(`*code*`)`

## <a name="arguments"></a>Arguments

* *source*: chaîne source qui sera mesurée pour la taille de chaîne.

## <a name="returns"></a>Retourne

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