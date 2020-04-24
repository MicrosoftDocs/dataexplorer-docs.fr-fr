---
title: Gestion des tables - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la gestion des tables dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/27/2020
ms.openlocfilehash: 7b7e4c5c7111354864aa939ece76be2ab0a8ac15
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519565"
---
# <a name="tables-management"></a>Gestion des tables

Ce sujet traite du cycle de vie des tables et des commandes de contrôle associées.

Sélectionnez les liens dans le tableau ci-dessous pour plus d’informations à leur sujet.

| Commandes                                                                                                                 | Opération                       |
|--------------------------------------------------------------------------------------------------------------------------|---------------------------------|
| [`.alter table docstring`](alter-table-docstring-command.md), [`.alter table folder`](alter-table-folder-command.md)                                                                                                                                                                                                   | Gérer les propriétés d’affichage de table |
| [`.create ingestion mapping`](create-ingestion-mapping-command.md), [`.show ingestion mappings`](show-ingestion-mapping-command.md), [`.alter ingestion mapping`](alter-ingestion-mapping-command.md), [`.drop ingestion mapping`](drop-ingestion-mapping-command.md)                                                                    | Gérer la cartographie de l’ingestion        |
| [`.create tables`](create-tables-command.md), [`.create table`](create-table-command.md), [`.alter table`](alter-table-command.md), [`.alter-merge table`](alter-table-command.md), [`.drop tables`](drop-table-command.md), [`.drop table`](drop-table-command.md), [`.undo drop table`](undo-drop-table-command.md), [`.rename table`](rename-table-command.md) | Créer/modifier/déposer des tables       |
| [`.show tables`](show-tables-command.md) [`.show table details`](show-table-details-command.md)[`.show table schema`](show-table-schema-command.md)                                                                                      | Énumérer les tables dans une base de données  |
| `.ingest`, `.set` `.append`, `.set-or-append` (voir [Ingestion de données](./data-ingestion/index.md) pour plus de détails).)                                                                                                                                                                                      | Ingestion de données dans un tableau     |

## <a name="crud-naming-conventions-for-tables"></a>Conventions de nommage CRUD pour tables 
(Voir tous les détails dans les sections liées à dans le tableau, ci-dessus.)
 
| Syntaxe de commande                             | Sémantique                                                                                                             |
|--------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| `.create entityType entityName ...`        | Si une entité de ce type et de ce nom existe, renvoie l’entité. Sinon, créez l’entité.                          |
| `.create-merge entityType entityName...`   | Si une entité de ce type et de ce nom existe, fusionnez l’entité existante avec l’entité spécifiée. Sinon, créez l’entité. |
| `.alter entityType entityName ...`         | Si une entité de ce type et de ce nom n’existe pas, erreur. Dans le cas contraire, remplacez-le par l’entité spécifiée.            |
| `.alter-merge entityType entityName ...`   | Si une entité de ce type et de ce nom n’existe pas, erreur. Dans le cas contraire, fusionnez-le avec l’entité spécifiée.              |
| `.drop entityType entityName ...`          | Si une entité de ce type et de ce nom n’existe pas, erreur. Sinon, lâchez-le.                                         |
| `.drop entityType entityName ifexists ...` | Si une entité de ce type et de ce nom n’existe pas, retournez. Sinon, lâchez-le.                                        |
 
> [!NOTE]
> "Merge" est une fusion logique de deux entités:
>
> * Si une propriété est définie pour une entité mais pas pour l’autre, elle apparaît avec sa valeur initiale dans l’entité fusionnée.
> * Si une propriété est définie pour les deux entités et a la même valeur dans les deux, elle apparaît une fois avec cette valeur dans l’entité fusionnée.
> * Si une propriété est définie pour les deux entités mais a des valeurs différentes, une erreur est soulevée.