---
title: Défaillances partielles de requêtes - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les défaillances partielles de requête dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d65256c0ac50a80e3e3b095e8d2348dbae5e585b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523118"
---
# <a name="partial-query-failures"></a>Échecs des requêtes partielles

Une *défaillance partielle de la requête* est un défaut d’exécution de la requête qui est détectée seulement après que la requête a commencé la phase réelle d’exécution. À ce moment-là, Kusto a `200 OK` déjà retourné la ligne de statut HTTP au client, et ne peut pas «le reprendre», de sorte qu’il doit indiquer l’échec dans le flux de résultat qui porte les résultats de requête au client. (En fait, il peut avoir déjà retourné certaines données de résultat à l’appelant.)

Il existe plusieurs types d’échecs partiels de requête :
* [Questions en fuite](runawayqueries.md): Des requêtes qui prennent trop de ressources.
* [Découpage de résultat](resulttruncation.md): Requêtes dont l’ensemble de résultats a été tronqué car il a dépassé certaines limites.
* [Débordements](overflow.md): Requêtes qui déclenchent une erreur de débordement.

Les défaillances partielles des requêtes peuvent être signalées au client de l’une des deux façons :

* Dans le cadre des données de résultat (un type spécial d’enregistrement indique que ce n’est pas les données elles-mêmes). C’est la façon par défaut.
* Dans le cadre de la table "QueryStatus" dans le flux de résultats. Ceci est fait `deferpartialqueryfailures` en utilisant l’option dans la fente de `properties` la demande (`Kusto.Data.Common.ClientRequestProperties.OptionDeferPartialQueryFailures`).
  Les clients qui le font assument la responsabilité de consommer `QueryStatus` l’ensemble du flux de résultat `Severity` `2` du service, de localiser le résultat et de s’assurer qu’aucun enregistrement dans ce résultat n’a un ou moins petit. 