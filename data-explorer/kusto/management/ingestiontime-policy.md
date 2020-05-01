---
title: Gestion de la stratégie Kusto IngestionTime-Azure Explorateur de données
description: Cet article décrit la stratégie IngestionTime dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 907c6ddf84d772f800fce45d3c1245bbd11b0c85
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616454"
---
# <a name="ingestiontime-policy"></a>Stratégie sur le moment de l’ingestion

La stratégie IngestionTime est une stratégie facultative définie sur les tables (elle est activée par défaut).
Il fournit le temps approximatif d’ingestion des enregistrements dans une table.

La valeur du temps d’ingestion est accessible au moment `ingestion_time()` de la requête à l’aide de la fonction.

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