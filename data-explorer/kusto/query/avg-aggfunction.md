---
title: AVG () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit AVG () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: f058af075a856d12a2a6a81419f32b6efbd9ea16
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349395"
---
# <a name="avg-aggregation-function"></a>AVG () (fonction d’agrégation)

Calcule la moyenne de *expr* dans le groupe. 

* Peut uniquement être utilisé dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

résumer `avg(` *expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation. Les enregistrements avec des `null` valeurs sont ignorés et ne sont pas inclus dans le calcul.

## <a name="returns"></a>Retourne

Valeur moyenne de *expr* dans le groupe.
 