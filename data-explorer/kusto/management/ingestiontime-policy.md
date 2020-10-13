---
title: Gestion de la stratégie Kusto IngestionTime-Azure Explorateur de données
description: Familiarisez-vous avec les commandes de stratégie IngestionTime dans Azure Explorateur de données. Découvrez comment accéder aux heures d’ingestion et comment activer et désactiver cette stratégie.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2e1671373547a4ed069d67bc9153248f23bc3b5e
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92003108"
---
# <a name="ingestiontime-policy-command"></a>Commande sur le moment de l’ingestion

La stratégie IngestionTime est une stratégie facultative définie sur les tables (elle est activée par défaut).
Il fournit le temps approximatif d’ingestion des enregistrements dans une table.

La valeur du temps d’ingestion est accessible au moment de la requête à l’aide de la `ingestion_time()` fonction.

```kusto
T | extend ingestionTime = ingestion_time()
```

Pour activer/désactiver la stratégie :
```kusto
.alter table table_name policy ingestiontime true
```

Pour activer/désactiver la stratégie de plusieurs tables :
```kusto
.alter tables (table_name [, ...]) policy ingestiontime true
```

Pour afficher la stratégie :
```kusto
.show table table_name policy ingestiontime  

.show table * policy ingestiontime  
```

Pour supprimer la stratégie (égale à la désactivation) :
```kusto
.delete table table_name policy ingestiontime  
```