---
title: Expressions régulières-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les expressions régulières dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.localizationpriority: high
ms.openlocfilehash: e4dda7ca499ac9fc9f90f6576758797d3f62a299
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513078"
---
# <a name="re2-syntax"></a>Syntaxe RE2

La syntaxe d’expression régulière RE2 décrit la syntaxe de la bibliothèque d’expressions régulières utilisée par Kusto (RE2).
Certaines fonctions de Kusto effectuent la correspondance, la sélection et l’extraction de chaînes à l’aide d’une expression régulière

- [countof()](countoffunction.md)
- [extract()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [matches regex](datatypes-string-operators.md)
- [opérateur parse](parseoperator.md)
- [remplacer ()](replacefunction.md)
- [Trim ()](trimfunction.md)
- [TrimEnd ()](trimendfunction.md)
- [TrimStart ()](trimstartfunction.md)

La syntaxe des expressions régulières prises en charge par Kusto est celle de la [bibliothèque RE2](https://github.com/google/re2/wiki/Syntax). Ces expressions doivent être encodées dans Kusto comme des `string` littéraux et toutes les règles de guillemets de chaîne de Kusto s’appliquent. Par exemple, l’expression régulière `\A` correspond au début d’une ligne et est spécifiée dans Kusto comme littéral de chaîne `"\\A"` (Notez la barre oblique inverse « supplémentaire » ( `\` )).
