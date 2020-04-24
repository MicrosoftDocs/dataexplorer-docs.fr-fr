---
title: Politique de commande de ligne - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique de commande de ligne dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 4dca1a4083a6141eb94d9bf3cf69f7134ebfca04
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520211"
---
# <a name="row-order-policy"></a>Stratégie sur l’ordre des lignes

La politique d’ordre de ligne est une politique facultative fixée sur les tables, qui fournit un « indice » à Kusto sur la commande souhaitée des lignes dans un fragment de données.

L’objectif principal de la politique n’est pas d’améliorer la compression (bien qu’il s’agit d’un effet secondaire potentiel), mais d’améliorer les performances des requêtes qui sont connues pour être réduites à un petit sous-ensemble de valeurs dans les colonnes ordonnées.

L’application de la politique est appropriée lorsque :
* La majorité des requêtes filtrent sur des valeurs spécifiques d’une colonne spécifique à grande dimension (comme un « ID d’application » ou une « pièce d’identité de locataire »)
* Il est peu probable que les données ingérées dans le tableau soient précommercrites selon cette colonne.

Bien qu’il n’y ait pas de limites codées en dur fixées sur la quantité de colonnes (clés de tri) qui peuvent être définies comme faisant partie de la stratégie, chaque colonne supplémentaire ajoute des frais généraux au processus d’ingestion, et comme plus de colonnes sont ajoutées - le rendement effectif diminue.

> [!NOTE]
> Une fois que la politique sera appliquée à un tableau, elle aura une incidence sur les données ingérées à partir de ce moment.

Les commandes de contrôle pour la gestion des politiques de l’ordre des rames peuvent être trouvées [ici](../management/roworder-policy.md)