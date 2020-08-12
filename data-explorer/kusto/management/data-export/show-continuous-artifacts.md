---
title: Afficher les artefacts d’exportation de données en continu-Azure Explorateur de données
description: Cet article explique comment afficher des artefacts d’exportation de données continus dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: 68b33ffc46df1e3bc4f63f8f7e9c86786116308b
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149452"
---
# <a name="show-continuous-export-artifacts"></a>Afficher les artefacts d’exportation continus

Retourne tous les artefacts exportés par l’exportation continue dans toutes les exécutions. Filtrez les résultats selon la colonne timestamp dans la commande pour afficher uniquement les enregistrements intéressants. L’historique des artefacts exportés est conservé pendant 14 jours. 

## <a name="syntax"></a>Syntaxe

`.show` `continuous-export` *ContinuousExportName* `exported-artifacts`

## <a name="properties"></a>Propriétés

| Propriété             | Type   | Description                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | Nom de l’exportation continue. |

## <a name="output"></a>Output

| Paramètre de sortie  | Type     | Description                            |
|-------------------|----------|----------------------------------------|
| Timestamp         | Datetime | Horodateur de l’exécution de l’exportation continue |
| ExternalTableName | String   | Nom de la table externe             |
| Path              | String   | Chemin de sortie                            |
| NumRecords        | long     | Nombre d’enregistrements exportés dans le chemin     |

## <a name="example"></a>Exemple

```kusto
.show continuous-export MyExport exported-artifacts | where Timestamp > ago(1h)
```

| Timestamp                   | ExternalTableName | Path             | NumRecords | SizeInBytes |
|-----------------------------|-------------------|------------------|------------|-------------|
| 2018-12-20 07:31:30.2634216 | ExternalBlob      | `http://storageaccount.blob.core.windows.net/container1/1_6ca073fd4c8740ec9a2f574eaa98f579.csv` | 10                          | 1 024              |
