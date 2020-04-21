---
title: Tables de faits et de dimensions - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les tableaux de faits et de dimension dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 1a88f8549bf5accce197294d433ece8ccb683281
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523237"
---
# <a name="fact-and-dimension-tables"></a>Tables de faits et de dimension

Lors de la conception du schéma d’une base de données Kusto, il est utile de penser que les tableaux appartiennent largement à l’une des deux catégories :
* [Tableaux d’information](https://en.wikipedia.org/wiki/Fact_table) et
* [Tables dimension](https://en.wikipedia.org/wiki/Dimension_(data_warehouse)#Dimension_table).

**Les tableaux d’information** sont des tableaux dont les dossiers sont des « faits » immuables, tels que les journaux de service et les informations sur les mesures. Les dossiers sont annexés progressivement dans la table (de façon en streaming ou en gros morceaux), et sont conservés jusqu’à ce qu’ils doivent être supprimés pour des raisons de coût ou parce qu’ils perdent leur valeur. Les enregistrements ne sont autrement jamais mis à jour.

**Les tables dimensionnelles** contiennent des données de référence (telles que des tableaux de recherche d’un identifiant d’entité à ses propriétés) et des données de type instantané (tableaux dont l’ensemble du contenu change dans une seule transaction). Habituellement, ces tableaux ne sont pas régulièrement ingérés avec de nouvelles données. Au lieu de cela, l’ensemble du contenu de données est mis à jour à la fois en utilisant des opérations telles que [.set-or-replace](../management/data-ingestion/ingest-from-query.md), [.move extents](../management/extents-commands.md#move-extents), ou [.rename tables](../management/rename-table-command.md).
Dans certains cas, les tableaux de dimension peuvent être dérivés de tableaux de faits via une stratégie de [mise à jour](../management/updatepolicy.md) sur le tableau des faits, ainsi que quelques questions sur la table qui prend le dernier enregistrement pour chaque entité.

Il est important de se rendre compte qu’il n’y a aucun moyen de «marquer» une table à Kusto comme étant une table de fait ou une table de dimension. La façon dont les données sont ingérées dans la table et la façon dont les tableaux sont utilisés est ce qui est important.

> [!NOTE]
> Parfois, Kusto est utilisé pour tenir les données de l’entité dans les tableaux de fait, de sorte que les données de l’entité change lentement. Par exemple, les données concernant une entité physique (par exemple, une pièce d’équipement de bureau) qui change rarement d’emplacement.
> Comme les données dans Kusto est immuable, la pratique courante est`string`d’avoir chaque table tenir deux colonnes: une identité ( ) colonne qui identifie l’entité, et une dernière modifiée ( )`datetime`colonne de timestamp. Seul le dernier enregistrement pour chaque identité d’entité est ensuite récupéré.



## <a name="commands-that-differentiate-fact-and-dimension-tables"></a>Commandes qui différencient les tableaux de faits et de dimension

Il existe des processus à Kusto qui différencient les tableaux de faits et les tables de dimension.

* [exportation continue](../management/data-export/continuous-data-export.md)




Ces processus sont garantis pour traiter les données dans les tableaux de fait précisément une fois, en s’appuyant sur le mécanisme [de curseur de base de données.](../management/databasecursor.md)

Par exemple, chaque exécution d’un emploi à l’exportation continue exporte tous les documents qui ont été ingérés depuis la dernière mise à jour du curseur de base de données. Par conséquent, les emplois à l’exportation continus doivent faire la distinction entre les tableaux de faits (processus seulement données nouvellement ingérées) et les tableaux de dimension (étant utilisés comme des recherchers, de sorte que l’ensemble du tableau doit être pris en compte).