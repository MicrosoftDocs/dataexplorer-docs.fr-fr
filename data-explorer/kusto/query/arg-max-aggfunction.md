---
title: arg_max() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit arg_max () (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 73953a17b1819081c8458d5dbde3fa6d55c8866e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518953"
---
# <a name="arg_max-aggregation-function"></a>arg_max)) (fonction d’agrégation)

Trouve une ligne dans le groupe qui maximise *ExprToMaximize*, et retourne `*` la valeur *d’ExprToReturn* (ou de retourner la rangée entière).

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

`summarize`[`(`*NameExprToMaximize* `,` *NameExprToReturn* [`,` ...] `)=` `arg_max` ] `(` *ExprToMaximize*, `*`  |  *ExprToReturn* [`,` ...]`)`

**Arguments**

* *ExprToMaximize*: Expression qui sera utilisée pour le calcul de l’agrégation. 
* *ExprToReturn*: Expression qui sera utilisée pour le retour de la valeur lorsque *ExprToMaximize* est maximum. L’expression au retour peut être une wildcard pour retourner toutes les colonnes de la table d’entrée.
* *NameExprToMaximize*: Un nom optionnel pour la colonne de résultat représentant *ExprToMaximize*.
* *NomExprToReturn*: Noms optionnels supplémentaires pour les colonnes de résultat représentant *ExprToReturn*.

**Retourne**

Trouve une ligne dans le groupe qui maximise *ExprToMaximize*, et retourne `*` la valeur *d’ExprToReturn* (ou de retourner la rangée entière).

**Exemples**

Voir des exemples pour [arg_min()](arg-min-aggfunction.md) fonction d’agrégation