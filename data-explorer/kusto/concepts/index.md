---
title: Bien démarrer avec Kusto - Azure Data Explorer | Microsoft Docs
description: Cet article montre comment commencer à utiliser Kusto dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5b66dd59dd17f1671c68e63e35ee62f56e6ec8fe
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610365"
---
# <a name="getting-started-with-kusto"></a>Bien démarrer avec Kusto

Kusto est un service permettant de stocker des Big Data et d’exécuter des tâches d’analytique interactives sur ces données.

Il est basé sur des systèmes de gestion de base de données relationnelle et prend en charge des entités, telles que des bases de données, des tables et des colonnes. De plus, il fournit des opérateurs de requête d’analytique complexes (colonnes calculées, recherche et filtrage de lignes, regroupement par agrégats, jointures).

Les excellentes performances de requêtes et d’ingestion des données de Kusto sont obtenues en « sacrifiant » la possibilité de mettre à jour localement des lignes seules, ainsi que des contraintes et des transactions intertables. Par conséquent, il complète, plutôt qu’il ne remplace, les systèmes SGBDR traditionnels dans les scénarios tels que l’OLTP et l’entreposage des données.

En tant que service Big Data, Kusto gère aussi bien les données structurées, que les données semi-structurées (ex : types imbriqués similaires à JSON) ou non structurées (texte libre).

## <a name="interacting-with-kusto"></a>Interaction avec Kusto

Pour les utilisateurs, le principal moyen d’interagir avec Kusto est d’utiliser l’un des nombreux [outils clients](../tools/index.md) qui sont disponibles pour Kusto. Même si l’envoi de [requêtes SQL](../api/tds/t-sql.md) à Kusto est pris en charge, le principal moyen d’interagir avec Kusto est d’utiliser le [langage de requête Kusto](../query/index.md) pour envoyer des requêtes de données, et d’utiliser les [commandes de contrôle](../management/index.md) pour gérer les entités Kusto, découvrir les métadonnées, etc. Les requêtes et les commandes de contrôle sont de courts « programmes » textuels.

## <a name="queries"></a>Requêtes

Une requête Kusto est une requête en lecture seule qui permet de traiter des données Kusto et de retourner les résultats de ce traitement, sans modifier les données ou les métadonnées Kusto. Les requêtes Kusto peuvent utiliser le [langage SQL](../api/tds/t-sql.md) ou le [langage de requête Kusto](../query/index.md).
À titre d’exemple, la requête suivante compte le nombre de lignes de la table `Logs` pour lesquelles la valeur de la colonne `Level` est égale à la chaîne `Critical` :

```kusto
Logs
| where Level == "Critical"
| count
```

Les requêtes ne peuvent pas commencer par un point (`.`) ou par le symbole dièse (`#`).

## <a name="control-commands"></a>Commandes de contrôle

Les commandes de contrôle sont des requêtes qui sont envoyées à Kusto pour traiter et éventuellement modifier des données ou des métadonnées. Par exemple, la commande de contrôle suivante crée une table Kusto avec deux colonnes, `Level` et `Text` :

```kusto
.create table Logs (Level:string, Text:string)
```

Les commandes de contrôle ont leur propre syntaxe, qui ne fait pas partie de la syntaxe du langage de requête Kusto, bien que les deux partagent un grand nombre de concepts. Les commandes de contrôle se distinguent des requêtes par le fait que le premier caractère du texte d’une commande est un point (`.`) (ce qui n’est pas possible pour une requête).
Cette différence permet d’éviter de nombreux types d’attaques de sécurité, car elle empêche l’incorporation de commandes de contrôle à l’intérieur de requêtes.

Toutes les commandes de contrôle ne modifient pas les données ou les métadonnées Kusto. De nombreuses commandes (celles qui commencent par `.show`) sont utilisées pour afficher des métadonnées ou des données relatives à Kusto. Par exemple, la commande `.show tables` retourne la liste de toutes les tables de la base de données active.
