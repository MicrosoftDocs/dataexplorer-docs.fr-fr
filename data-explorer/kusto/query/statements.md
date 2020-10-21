---
title: Instructions de requête-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les instructions de requête dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: e546f71c3eb4eda09ff7061dd1a3be9eb6ad6c88
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252874"
---
# <a name="query-statement-types"></a>Types d’instruction de requête

::: zone pivot="azuredataexplorer"

Une requête se compose d’une ou plusieurs **instructions de requête**, séparées par un point-virgule ( `;` ).
Au moins une de ces instructions de requête doit être une [instruction d’expression tabulaire](./tabularexpressionstatements.md).
L’instruction d’expression tabulaire génère un ou plusieurs résultats tabulaires.
Lorsque la requête contient plusieurs instructions d’expression tabulaire, la requête contient un [lot](./batches.md) d’instructions d’expression tabulaire, et les résultats tabulaires générés par ces instructions sont tous retournés par la requête.

Deux types d’instructions de requête :

* Les instructions qui sont principalement utilisées par les utilisateurs ([instructions de requête](#user-query-statements)de l’utilisateur),
* Les instructions qui ont été conçues pour prendre en charge les scénarios dans lesquels les applications de niveau intermédiaire prennent des requêtes utilisateur et envoient une version modifiée de celles-ci à Kusto ([instructions de requête d’application](#application-query-statements)).

Certaines instructions de requête sont utiles dans les deux scénarios.

> [!NOTE]
> L’effet d’une instruction de requête commence au point où l’instruction apparaît dans la requête et se termine à la fin de la requête. Une fois la requête terminée, toutes ses ressources sont libérées et n’ont aucun impact sur les requêtes ultérieures (autres que les effets secondaires, par exemple si la requête est enregistrée dans un journal de toutes les requêtes exécutées ou si ses résultats sont mis en cache).

## <a name="user-query-statements"></a>Instructions de requête de l’utilisateur

Voici la liste des instructions de requête utilisateur :

* Une [instruction Let](./letstatement.md) définit une liaison entre un nom et une expression.
  Les instructions Let peuvent être utilisées pour fractionner une longue requête en petites parties nommées plus faciles à comprendre.

* Une [instruction Set](./setstatement.md) définit une option de requête qui affecte le mode de traitement de la requête et les résultats retournés.

* Une [instruction d’expression tabulaire](./tabularexpressionstatements.md), l’instruction de requête la plus importante, retourne les données « intéressantes » en tant que résultats.

## <a name="application-query-statements"></a>Instructions de requête d’application

Voici une liste des instructions de requête d’application :

* Une [instruction d’alias](./aliasstatement.md) définit un alias pour une autre base de données (dans le même cluster ou sur un cluster distant).

* Une [instruction de modèle](./patternstatement.md), qui peut être utilisée par les applications basées sur Kusto et exposer le langage de requête à leurs utilisateurs pour s’injecter dans le processus de résolution de nom de requête.

* Une [instruction de paramètres de requête](./queryparametersstatement.md), qui est utilisée par les applications basées sur Kusto pour se protéger contre les attaques par injection (de la même façon que les paramètres de commande protègent SQL contre les attaques par injection de code SQL).

* [Instruction Restrict](./restrictstatement.md), qui est utilisée par les applications basées sur Kusto pour limiter les requêtes à un sous-ensemble spécifique de données dans Kusto (y compris la restriction de l’accès à des colonnes et des enregistrements spécifiques).

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
