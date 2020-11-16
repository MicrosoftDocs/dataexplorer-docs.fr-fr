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
ms.openlocfilehash: da8fe0bfedf5766f4599509e83fa1c7c050d069f
ms.sourcegitcommit: 88f8ad67711a4f614d65d745af699d013d01af32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2020
ms.locfileid: "94638986"
---
# <a name="parse_command_line"></a>parse_command_line()

Analyse une chaîne de ligne de commande Unicode et retourne un tableau dynamique des arguments de ligne de commande.

## <a name="syntax"></a>Syntax

`parse_command_line(`*COMMAND_LINE* , *parser_type*`)`

## <a name="arguments"></a>Arguments

* *COMMAND_LINE* : ligne de commande à analyser.
* *parser_type* : la seule valeur actuellement prise en charge est `"windows"` , qui analyse la ligne de commande de la même façon que [CommandLineToArgvW](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw).

## <a name="returns"></a>Retours

Tableau dynamique des arguments de ligne de commande.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print parse_command_line("echo \"hello world!\"", "windows")
```

|Résultats|
|---|
|[« ECHO », « Hello World ! »]|
