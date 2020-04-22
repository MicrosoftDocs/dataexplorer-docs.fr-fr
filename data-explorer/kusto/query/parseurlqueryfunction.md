---
title: parse_urlquery() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit parse_urlquery() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f00fefcd6245528d7ae50d6046d97289a92317d
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744623"
---
# <a name="parse_urlquery"></a>parse_urlquery()

Retourne `dynamic` un objet contient les paramètres de requête.

**Syntaxe**

`parse_urlquery(`*Requête*`)`

**Arguments**

* *requête*: Une chaîne représente une requête url.

**Retourne**

Un objet de [type dynamique](./scalar-data-types/dynamic.md) qui inclut les paramètres de requête.

**Exemple**

```kusto
parse_urlquery("k1=v1&k2=v2&k3=v3")
```

résultatra :

```kusto
 {
    "Query Parameters":"{"k1":"v1", "k2":"v2", "k3":"v3"}",
 }
```

**Remarques**

* Le format d’entrée doit suivre les normes de requête URL (& de la valeur de clé...)
 