---
title: Opérateur render - Azure Data Explorer
description: Cet article décrit l’opérateur render dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/29/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 5670f3f9c7aa8b3d6b10f88433d19246e2daf6d6
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95783333"
---
# <a name="render-operator"></a>render, opérateur

Indique à l’agent utilisateur d’afficher les résultats de la requête d’une manière particulière.

```kusto
range x from 0.0 to 2*pi() step 0.01 | extend y=sin(x) | render linechart
```

> [!NOTE]
> * L’opérateur render doit être le dernier opérateur dans la requête et il doit être utilisé uniquement avec les requêtes qui produisent un seul résultat de tabular data stream.
> * L’opérateur render ne modifie pas les données. Il injecte une annotation (« visualisation ») dans les propriétés étendues du résultat. L’annotation contient les informations fournies par l’opérateur dans la requête.
> * L’interprétation des informations de visualisation est effectuée par l’agent utilisateur. Différents agents (tels que Kusto.Explorer, Kusto.WebExplorer) peuvent prendre en charge différentes visualisations.

## <a name="syntax"></a>Syntaxe

*T* `|` `render` *Visualization* [`with` `(` *PropertyName* `=` *PropertyValue* [`,` ...] `)`]

Où :

* La *visualisation* indique le type de visualisation à utiliser. Les valeurs prises en charge sont les suivantes :

::: zone pivot="azuredataexplorer"

|*Visualisation*     |Description|
|--------------------|-|
| `anomalychart`     | Semblable au graphique temporel, mais [met en évidence les anomalies](./samples.md#get-more-from-your-data-by-using-kusto-with-machine-learning) à l’aide de la fonction [series_decompose_anomalies](./series-decompose-anomaliesfunction.md). |
| `areachart`        | Graphique en aires. La première colonne correspond à l’axe x et doit être une colonne numérique. Les autres colonnes numériques correspondent aux axes y. |
| `barchart`         | La première colonne est l’axe x et peut être de type Texte, DateTime ou Numérique. Les autres colonnes sont numériques, affichées sous forme de bandes horizontales.|
| `card`             | Le premier enregistrement de résultat est traité comme un ensemble de valeurs scalaires et s’affiche sous la forme d’une carte. |
| `columnchart`      | Comme `barchart` avec des bandes verticales au lieu de bandes horizontales.|
| `ladderchart`      | Les deux dernières colonnes sont l’axe x. Les autres colonnes sont l’axe y.|
| `linechart`        | graphique linéaire. La première colonne correspond à l’axe x et doit être une colonne numérique. Les autres colonnes numériques correspondent aux axes y. |
| `piechart`         | la première colonne correspond à l’axe des couleurs, la deuxième est de type numérique. |
| `pivotchart`       | Affiche un tableau croisé dynamique et un graphique. L’utilisateur peut sélectionner de manière interactive des données, des colonnes, des lignes et divers types de graphiques. |
| `scatterchart`     | Graphique des points. La première colonne correspond à l’axe x et doit être une colonne numérique. Les autres colonnes numériques correspondent aux axes y. |
| `stackedareachart` | Graphique en aires empilées. La première colonne correspond à l’axe x et doit être une colonne numérique. Les autres colonnes numériques correspondent aux axes y. |
| `table`            | Par défaut, les résultats s’affichent sous forme de table.|
| `timechart`        | graphique linéaire. La première colonne est l’axe X et doit être de type datetime. Les autres colonnes numériques correspondent aux axes y. Il existe une colonne de type chaîne dont les valeurs sont utilisées pour « regrouper » les colonnes numériques et pour créer des lignes différentes dans le graphique (les autres colonnes de type chaîne sont ignorées). |
| `timepivot`        | Navigation interactive sur la chronologie des événements (pivotement sur l’axe du temps)|

::: zone-end

::: zone pivot="azuremonitor"

|*Visualisation*     |Description|
|--------------------|-|
| `areachart`        | Graphique en aires. La première colonne correspond à l’axe x et doit être une colonne numérique. Les autres colonnes numériques correspondent aux axes y. |
| `barchart`         | La première colonne est l’axe x et peut être de type Texte, DateTime ou Numérique. Les autres colonnes sont numériques, affichées sous forme de bandes horizontales.|
| `columnchart`      | Comme `barchart` avec des bandes verticales au lieu de bandes horizontales.|
| `piechart`         | la première colonne correspond à l’axe des couleurs, la deuxième est de type numérique. |
| `scatterchart`     | Graphique des points. La première colonne correspond à l’axe x et doit être une colonne numérique. Les autres colonnes numériques correspondent aux axes y. |
| `table`            | Par défaut, les résultats s’affichent sous forme de table.|
| `timechart`        | graphique linéaire. La première colonne est l’axe X et doit être de type datetime. Les autres colonnes numériques correspondent aux axes y. Il existe une colonne de type chaîne dont les valeurs sont utilisées pour « regrouper » les colonnes numériques et pour créer des lignes différentes dans le graphique (les autres colonnes de type chaîne sont ignorées).|

::: zone-end

* *PropertyName*/*PropertyValue* indique les informations supplémentaires à utiliser lors du rendu.
  Toutes les propriétés sont facultatives. Les propriétés prises en charge sont :

::: zone pivot="azuredataexplorer"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`accumulate`  |Indique si la valeur de chaque mesure est ajoutée à tous ses prédécesseurs. (`true` ou `false`)|
|`kind`        |Élaboration plus poussée du type de visualisation. Voir ci-dessous.                         |
|`legend`      |Indique s’il faut afficher une légende ou non (`visible` ou `hidden`).                       |
|`series`      |Liste de colonnes (délimitées par des virgules) dont les valeurs par enregistrement combinées définissent la série à laquelle l’enregistrement appartient.|
|`ymin`        |Valeur minimale à afficher sur l’axe y.                                      |
|`ymax`        |Valeur maximale à afficher sur l’axe y.                                      |
|`title`       |Titre de la visualisation (de type `string`).                                |
|`xaxis`       |Comment mettre à l’échelle l’axe x (`linear` ou `log`).                                      |
|`xcolumn`     |Quelle colonne dans le résultat est utilisée pour l’axe x.                                |
|`xtitle`      |Titre de l’axe x (de type `string`).                                       |
|`yaxis`       |Comment mettre à l’échelle l’axe y (`linear` ou `log`).                                      |
|`ycolumns`    |Liste de colonnes séparées par des virgules qui se composent des valeurs fournies par valeur de la colonne x.|
|`ysplit`      |Comment fractionner plusieurs visualisations. Voir ci-dessous.                               |
|`ytitle`      |Titre de l’axe y (de type `string`).                                       |
|`anomalycolumns`|Propriété pertinente uniquement pour `anomalychart`. Liste délimitée par des virgules des colonnes qui sont considérées comme des séries d’anomalies et affichées comme des points sur le graphique|

::: zone-end

::: zone pivot="azuremonitor"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`kind`        |Élaboration plus poussée du type de visualisation. Voir ci-dessous.                         |
|`series`      |Liste de colonnes (délimitées par des virgules) dont les valeurs par enregistrement combinées définissent la série à laquelle l’enregistrement appartient.|
|`title`       |Titre de la visualisation (de type `string`).                                |
|`yaxis`       |Comment mettre à l’échelle l’axe y (`linear` ou `log`).                                      |

::: zone-end

Certaines visualisations peuvent être élaborées en fournissant la propriété `kind`.
Celles-ci sont les suivantes :

|*Visualisation*|`kind`             |Description                        |
|---------------|-------------------|-----------------------------------|
|`areachart`    |`default`          |Chaque « zone » est autonome.     |
|               |`unstacked`        |Comme pour `default`.                 |
|               |`stacked`          |Empilez les « zones » à droite.        |
|               |`stacked100`       |Empilez les « zones » à droite et étendez chacune d’elles à la même largeur que les autres.|
|`barchart`     |`default`          |Chaque « barre » est autonome.      |
|               |`unstacked`        |Comme pour `default`.                 |
|               |`stacked`          |Empilez les « barres ».                      |
|               |`stacked100`       |Empilez les « barres » et étendez chacune d’elles à la même largeur que les autres.|
|`columnchart`  |`default`          |Chaque « colonne » est autonome.   |
|               |`unstacked`        |Comme pour `default`.                 |
|               |`stacked`          |Empilez les « colonnes » les unes sur les autres.|
|               |`stacked100`       |Empilez les « colonnes » et étendez chacune d’elles à la même hauteur que les autres.|
|`scatterchart` |`map`              |Les colonnes attendues sont [Longitude, Latitude] ou Point JSON. La colonne de série est facultative.|
|`piechart`     |`map`              |Les colonnes attendues sont de type [Longitude, Latitude] ou Point GeoJSON, Axe des couleurs et Numérique. Pris en charge dans Kusto Explorer Desktop.|

::: zone pivot="azuredataexplorer"

Certaines visualisations prennent en charge le fractionnement en plusieurs valeurs d’axe y :

|`ysplit`  |Description                                                       |
|----------|------------------------------------------------------------------|
|`none`    |Un seul axe y est affiché pour toutes les données de la série. (Par défaut)       |
|`axes`    |Un graphique unique s’affiche avec plusieurs axes y (un par série).|
|`panels`  |Un graphique est rendu pour chaque valeur `ycolumn` (jusqu’à une certaine limite).|

::: zone-end

> [!NOTE]
> Le modèle de données de l’opérateur render examine les données tabulaires comme s’il s’agissait de trois types de colonnes :
>
> * Colonne de l’axe des x (indiquée par la propriété `xcolumn`).
> * Les colonnes de la série (n’importe quel nombre de colonnes indiqué par la propriété `series`.) Pour chaque enregistrement, les valeurs combinées de ces colonnes définissent une série unique et le graphique comporte autant de séries qu’il y a de valeurs combinées distinctes.
> * Colonnes de l’axe y (n’importe quel nombre de colonnes indiquées par la propriété `ycolumns`).
  Pour chaque enregistrement, la série comporte autant de mesures (« points » dans le graphique) qu’il y a de colonnes d’axe y.

> [!TIP]
> 
> * Utilisez `where`, `summarize` et `top` pour limiter le volume que vous affichez.
> * Triez les données pour définir l’ordre de l’axe x.
> * Les agents utilisateurs sont libres de « deviner » la valeur des propriétés qui ne sont pas spécifiées par la requête. En particulier, les colonnes « sans intérêt » dans le schéma du résultat peuvent être traduites par des erreurs. Essayez d’exclure ces colonnes lorsque cela se produit. 

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range x from -2 to 2 step 0.1
| extend sin = sin(x), cos = cos(x)
| extend x_sign = iif(x > 0, "x_pos", "x_neg")
| extend sum_sign = iif(sin + cos > 0, "sum_pos", "sum_neg")
| render linechart with  (ycolumns = sin, cos, series = x_sign, sum_sign)
```

::: zone pivot="azuredataexplorer"

[Exemples de rendu dans le tutoriel](./tutorial.md#displaychartortable)

[Détection des anomalies](./samples.md#get-more-from-your-data-by-using-kusto-with-machine-learning)

::: zone-end
