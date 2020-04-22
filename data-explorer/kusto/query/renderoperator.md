---
title: opérateur de rendu - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de rendu dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/29/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 645860405a165f3304f655e42d2ced9b8777b8d0
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765476"
---
# <a name="render-operator"></a>render, opérateur

Demande à l’agent utilisateur de rendre les résultats de la requête d’une manière particulière.

```kusto
range x from 0.0 to 2*pi() step 0.01 | extend y=sin(x) | render linechart
```

> [!NOTE]
> * L’opérateur de rendu doit être le dernier opérateur de la requête, et utilisé uniquement avec des requêtes qui produisent un seul résultat de flux de données tabulaire.
> * L’opérateur de rendu ne modifie pas les données. Il injecte une annotation (« Visualisation ») dans les propriétés étendues du résultat. L’annotation contient les informations fournies par l’opérateur dans la requête.
> * L’interprétation des informations de visualisation est effectuée par l’agent utilisateur. Différents agents (tels que Kusto.Explorer,Kusto.WebExplorer) pourraient prendre en charge différentes visualisations.

**Syntaxe**

*T* `|` `with` `(` *PropertyName* `=` `,` *PropertyValue* *Visualization* Visualisation [ PropertyName PropertyValue [ ...] `render` `)`]

Où :

* *La visualisation* indique le type de visualisation à utiliser. Les valeurs prises en charge sont les suivantes :

::: zone pivot="azuredataexplorer"

|*Visualisation*     |Description|
|--------------------|-|
| `anomalychart`     | Semblable à timechart, mais [met en évidence les anomalies](./samples.md#get-more-out-of-your-data-in-kusto-using-machine-learning) en utilisant [series_decompose_anomalies](./series-decompose-anomaliesfunction.md) fonction. |
| `areachart`        | Graphique de zone. La première colonne est l’axe x et doit être une colonne numérique. D’autres colonnes numériques sont des y-axes. |
| `barchart`         | La première colonne est l’axe x et peut être texte, date ou numérique. D’autres colonnes sont numériques, affichées sous forme de bandes horizontales.|
| `card`             | Le premier enregistrement de résultat est traité comme un ensemble de valeurs scalaires et montre comme une carte. |
| `columnchart`      | Comme `barchart` avec des bandes verticales au lieu de bandes horizontales.|
| `ladderchart`      | Les deux dernières colonnes sont l’axe x, d’autres colonnes sont y-axe.|
| `linechart`        | graphique linéaire. La première colonne est x-axe, et doit être une colonne numérique. D’autres colonnes numériques sont des y-axes. |
| `piechart`         | la première colonne correspond à l’axe des couleurs, la deuxième est de type numérique. |
| `pivotchart`       | Affiche une table pivot et un graphique. L’utilisateur peut sélectionner de manière interactive des données, des colonnes, des lignes et différents types de graphiques. |
| `scatterchart`     | Graphique de points. La première colonne est x-axe et doit être une colonne numérique. D’autres colonnes numériques sont des y-axes. |
| `stackedareachart` | Graphique de zone empilé. La première colonne est x-axe, et doit être une colonne numérique. D’autres colonnes numériques sont des y-axes. |
| `table`            | Défaut - les résultats sont affichés sous forme de table.|
| `timechart`        | graphique linéaire. La première colonne est l’axe X et doit être de type datetime. D’autres colonnes (numériques) sont des y-axes. Il y a une colonne de chaîne dont les valeurs sont utilisées pour « regrouper » les colonnes numériques et créer différentes lignes dans le graphique (d’autres colonnes de chaîne sont ignorées). |
| `timepivot`        | Navigation interactive sur la ligne de temps des événements (pivotage sur l’axe du temps)|

::: zone-end

::: zone pivot="azuremonitor"

|*Visualisation*     |Description|
|--------------------|-|
| `areachart`        | Graphique de zone. La première colonne est l’axe x et doit être une colonne numérique. D’autres colonnes numériques sont des y-axes. |
| `barchart`         | La première colonne est l’axe x et peut être texte, date ou numérique. D’autres colonnes sont numériques, affichées sous forme de bandes horizontales.|
| `columnchart`      | Comme `barchart` avec des bandes verticales au lieu de bandes horizontales.|
| `piechart`         | la première colonne correspond à l’axe des couleurs, la deuxième est de type numérique. |
| `scatterchart`     | Graphique de points. La première colonne est l’axe x et doit être une colonne numérique. D’autres colonnes numériques sont des y-axes. |
| `table`            | Défaut - les résultats sont affichés sous forme de table.|
| `timechart`        | graphique linéaire. La première colonne est l’axe X et doit être de type datetime. D’autres colonnes (numériques) sont des y-axes. Il y a une colonne de chaîne dont les valeurs sont utilisées pour « regrouper » les colonnes numériques et créer différentes lignes dans le graphique (d’autres colonnes de chaîne sont ignorées).|

::: zone-end

* *PropertyName*/*PropertyValue* indique des informations supplémentaires à utiliser lors du rendu.
  Toutes les propriétés sont facultatives. Les propriétés prises en charge sont :

::: zone pivot="azuredataexplorer"

|*Propertyname*|*Propertyvalue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`accumulate`  |Si la valeur de chaque mesure est ajoutée à tous ses prédécesseurs. (`true` `false`( ou )|
|`kind`        |Élaboration supplémentaire du type de visualisation. Voir ci-dessous.                         |
|`legend`      |Que ce soit pour`visible` afficher `hidden`une légende ou non ( ou ).                       |
|`series`      |Liste de colonnes comma-délimitées dont les valeurs combinées par enregistrement définissent la série à laquelle appartient l’enregistrement.|
|`ymin`        |La valeur minimale à afficher sur l’axe Y.                                      |
|`ymax`        |La valeur maximale à afficher sur l’axe Y.                                      |
|`title`       |Le titre de la visualisation `string`(de type ).                                |
|`xaxis`       |Comment mettre à l’échelle `log`l’axe x (ou`linear` ).                                      |
|`xcolumn`     |Quelle colonne dans le résultat est utilisée pour l’axe x.                                |
|`xtitle`      |Le titre de l’axe `string`x (de type).                                       |
|`yaxis`       |Comment mettre à l’échelle l’axe y (`linear` ou `log`).                                      |
|`ycolumns`    |Liste de colonnes comma-délimitées qui se composent des valeurs fournies par valeur de la colonne x.|
|`ysplit`      |Comment diviser plusieurs la visualisation. Voir ci-dessous.                               |
|`ytitle`      |Le titre de l’axe `string`y (de type ).                                       |
|`anomalycolumns`|Propriété pertinente `anomalychart`uniquement pour . Liste des colonnes comma-délimitées qui seront considérées comme des séries d’anomalies et affichées comme points sur le graphique|

::: zone-end

::: zone pivot="azuremonitor"

|*Propertyname*|*Propertyvalue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`kind`        |Élaboration supplémentaire du type de visualisation. Voir ci-dessous.                         |
|`series`      |Liste de colonnes comma-délimitées dont les valeurs combinées par enregistrement définissent la série à laquelle appartient l’enregistrement.|
|`title`       |Le titre de la visualisation `string`(de type ).                                |
|`yaxis`       |Comment mettre à l’échelle l’axe y (`linear` ou `log`).                                      |

::: zone-end

Certaines visualisations peuvent être approfondies `kind` en fournissant la propriété.
Ces règles sont les suivantes :

|*Visualisation*|`kind`             |Description                        |
|---------------|-------------------|-----------------------------------|
|`areachart`    |`default`          |Chaque "zone" se tient seule.     |
|               |`unstacked`        |Identique à `default`.                 |
|               |`stacked`          |Empilez les « zones » vers la droite.        |
|               |`stacked100`       |Empilez les « zones » à droite et étendez-les à la même largeur que les autres.|
|`barchart`     |`default`          |Chaque "bar" se tient tout seul.      |
|               |`unstacked`        |Identique à `default`.                 |
|               |`stacked`          |Empilez les "barres".                      |
|               |`stacked100`       |Empilez "bard" et étirer chacun à la même largeur que les autres.|
|`columnchart`  |`default`          |Chaque "colonne" se tient seule.   |
|               |`unstacked`        |Identique à `default`.                 |
|               |`stacked`          |Empilez les « colonnes » l’une au sommet de l’autre.|
|               |`stacked100`       |Empilez les « colonnes » et étendez-les à la même hauteur que les autres.|
|`piechart`     |`map`              |Les colonnes attendues sont [Longitude, Latitude] ou GeoJSON point, l’axe de couleur et numérique. Pris en charge dans le bureau Kusto Explorer.|
|`scatterchart` |`map`              |Les colonnes attendues sont [Longitude, Latitude] ou GeoJSON point. La colonne de série est facultative. Pris en charge dans le bureau Kusto Explorer.|

::: zone pivot="azuredataexplorer"

Certaines visualisations favorisent la division en plusieurs valeurs y-axe :

|`ysplit`  |Description                                                       |
|----------|------------------------------------------------------------------|
|`none`    |Un seul y-axe est affiché pour toutes les données de série. (Par défaut)       |
|`axes`    |Un seul graphique est affiché avec plusieurs y-axes (un par série).|
|`panels`  |Un graphique est rendu `ycolumn` pour chaque valeur (jusqu’à une certaine limite).|

::: zone-end

> [!NOTE]
> Le modèle de données de l’opérateur de rendu examine les données tabulaires comme s’il avait trois types de colonnes :
>
> * La colonne d’axe `xcolumn` x (indiquée par la propriété).
> * Les colonnes de la série `series` (tout nombre de colonnes indiquées par la propriété.) Pour chaque enregistrement, les valeurs combinées de ces colonnes définissent une seule série, et le graphique a autant de séries qu’il y a des valeurs combinées distinctes.
> * Les colonnes d’axe y (tout nombre de colonnes indiquées par la `ycolumns` propriété).
  Pour chaque enregistrement, la série a autant de mesures ("points" dans le graphique) qu’il y a des colonnes y-axe.

> [!TIP]
> 
> * `where`Utilisez, `summarize` `top` et pour limiter le volume que vous affichez.
> * Trier les données pour définir l’ordre de l’axe x.
> * Les agents utilisateurs sont libres de « deviner » la valeur des propriétés qui ne sont pas spécifiées par la requête. En particulier, avoir des colonnes « inintéressantes » dans le schéma du résultat pourrait se traduire par leur erreur de deviner. Essayez de projeter ces colonnes lorsque cela se produit. 

**Exemple**

```kusto
range x from -2 to 2 step 0.1
| extend sin = sin(x), cos = cos(x)
| extend x_sign = iif(x > 0, "x_pos", "x_neg")
| extend sum_sign = iif(sin + cos > 0, "sum_pos", "sum_neg")
| render linechart with  (ycolumns = sin, cos, series = x_sign, sum_sign)
```

::: zone pivot="azuredataexplorer"

[Rendre des exemples dans le tutoriel](./tutorial.md#render-display-a-chart-or-table).

[Détection d’anomalies](./samples.md#get-more-out-of-your-data-in-kusto-using-machine-learning)

::: zone-end
