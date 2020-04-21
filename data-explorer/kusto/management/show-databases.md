---
title: .afficher les bases de données - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .show bases de données dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1827c3ea20d984c6846ef9feb603398020efe275
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610181"
---
# <a name="show-databases"></a>.afficher les bases de données

Renvoie un tableau dans lequel chaque enregistrement correspond à une base de données dans le cluster auquel l’utilisateur a accès.

Pour voir retourner un tableau montrant les propriétés de la base de données contextuelle, voir [.afficher la base de données](show-database.md).

**Syntaxe**

`.show` `databases`

**Schéma de sortie**

|Nom de la colonne       |Type de colonne|Description                                                                  |
|------------------|-----------|-----------------------------------------------------------------------------|
|nom_base_de_données      |`string`   |Le nom de la base de données tel qu’attaché au cluster.                         |
|PersistantStorage |`string`   |La « racine » persistante de stockage de la base de données. (Uniquement réservé à un usage interne.)      |
|Version           |`string`   |Version de la base de données. (Uniquement réservé à un usage interne.)                        |
|IsCurrent         |`bool`     |La question de savoir si cette base de données est le contexte de la base de données de la demande.                |
|Base de donnéesAccessMode|`string`   |Un `ReadWrite`de `ReadOnly` `ReadOnlyFollowing`, `ReadWriteEphemeral`, , ou .|
|PrettyName (En)        |`string`   |Le joli nom de la base de données, le cas échéant.                                    |
|ReservedSlot1     |`bool`     |Réservé. (Uniquement réservé à un usage interne.)                                           |
|DatabaseId        |`guid`     |Un identifiant unique à l’échelle mondiale pour la base de données. (Uniquement réservé à un usage interne.)      |
