---
title: Stratégie d’ingestion de streaming-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la stratégie d’ingestion de streaming dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2020
ms.openlocfilehash: 312e6cad984491b39bd9a77f0b34d142798e922e
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257992"
---
# <a name="streaming-ingestion-policy"></a>Stratégie d’ingestion de streaming

## <a name="streaming-ingestion-target-scenario"></a>Scénario de cible d’ingestion de streaming

L’ingestion de la diffusion en continu est ciblée pour les scénarios qui nécessitent une faible latence, avec un temps d’ingestion de moins de 10 secondes pour les données de volume variées. Il est utilisé pour optimiser le traitement opérationnel de nombreuses tables, dans une ou plusieurs bases de données, où le flux de données dans chaque table est relativement faible (quelques enregistrements par seconde), mais le volume global d’ingestion de données est élevé (des milliers d’enregistrements par seconde).

Utilisez l’ingestion classique (Bulk) au lieu de l’ingestion de diffusion en continu lorsque la quantité de données augmente jusqu’à 4 Go par heure et par table. 

* Pour savoir comment implémenter cette fonctionnalité, consultez ingestion de la [diffusion en continu](../../ingest-data-streaming.md).
* Pour plus d’informations sur les commandes de contrôle d’ingestion de streaming, consultez [commandes de contrôle utilisées pour gérer la stratégie d’ingestion de streaming](streamingingestion-policy.md) .
 
## <a name="streaming-ingestion-policy-definition"></a>Définition de la stratégie d’ingestion de streaming

La stratégie d’ingestion de streaming contient les propriétés suivantes :

* **IsEnabled**:
  * définit l’état de la fonctionnalité de diffusion en continu pour la table ou la base de données
  * obligatoire, aucune valeur par défaut, doit être définie explicitement sur *true* ou *false*
* **HintAllocatedRate**:
  * Si SET fournit un indicateur sur le volume horaire de données en gigaoctets attendu pour la table. Cet indicateur aide le système à ajuster la quantité de ressources allouées à une table pour la prise en charge de l’ingestion de diffusion en continu.
  * valeur par défaut *null* (unset)

Pour activer l’ingestion de la diffusion en continu sur une table, définissez la stratégie d’ingestion de streaming avec *IsEnabled* définie sur *true*. Cette définition peut être définie sur une table elle-même ou sur la base de données.
La définition de cette stratégie au niveau de la base de données applique les mêmes paramètres à toutes les tables existantes et futures de la base de données. Si la stratégie d’ingestion de streaming est définie au niveau de la table et de la base de données, le paramètre de niveau table est prioritaire. Ce paramètre signifie que l’ingestion de streaming peut être activée en général pour la base de données, mais désactivée spécifiquement pour certaines tables, ou inversement.

> [!NOTE]
> Si une table n’obtient pas directement l’ingestion de la diffusion en continu, mais uniquement par le biais d’une stratégie de mise à jour, aucune stratégie d’ingestion de diffusion en continu ne doit être définie sur cette table.


## <a name="set-the-data-rate-hint"></a>Définir l’indicateur de débit de données
La stratégie d’ingestion de streaming peut fournir une indication sur le volume horaire des données attendues pour la table. Cet indicateur aide le système à ajuster la quantité de ressources allouées pour cette table à la prise en charge de l’ingestion de diffusion en continu.
Définissez l’indicateur si le taux de données de streaming entrant dans la table dépasse 1 Go/heure.
Si vous définissez _HintAllocatedRate_ dans la stratégie d’ingestion de streaming pour la base de données, définissez-la par la table avec la fréquence de données la plus élevée attendue. Il n’est pas recommandé de définir l’indicateur effectif pour une table sur une valeur bien supérieure à la vitesse de transmission de données de pointe horaire attendue. Ce paramètre peut avoir un effet néfaste sur les performances des requêtes.
