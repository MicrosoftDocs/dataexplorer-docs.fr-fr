---
title: arg_max () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit arg_max () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: bd3d0b2c9d0c68ae646960b0b4b0b3ca41c0bb43
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248139"
---
# <a name="arg_max-aggregation-function"></a>arg_max () (fonction d’agrégation)

Recherche une ligne dans le groupe qui maximise *ExprToMaximize*et retourne la valeur de *ExprToReturn* (ou `*` pour retourner la ligne entière).

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

`summarize`[ `(` *NameExprToMaximize* `,` *NameExprToReturn* [ `,` ...] `)=` ] `arg_max` `(` *ExprToMaximize*, `*`  |  *ExprToReturn* [ `,` ...]`)`

## <a name="arguments"></a>Arguments

* *ExprToMaximize*: expression qui sera utilisée pour le calcul de l’agrégation. 
* *ExprToReturn*: expression qui sera utilisée pour retourner la valeur lorsque *ExprToMaximize* est maximal. L’expression à retourner peut être un caractère générique (*) pour retourner toutes les colonnes de la table d’entrée.
* *NameExprToMaximize*: nom facultatif de la colonne de résultat représentant *ExprToMaximize*.
* *NameExprToReturn*: noms facultatifs supplémentaires pour les colonnes de résultats représentant *ExprToReturn*.

## <a name="returns"></a>Retours

Recherche une ligne dans le groupe qui maximise *ExprToMaximize*et retourne la valeur de *ExprToReturn* (ou `*` pour retourner la ligne entière).

## <a name="examples"></a>Exemples

Voir des exemples pour la fonction d’agrégation [arg_min ()](arg-min-aggfunction.md)