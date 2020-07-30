---
title: translate ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit translate () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2019
ms.openlocfilehash: d0e99048f3f1b0e3ce5c6c59a65ea645b22d15fe
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340052"
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