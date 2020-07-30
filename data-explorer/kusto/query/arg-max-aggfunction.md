---
title: arg_max () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit arg_max () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 1ce8bb0635743fd6692cda3b707bbb7d88e07af9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349701"
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

## <a name="returns"></a>Retourne

Recherche une ligne dans le groupe qui maximise *ExprToMaximize*et retourne la valeur de *ExprToReturn* (ou `*` pour retourner la ligne entière).

## <a name="examples"></a>Exemples

Voir des exemples pour la fonction d’agrégation [arg_min ()](arg-min-aggfunction.md)