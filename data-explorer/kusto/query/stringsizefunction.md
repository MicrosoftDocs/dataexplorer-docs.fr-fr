---
title: string_size() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit string_size() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7886e7b8fd5039c9b197ae435bce4f40b93e5038
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506883"
---
# <a name="string_size"></a>string_size()

Retourne la taille, dans les octets, de la chaîne d’entrée.

**Syntaxe**

`string_size(`*Source*`)`

**Arguments**

* *source*: La chaîne source qui sera mesurée pour la taille des cordes.

**Retourne**

Retourne la longueur, dans les octets, de la chaîne d’entrée.

**Exemples**

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