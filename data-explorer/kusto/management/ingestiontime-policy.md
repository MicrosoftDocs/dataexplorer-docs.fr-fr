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
ms.openlocfilehash: f5ffc7ae648a9254564af0705cda84f3c79da99b
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966939"
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