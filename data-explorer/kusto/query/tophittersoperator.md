---
title: opérateur Top-Hitters-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’opérateur Top-Hitters dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: babb4e023d29c7894661e3acf2c0a09e753011c2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340817"
---
# <a name="top-hitters-operator"></a>top-hitters, opérateur

Retourne une approximation des *N* premiers résultats (en supposant une distribution asymétrique de l’entrée).

```kusto
T | top-hitters 25 of Page by Views 
```

## <a name="syntax"></a>Syntaxe

*Expression T* `| top-hitters` *numberOfRows* `of` *sort_key* `[` `by` *expression*`]`

## <a name="arguments"></a>Arguments

* *NumberOfRows*: nombre de lignes de *T* à retourner. Vous pouvez spécifier n’importe quelle expression numérique.
* *sort_key*: nom de la colonne selon laquelle trier les lignes.
* *expression*: (facultatif) expression qui sera utilisée pour l’estimation Hitters. 
    * *expression*: Top-Hitters retourne les lignes *numberOfRows* qui ont une valeur maximale de Sum (*expression*). L’expression peut être une colonne, ou toute autre expression qui prend la valeur d’un nombre. 
    *  Si l' *expression* n’est pas mentionnée, l’algorithme Top-Hitters compte les occurrences de la *clé de tri*.  

**Notes**

`top-hitters`est un algorithme d’approximation et doit être utilisé lors de l’exécution avec des données volumineuses. La approximation du Hitters supérieur est basée sur l’algorithme [Count-min-Sketch](https://en.wikipedia.org/wiki/Count%E2%80%93min_sketch) .  

## <a name="example"></a>Exemple

## <a name="getting-top-hitters-most-frequent-items"></a>Obtention des Hitters principaux (éléments les plus fréquents) 

L’exemple suivant montre comment rechercher les 5 langues les plus importantes avec la plupart des pages de Wikipédia (accessible après le 2016 avril). 

```kusto
PageViews
| where Timestamp > datetime(2016-04-01) and Timestamp < datetime(2016-05-01) 
| top-hitters 5 of Language 
```

|Langage|approximate_count_Language|
|---|---|
|en|1539954127|
|zh|339827659|
|de|262197491|
|ru|227003107|
|fr|207943448|

## <a name="getting-top-hitters-based-on-column-value-"></a>Obtention des Hitters supérieurs (en fonction de la valeur de colonne) * * *

L’exemple suivant montre comment rechercher les pages en anglais les plus consultées de l’année 2016. La requête utilise « views » (nombre entier) pour calculer la popularité des pages (nombre de vues). 

```kusto
PageViews
| where Timestamp > datetime(2016-01-01)
| where Language == "en"
| where Page !has 'Special'
| top-hitters 10 of Page by Views
```

|Page|approximate_sum_Views|
|---|---|
|Main_Page|1325856754|
|Web_scraping|43979153|
|Java_ (programming_language)|16489491|
|United_States|13928841|
|Wikipédia|13584915|
|Donald_Trump|12376448|
|YouTube|11917252|
|The_Revenant_ (2015_film)|10714263|
|Star_Wars : _The_Force_Awakens|9770653|
|Portail : Current_events|9578000|