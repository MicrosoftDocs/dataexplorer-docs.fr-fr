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
ms.openlocfilehash: 9ed8ba6ac5e66326e6aa1d9e7a7f474506b57e26
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84328974"
---
# <a name="cross-cluster-queries-and-schema-changes"></a>Requêtes interclusters et changements du schéma

Lors de l’exécution d’une requête entre clusters, le cluster qui exécute l’interprétation de la requête initiale doit avoir le schéma des entités référencées dans les clusters distants.
Lorsque la requête suivante est envoyée à *CLUSTER1*

```kusto
Table1 | join ( cluster("Cluster2").database("SomeDB").Table2 ) on KeyColumn
``` 

*CLUSTER1* doit connaître les colonnes de *table2* sur *Cluster2* et les types de données de ces colonnes. Pour récupérer ces informations, la commande spéciale est envoyée de *CLUSTER1* à *Cluster2*.
L’envoi de la commande peut s’avérer onéreux. Une fois récupérés, les schémas des entités distantes sont mis en cache, et la requête suivante qui référence les mêmes entités s’exécute moins d’opérations réseau.

Les modifications apportées au schéma de l’entité distante peuvent entraîner des effets indésirables. Par exemple, les colonnes ajoutées ne sont pas reconnues, ou les colonnes supprimées provoquent une erreur de requête partielle au lieu d’une erreur sémantique.

Pour résoudre ce problème, les schémas mis en cache expirent une heure après la récupération, de sorte que toute requête exécutée plus d’une heure après la modification, fonctionne avec le schéma à jour.
