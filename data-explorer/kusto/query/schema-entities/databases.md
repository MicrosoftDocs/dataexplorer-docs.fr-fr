---
title: Bases de données-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les bases de données dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 3d981a4b56eef689081a4bc6a622221c126efa2e
ms.sourcegitcommit: 8b8228fe18e6408673891374a8048a7a3723921c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96516062"
---
# <a name="databases"></a>Bases de données

Kusto suit un modèle de relation pour le stockage des données où l’entité de niveau supérieur est un `database` . 

Le cluster Kusto peut héberger plusieurs bases de données, où chaque base de données hébergera sa propre collection de [tables](tables.md), de [fonctions stockées](stored-functions.md)et de [tables externes](externaltables.md).
Chaque base de données a son propre jeu d’autorisations, basé sur le [modèle de Access Control basée sur les rôles (RBAC)](../../management/access-control/index.md) .

**Remarques**  

* Les noms de base de données doivent respecter les règles applicables aux [noms d’entité](./entity-names.md).
* Le nombre maximal de bases de données par cluster est de 10 000.
