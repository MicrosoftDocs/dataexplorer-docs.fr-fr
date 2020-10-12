---
title: Refactorisation du code Kusto Explorer-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la refactorisation du code Kusto Explorer dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/05/2019
ms.openlocfilehash: 959cf8d25b20d459b48a0c8f1968541b50917a9d
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942231"
---
# <a name="kusto-explorer-code-refactoring"></a>Refactorisation du code Kusto Explorer

À l’instar des autres IDE, Kusto. Explorer offre plusieurs fonctionnalités pour la modification et la refactorisation des requêtes KQL.

## <a name="rename-variable-or-column-name"></a>Renommer une variable ou un nom de colonne

Lorsque vous cliquez sur `Ctrl` + `R` , `Ctrl` + `R` dans la fenêtre de l’éditeur de requête, vous pouvez renommer le symbole actuellement sélectionné.

Voir ci-dessous une capture instantanée illustrant l’expérience :

![Image GIF animée montrant une variable renommée dans la fenêtre de l’éditeur de requête. Trois occurrences sont remplacées simultanément par le nouveau nom.](./Images/KustoTools-KustoExplorer/ke-refactor-rename.gif "Refactoriser-renommer")

## <a name="extract-scalars-as-let-expressions"></a>Extraire les valeurs scalaires en tant qu' `let` expressions

Vous pouvez promouvoir le littéral actuellement sélectionné en tant qu' `let` expression en cliquant sur `Alt` + `Ctrl` + `M` . 

![Image GIF animée. Le pointeur de l’éditeur de requête démarre sur une expression littérale. Une instruction Let apparaît alors et définit cette valeur littérale sur une nouvelle variable.](./Images/KustoTools-KustoExplorer/ke-extract-as-let-literal.gif "extraire en tant que littéral Let")

## <a name="extract-tabular-statements-as-let-expressions"></a>Extraire les instructions tabulaires en tant qu' `let` expressions

Vous pouvez également promouvoir des expressions tabulaires en tant qu' `let` instructions en sélectionnant son texte, puis en cliquant sur `Alt` + `Ctrl` + `M` . 

![Image GIF animée. Une expression tabulaire est sélectionnée dans l’éditeur de requête. Une instruction Let apparaît alors et définit cette expression tabulaire sur une nouvelle variable.](./Images/KustoTools-KustoExplorer/ke-extract-as-let-tabular.gif "extraire-As-Let-tabulaire")
