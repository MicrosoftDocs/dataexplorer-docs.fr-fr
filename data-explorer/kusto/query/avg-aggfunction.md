---
title: avg() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit avg() (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: aadc756bdf4c6cab805f58a8a600815cf29680f7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518341"
---
# <a name="avg-aggregation-function"></a>avg() (fonction d’agrégation)

Calcule la moyenne *d’Expr* dans l’ensemble du groupe. 

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

résumer `avg(` *Expr*`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation. Les `null` enregistrements avec valeurs sont ignorés et ne sont pas inclus dans le calcul.

**Retourne**

La valeur moyenne *d’Expr* dans l’ensemble du groupe.
 