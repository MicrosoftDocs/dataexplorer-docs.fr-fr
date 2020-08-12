---
title: Afficher les échecs d’exportation de données en continu-Azure Explorateur de données
description: Cet article explique comment afficher les échecs d’exportation de données en continu dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: 54c95e3beecd25b504c52c2fe06ffa51160ac8ca
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149437"
---
# <a name="show-continuous-export-failures"></a>Afficher les échecs d’exportation continue

Retourne tous les échecs enregistrés dans le cadre de l’exportation continue. Filtrez les résultats selon la colonne timestamp dans la commande pour afficher uniquement les plages horaires qui vous intéressent. 

## <a name="syntax"></a>Syntaxe

`.show` `continuous-export` *ContinuousExportName* `failures`

## <a name="properties"></a>Propriétés

| Propriété             | Type   | Description                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | Nom de l’exportation continue  |

## <a name="output"></a>Output

| Paramètre de sortie | Type      | Description                                         |
|------------------|-----------|-----------------------------------------------------|
| Timestamp        | Datetime  | Horodateur de l’échec.                           |
| OperationId      | String    | ID d’opération de l’échec.                    |
| Nom             | String    | Nom de l’exportation continue.                             |
| LastSuccessRun   | Timestamp | Dernière exécution réussie de l’exportation continue.   |
| FailureKind      | String    | Échec/PartialFailure. PartialFailure indique que certains artefacts ont été exportés avec succès avant l’échec. |
| Détails          | String    | Détails de l’erreur d’échec.                              |

## <a name="example"></a>Exemple 

```kusto
.show continuous-export MyExport failures 
```

| Timestamp                   | OperationId                          | Nom     | LastSuccessRun              | FailureKind | Détails    |
|-----------------------------|--------------------------------------|----------|-----------------------------|-------------|------------|
| 2019-01-01 11:07:41.1887304 | ec641435-2505-4532-ba19-d6ab88c96a9d | MyExport | 2019-01-01 11:06:35.6308140 | Échec     | Détails... |
