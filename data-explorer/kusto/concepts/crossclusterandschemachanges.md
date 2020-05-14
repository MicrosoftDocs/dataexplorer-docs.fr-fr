---
title: Kusto requêtes entre clusters & modifications de schéma-Azure Explorateur de données
description: Cet article décrit les requêtes et les modifications de schéma entre clusters dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 58fd8cef3836d17eb327734338b1567d815d7349
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382027"
---
# <a name="cross-cluster-queries-and-schema-changes"></a>Requêtes interclusters et changements du schéma 

Lors de l’exécution d’une requête entre clusters, le cluster qui exécute l’interprétation de la requête initiale doit avoir le schéma des entités référencées dans le ou les clusters distants.
Par conséquent, lorsque la requête suivante est envoyée à *CLUSTER1*

```kusto
Table1 | join ( cluster("Cluster2").database("SomeDB").Table2 ) on KeyColumn
``` 

*CLUSTER1* doit savoir quelles colonnes *table2* sur *Cluster2* possède et quels sont les types de données de ces colonnes. Dans l’ordre, la commande spéciale est envoyée de *CLUSTER1* à *Cluster2*.
L’envoi de cette commande peut être très onéreux. par conséquent, une fois récupéré, les schémas des entités distantes sont mis en cache et la requête suivante qui référence les mêmes entités doit exécuter moins d’opérations réseau.

Cela peut entraîner des effets indésirables lorsque le schéma de l’entité distante change (ajout de colonnes non reconnues ou supprimées, provoquant une erreur de requête partielle au lieu d’une erreur sémantique).

Pour atténuer ce problème, les schémas mis en cache expirent dans 1 heure après la récupération. par conséquent, toute requête exécutée dans plus de 1 heure après la modification fonctionne avec le schéma à jour.