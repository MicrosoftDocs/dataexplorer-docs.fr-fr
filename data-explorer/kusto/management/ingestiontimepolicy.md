---
title: Stratégie IngestionTime-Azure Explorateur de données
description: Cet article décrit la stratégie IngestionTime dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 50e0083b1cdbed06106507fe69fb0d039c923c43
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257805"
---
# <a name="ingestiontime-policy"></a>Stratégie sur le moment de l’ingestion

La stratégie IngestionTime est une stratégie facultative qui peut être définie (activée) sur les tables.

Quand cette option est activée, Kusto ajoute une colonne masquée `datetime` à la table, appelée `$IngestionTime` . Désormais, chaque fois que de nouvelles données sont ingérées, l’heure d’ingestion est enregistrée dans la colonne masquée. Ce temps est mesuré par le cluster Kusto juste avant que les données ne soient validées. 

> [!NOTE]
> Chaque enregistrement a sa propre `$IngestionTime` valeur.

Étant donné que la colonne heure d’ingestion est masquée, vous ne pouvez pas interroger directement sa valeur.
Au lieu de cela, une fonction spéciale appelée [ingestion_time ()](../query/ingestiontimefunction.md) récupère cette valeur. S’il n’existe aucune `datetime` colonne dans la table ou si la stratégie IngestionTime n’a pas été activée lorsqu’un enregistrement a été ingéré, une valeur null est retournée.

La stratégie IngestionTime est conçue pour deux scénarios principaux :
* Pour permettre aux utilisateurs d’estimer la latence dans la réception des données.
  De nombreuses tables avec des données de journal ont une colonne timestamp. La valeur timestamp est renseignée par la source et indique l’heure à laquelle l’enregistrement a été généré. En comparant la valeur de cette colonne à la colonne heure d’ingestion, vous pouvez estimer la latence pour l’obtention des données dans. 
  
  > [!NOTE]
  > La valeur calculée n’est qu’une estimation, car la source et la Kusto n’ont pas nécessairement leurs horloges synchronisées.
  
* Pour prendre en charge les [curseurs de base de données](../management/databasecursor.md) qui permettent aux utilisateurs d’émettre des requêtes consécutives, la requête est limitée aux données qui ont été ingérées depuis la requête précédente.

Pour plus d’informations, consultez consultez les [commandes de contrôle pour la gestion de la stratégie IngestionTime](../management/ingestiontime-policy.md).
