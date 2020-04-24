---
title: Expressions régulières - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit des expressions régulières dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 0bc5dc6705d6cada446716f2f9d84618322ecd76
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510521"
---
# <a name="regular-expressions"></a>Expressions régulières

La syntaxe d’expression régulière RE2 décrit la syntaxe de la bibliothèque d’expression régulière utilisée par Kusto (re2).
Il y a quelques fonctions dans Kusto effectuer l’appariement de cordes, la sélection, et l’extraction en utilisant une expression régulière

- [countof()](countoffunction.md)
- [extrait()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [matches regex](datatypes-string-operators.md)
- [opérateur parse](parseoperator.md)
- [remplacer()](replacefunction.md)
- [garniture()](trimfunction.md)
- [trimend()](trimendfunction.md)
- [trimstart()](trimstartfunction.md)

La syntaxe d’expression régulière soutenue par Kusto est celle de la [bibliothèque re2](https://github.com/google/re2/wiki/Syntax), et est détaillée ci-dessous. Notez que ces expressions doivent être codées dans Kusto comme `string` littérales, et toutes les règles de citation de chaîne de Kusto s’appliquent. Par exemple, l’expression `\A` régulière correspond au début d’une ligne (voir le tableau `"\\A"` ci-dessous), et est spécifiée dans Kusto comme la chaîne littérale (notez le "extra" backslash (`\`) caractère.)

