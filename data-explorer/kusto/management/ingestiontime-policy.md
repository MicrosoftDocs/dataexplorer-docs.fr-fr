---
title: Politique IngestionTime - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique IngestionTime dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c42da570b961595be1fbcae352fe121d8b6f59ea
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520891"
---
# <a name="ingestiontime-policy"></a>Stratégie sur le moment de l’ingestion

La stratégie IngestionTime est une stratégie facultative définie sur les tables (elle est activée par défaut).
il fournit le temps approximatif de l’ingestion des enregistrements dans un tableau.

La valeur du temps d’ingestion `ingestion_time()` peut être consultée au moment de la requête à l’aide de la fonction.

```kusto
T | extend ingestionTime = ingestion_time()
```

Pour activer/désactiver la politique :
```kusto
.alter table table_name policy ingestiontime true
```

Pour activer/désactiver la politique de plusieurs tableaux :
```kusto
.alter tables (table_name [, ...]) policy ingestiontime true
```

Pour voir la politique :
```kusto
.show table table_name policy ingestiontime  

.show table * policy ingestiontime  
```

Supprimer la stratégie (égale à la désactivation) :
```kusto
.delete table table_name policy ingestiontime  
```