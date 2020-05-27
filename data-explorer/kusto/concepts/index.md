---
title: Bien démarrer avec Kusto
description: Cet article montre comment commencer à utiliser Kusto.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0a56878c8b79e651afcd1b8ce18a3220dc304211
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863385"
---
# <a name="getting-started-with-kusto"></a>Bien démarrer avec Kusto

Azure Data Explorer est un service permettant de stocker et d’exécuter une analytique interactive sur le Big Data.

Il est basé sur des systèmes de gestion de base de données relationnelle, prenant en charge des entités comme des bases de données, des tables et des colonnes. Les requêtes analytiques complexes sont effectuées avec le langage de requête Kusto. Certains opérateurs de requête incluent des colonnes calculées, la recherche et le filtrage des lignes, des regroupements par agrégats et des jointures.

Les excellentes performances de requêtes et d’ingestion des données du service sont obtenues en « sacrifiant » la possibilité de mettre à jour localement des lignes seules, ainsi que des contraintes et des transactions intertables. Par conséquent, il complète, plutôt qu’il ne remplace, les systèmes SGBDR traditionnels dans les scénarios tels que l’OLTP et l’entreposage des données.

Les données structurées, semi-structurées (ex : types imbriqués similaires à JSON) et non structurées (texte libre) sont gérées aussi bien les unes que les autres.

## <a name="interacting-with-azure-data-explorer"></a>Interaction avec Azure Data Explorer

Pour les utilisateurs, le principal moyen d’interagir avec Kusto est d’utiliser l’un des nombreux [outils clients](../tools/index.md) disponibles. Même si l’envoi de [requêtes SQL](../api/tds/t-sql.md) est pris en charge, le principal moyen d’interagir est d’utiliser le [langage de requête Kusto](../query/index.md) pour envoyer des requêtes de données, et d’utiliser les [commandes de contrôle](../management/index.md) pour gérer les entités, découvrir les métadonnées, etc. Les requêtes et les commandes de contrôle sont de courts « programmes » textuels.

## <a name="kusto-queries"></a>Requêtes Kusto

Une requête est une demande en lecture seule qui permet de traiter des données et de retourner les résultats de ce traitement, sans modifier les données ou les métadonnées. Les requêtes Kusto peuvent utiliser le [langage SQL](../api/tds/t-sql.md) ou le [langage de requête Kusto](../query/index.md). À titre d’exemple, la requête suivante compte le nombre de lignes de la table `Logs` pour lesquelles la valeur de la colonne `Level` est égale à la chaîne `Critical` :

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

Les commandes de contrôle ont leur propre syntaxe (qui ne fait pas partie de la syntaxe du langage de requête Kusto, bien que les deux partagent un grand nombre de concepts). Les commandes de contrôle se distinguent des requêtes par le fait que le premier caractère du texte d’une commande est un point (`.`) (ce qui n’est pas possible pour une requête).
Cette différence permet d’éviter de nombreux types d’attaques de sécurité, car elle empêche l’incorporation de commandes de contrôle à l’intérieur de requêtes.

Toutes les commandes de contrôle ne modifient pas les données ou les métadonnées. De nombreuses commandes (celles qui commencent par `.show`) sont utilisées pour afficher des métadonnées ou des données. Par exemple, la commande `.show tables` retourne la liste de toutes les tables de la base de données active.
