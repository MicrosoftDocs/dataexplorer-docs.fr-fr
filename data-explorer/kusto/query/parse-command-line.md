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
ms.openlocfilehash: a13e3def33ea7098a48db7ffefa4406097863c2a
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342720"
---
# <a name="parse_command_line"></a>parse_command_line()

Analyse une chaîne de ligne de commande Unicode et retourne un tableau dynamique des arguments de ligne de commande.

## <a name="syntax"></a>Syntax

`parse_command_line(`*COMMAND_LINE*,*parser_type*`)`

## <a name="arguments"></a>Arguments

* *COMMAND_LINE*: ligne de commande à analyser.
* *parser_type*: la seule valeur actuellement prise en charge est `"Windows"` , qui analyse la ligne de commande de la même façon que [CommandLineToArgvW](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw).

## <a name="returns"></a>Retours

Tableau dynamique des arguments de ligne de commande.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print parse_command_line("echo \"hello world!\"", "windows")
```

|Résultat|
|---|
|[« ECHO », « Hello World ! »]|