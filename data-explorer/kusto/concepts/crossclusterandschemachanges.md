---
title: Questions et changements de schémas à clusters croisés - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les requêtes à clusters croisés et les changements de schémas dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a9473add657fe8a888818f83c72f12d47138c00e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523288"
---
# <a name="cross-cluster-queries-and-schema-changes"></a>Requêtes interclusters et changements du schéma 

Lors de l’exécution de requêtes à sous-faisceau, le cluster qui fait l’interprétation initiale de la requête doit avoir le schéma des entités référencées en grappes éloignées.
Donc, lorsque la requête suivante envoyée à *Cluster1*

```kusto
Table1 | join ( cluster("Cluster2").database("SomeDB").Table2 ) on KeyColumn
``` 

*Cluster1* doit savoir quelles colonnes *Table2* sur *Cluster2* a et quels sont les types de données de ces colonnes. Afin d’accomplir que la commande spéciale est envoyée de *Cluster1* à *Cluster2*.
Cet envoi de cette commande peut être assez coûteux, donc une fois récupéré, les schémas pour les entités distantes sont mis en cache et la requête suivante faisant référence aux mêmes entités devra exécuter moins d’opérations réseau.

Cela peut causer des effets indésirables lorsque le schéma de l’entité distante change (colonnes ajoutées ne sont pas reconnues ou supprimées colonnes causant «erreur de requête partielle» au lieu d’une erreur sémantique).

Afin d’atténuer ce problème mis en cache schémas expirent dans 1 heure après la récupération, de sorte que toute requête exécutée dans plus d’une heure après le changement fonctionnera avec le schéma à jour.