---
title: opérateur de réorganisation du projet - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de réorganisation du projet dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bf56a69891f83aaabc12dbbcd8f758ecb963b493
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510844"
---
# <a name="project-reorder-operator"></a>project-reorder, opérateur

Réorganise les colonnes dans la sortie de résultat.

```kusto
T | project-reorder Col2, Col1, Col* asc
```

**Syntaxe**

*T* `| project-reorder` *ColumnNameOrPattern* [`asc`|`desc`] [`,` ...]

**Arguments**

* *T*: La table d’entrée.
* *ColonneNameOrPattern:* Le nom de la colonne ou du modèle de wildcard de colonne ajouté à la sortie.
* Pour les motifs `asc` wildcard: spécifier ou `desc` commander des colonnes en utilisant leurs noms dans l’ordre ascendant ou descendant. Si `asc` `desc` l’ordre n’est pas spécifié ou non, l’ordre est déterminé par les colonnes correspondantes telles qu’elles apparaissent dans le tableau source.

**Retourne**

Un tableau qui contient des colonnes dans l’ordre spécifié par les arguments de l’opérateur. `project-reorder`ne renomme pas ou supprimer les colonnes de la table, donc, toutes les colonnes qui existaient dans le tableau source, apparaissent dans le tableau de résultat.

**Remarques**

- Dans l’appariement ambigu *de ColumnNameOrPattern,* la colonne apparaît dans la première position correspondant au modèle.
- Spécifier `project-reorder` les colonnes pour le est facultatif. Les colonnes qui ne sont pas spécifiées apparaissent explicitement comme les dernières colonnes de la table de sortie.

* Utiliser [`project-away`](projectawayoperator.md) pour enlever les colonnes.
* Utilisez [`project-rename`](projectrenameoperator.md) pour renommer les colonnes.


**Exemples**

Réorganiser une table avec trois colonnes (a, b, c) de sorte que la deuxième colonne (b) apparaîtra en premier.

```kusto
print a='a', b='b', c='c'
|  project-reorder b
```

|b|a|c|
|---|---|---|
|b|a|c|

Réorganiser les colonnes d’une table `a` de sorte que les colonnes commençant par apparaîtront devant d’autres colonnes.

```kusto
print b = 'b', a2='a2', a3='a3', a1='a1'
|  project-reorder a* asc
```

|a1|a2|a3|b|
|---|---|---|---|
|a1|a2|a3|b|