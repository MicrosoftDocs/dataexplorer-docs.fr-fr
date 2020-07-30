---
title: parse_url ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit parse_url () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 94a35dbf742b6df31012e68b5f2b2f09bec9b7e5
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346284"
---
# <a name="parse_url"></a>parse_url()

Analyse une URL absolue `string` et retourne un `dynamic` objet contient des parties d’URL.


## <a name="syntax"></a>Syntaxe

`parse_url(`*URL*`)`

## <a name="arguments"></a>Arguments

* *URL*: une chaîne représente une URL ou la partie requête de l’URL.

## <a name="returns"></a>Retourne

Objet de type [dynamique](./scalar-data-types/dynamic.md) qui inclut les composants d’URL : schéma, hôte, port, chemin d’accès, nom d’utilisateur, mot de passe, paramètres de requête, fragment.

## <a name="example"></a>Exemple

```kusto
T | extend Result = parse_url("scheme://username:password@host:1234/this/is/a/path?k1=v1&k2=v2#fragment")
```

se traduira par

```
 {
    "Scheme":"scheme",
    "Host":"host",
    "Port":"1234",
    "Path":"this/is/a/path",
    "Username":"username",
    "Password":"password",
    "Query Parameters":"{"k1":"v1", "k2":"v2"}",
    "Fragment":"fragment"
 }
```