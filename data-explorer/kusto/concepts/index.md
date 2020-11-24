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
ms.localizationpriority: high
ms.openlocfilehash: df4303a56451a3b2723625005a3f38996dc7cf26
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512262"
---
# <a name="getting-started-with-kusto"></a>Bien démarrer avec Kusto

Azure Data Explorer est un service permettant de stocker et d’exécuter une analytique interactive sur le Big Data.

Il est basé sur des systèmes de gestion de base de données relationnelle, et prend en charge des entités comme des bases de données, des tables et des colonnes. Les requêtes analytiques complexes sont effectuées avec le langage de requête Kusto. Voici quelques-uns des opérateurs de requête :
* colonnes calculées
* recherche et filtrage sur les lignes
* agrégats de regroupement
* fonctions de jointure

Le service offre d’excellentes performances pour l’ingestion des données et pour les requêtes : 
* Il sacrifie la possibilité d’effectuer des mises à jour sur place de lignes individuelles, et celle de définir des contraintes ou d’effectuer des transactions intertables. 
* Au lieu de les remplacer, le service vient donc plutôt en complément des systèmes SGBDR traditionnels dans les scénarios comme l’OLTP et l’entreposage de données.
* Les données structurées, semi-structurées (par exemple des types imbriqués similaires à JSON) et non structurées (texte libre) sont toutes prises en charge.

## <a name="interacting-with-azure-data-explorer"></a>Interaction avec Azure Data Explorer

Le principal moyen pour les utilisateurs d’interagir avec Azure Data Explorer (Kusto) :
* Utilisez l’un des [outils de requête](../../tools-integrations-overview.md#azure-data-explorer-query-tools). 
* [Requêtes SQL](../api/tds/t-sql.md).
*  Le [langage de requête Kusto](../query/index.md) (KQL, Kusto Query Language) est le principal moyen d’interaction. KQL vous permet d’envoyer des requêtes de données et d’utiliser des [commandes de contrôle](../management/index.md) pour gérer les entités, découvrir les métadonnées, etc.
Les requêtes et les commandes de contrôle sont de petits « programmes » textuels.

## <a name="kusto-queries"></a>Requêtes Kusto

Une requête est une demande en lecture seule qui permet de traiter des données et de retourner les résultats de ce traitement, sans modifier les données ou les métadonnées. Les requêtes Kusto peuvent utiliser le [langage SQL](../api/tds/t-sql.md) ou le [langage de requête Kusto](../query/index.md). À titre d’exemple, la requête suivante compte le nombre de lignes de la table `Logs` pour lesquelles la valeur de la colonne `Level` est égale à la chaîne `Critical` :

```kusto
Logs
| where Level == "Critical"
| count
```

> [!NOTE]
> Les requêtes ne peuvent pas commencer par un point (`.`) ou par le symbole dièse (`#`).

## <a name="control-commands"></a>Commandes de contrôle

Les commandes de contrôle sont des requêtes qui sont envoyées à Kusto pour traiter et éventuellement modifier des données ou des métadonnées. Par exemple, la commande de contrôle suivante crée une table Kusto avec deux colonnes, `Level` et `Text` :

```kusto
.create table Logs (Level:string, Text:string)
```

Les commandes de contrôle ont leur propre syntaxe (qui ne fait pas partie de la syntaxe du langage de requête Kusto, même si tous deux partagent un grand nombre de concepts). Les commandes de contrôle se distinguent des requêtes par le fait que le premier caractère du texte d’une commande est un point (`.`) (ce qui n’est pas possible pour une requête).
Cette différence permet d’éviter de nombreux types d’attaques de sécurité, car elle empêche l’incorporation de commandes de contrôle à l’intérieur des requêtes.

Toutes les commandes de contrôle ne modifient pas les données ou les métadonnées. Les nombreuses commandes commençant par `.show` sont utilisées pour afficher des métadonnées ou des données. Par exemple, la commande `.show tables` retourne la liste de toutes les tables de la base de données active.
