---
title: prendre opérateur - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de prise dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0c8f724e139e13bf9ece00d5af09f2a3cf25b03a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506594"
---
# <a name="take-operator"></a>opérateur take

Revenez au nombre spécifié de lignes.

```kusto
T | take 5
```

Il n’y a aucune garantie quels enregistrements sont retournés, à moins que les données source ne soient triées.

**Syntaxe**

`take`*NumberOfRows* 
 `limit` *NumberOfRows*

(`take` `limit` et sont synonymes.)

**Remarques**

`take`est un moyen simple, rapide et efficace de visualiser un petit échantillon d’enregistrements lors de la navigation des données de manière interactive, mais sachez qu’il ne garantit aucune cohérence dans ses résultats lors de l’exécution plusieurs fois, même si l’ensemble de données n’a pas changé.

Même est le nombre de lignes retournées par la requête n’est pas explicitement limitée par la requête (aucun opérateur n’est `take` utilisé), Kusto limite ce nombre par défaut.
S’il vous plaît voir [Kusto limites de requête](../concepts/querylimits.md) pour plus de détails.

Voir : [trier l’opérateur](sortoperator.md)
[supérieur opérateur](topoperator.md)
[opérateur opérateur top-nested opérateur](topnestedoperator.md)

## <a name="does-kusto-support-paging-of-query-results"></a>Kusto soutient-il le pagination des résultats de requête ?

Kusto ne fournit pas de mécanisme de pagination intégré.

Kusto est un service complexe qui optimise en permanence les données qu’il stocke pour fournir d’excellentes performances de requête sur d’énormes ensembles de données. Bien que le pagination soit un mécanisme utile pour les clients apatrides avec des ressources limitées, il déplace le fardeau vers le service de backend qui doit suivre l’information de l’état client. Par la suite, la performance et l’évolutivité du service de backend sont sévèrement limitées.

Pour le support de la recherche de nourriture implémenter l’une des fonctionnalités suivantes :

* Exporter le résultat d’une requête vers un stockage externe et le paging à travers les données générées.

* Rédaction d’une application de niveau intermédiaire qui fournit une API de paging imposante en cachant les résultats d’une requête Kusto.