---
title: Ingestion de diffusion en continu et modifications de schéma-Azure Explorateur de données
description: Cet article décrit les options de gestion des modifications de schéma avec l’ingestion de diffusion en continu dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/20/2020
ms.openlocfilehash: f1ab3d012be7751eba33447b325748859509af00
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84784563"
---
# <a name="streaming-ingestion-and-schema-changes"></a>Ingestion de diffusion en continu et modifications de schéma

## <a name="background"></a>Arrière-plan

Les nœuds de cluster cachent le schéma des bases de données qui reçoivent des données via l’ingestion de diffusion en continu. Ce processus optimise les performances et l’utilisation des ressources de cluster, mais peut entraîner des retards de propagation lorsque le schéma est modifié.

Voici quelques exemples de modifications de schéma :

* Création et suppression de bases de données et de tables
* Ajout, suppression, Refrappe ou attribution d’un nouveau nom aux colonnes de la table
* Ajout ou suppression de mappages d’ingestion précréés
* Ajout, suppression ou modification de stratégies

Si les modifications de schéma et les flux d’ingestion de diffusion en continu ne sont pas coordonnés, certaines des demandes d’ingestion de streaming peuvent échouer. Les échecs peuvent inclure des erreurs liées au schéma ou l’insertion de données incomplètes ou déformées dans la table.
Lors de l’implémentation d’une application d’ingestion personnalisée, il est vivement recommandé de gérer les défaillances liées au schéma en effectuant de nouvelles tentatives pendant une période limitée, ou en reroutant les données des demandes ayant échoué via des méthodes d’ingestion en attente.

## <a name="clearing-the-schema-cache"></a>Effacement du cache de schéma

Réduisez les effets du délai de propagation en effaçant explicitement le cache de schéma sur les nœuds du cluster.
Effacez le cache de schéma à l’aide de l’un des commandes [effacer le cache de schéma pour](clear-schema-cache-command.md) la gestion de l’ingestion de streaming.
Si le flux d’ingestion de streaming et les modifications de schéma sont coordonnées, vous pouvez éliminer complètement les défaillances et la distorsion des données associées. 

**Exemple de Flow coordonné :**

1. Interruption de l’ingestion de diffusion en continu.
1. Attendez la fin de toutes les demandes d’ingestion de streaming en suspens>
1. Modifiez le schéma.
1. Émettez une ou plusieurs `.clear cache streaming ingestion` commandes de schéma. 
    * Répéter jusqu’à la réussite et que toutes les lignes du résultat de la commande indiquent une réussite
1. Reprendre l’ingestion de la diffusion en continu.

> [!NOTE]
> L’utilisation fréquente des commandes de schéma Clear cache ingestion streaming peut avoir un effet néfaste sur les performances de l’ingestion de diffusion en continu.
