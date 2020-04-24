---
title: compte() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le compte () (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 59b898d44507d844db1f714ef15effa004e5546f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516998"
---
# <a name="count-aggregation-function"></a>compte() (fonction d’agrégation)

Renvoie un nombre de dossiers par groupe de synthèse (ou au total, si la résumation se fait sans regroupement).

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)
* Utilisez la fonction d’agrégation [countif](countif-aggfunction.md) pour ne compter `true`que les enregistrements pour lesquels certains prédicent sur les retours.

**Syntaxe**

Résumer`count()`

**Retourne**

Renvoie un nombre de dossiers par groupe de synthèse (ou au total, si la résumation se fait sans regroupement).