---
title: Questions - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les requêtes dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 2ac338600320d3f83a92e22e405f630ee308df2f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510776"
---
# <a name="queries"></a>Requêtes

Une requête est une opération de lecture uniquement contre les données ingérées d’un cluster de moteur Kusto. Les requêtes s’exécutent toujours dans le cadre d’une base de données particulière dans le cluster (bien qu’elles puissent également se référer à des données dans une autre base de données, ou même dans un autre cluster).

Comme la requête ad hoc des données est le scénario prioritaire pour Kusto, la syntaxe Kusto Query Language est optimisée pour les utilisateurs non experts qui œuvrent et exécutent des requêtes sur leurs données et étant en mesure de comprendre sans ambiguïté ce que chaque requête fait (logiquement).

La syntaxe linguistique est celle d’un flux de données, où « données » signifie vraiment « données tabulaires » (données dans une ou plusieurs lignes/colonnes forme rectangulaire). À tout le moins, une requête se compose de références de données sources (références aux tables Kusto) et d’un ou de plusieurs **opérateurs de requête appliqués** dans l’ordre, indiqués visuellement par l’utilisation d’un caractère de tuyau ()`|`aux opérateurs de délimit.

Par exemple :

```kusto
StormEvents 
| where State == 'FLORIDA' and StartTime > datetime(2000-01-01)
| count
```
    
Chaque filtre précédé de la barre verticale `|` est une instance d’un *opérateur*, assortie de certains paramètres. L’entrée de l’opérateur est la table résultant du pipeline précédent. Dans la plupart des cas, tous les paramètres sont des [expressions scalaires](./scalar-data-types/index.md) sur les colonnes de l’entrée.
Dans certains cas, les paramètres correspondent aux noms des colonnes d’entrée et, parfois, le paramètre est une seconde table. Le résultat d’une requête est toujours une table, même si elle ne contient qu’une colonne et qu’une ligne.

## <a name="reference-query-operators"></a>Référence: Opérateurs de requête

> `T` est utilisé dans les exemples de requête ci-dessous pour désigner la table source ou le pipeline précédent.