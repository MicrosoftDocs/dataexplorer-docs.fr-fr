---
title: Créer ou modifier l’exportation de données en continu-Azure Explorateur de données
description: Cet article explique comment créer ou modifier l’exportation de données en continu dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: 11822a58b2ced0b257ad649997bc154ab4be19d5
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337582"
---
# <a name="create-or-alter-continuous-export"></a>Créer ou modifier une exportation continue

Crée ou modifie une tâche d’exportation continue.

## <a name="syntax"></a>Syntax

`.create-or-alter` `continuous-export` *ContinuousExportName* <br>
[ `over` `(` *T1*, *T2* `)` ] <br>
`to``table` *ExternalTableName* <br> [ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ]<br>
\<| *Demande*

## <a name="properties"></a>Propriétés

| Propriété             | Type     | Description   |
|----------------------|----------|---------------------------------------|
| ContinuousExportName | String   | Nom de l’exportation continue. Le nom doit être unique dans la base de données et utilisé pour exécuter régulièrement l’exportation continue.      |
| ExternalTableName    | String   | Nom de la [table externe](../external-table-commands.md) vers laquelle effectuer l’exportation.  |
| Requête                | String   | Requête à exporter.  |
| sur (T1, T2)        | String   | Liste facultative de tables de faits séparées par des virgules dans la requête. Si cette valeur n’est pas spécifiée, toutes les tables référencées dans la requête sont supposées être des tables de faits. S’ils sont spécifiés, les tables qui *ne sont pas* dans cette liste sont traitées comme des tables de dimension et ne sont pas étendues (tous les enregistrements participent à toutes les exportations). Pour plus d’informations, consultez [vue d’ensemble de l’exportation continue de données](continuous-data-export.md) . |
| intervalBetweenRuns  | Timespan | Intervalle de temps entre les exécutions d’exportation continue. Doit être supérieur à 1 minute.   |
| forcedLatency        | Timespan | Durée facultative pour limiter la requête aux enregistrements qui ont été ingérés uniquement avant cette période (par rapport à l’heure actuelle). Cette propriété est utile si, par exemple, la requête effectue des agrégations/jointures et que vous souhaitez vous assurer que tous les enregistrements appropriés ont déjà été ingérés avant d’exécuter l’exportation.

En plus de ce qui précède, toutes les propriétés prises en charge dans la [commande exporter vers une table externe](export-data-to-an-external-table.md) sont prises en charge dans la commande d’exportation continue. 

## <a name="example"></a>Exemple

```kusto
.create-or-alter continuous-export MyExport
over (T)
to table ExternalBlob
with
(intervalBetweenRuns=1h, 
 forcedLatency=10m, 
 sizeLimit=104857600)
<| T
```

| Nom     | ExternalTableName | Requête | ForcedLatency | IntervalBetweenRuns | CursorScopedTables         | ExportProperties                   |
|----------|-------------------|-------|---------------|---------------------|----------------------------|------------------------------------|
| MyExport | ExternalBlob      | S     | 00:10:00      | 01:00:00            | [<br>  "['DB']. ['] "<br>] | {<br>  « SizeLimit » : 104857600<br>} |