---
title: évaluer l’opérateur de plug-in-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit évaluer l’opérateur de plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: d01b3b5178801fe1b5e55f51987564674ce4aeae
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128629"
---
# <a name="evaluate-operator-plugins"></a>évaluer les plug-ins d’opérateur

Appelle une extension de requête côté service (plug-in).

L' `evaluate` opérateur est un opérateur tabulaire qui donne la possibilité d’appeler des extensions de langage de requête appelées **plug-ins**. Les plug-ins peuvent être activés ou désactivés (contrairement à d’autres constructions de langage, qui sont toujours disponibles) et ne sont pas « liés » par la nature relationnelle de la langue (par exemple, ils peuvent ne pas avoir un schéma de sortie prédéfini, déterminé statiquement).

**Syntaxe** 

[*T* `|` ] `evaluate` [ *evaluateParameters* ] *PluginName* `(` [*PluginArg1* [ `,` *PluginArg2*]...`)`

Où :

* *T* est une entrée tabulaire facultative dans le plug-in. (Certains plug-ins n’acceptent aucune entrée et agissent comme une source de données tabulaires.)
* *PluginName* est le nom obligatoire du plug-in appelé.
* *PluginArg1*,... arguments facultatifs du plug-in.
* *evaluateParameters*: zéro ou plusieurs paramètres (séparés par des espaces) sous la forme d’une valeur de *nom* `=` *Value* qui contrôlent le comportement de l’opération d’évaluation et du plan d’exécution. Chaque plug-in peut décider différemment comment gérer chaque paramètre. Reportez-vous à la documentation de chaque plug-in pour obtenir un comportement spécifique.  

Les paramètres suivants sont pris en charge : 

  |Nom                |Valeurs                           |Description                                |
  |--------------------|---------------------------------|-------------------------------------------|
  |`hint.distribution` |`single`, `per_node`, `per_shard`| [Indicateurs de distribution](#distributionhints) |
  |`hint.pass_filters` |`true`, `false`| Autorisez `evaluate` l’opérateur à transférer tous les filtres correspondants avant le plug-in. Le filtre est considéré comme « mis en correspondance » s’il fait référence à une colonne existante avant l' `evaluate` opérateur. Valeur par défaut : `false` |
  |`hint.pass_filters_column` |*column_name*| Autorisez l’opérateur de plug-in à transférer des filtres faisant référence à *column_name* avant le plug-in. Le paramètre peut être utilisé plusieurs fois avec des noms de colonnes différents. |

**Notes**

* Syntaxiquement, `evaluate` se comporte de la même façon que l' [opérateur d’appel](./invokeoperator.md), qui appelle des fonctions tabulaires.
* Les plug-ins fournis par le biais de l’opérateur Evaluate ne sont pas liés par les règles régulières d’exécution de la requête ou d’évaluation des arguments.
* Des plug-ins spécifiques peuvent avoir des restrictions spécifiques. Par exemple, les plug-ins dont le schéma de sortie dépend des données (par exemple, le plug-in [bag_unpack](./bag-unpackplugin.md) et le [plug-in pivot](./pivotplugin.md)) ne peuvent pas être utilisés lors de requêtes entre clusters.

<a id="distributionhints"/>**Indicateurs de distribution**</a>

Les indicateurs de distribution spécifient la façon dont l’exécution du plug-in sera répartie entre plusieurs nœuds de cluster. Chaque plug-in peut implémenter une prise en charge différente pour la distribution. La documentation du plug-in spécifie les options de distribution prises en charge par le plug-in.

Valeurs possibles :

* `single`: Une seule instance du plug-in s’exécutera sur l’ensemble des données de la requête.
* `per_node`: Si la requête avant l’appel de plug-in est distribuée entre les nœuds, une instance du plug-in s’exécutera sur chaque nœud sur les données qu’elle contient.
* `per_shard`: Si les données avant l’appel du plug-in sont distribuées entre les partitions, une instance du plug-in est exécutée sur chaque partition des données.
