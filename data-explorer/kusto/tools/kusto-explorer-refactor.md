---
title: Kusto Explorer Code Refactoring - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Kusto Explorer Code Refactoring dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/05/2019
ms.openlocfilehash: 0a89cf9c648fc4811d56c22012cdb25d5505eb4c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523985"
---
# <a name="kusto-explorer-code-refactoring"></a>Kusto Explorer Code Refactoring

Semblable à d’autres IDE, Kusto.Explorer offre plusieurs fonctionnalités à l’édition de requêteS KQL et la refactorisation.

## <a name="rename-variable-or-column-name"></a>Renommer le nom variable ou de colonne

En `Ctrl` + `R`cliquant, `Ctrl` + `R` dans la fenêtre De l’éditeur De requête vous permettra de renommer le symbole actuellement sélectionné.

Voir l’instantané ci-dessous qui démontre l’expérience :

![texte de remplacement](./Images/KustoTools-KustoExplorer/ke-refactor-rename.gif "renommer le refacteur")

## <a name="extract-scalars-as-let-expressions"></a>Extraire les scalars comme `let` expressions

Vous pouvez promouvoir littéral actuellement `Alt` + `Ctrl` + `M`sélectionné comme `let` expression en cliquant . 

![texte de remplacement](./Images/KustoTools-KustoExplorer/ke-extract-as-let-literal.gif "extrait-comme-laisser-littéral")

## <a name="extract-tabular-statements-as-let-expressions"></a>Extraire les `let` déclarations tabulaires comme expressions

Vous pouvez également promouvoir `let` les expressions tabulaires comme `Alt` + `Ctrl` +des déclarations en sélectionnant son texte, puis en `M`cliquant . 

![texte de remplacement](./Images/KustoTools-KustoExplorer/ke-extract-as-let-tabular.gif "extrait-comme-laisser-tabulaire")
