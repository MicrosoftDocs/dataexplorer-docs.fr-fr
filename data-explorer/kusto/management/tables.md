---
title: Gestion des tables-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la gestion des tables dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/27/2020
ms.openlocfilehash: ed70e01f7d955ba92806e7e11f740490e87cc664
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011446"
---
# <a name="tables-management"></a>Gestion des tables

Cette rubrique décrit le cycle de vie des tables et des commandes de contrôle associées.

Sélectionnez les liens dans le tableau ci-dessous pour obtenir plus d’informations à leur sujet.

| Commandes                                                                                                                 | Opération                       |
|--------------------------------------------------------------------------------------------------------------------------|---------------------------------|
| [`.alter table docstring`](alter-table-docstring-command.md), [`.alter table folder`](alter-table-folder-command.md)                                                                                                                                                                                                   | Gérer les propriétés d’affichage de table |
| [`.create ingestion mapping`](create-ingestion-mapping-command.md), [`.show ingestion mappings`](show-ingestion-mapping-command.md), [`.alter ingestion mapping`](alter-ingestion-mapping-command.md), [`.drop ingestion mapping`](drop-ingestion-mapping-command.md)                                                                    | Gérer le mappage d’ingestion        |
| [`.create tables`](create-tables-command.md), [`.create table`](create-table-command.md), [`.alter table`](alter-table-command.md), [`.alter-merge table`](alter-table-command.md), [`.drop tables`](drop-table-command.md), [`.drop table`](drop-table-command.md), [`.undo drop table`](undo-drop-table-command.md), [`.rename table`](rename-table-command.md) | Créer/modifier/supprimer des tables       |
| [`.show tables`](show-tables-command.md) [`.show table details`](show-table-details-command.md)[`.show table schema`](show-table-schema-command.md)                                                                                      | Énumérer des tables dans une base de données  |
| `.ingest`, `.set` , `.append` , `.set-or-append` (voir ingestion de [données](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands) pour plus d’informations).)                                                                                                                                                                                      | Ingestion de données dans une table     |

## <a name="crud-naming-conventions-for-tables"></a>Conventions d’affectation de noms CRUD pour les tables 
(Pour plus d’informations, consultez les sections liées à dans le tableau ci-dessus.)
 
| Syntaxe de la commande                             | Sémantique                                                                                                             |
|--------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| `.create entityType entityName ...`        | Si une entité de ce type et de ce nom existe, retourne l’entité. Dans le cas contraire, créez l’entité.                          |
| `.create-merge entityType entityName...`   | Si une entité de ce type et de ce nom existe, fusionnez l’entité existante avec l’entité spécifiée. Dans le cas contraire, créez l’entité. |
| `.alter entityType entityName ...`         | Si une entité de ce type et de ce nom n’existe pas, erreur. Sinon, remplacez-le par l’entité spécifiée.            |
| `.alter-merge entityType entityName ...`   | Si une entité de ce type et de ce nom n’existe pas, erreur. Sinon, fusionnez-le avec l’entité spécifiée.              |
| `.drop entityType entityName ...`          | Si une entité de ce type et de ce nom n’existe pas, erreur. Sinon, supprimez-le.                                         |
| `.drop entityType entityName ifexists ...` | Si une entité de ce type et de ce nom n’existe pas, retournez. Sinon, supprimez-le.                                        |
 
> [!NOTE]
> « Merge » est une fusion logique de deux entités :
>
> * Si une propriété est définie pour une entité, mais pas pour l’autre, elle apparaît avec sa valeur d’origine dans l’entité fusionnée.
> * Si une propriété est définie pour les deux entités et a la même valeur dans les deux, elle apparaît une fois avec cette valeur dans l’entité fusionnée.
> * Si une propriété est définie pour les deux entités mais a des valeurs différentes, une erreur est générée.