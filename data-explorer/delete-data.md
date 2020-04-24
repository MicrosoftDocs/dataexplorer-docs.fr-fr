---
title: Supprimer des données de l’Explorateur de données Azure
description: Cet article décrit des scénarios de suppression dans Azure Data Explorer, notamment des suppressions basées sur un vidage, un abandon d’étendues ou une rétention.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: efbea2f9b8502f2521f2376f4aaf57902fa4e302
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493293"
---
# <a name="delete-data-from-azure-data-explorer"></a>Supprimer des données de l’Explorateur de données Azure

Azure Data Explorer prend en charge différents scénarios de suppression décrits dans cet article. 

## <a name="delete-data-using-the-retention-policy"></a>Supprimer des données en utilisant la stratégie de rétention

Azure Data Explorer supprime automatiquement des données en fonction de la [stratégie de rétention](kusto/management/retentionpolicy.md). Cette méthode est la manière la plus efficace et simple de supprimer des données. Définissez la stratégie de rétention au niveau de la base de données ou de la table.

Prenons l’exemple d’une base de données ou d’une table qui est définie pour être conservée 90 jours. Si seuls 60 jours de données sont nécessaires, supprimez les données plus anciennes comme suit :

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Supprimer des données en abandonnant des étendues

Une [étendue (partition de données)](kusto/management/extents-overview.md) est la structure interne dans laquelle des données sont stockées. Chaque étendue peut contenir des millions d’enregistrements. Il est possible de supprimer des étendues individuellement ou en tant que groupe à l’aide de [commandes d’abandon d’étendue(s)](kusto/management/extents-commands.md#drop-extents). 

### <a name="examples"></a>Exemples

Vous pouvez supprimer toutes les lignes d’une table ou juste une étendue spécifique.

* Supprimer toutes les lignes d’une table :

    ```kusto
    .drop extents from TestTable
    ```

* Supprimer une étendue spécifique :

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Supprimer des lignes individuelles par vidage

Vous pouvez utiliser un [vidage des données](kusto/concepts/data-purge.md) pour supprimer des lignes individuelles. La suppression n’est pas immédiate et nécessite des ressources système significatives. Par conséquent, un vidage n’est recommandé qu’à des fins de conformité.  

