---
title: parse_url ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit parse_url () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0ce68151270369bd7dc0898b98029bacddf0c243
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248626"
---
# <a name="parse_url"></a>parse_url()

Analyse une URL absolue `string` et retourne un `dynamic` objet contient des parties d’URL.


## <a name="syntax"></a>Syntaxe

`parse_url(`*URL*`)`

## <a name="arguments"></a>Arguments

* *URL*: une chaîne représente une URL ou la partie requête de l’URL.

## <a name="returns"></a>Retours

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