---
title: Traduction de requêtes SQL en langage Kusto - Azure Data Explorer
description: Cet article décrit la traduction de requêtes SQL en langage Kusto dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.localizationpriority: high
ms.openlocfilehash: 16a0c6ef7064ae5e16fb649f3cd089196533ec45
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513367"
---
# <a name="sql-to-kusto-cheat-sheet"></a>Aide-mémoire SQL vers Kusto

Kusto prend en charge un sous-ensemble du langage SQL. Pour obtenir la liste complète des fonctionnalités non prises en charge, consultez la liste des [problèmes SQL connus](../api/tds/sqlknownissues.md).

Le langage principal utilisé pour l’interaction avec Kusto est KQL (Kusto Query Language). Pour faciliter la transition et l’apprentissage, vous pouvez utiliser Kusto pour convertir des requêtes SQL en KQL. Envoyez une requête SQL à Kusto, en la faisant précéder du verbe « EXPLAIN ».

Par exemple :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
EXPLAIN 
SELECT COUNT_BIG(*) as C FROM StormEvents 
```

|Requête|
|---|
|StormEvents<br>| summarize C=count()<br>| projet C|

## <a name="sql-to-kusto-cheat-sheet"></a>Aide-mémoire SQL vers Kusto

Le tableau ci-dessous montre des exemples de requêtes SQL et leurs équivalents KQL.

|Category |Requête SQL |Requête Kusto
|---|---|---
Sélectionnez les données d’une table |<code>SELECT * FROM dependencies</code> | <code>dependencies</code>
--|<code>SELECT name, resultCode FROM dependencies</code> |<code>dependencies &#124; project name, resultCode</code>
--|<code>SELECT TOP 100 * FROM dependencies</code> | <code>dependencies &#124; take 100</code>
Évaluation de la valeur null |<code>SELECT * FROM dependencies<br>WHERE resultCode IS NOT NULL</code> | <code>dependencies<br>&#124; where isnotnull(resultCode)</code>
Opérateurs de comparaison (date) |<code>SELECT * FROM dependencies<br>WHERE timestamp > getdate()-1</code>| <code>dependencies<br>&#124; where timestamp > ago(1d)</code>
--|<code>SELECT * FROM dependencies<br>WHERE timestamp BETWEEN ... AND ...</code> |<code>dependencies<br>&#124; where timestamp > datetime(2016-10-01)<br>&nbsp;&nbsp;and timestamp <= datetime(2016-11-01)</code>
Opérateurs de comparaison (chaîne)|<code>SELECT * FROM dependencies<br>WHERE type = "Azure blob"</code> |<code>dependencies<br>&#124; where type == "Azure blob"</code>
--|<code>-- substring<br>SELECT * FROM dependencies<br>WHERE type like "%blob%"</code> |<code>// substring<br>dependencies<br>&#124; where type contains "blob"</code>
--|<code>-- wildcard<br>SELECT * FROM dependencies<br>WHERE type like "Azure%"</code> |<code>// wildcard<br>dependencies<br>&#124; where type startswith "Azure"<br>// or<br>dependencies<br>&#124; where type matches regex "^Azure.*"</code>
Comparaison (booléenne) |<code>SELECT * FROM dependencies<br>WHERE !(success)</code> |<code>dependencies<br>&#124; where success == "False"</code>
Distinct |<code>SELECT DISTINCT name, type  FROM dependencies</code> |<code>dependencies<br>&#124; summarize by name, type</code>
Regroupement, agrégation |<code>SELECT name, AVG(duration) FROM dependencies<br>GROUP BY name</code> |<code>dependencies<br>&#124; summarize avg(duration) by name</code>
Alias de colonne, étendre |<code>SELECT operationName as Name, AVG(duration) as AvgD FROM dependencies<br>GROUP BY name</code> |<code>dependencies<br>&#124; summarize AvgD = avg(duration) by Name=operationName</code>
Classement |<code>SELECT name, timestamp FROM dependencies<br>ORDER BY timestamp ASC</code> |<code>dependencies<br>&#124; project name, timestamp<br>&#124; order by timestamp asc nulls last</code>
N premiers par mesure |<code>SELECT TOP 100 name, COUNT(*) as Count FROM dependencies<br>GROUP BY name<br>ORDER BY Count DESC</code> |<code>dependencies<br>&#124; summarize Count = count() by name<br>&#124; top 100 by Count desc</code>
Union |<code>SELECT * FROM dependencies<br>UNION<br>SELECT * FROM exceptions</code> |<code>union dependencies, exceptions</code>
--|<code>SELECT * FROM dependencies<br>WHERE timestamp > ...<br>UNION<br>SELECT * FROM exceptions<br>WHERE timestamp > ...</code> |<code>dependencies<br>&#124; where timestamp > ago(1d)<br>&#124; union<br>&nbsp;&nbsp;(exceptions<br>&nbsp;&nbsp;&#124; where timestamp > ago(1d))</code>
Join |<code>SELECT * FROM dependencies <br>LEFT OUTER JOIN exception<br>ON dependencies.operation_Id = exceptions.operation_Id</code> |<code>dependencies<br>&#124; join kind = leftouter<br>&nbsp;&nbsp;(exceptions)<br>on $left.operation_Id == $right.operation_Id</code>
Requêtes imbriquées |<code>SELECT * FROM dependencies<br>WHERE resultCode == <br>(SELECT TOP 1 resultCode FROM dependencies<br>WHERE resultId = 7<br>ORDER BY timestamp DESC)</code> |<code>dependencies<br>&#124; where resultCode == toscalar(<br>&nbsp;&nbsp;dependencies<br>&nbsp;&nbsp;&#124; where resultId == 7<br>&nbsp;&nbsp;&#124; top 1 by timestamp desc<br>&nbsp;&nbsp;&#124; project resultCode)</code>
Having |<code>SELECT COUNT(\*) FROM dependencies<br>GROUP BY name<br>HAVING COUNT(\*) > 3</code> |<code>dependencies<br>&#124; summarize Count = count() by name<br>&#124; where Count > 3</code>|
