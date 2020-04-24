---
title: Aperçu de la gestion des fonctions stockées - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’aperçu de la gestion des fonctions stockées dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: f3768c6252a96215d37bd9f19a44cbf4d3afc731
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520959"
---
# <a name="stored-functions-management-overview"></a>Aperçu de la gestion des fonctions stockées
Cette section décrit les commandes de contrôle utilisées pour créer et modifier les [fonctions définies par l’utilisateur](../query/functions/user-defined-functions.md)suivantes :

|Fonction |Description|
|---------|-----------|
|[.modifier la fonction](alter-function.md) |Modifie une fonction existante et la stocke à l’intérieur des métadonnées de base de données |
|[.alter fonction docstring](alter-docstring-function.md) |Modifie la valeur DocString d’une fonction existante |
|[.modifier le dossier de fonction](alter-folder-function.md) |Modifie la valeur De pli d’une fonction existante |
|[.créer la fonction](create-function.md) |Crée une fonction stockée |
|[.créer ou modifier la fonction](create-alter-function.md) |Crée une fonction stockée ou modifie une fonction existante et la stocke à l’intérieur des métadonnées de base de données |
|[.drop fonction et .drop fonctions](drop-function.md) |Laisse tomber une fonction (ou fonctions) de la base de données |
|[.afficher les fonctions et .show fonction](show-function.md) |Répertorie toutes les fonctions stockées, ou une fonction spécifique, dans la base de données actuellement sélectionnée |