---
title: parse_urlquery ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit parse_urlquery () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b1092edfaca8c6789ec6c0dc478fb27505531b1e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244685"
---
# <a name="parse_urlquery"></a>parse_urlquery()

Retourne un `dynamic` objet qui contient les paramètres de requête.

## <a name="syntax"></a>Syntaxe

`parse_urlquery(`*demande*`)`

## <a name="arguments"></a>Arguments

* *requête*: une chaîne représente une requête d’URL.

## <a name="returns"></a>Retours

Objet de type [dynamique](./scalar-data-types/dynamic.md) qui comprend les paramètres de requête.

## <a name="example"></a>Exemple

```kusto
parse_urlquery("k1=v1&k2=v2&k3=v3")
```

se produit :

```kusto
 {
    "Query Parameters":"{"k1":"v1", "k2":"v2", "k3":"v3"}",
 }
```

**Notes**

* Le format d’entrée doit respecter les normes de requête d’URL (key = value&...)
 