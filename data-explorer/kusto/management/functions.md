---
title: Vue d’ensemble de la gestion des fonctions stockées-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la vue d’ensemble de la gestion des fonctions stockées dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: 4354538b206ee86e34941718bcf6d74130647fb6
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321350"
---
# <a name="stored-functions-management-overview"></a>Vue d’ensemble de la gestion des fonctions stockées
Cette section décrit les commandes de contrôle utilisées pour créer et modifier les [fonctions définies par l’utilisateur](../query/functions/user-defined-functions.md)suivantes :

|Fonction |Description|
|---------|-----------|
|[`.alter function`](alter-function.md) |Modifie une fonction existante et la stocke dans les métadonnées de la base de données |
|[`.alter function docstring`](alter-docstring-function.md) |Modifie la valeur DocString d’une fonction existante |
|[`.alter function folder`](alter-folder-function.md) |Modifie la valeur de dossier d’une fonction existante |
|[`.create function`](create-function.md) |Crée une fonction stockée |
|[`.create-or-alter function`](create-alter-function.md) |Crée une fonction stockée ou modifie une fonction existante et la stocke dans les métadonnées de la base de données |
|[`.drop function` les `.drop functions`](drop-function.md) |Supprime une fonction (ou des fonctions) de la base de données. |
|[`.show functions` les `.show function`](show-function.md) |Répertorie toutes les fonctions stockées, ou une fonction spécifique, dans la base de données actuellement sélectionnée. |