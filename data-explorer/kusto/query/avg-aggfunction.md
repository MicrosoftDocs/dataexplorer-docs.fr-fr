---
title: AVG () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit AVG () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 949bd463f57b9eb0b674fe780aa50e78e30926a2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248040"
---
# <a name="avg-aggregation-function"></a>AVG () (fonction d’agrégation)

Calcule la moyenne de *expr* dans le groupe. 

* Peut uniquement être utilisé dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

résumer `avg(` *expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation. Les enregistrements avec des `null` valeurs sont ignorés et ne sont pas inclus dans le calcul.

## <a name="returns"></a>Retours

Valeur moyenne de *expr* dans le groupe.
 