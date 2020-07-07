---
title: Vue d’ensemble de la gestion (commandes de contrôle) - Azure Data Explorer | Microsoft Docs
description: Cet article décrit la gestion (commandes de contrôle) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b42cf002382cf4f7b79f7f734b6c7137f42fcbc8
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967024"
---
# <a name="management-control-commands-overview"></a>Vue d’ensemble de la gestion (commandes de contrôle)

Cette section décrit les commandes de contrôle utilisées pour gérer Kusto.
Les commandes de contrôle sont des demandes adressées au service pour récupérer des informations qui ne sont pas nécessairement des données incluses dans des tables de base de données ou pour modifier l’état du service, etc.

## <a name="differentiating-control-commands-from-queries"></a>Différenciation des commandes de contrôle et des requêtes

Kusto utilise trois mécanismes pour différencier les requêtes et les commandes de contrôle : au niveau du langage, au niveau du protocole et au niveau de l’API. Cette différenciation est effectuée à des fins de sécurité.

Au niveau du langage, le premier caractère du texte d’une demande détermine si celle-ci est une commande de contrôle ou une requête. Les commandes de contrôle doivent commencer par le caractère point (`.`) alors qu’aucune requête ne peut commencer par ce caractère.

Au niveau du protocole, des points de terminaison HTTP/HTTPs différents sont utilisés pour les commandes de contrôle par rapport aux requêtes.

Au niveau de l’API, différentes fonctions sont utilisées pour envoyer les commandes de contrôle par rapport aux requêtes.

## <a name="combining-queries-and-control-commands"></a>Combinaison de requêtes et de commandes de contrôle

Les commandes de contrôle peuvent faire référence à des requêtes (mais pas à l’inverse) ou à d’autres commandes de contrôle.
Il existe plusieurs scénarios pris en charge :

1. **AdminThenQuery** : Une commande de contrôle est exécutée et son résultat (représenté sous la forme d’une table de données temporaire) sert d’entrée à une requête.
2. **AdminFromQuery** : Une requête ou une commande d’administration `.show` est exécutée et son résultat (représenté sous la forme d’une table de données temporaire) sert d’entrée à une commande de contrôle.

Notez que dans tous les cas, la combinaison complète forme techniquement une commande de contrôle, et non une requête, donc le texte de la demande doit commencer par un point (`.`), et la demande doit être envoyée au point de terminaison de gestion du service.

Notez également que des [instructions de requête](../query/statements.md) apparaissent dans la partie requête du texte (elles ne peuvent pas précéder la commande elle-même).

>[!NOTE]
> N’exécutez pas les opérations [command-then-query] trop fréquemment.
> *command-then-query* envoie le jeu de résultats de la commande de contrôle et applique des filtres/agrégations à celui-ci.
>  * Par exemple : `.show ... | where ... | summarize ...`
>   * Lors de l’exécution de quelque chose comme : `.show cluster extents | count` (emphase sur `| count`), Kusto prépare d’abord une table de données qui contient tous les détails de toutes les étendues du cluster. Le système envoie ensuite cette table uniquement en mémoire au moteur Kusto pour effectuer le compte. Le système fonctionne en réalité dans un chemin non optimisé pour vous donner une réponse très simple.


**AdminThenQuery** est indiqué de l’une des deux manières suivantes :

1. En utilisant une barre verticale (`|`), la requête traite alors les résultats de la commande de contrôle comme s’il s’agissait d’un autre opérateur de requête produisant des données.
2. En utilisant un point-virgule (`;`), qui introduit alors les résultats de la commande de contrôle dans un symbole spécial appelé `$command_results`, il est ensuite possible d’utiliser ce symbole dans la requête autant de fois que nécessaire.

Par exemple :

```kusto
// 1. Using pipe: Count how many tables are in the database-in-scope:
.show tables
| count

// 2. Using semicolon: Count how many tables are in the database-in-scope:
.show tables;
$command_results
| count

// 3. Using semicolon, and including a let statement:
.show tables;
let useless=(n:string){strcat(n,'-','useless')};
$command_results | extend LastColumn=useless(TableName)
```

**AdminFromQuery** est indiqué par la combinaison de caractères `<|`. Par exemple, dans le code suivant, nous exécutons d’abord une requête qui produit une table contenant une seule colonne (nommée `str` de type `string`) et une seule ligne, puis nous l’écrivons en tant que nom de table `MyTable` dans la base de données en contexte :

```kusto
.set MyTable <|
let text="Hello, World!";
print str=Text
```


