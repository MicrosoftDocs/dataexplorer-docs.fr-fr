---
title: diffpatterns_text plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit diffpatterns_text plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 58f059e2346dfb3f15bff295126a14a97f10f317
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516012"
---
# <a name="diffpatterns_text-plugin"></a>diffpatterns_text plugin

Compare deux ensembles de données de valeurs de chaîne et trouve des modèles de texte qui caractérisent les différences entre les deux ensembles de données.

```kusto
T | evaluate diffpatterns_text(TextColumn, BooleanCondition)
```

Le `diffpatterns_text` renvoi d’un ensemble de modèles de texte qui capturent différentes parties des données dans les deux ensembles (c.-à-d. un modèle capturant un grand pourcentage des lignes lorsque la condition est `true` et faible pourcentage des lignes lorsque la condition est `false`). Les motifs sont construits à partir de jetons consécutifs (séparés `*` par l’espace blanc), avec un jeton de la colonne de texte ou un représentant d’une wildcard. Chaque modèle est représenté par une ligne dans les résultats.

**Syntaxe**

`T | evaluate diffpatterns_text(`TextColumn, BooleanCondition [, MinTokens, Seuil , MaxTokens]`)` 

**Arguments requis**

* TextColumn - *column_name*

    La colonne de texte à analyser, doit être de chaîne de type.
    
* BooleanCondition - *Expression Boolean*

    Définit comment générer les deux sous-ensembles d’enregistrement à comparer à la table d’entrée. L’algorithme divise la requête en deux ensembles de données, "Vrai" et "Faux" selon la condition, puis analyse les différences (texte) entre eux. 

**Arguments facultatifs**

Tous les autres arguments sont facultatifs, mais ils doivent alors être ordonnés comme ci-dessous. 

* MinTokens - 0 < *int* < 200 [par défaut: 1]

    Définit le nombre minimal de jetons non wildcard par modèle de résultat.

* Seuil - 0,015 < *double* < 1 [par défaut: 0,05]

    Définit la différence de modèle (ratio) minimale entre les deux ensembles (voir [diffpatternes](diffpatternsplugin.md)).

* MaxTokens - 0 < *int* [par défaut: 20]

    Définit le nombre maximal de jetons (dès le début) par modèle de résultat, en spécifiant une limite inférieure diminue le temps d’exécution de la requête.

**Retourne**

Le résultat de diffpatterns_text renvoie les colonnes suivantes :

* Count_of_True: Le nombre de lignes correspondant au modèle `true`lorsque la condition est .
* Count_of_False: Le nombre de lignes correspondant au modèle `false`lorsque la condition est .
* Percent_of_True: Le pourcentage de lignes correspondant au modèle des lignes `true`lorsque la condition est .
* Percent_of_False: Le pourcentage de lignes correspondant au modèle des lignes `false`lorsque la condition est .
* Modèle: Le modèle de texte contenant des`*`jetons de la chaîne de texte et ' pour les wildcards. 

> [!NOTE]
> Les modèles ne sont pas nécessairement distincts et peuvent ne pas fournir une couverture complète de l’ensemble de données. Les modèles peuvent se chevaucher et certaines lignes peuvent ne pas correspondre à n’importe quel modèle.

**Exemple**

```kusto
StormEvents     
| where EventNarrative != "" and monthofyear(StartTime) > 1 and monthofyear(StartTime) < 9
| where EventType == "Drought" or EventType == "Extreme Cold/Wind Chill"
| evaluate diffpatterns_text(EpisodeNarrative, EventType == "Extreme Cold/Wind Chill", 2)
```
|Count_of_True|Count_of_False|Percent_of_True|Percent_of_False|Modèle|
|---|---|---|---|---|
|11|0|6.29|0|Vents se déplaçant vers le nord-ouest dans le sillage d’un creux de surface a apporté de fortes chutes de neige d’effet de lac sous le vent - Lac Supérieur de|
|9|0|5.14|0|La haute pression canadienne s’est installée dans la région, ce qui a produit les températures les plus froides depuis février 2006. Durées et températures glaciales|
|0|34|0|6.24|Dans l’ouest du Tennessee, le Tennessee de l’Ouest.|
|0|42|0|7.71|À travers l’ouest du Colorado. *|
|0|45|0|8.26|En dessous de la normale|
|0|110|0|20.18|En dessous de la normale|

