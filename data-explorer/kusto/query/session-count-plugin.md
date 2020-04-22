---
title: session_count plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit session_count plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 76ce6ca3be1859aba0a94ae155c60342be3f1ad1
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663150"
---
# <a name="session_count-plugin"></a>plug-in session_count

Calcule le nombre de sessions en fonction de la colonne d’identification sur une chronologie.

```kusto
T | evaluate session_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 1min, 30min, dim1, dim2, dim3)
```

**Syntaxe**

*T* `| evaluate` `,` *Start* `,` *End* `,` *Bin* `,` `,` `,` `,` *IdColumn* `,` *dim2* *dim1* *LookBackWindow* *TimelineColumn* IdColumn TimelineColumn Start End Bin LookBackWindow [ dim1 dim2 ...] `session_count(``)`

**Arguments**

* *T*: L’expression tabulaire d’entrée.
* *IdColumn*: Le nom de la colonne avec des valeurs d’identification qui représentent l’activité de l’utilisateur. 
* *TimelineColumn*: Le nom de la colonne qui représente la chronologie.
* *Début*: Scalar avec la valeur de la période de début d’analyse.
* *Fin*: Scalar avec valeur de la période de fin d’analyse.
* *Bin*: valeur constante scalaire de la période d’étape d’analyse de session.
* *LookBackWindow*: valeur constante scalaire représentant la période de retour de session. Si l’id d’apparaît dans une fenêtre de `IdColumn` temps à l’intérieur `LookBackWindow` - la session est considérée comme une existante, si elle ne le fait pas - la session est considérée comme nouvelle.
* *dim1*, *dim2*, ...: (facultatif) liste des colonnes de dimensions qui tranchent le calcul du nombre de session.

**Retourne**

Retourne un tableau qui a les valeurs de comptage de session pour chaque période de chronologie et pour chaque combinaison de dimensions existantes.

Le schéma de table de sortie est :

|*TimelineColumn (en)*|dim1|..|dim_n|count_sessions|
|---|---|---|---|---|--|--|--|--|--|--|
|type: à partir de *TimelineColumn*|..|..|..|long|


**Exemples**


Pour le plaisir de l’exemple, nous rendrons les données déterministes - un tableau avec deux colonnes:
- Chronologie : un nombre courant de 1 à 10 000
- Id: Id de l’utilisateur de 1 à 50

`Id`s’il `Timeline` s’agit d’un `Timeline` diviseur (Timeline % Id 0).

Cela signifie que `Id==1` l’événement `Timeline` avec apparaîtra `Id==2` à `Timeline` n’importe quelle fente, événement avec à chaque fente seconde, et ainsi de suite.

Voici quelques 20 lignes des données :

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

|Chronologie|Id|
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

Définissons une session dans les termes suivants: session considérée`Id`comme active tant que l’utilisateur ( ) apparaît au moins une fois à un délai de 100 créneaux horaires, tandis que la fenêtre de recherche de session est de 41 créneaux horaires.

La requête suivante montre le nombre de sessions actives selon la définition ci-dessus.

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

:::image type="content" source="images/queries/example-session-count.png" alt-text="Exemple de nombre de sessions":::
