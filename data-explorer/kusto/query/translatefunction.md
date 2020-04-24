---
title: traduire() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit traduire () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2019
ms.openlocfilehash: f128ce31af7fc720ee81b7471d3d74616197b8d4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505710"
---
# <a name="translate"></a>translate()

Remplace un ensemble de caractères ('searchList') par un autre ensemble de caractères ('replacementList') dans une chaîne donnée.
La fonction recherche les caractères dans la 'searchList' et les remplace par les caractères correspondants dans 'replacementList'

**Syntaxe**

`translate(`*searchList* `,` *replacementList* `,` *texte*`)`

**Arguments**

* *searchList*: La liste des caractères qui devraient être remplacés
* *replacementList*: La liste des caractères qui doivent remplacer les caractères dans 'searchList'
* *texte*: Une chaîne à rechercher

**Retourne**

*texte* après avoir remplacé toutes les ocurrences de caractères dans 'replacementList' par les caractères correspondants dans 'searchList'

**Exemples**

|Entrée                                 |Output   |
|--------------------------------------|---------|
|`translate("abc", "x", "abc")`        |`"xxx"`  |
|`translate("abc", "", "ab")`          |`""`     |
|`translate("krasp", "otsku", "spark")`|`"kusto"`|