---
title: Déclarations de requête - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les déclarations de requête dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 20eeb1aa755fcf4e3068cba061a2738a375e1847
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765563"
---
# <a name="query-statements"></a>Instructions de requête

::: zone pivot="azuredataexplorer"

Une requête se compose d’une ou plusieurs **déclarations de**requête`;`, délimitées par un point-virgule ().
Au moins une de ces déclarations de requête doit être une [déclaration d’expression tabulaire](./tabularexpressionstatements.md).
L’énoncé d’expression tabulaire génère un ou plusieurs résultats tabulaires.
Lorsque la requête a plus d’une déclaration d’expression tabulaire, la requête a un [lot](./batches.md) de déclarations d’expression tabulaire, et les résultats tabulaires générés par ces déclarations sont tous retournés par la requête.

Deux types d’énoncés de requête :

* Déclarations qui sont principalement utilisées par les utilisateurs[(déclarations de requête des utilisateurs),](#user-query-statements)
* Déclarations qui ont été conçues pour prendre en charge les scénarios dans lesquels les applications de niveau intermédiaire prennent des requêtes utilisateur et envoient une version modifiée de ces questions à Kusto[(instructions de requête d’application](#application-query-statements)).

Certaines déclarations de requêtes sont utiles dans les deux scénarios.

> [!NOTE]
> L'"effet" d’une déclaration de requête commence au point où la déclaration apparaît dans la requête et se termine à la fin de la requête. Une fois la requête terminée, toutes ses ressources sont libérées et elle n’a aucun impact sur les requêtes futures (autres que les effets secondaires, comme avoir la requête enregistrée dans un journal de toutes les requêtes exécutées, ou avoir ses résultats mis en cache.)

## <a name="user-query-statements"></a>Déclarations de requête des utilisateurs

Voici une liste des relevés de requête des utilisateurs :

* Une [déclaration de laisser](./letstatement.md) définit une liaison entre un nom et une expression.
  Laissez les déclarations peuvent être utilisées pour briser une longue requête en petites pièces nommées qui sont plus faciles à comprendre.

* Une [déclaration définie](./setstatement.md) définit une option de requête qui influe sur la façon dont la requête est traitée et ses résultats retournés.

* Une [déclaration d’expression tabulaire](./tabularexpressionstatements.md), l’énoncé de requête le plus important, renvoie les données « intéressantes » comme résultats.

## <a name="application-query-statements"></a>Instructions de requête d’application

Voici une liste d’instructions de requête d’application :

* Une [déclaration d’alias](./aliasstatement.md) définit un alias à une autre base de données (dans le même cluster ou sur un cluster distant).

* Une [déclaration de modèle](./patternstatement.md), qui peut être utilisée par des applications qui sont construites au-dessus de Kusto et exposer le langage de requête à leurs utilisateurs pour s’injecter dans le processus de résolution de nom de requête.

* Une déclaration de [paramètres de requête](./queryparametersstatement.md), qui est utilisé par les applications qui sont construites au-dessus de Kusto pour se protéger contre les attaques d’injection (semblable à la façon dont les paramètres de commande protègent SQL contre les attaques d’injection SQL.)

* Une [déclaration restrictive](./restrictstatement.md), qui est utilisée par les applications qui sont construites au-dessus de Kusto pour limiter les requêtes à un sous-ensemble spécifique de données dans Kusto (y compris la restriction de l’accès à des colonnes et des enregistrements spécifiques.)

::: zone-end

::: zone pivot="azuremonitor"

Ce n’est pas pris en charge dans Azure Monitor

::: zone-end
