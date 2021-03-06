---
title: plug-in session_count-Azure Explorateur de données
description: Cet article décrit session_count plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0790a3ab173bc653cbd3c4c15b3f28e5a0c70cd5
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252901"
---
# <a name="session_count-plugin"></a>plug-in session_count

Calcule le nombre de sessions en fonction de la colonne ID sur une chronologie.

```kusto
T | evaluate session_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 1min, 30min, dim1, dim2, dim3)
```

## <a name="syntax"></a>Syntaxe

*T* `| evaluate` `session_count(` *IdColumn* `,` *TimelineColumn* `,` *Start* `,` *end* `,` *bin* `,` *LookBackWindow* [ `,` *dim1* `,` *dim2* `,` ...]`)`

## <a name="arguments"></a>Arguments

* *T*: expression tabulaire d’entrée.
* *IdColumn*: nom de la colonne avec des valeurs d’ID qui représentent l’activité de l’utilisateur. 
* *TimelineColumn*: nom de la colonne qui représente la chronologie.
* *Start*: scalaire avec la valeur de la période de démarrage de l’analyse.
* *End*: scalaire avec la valeur de la période de fin de l’analyse.
* *Bin*: valeur constante scalaire de l’étape d’analyse de la session.
* *LookBackWindow*: valeur constante scalaire représentant la période de lookback de session. Si l’ID de `IdColumn` s’affiche dans une fenêtre de temps dans `LookBackWindow` , la session est considérée comme étant existante. Si l’ID ne s’affiche pas, la session est considérée comme nouvelle.
* *dim1*, *dim2*,... : (facultatif) liste des colonnes de dimensions qui découpent le calcul du nombre de sessions.

## <a name="returns"></a>Retours

Retourne une table qui contient les valeurs du nombre de sessions pour chaque période de chronologie et pour chaque combinaison de dimensions existante.

Le schéma de la table de sortie est le suivant :

|*TimelineColumn*|dim1|..|dim_n|count_sessions|
|---|---|---|---|---|--|--|--|--|--|--|
|type : à partir de *TimelineColumn*|..|..|..|long|


## <a name="examples"></a>Exemples

Pour cet exemple, les données sont déterministes et nous utilisons une table avec deux colonnes :
- Chronologie : nombre en cours de 1 à 10 000
- ID : ID de l’utilisateur, de 1 à 50

`Id` apparaît dans l' `Timeline` emplacement spécifique s’il s’agit d’un séparateur de `Timeline` (chronologie% ID = = 0).

Un événement avec apparaît `Id==1` à n’importe quel `Timeline` emplacement, un événement avec `Id==2` à chaque deuxième `Timeline` emplacement, et ainsi de suite.

Voici quelques 20 lignes de données :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _data = range Timeline from 1 to 10000 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// Look on few lines of the data
_data
| order by Timeline asc, Id asc
| limit 20
```

|Durée|Id|
|---|---|
|1|1|
|2|1|
|2|2|
|3|1|
|3|3|
|4|1|
|4|2|
|4|4|
|5|1|
|5|5|
|6|1|
|6|2|
|6|3|
|6|6|
|7|1|
|7|7|
|8|1|
|8|2|
|8|4|
|8|8|

Nous allons définir une session dans les conditions suivantes : la session considérée comme active tant que l’utilisateur ( `Id` ) apparaît au moins une fois au cours d’une période de 100 de temps, tandis que la fenêtre de recherche de session est 41 créneaux horaires.

La requête suivante affiche le nombre de sessions actives en fonction de la définition ci-dessus.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart 
```

:::image type="content" source="images/session-count-plugin/example-session-count.png" alt-text="Exemple de nombre de sessions" border="false":::
