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
ms.openlocfilehash: f330c10e95cdc36eae497811ef895ef827918b43
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346488"
---
# <a name="parse_command_line"></a>parse_command_line()

Analyse une chaîne de ligne de commande Unicode et retourne un tableau dynamique des arguments de ligne de commande.

## <a name="syntax"></a>Syntaxe

`parse_command_line(`*COMMAND_LINE*,*parser_type*`)`

## <a name="arguments"></a>Arguments

* *COMMAND_LINE*: ligne de commande à analyser.
* *parser_type*: la seule valeur actuellement prise en charge est `"Windows"` , qui analyse la ligne de commande de la même façon que [CommandLineToArgvW](https://docs.microsoft.com/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw).

## <a name="returns"></a>Retourne

Tableau dynamique des arguments de ligne de commande.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print parse_command_line("echo \"hello world!\"", "windows")
```

|Résultat|
|---|
|[« ECHO », « Hello World ! »]|
