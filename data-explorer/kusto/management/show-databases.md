---
title: . afficher les bases de données-Azure Explorateur de données
description: Cet article décrit. afficher les bases de données dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e5ff64db05bb85d88ed3da3347ea95cf02b0234b
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818542"
---
# <a name="show-databases"></a>.show databases

Retourne une table dans laquelle chaque enregistrement correspond à une base de données du cluster à laquelle l’utilisateur a accès.

Pour obtenir une table qui indique les propriétés de la base de données de contexte, consultez [`.show database`](show-database.md) .

**Syntaxe**

`.show` `databases`

**Schéma de sortie**

|Nom de la colonne       |Type de colonne|Description                                                                  |
|------------------|-----------|-----------------------------------------------------------------------------|
|nom_base_de_données      |`string`   |Nom de la base de données attachée au cluster                    |
|PersistentStorage |`string`   |« Racine » de stockage persistant de la base de données. Usage interne uniquement          |
|Version           |`string`   |Version de la base de données. Usage interne uniquement                       |
|IsCurrent         |`bool`     |Indique si cette base de données est le contexte de base de données de la demande                    |
|DatabaseAccessMode|`string`   |L’une des,, `ReadWrite` `ReadOnly` `ReadOnlyFollowing` ou`ReadWriteEphemeral`    |
|PrettyName        |`string`   |Nom convivial de la base de données, le cas échéant                        |
|ReservedSlot1     |`bool`     |Réservé. Usage interne uniquement              |
|DatabaseId        |`guid`     |Identificateur global unique de la base de données. Usage interne uniquement          |
