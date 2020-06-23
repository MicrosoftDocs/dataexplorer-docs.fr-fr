---
title: Tables de faits et de dimension-Azure Explorateur de données
description: Cet article décrit les tables de faits et de dimension dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 6db37366ddd3d70aaa89c0d6eebd1ec8affbb76d
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264440"
---
# <a name="fact-and-dimension-tables"></a>Tables de faits et de dimension

Lorsque vous concevez le schéma d’une base de données Azure Explorateur de données, pensez aux tables qui appartiennent à l’une des deux catégories.
* [Tables de faits](https://en.wikipedia.org/wiki/Fact_table)
* [Tables de dimension](https://en.wikipedia.org/wiki/Dimension_(data_warehouse)#Dimension_table)

## <a name="fact-tables"></a>Tables de faits
Les tables de faits sont des tables dont les enregistrements sont des « faits » immuables, tels que les journaux de service et les informations de mesure. Les enregistrements sont progressivement ajoutés à la table en mode de diffusion en continu ou en blocs volumineux. Les enregistrements restent là jusqu’à ce qu’ils soient supprimés en raison du coût ou parce qu’ils ont perdu leur valeur. Les enregistrements ne sont pas mis à jour dans le cas contraire.

Les données d’entité sont parfois conservées dans les tables de faits, où les données d’entité changent lentement. Par exemple, les données relatives à une entité physique, telles qu’un équipement de bureau qui modifie rarement l’emplacement.
Étant donné que les données dans Kusto sont immuables, la pratique courante consiste à faire en sorte que chaque table contienne deux colonnes :
* Colonne d’identité ( `string` ) qui identifie l’entité
* Colonne timestamp Last-modified ( `datetime` )

Seul le dernier enregistrement pour chaque identité d’entité est ensuite récupéré.

## <a name="dimension-tables"></a>Tables de dimension
Tables de dimension :
* Stocker des données de référence, telles que des tables de recherche à partir d’un identificateur d’entité, à ses propriétés
* Conserver des données de type instantané dans des tables dont le contenu entier change dans une transaction unique

Les tables de dimension ne sont pas ingérées régulièrement avec de nouvelles données. Au lieu de cela, l’ensemble du contenu des données est mis à jour en même temps, à l’aide d’opérations telles que [. set-or-Replace](../management/data-ingestion/ingest-from-query.md), [. Move extents](../management/extents-commands.md#move-extents)ou [. Rename tables](../management/rename-table-command.md).

Parfois, les tables de dimension peuvent être dérivées de tables de faits. Ce processus peut être effectué via une [stratégie de mise à jour](../management/updatepolicy.md) sur la table de faits, avec une requête sur la table qui prend le dernier enregistrement pour chaque entité.

## <a name="differentiate-fact-and-dimension-tables"></a>Différencier les tables de faits et de dimension

Il existe des processus dans Kusto qui différencient les tables de faits et les tables de dimension. L’une d’elles est l' [exportation continue](../management/data-export/continuous-data-export.md).

Ces mécanismes sont assurés de traiter précisément les données des tables de faits une fois. Elles s’appuient sur le mécanisme [de curseur de base de données](../management/databasecursor.md) .

Par exemple, chaque exécution d’une tâche d’exportation continue exporte tous les enregistrements qui ont été ingérés depuis la dernière mise à jour du curseur de base de données. Les travaux d’exportation continue doivent faire la différence entre les tables de faits et les tables de dimension. Les tables de faits traitent uniquement les données nouvellement ingérées et les tables de dimension sont utilisées comme recherches. Par conséquent, la table entière doit être prise en compte.

Il n’existe aucun moyen de « marquer » une table comme étant une « table de faits » ou une « table de dimension ».
La façon dont les données sont ingérées dans la table, et la façon dont la table est utilisée, est ce qui identifie son type.
