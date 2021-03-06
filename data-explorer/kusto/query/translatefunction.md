---
title: translate ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit translate () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2019
ms.openlocfilehash: 5d186172a0be1780347dbf89600c200c00687291
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252036"
---
# <a name="translate"></a>translate()

Remplace un jeu de caractères (« searchList ») par un autre jeu de caractères (« replacementList ») dans une chaîne donnée.
La fonction recherche les caractères dans le « searchList » et les remplace par les caractères correspondants dans « replacementList »

## <a name="syntax"></a>Syntaxe

`translate(`*searchList* `,` *replacementList* `,` *texte*`)`

## <a name="arguments"></a>Arguments

* *searchList*: liste des caractères à remplacer.
* *replacementList*: liste des caractères qui doivent remplacer les caractères de’searchList'
* *Text*: chaîne à rechercher

## <a name="returns"></a>Retours

*texte* après remplacement de tous les ocurrences de caractères dans’replacementList’par les caractères correspondants dans’searchList'

## <a name="examples"></a>Exemples

|Entrée                                 |Output   |
|--------------------------------------|---------|
|`translate("abc", "x", "abc")`        |`"xxx"`  |
|`translate("abc", "", "ab")`          |`""`     |
|`translate("krasp", "otsku", "spark")`|`"kusto"`|