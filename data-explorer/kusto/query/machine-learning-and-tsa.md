---
title: Analyse des séries chronologiques-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’analyse des séries chronologiques dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/06/2019
ms.openlocfilehash: 79857813c55fdc3af7224a7fd4e780ebf381f2bc
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246442"
---
# <a name="time-series-analysis"></a>Analyse des séries chronologiques 

Le langage de requête Kusto offre une prise en charge dans les séries sous la forme d’un type de données natif.
L’opérateur make-Series transforme les données en un type de données de série, et une famille de fonctions est fournie pour le traitement avancé de ce type de données. Les fonctions incluent le nettoyage des données, la modélisation Machine Learning et la détection des valeurs hors norme.