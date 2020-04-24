---
title: parse_url() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit parse_url () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dfc093964ce5b91acc01f798f8f62651266ab153
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511490"
---
# <a name="parse_url"></a>parse_url()

Parse une `string` URL absolue `dynamic` et renvoie un objet contient des pièces URL.


**Syntaxe**

`parse_url(`*Url*`)`

**Arguments**

* *URL*: Une chaîne représente une URL ou la partie requête de l’URL.

**Retourne**

Un objet de [la dynamique](./scalar-data-types/dynamic.md) de type qui comprenait les composants d’URL : Schéma, Hôte, Port, Chemin, Nom d’utilisateur, Mot de Passe, Paramètres de requête, Fragment.

**Exemple**

```kusto
T | extend Result = parse_url("scheme://username:password@host:1234/this/is/a/path?k1=v1&k2=v2#fragment")
```

résultatra

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