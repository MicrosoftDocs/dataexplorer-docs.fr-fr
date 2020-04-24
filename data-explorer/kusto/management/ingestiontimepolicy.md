---
title: Politique IngestionTime - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique IngestionTime dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 0c1755115a9e9f4c60c7f5574eb9b784520ecb88
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520823"
---
# <a name="ingestiontime-policy"></a>Stratégie sur le moment de l’ingestion

La politique IngestionTime est une stratégie facultative qui peut être définie (activée) sur les tables.

Lorsque cette politique est activée sur une `datetime` table, Kusto `$IngestionTime`ajoute une colonne cachée à la table, appelée . À partir de ce moment, chaque fois que de nouvelles données sont ingérées dans le tableau, le temps d’ingestion (mesuré par le cluster Kusto juste avant l’engagement des données) est enregistré dans la colonne cachée pour tous les enregistrements ingérés. (Notez que chaque `$IngestionTime` enregistrement a sa propre valeur, comme n’importe quelle autre colonne.)

Comme la colonne de temps d’ingestion est cachée, on ne peut pas interroger directement sa valeur.
Au lieu de cela, une fonction spéciale appelée [ingestion_time))](../query/ingestiontimefunction.md) est fournie pour récupérer cette valeur. S’il n’y a pas de telle colonne dans le tableau, ou si la politique IngestionTime n’a pas été activée lorsqu’un enregistrement a été ingéré, une valeur nulle est retournée.

La politique IngestionTime a été conçue pour deux scénarios principaux :
* Permettre aux utilisateurs d’estimer la latence de bout en bout dans l’ingestion de données.
  De nombreux tableaux contenant des données de journal ont une colonne timetamp dont la valeur est remplie par la source pour indiquer l’heure à laquelle l’enregistrement a été produit. En comparant la valeur de cette colonne avec la colonne de temps d’ingestion, on peut estimer la latence dans l’obtention des données. (Notez qu’il ne s’agit que d’une estimation, parce que la source et Kusto n’ont pas nécessairement leurs horloges synchronisées.)
* Pour prendre en charge [les curseurs de base de données](../management/databasecursor.md), permettant aux utilisateurs d’émettre des requêtes consécutives et à chaque fois de limiter la requête aux données qui ont été ingérées depuis la requête précédente.



Pour en savoir plus sur les commandes de contrôle pour la gestion de la politique IngestionTime, [voir ici](../management/ingestiontime-policy.md).