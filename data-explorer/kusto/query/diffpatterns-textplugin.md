---
title: plug-in diffpatterns_text-Azure Explorateur de données
description: Cet article décrit diffpatterns_text plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9321f30d2643f6e398d73cf7960490708626f723
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348358"
---
# <a name="diffpatterns_text-plugin"></a>diffpatterns_text, plug-in

Compare deux jeux de données de valeurs de chaîne et recherche des modèles de texte qui caractérisent les différences entre les deux jeux de données.

```kusto
T | evaluate diffpatterns_text(TextColumn, BooleanCondition)
```

`diffpatterns_text`Retourne un jeu de modèles de texte qui capturent différentes parties des données dans les deux jeux (c’est-à-dire un modèle qui capture un grand pourcentage des lignes lorsque la condition est `true` et un pourcentage faible des lignes lorsque la condition est `false` ). Les modèles sont créés à partir de jetons consécutifs (séparés par des espaces blancs), avec un jeton de la colonne de texte ou un `*` qui représente un caractère générique. Chaque modèle est représenté par une ligne dans les résultats.

## <a name="syntax"></a>Syntaxe

`T | evaluate diffpatterns_text(`TextColumn, BooleanCondition [, MinTokens, Threshold, MaxTokens]`)` 

**Arguments obligatoires**

* TextColumn- *column_name*

    La colonne de texte à analyser doit être de type chaîne.
    
* BooleanCondition- *expression booléenne*

    Définit comment générer les deux sous-ensembles d’enregistrements à comparer à la table d’entrée. L’algorithme divise la requête en deux jeux de données, « true » et « false » en fonction de la condition, puis analyse les différences (texte) entre elles. 

**Arguments facultatifs**

Tous les autres arguments sont facultatifs, mais ils doivent alors être ordonnés comme ci-dessous. 

* MinTokens-0 < *int* < 200 [valeur par défaut : 1]

    Définit le nombre minimal de jetons non génériques par résultat.

* Seuil-0,015 < *double* < 1 [par défaut : 0,05]

    Définit la différence de modèle minimal (ratio) entre les deux jeux (voir [diffpatterns](diffpatternsplugin.md)).

* MaxTokens-0 < *int* [par défaut : 20]

    Définit le nombre maximal de jetons (à partir du début) par résultat, en spécifiant une limite inférieure pour réduire l’exécution de la requête.

## <a name="returns"></a>Retourne

Le résultat de diffpatterns_text retourne les colonnes suivantes :

* Count_of_True : nombre de lignes correspondant au modèle lorsque la condition est `true` .
* Count_of_False : nombre de lignes correspondant au modèle lorsque la condition est `false` .
* Percent_of_True : pourcentage de lignes correspondant au modèle des lignes lorsque la condition est `true` .
* Percent_of_False : pourcentage de lignes correspondant au modèle des lignes lorsque la condition est `false` .
* Pattern : modèle de texte contenant des jetons de la chaîne de texte et `*` de' 'pour les caractères génériques. 

> [!NOTE]
> Les modèles ne sont pas nécessairement distincts et peuvent ne pas fournir une couverture complète du jeu de données. Les modèles peuvent se chevaucher et certaines lignes peuvent ne pas correspondre à un modèle.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents     
| where EventNarrative != "" and monthofyear(StartTime) > 1 and monthofyear(StartTime) < 9
| where EventType == "Drought" or EventType == "Extreme Cold/Wind Chill"
| evaluate diffpatterns_text(EpisodeNarrative, EventType == "Extreme Cold/Wind Chill", 2)
```

|Count_of_True|Count_of_False|Percent_of_True|Percent_of_False|Modèle|
|---|---|---|---|---|
|11|0|6,29|0|Changement de la sortie du Nord-Ouest dans * éveil * un creux de la surface a entraîné un effet de lac élevé Snowfall downwind * Lake Superior de|
|9|0|5,14|0|Haute pression canadien réglée * * région * produit les températures les plus froidles depuis février * 2006. Durées * températures figées|
|0|34|0|6,24|* * * * * * * * * * * * * * * * * * * West Tennessee,|
|0|42|0|7,71|* * * * * * a provoqué * * * * * * * * * à travers l’Ouest Colorado. *|
|0|45|0|8,26|* * en dessous de la normale *|
|0|110|0|20,18|Inférieure à la normale *|

