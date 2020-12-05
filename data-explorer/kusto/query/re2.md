---
title: Expressions régulières - Azure Data Explorer | Microsoft Docs
description: Cet article décrit les expressions régulières dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.localizationpriority: high
ms.openlocfilehash: e4dda7ca499ac9fc9f90f6576758797d3f62a299
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513078"
---
# <a name="re2-syntax"></a>Syntaxe RE2

La syntaxe d’expression régulière RE2 décrit la syntaxe de la bibliothèque d’expressions régulières utilisée par Kusto (RE2).
Certaines fonctions de Kusto effectuent la mise en correspondance, la sélection et l’extraction de chaînes à l’aide d’une expression régulière

- [countof()](countoffunction.md)
- [extract()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [matches regex](datatypes-string-operators.md)
- [opérateur parse](parseoperator.md)
- [replace()](replacefunction.md)
- [trim()](trimfunction.md)
- [trimend()](trimendfunction.md)
- [trimstart()](trimstartfunction.md)

La syntaxe d’expression régulière prise en charge par Kusto est celle de la [bibliothèque RE2](https://github.com/google/re2/wiki/Syntax). Ces expressions doivent être encodées dans Kusto en tant que littéraux `string`, et toutes les règles de citation de chaîne de Kusto s’appliquent. Par exemple, l’expression régulière `\A` correspond au début d’une ligne et est spécifiée dans Kusto comme littéral de chaîne `"\\A"` (notez la barre oblique inversée « supplémentaire » (`\`)).
