---
title: évaluer l’opérateur de plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit évaluer l’opérateur de plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 1aae36df29abf705ba821abdc2d1da96e4635a60
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515740"
---
# <a name="evaluate-plugin-operator"></a>evaluate plugin, opérateur

Invoque une extension de requête côté service (plugin).

L’opérateur `evaluate` est un opérateur tabulaire qui offre la possibilité d’invoquer des extensions de langage de requête connues sous le nom **de plugins**. Les plugins peuvent être activés ou désactivés (contrairement à d’autres constructions linguistiques qui sont toujours disponibles) et ne sont pas « liés » par la nature relationnelle de la langue (par exemple, ils peuvent ne pas avoir un schéma de sortie prédéfini, statiquement déterminé).

**Syntaxe** 

[*T* `|`T `evaluate` ] [ *assessParameters* ] *PluginName* `(` `,` [*PluginArg1* [ *PluginArg2*]...`)`

Où :

* *T* est une entrée tabulaire facultative au plugin. (Certains plugins ne prennent aucune entrée et agissent comme une source de données tabulaire.)
* *PluginName* est le nom obligatoire du plugin invoqué.
* *PluginArg1*, ... sont les arguments facultatifs pour le plugin.
* *évaluerParameters*: Paramètres nuls ou plus (séparés dans l’espace) sous la forme de *valeur nomine* `=` *Value* qui contrôlent le comportement du plan d’évaluation et d’exécution. Les paramètres suivants sont pris en charge : 

  |Nom                |Valeurs                           |Description                                |
  |--------------------|---------------------------------|-------------------------------------------|
  |`hint.distribution` |`single`, `per_node`, `per_shard`| [Conseils de distribution](#distribution-hints) |

**Remarques**

* Syntactiquement, `evaluate` se comporte de la même manière que [l’opérateur invoque](./invokeoperator.md), qui invoque des fonctions tabulaires.
* Les plugins fournis par l’opérateur d’évaluation ne sont pas liés par les règles régulières d’exécution des requêtes ou d’évaluation des arguments.
Les plugins spécifiques peuvent avoir des restrictions spécifiques. Par exemple, les plugins dont le schéma de sortie dépend des données (par exemple, [bag_unpack plugin)](./bag-unpackplugin.md)ne peuvent pas être utilisés lors de l’exécution des requêtes à grappes croisées.

## <a name="distribution-hints"></a>Conseils de distribution

Les conseils de distribution spécifient comment l’exécution du plugin sera répartie sur plusieurs nœuds de cluster. Chaque plugin peut implémenter un support différent pour la distribution. La documentation du plugin spécifie les options de distribution prises en charge par le plugin.

Valeurs possibles :

* `single`: Une seule instance du plugin s’exécutera sur l’ensemble des données de requête.
* `per_node`: Si la requête avant l’appel plugin est distribuée entre les nœuds, alors une instance du plugin s’exécutera sur chaque nœud sur les données qu’il contient.
* `per_shard`: Si les données avant l’appel plugin sont distribuées sur des éclats, alors une instance du plugin s’exécutera sur chaque fragment de données.