---
title: opérateur de haut niveau - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de haut niveau dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7105bd4f9818deab1f0fa5dbe25dbb2d5b1b4afc
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663122"
---
# <a name="top-hitters-operator"></a>top-hitters, opérateur

Retourne une approximation des premiers résultats *N* (en supposant une distribution biaisée de l’entrée).

```kusto
T | top-hitters 25 of Page by Views 
```

**Syntaxe**

*T* `| top-hitters` *NumberOfRows* `of` *sort_key* `[` `by` *expression*`]`

**Arguments**

* *NumberOfRows*: Le nombre de rangées de *T* à revenir. Vous pouvez spécifier n’importe quelle expression numérique.
* *sort_key*: Le nom de la colonne par laquelle trier les lignes.
* *expression*: (facultatif) Une expression qui sera utilisée pour l’estimation des meilleurs frappeurs. 
    * *expression*: les meilleurs frappeurs retourneront *les rangées NumberOfRows* qui ont un maximum approximatif de somme *(expression).* L’expression peut être une colonne, ou toute autre expression qui évalue à un nombre. 
    *  Si *l’expression* n’est pas mentionnée, l’algorithme top-hitters comptera les occurrences de la *clé de tri.*  

**Remarques**

`top-hitters`est un algorithme d’approximation et doit être utilisé lors de l’exécution avec de grandes données. L’approximation des meilleurs frappeurs est basée sur l’algorithme [Count-Min-Sketch.](https://en.wikipedia.org/wiki/Count%E2%80%93min_sketch)  

**Exemple**

## <a name="getting-top-hitters-most-frequent-items"></a>Obtenir les meilleurs frappeurs (articles les plus fréquents) 

L’exemple suivant montre comment trouver les 5 langues les plus performantes avec la plupart des pages de Wikipédia (accessible après avril 2016). 

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

## <a name="getting-top-hitters-based-on-column-value-"></a>Obtenir les meilleurs frappeurs (basé sur la valeur de la colonne)

L’exemple suivant montre comment trouver les pages anglaises les plus regardées de Wikipédia de l’année 2016. La requête utilise 'Views' (numéro d’intégrage) pour calculer la popularité de la page (nombre de vues). 

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
|Wikipedia|13584915|
|Donald_Trump|12376448|
|YouTube|11917252|
|The_Revenant_ (2015_film)|10714263|
|Star_Wars:_The_Force_Awakens|9770653|
|Portail:Current_events|9578000|