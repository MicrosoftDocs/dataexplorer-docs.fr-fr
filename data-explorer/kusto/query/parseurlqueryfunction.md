---
title: parse_urlquery ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit parse_urlquery () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6d34ece3a945485b8a809089d030fa954b070a28
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346267"
---
# <a name="parse_urlquery"></a>parse_urlquery()

Retourne un `dynamic` objet qui contient les paramètres de requête.

## <a name="syntax"></a>Syntaxe

`parse_urlquery(`*demande*`)`

## <a name="arguments"></a>Arguments

* *requête*: une chaîne représente une requête d’URL.

## <a name="returns"></a>Retourne

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

**Remarques**

* Le format d’entrée doit respecter les normes de requête d’URL (key = value&...)
 