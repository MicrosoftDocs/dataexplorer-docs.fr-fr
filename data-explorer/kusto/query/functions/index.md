---
title: Fonctions - Azure Data Explorer | Microsoft Docs
description: Cet article décrit les fonctions Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
adobe-target: true
ms.openlocfilehash: 234ae530fcd664d8d12418ed85d8394385bebf75
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359929"
---
# <a name="function-types"></a>Types de fonction

Les **fonctions** sont des requêtes ou des parties de requête réutilisables. Kusto prend en charge plusieurs types de fonctions :

* **Fonctions stockées**, qui sont des fonctions définies par l’utilisateur stockées et gérées par un seul type d’entité de schéma d’une base de données.
  Consultez [Fonctions stockées](../schema-entities/stored-functions.md).
* **Fonctions définies par une requête**, qui sont des fonctions définies par l’utilisateur, définies et utilisées au sein de l’étendue d’une seule requête. La définition de telles fonctions s’effectue par le biais d’une [instruction let](../letstatement.md).
  Consultez [Fonctions définies par l’utilisateur](./user-defined-functions.md).
* **Fonctions intégrées**, qui sont codées en dur (définies par Kusto et non modifiables par les utilisateurs).