---
title: parse_command_line ()-Azure Explorateur de données
description: Cet article décrit parse_command_line () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 06/28/2020
ms.openlocfilehash: 0296b41dc10092f0b274491c3fab3355fc82a2d9
ms.sourcegitcommit: 4eb64e72861d07cedb879e7b61a59eced74517ec
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2020
ms.locfileid: "85518134"
---
# <a name="parse_command_line"></a>parse_command_line()

Analyse une chaîne de ligne de commande Unicode et retourne un tableau dynamique des arguments de ligne de commande.

**Syntaxe**

`parse_command_line(`*COMMAND_LINE*,*parser_type*`)`

**Arguments**

* *COMMAND_LINE*: ligne de commande à analyser.
* *parser_type*: la seule valeur actuellement prise en charge est `"Windows"` , qui analyse la ligne de commande de la même façon que [CommandLineToArgvW](https://docs.microsoft.com/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw).

**Retourne**

Tableau dynamique des arguments de ligne de commande.

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print parse_command_line("echo \"hello world!\"", "windows")
```

|Résultats|
|---|
|[« ECHO », « Hello World ! »]|
