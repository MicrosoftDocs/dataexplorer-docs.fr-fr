---
title: Bases de données - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les bases de données dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: a94b168d8aa8443251f3a01dc659e114b0aacaf5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509433"
---
# <a name="databases"></a>Bases de données

Kusto suit un modèle de relation de stockage `database`des données où l’entité de niveau supérieur est un . 

Le cluster Kusto peut héberger plusieurs bases de données, où chaque base de données hébergera sa propre collection de [tables,](tables.md)de [fonctions stockées](stored-functions.md)et de [tables externes.](externaltables.md)
Chaque base de données a ses propres autorisations définies, basées sur [le modèle de contrôle d’accès basé sur les rôles (RBAC)](../../management/access-control/index.md)

**Remarques**  

* Les noms de base de données sont insensibles aux cas.
* Les noms de base de données doivent suivre les règles pour les [noms d’entités](./entity-names.md).
* La limite maximale des bases de données par cluster est de 10 000.