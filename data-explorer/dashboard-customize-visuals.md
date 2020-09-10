---
title: Personnaliser les visuels du tableau de bord Azure Data Explorer
description: Personnalisez aisément les visuels de votre tableau de bord Azure Data Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/25/2020
ms.openlocfilehash: 22463307df0358228469fe480f5578222bed52bf
ms.sourcegitcommit: a4779e31a52d058b07b472870ecd2b8b8ae16e95
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379732"
---
# <a name="customize-azure-data-explorer-dashboard-visuals"></a>Personnaliser les visuels du tableau de bord Azure Data Explorer

Les visuels sont essentiels dans n’importe quel tableau de bord Azure Data Explorer. Ce document détaille les différents types de visuels et décrit les différentes options à la disposition des utilisateurs du tableau de bord pour personnaliser leurs visuels.

> [!NOTE]
> La personnalisation des visuels est disponible en mode d’édition pour les éditeurs de tableaux de bord.

## <a name="prerequisites"></a>Prérequis

[Visualiser des données avec des tableaux de bord Azure Data Explorer](azure-data-explorer-dashboards.md)

## <a name="types-of-visuals"></a>Types de visuels

Azure Data Explorer prend en charge différents types de visuels. Cette section décrit les différents types de visuels et les colonnes de données nécessaires dans votre jeu de résultats pour tracer ces visuels.

### <a name="table"></a>Table de charge de travail

:::image type="content" source="media/dashboard-customize-visuals/table.png" alt-text="type de visuel de tableau":::

Par défaut, les résultats s’affichent sous forme de tableau. Le visuel de tableau est idéal pour la présentation de données détaillées ou complexes.

### <a name="bar-chart"></a>Graphique à barres

:::image type="content" source="media/dashboard-customize-visuals/bar-chart.png" alt-text="type de visuel de graphique à barres":::

Le visuel de graphique à barres a besoin d’au moins deux colonnes dans le résultat de la requête. Par défaut, la première colonne est utilisée pour l’axe des ordonnées (y). Cette colonne peut contenir des types de données textuels, DateHeure ou numériques. Les autres colonnes sont utilisées pour l’axe des abscisses (x) et contiennent des types de données numériques à afficher sous forme de lignes horizontales. Les graphiques à barres sont principalement utilisés pour comparer des valeurs discrètes numériques et nominales, la longueur de chaque ligne représentant sa valeur.

### <a name="column-chart"></a>Histogramme

:::image type="content" source="media/dashboard-customize-visuals/column-chart.png" alt-text="type de visuel d’histogramme":::

Le visuel d’histogramme a besoin d’au moins deux colonnes dans le résultat de la requête. Par défaut, la première colonne est utilisée pour l’axe des abscisses (x). Cette colonne peut contenir des types de données textuels, DateHeure ou numériques. Les autres colonnes sont utilisées pour l’axe des ordonnées (y) et contiennent des types de données numériques à afficher sous forme de lignes verticales. Les histogrammes permettent de comparer des éléments de sous-catégories spécifiques dans une plage de catégorie principale, la longueur de chaque ligne représentant sa valeur.

### <a name="area-chart"></a>Graphique en aires

:::image type="content" source="media/dashboard-customize-visuals/area-chart.png" alt-text="type de visuel de graphique en aires":::

Le visuel de graphique en aires affiche une relation de série chronologique. La première colonne de la requête doit être numérique et elle est utilisée pour l’axe des abscisses (x). Les autres colonnes numériques correspondent aux axes y. Contrairement aux graphiques en courbes, les graphiques en aires représentent également visuellement un volume. Les graphiques en aires sont la solution idéale pour indiquer le changement entre différents jeux de données.

### <a name="line-chart"></a>Graphique en courbes

:::image type="content" source="media/dashboard-customize-visuals/line-chart.png" alt-text="type de visuel de graphique en courbes":::

Le visuel de graphique en courbes est le type de graphique le plus élémentaire. La première colonne de la requête doit être numérique et est utilisée pour l’axe des abscisses (x). Les autres colonnes numériques correspondent aux axes y. Les graphiques en courbes effectuent le suivi des modifications sur de courtes ou longues périodes. Dans le cas de petites variations, les graphiques en courbes sont plus utiles que les graphiques à barres.

### <a name="stat"></a>Stat

:::image type="content" source="media/dashboard-customize-visuals/stat.png" alt-text="type de visuel stat":::

Le visuel stat montre un seul élément. Si la sortie contient plusieurs colonnes et lignes, le visuel stat montre le premier élément de la première colonne. Des cartes stat sont utiles pour mettre en évidence les indicateurs de performance clés sur le tableau de bord.

### <a name="pie-chart"></a>Graphique en secteurs

:::image type="content" source="media/dashboard-customize-visuals/pie-chart.png" alt-text="type de visuel de graphique en secteurs":::

Le visuel de graphique en secteurs a besoin d’au moins deux colonnes dans le résultat de la requête. Par défaut, la première colonne est utilisée pour l’axe des couleurs. Cette colonne peut contenir des types de données textuels, DateHeure ou numériques. D’autres colonnes sont utilisées pour déterminer la taille de chaque tranche et elles contiennent des types de données numériques. Les graphiques en secteurs sont utilisés pour présenter une composition de catégories et leurs proportions par rapport à un total.

### <a name="scatter-chart"></a>Graphique à nuages de points

:::image type="content" source="media/dashboard-customize-visuals/scatter-chart.png" alt-text="type de visuel de graphique à nuages de points":::

Dans un visuel de graphique à nuages de points, la première colonne correspond à l’axe des abscisses (x) et doit être une colonne numérique. Les autres colonnes numériques correspondent aux axes y. Les nuages de points sont utilisés pour observer les relations entre des variables.

### <a name="time-chart"></a>Graphique temporel 

:::image type="content" source="media/dashboard-customize-visuals/time-chart.png" alt-text="type de visuel de graphique temporel":::

Un visuel de graphique temporel est un type de graphique en courbes. La première colonne de la requête correspond à l’axe des abscisses (x) et doit être de type DateHeure. Les autres colonnes numériques correspondent aux axes y. Les valeurs d’une colonne de type chaîne sont utilisées pour regrouper les colonnes numériques et créer des lignes différentes dans le graphique. Les autres colonnes de chaîne sont ignorées. Le visuel de graphique temporel est semblable à un [graphique en courbes](#line-chart) si ce n’est que l’axe des abscisses (x) correspond toujours au temps.

### <a name="anomaly-chart"></a>Graphique d’anomalies 

:::image type="content" source="media/dashboard-customize-visuals/anomaly-chart.png" alt-text="type de visuel de graphique d’anomalies":::

Un visuel de graphique d’anomalies est semblable à un [graphique temporel](#time-chart), mais il met en évidence les anomalies à l’aide de la fonction `series_decompose_anomalies`.

### <a name="map"></a>Carte

:::image type="content" source="media/dashboard-customize-visuals/map.png" alt-text="type de visuel de carte":::

Pour afficher le visuel de carte, quatre colonnes sont nécessaires dans la requête :
* Chaîne (première colonne) utilisée pour l’étiquette de survol
* Longitude (réel)
* Latitude (réel)
* Taille de la bulle (entier). Cette colonne peut être constante si des tailles différentes ne sont pas requises.

Les cartes sont utiles pour visualiser les données avec des coordonnées géographiques. Le visuel de carte présente aussi une fonctionnalité de zoom intégrée.

## <a name="customize-visuals"></a>Personnaliser les visuels

1. Sélectionnez **Modifier** dans le menu du tableau de bord pour passer en mode d’édition.
1. Pour accéder à la boîte de dialogue de personnalisation des visuels sur une carte, cliquez sur le menu déroulant > **Modifier la carte**. Lorsque vous créez une carte avec **Ajouter une requête**, vous pouvez également sélectionner **Modifier la carte**.

:::image type="content" source="media/dashboard-customize-visuals/edit-card.png" alt-text="modifier la carte pour la personnalisation des visuels":::

### <a name="select-properties-to-customize-visuals"></a>Sélectionner des propriétés pour personnaliser les visuels

Utilisez les propriétés suivantes pour personnaliser les visuels.

:::image type="content" source="media/dashboard-customize-visuals/visual-customization-sidebar.png" alt-text="Barre latérale de personnalisation des visuels":::

|Section  |Description | Types d’éléments visuels
|---------|---------|-----|
|**Général**    |    Sélectionnez le format de graphique **empilé** ou **non empilé**.  | Graphiques à barres, histogrammes et graphiques en aires |
|**Données**    |   Sélectionnez les **colonnes Y et X** pour votre visuel. Conservez la sélection **Infer** pour que la plateforme sélectionne automatiquement une colonne en fonction du résultat de la requête.    |Graphiques à barres, histogrammes, graphiques à nuages de points et graphiques d’anomalies|
|**Légende**    |   Activez ou désactivez pour afficher ou masquer l’affichage des légendes sur vos visuels.   |Graphiques à barres, histogrammes, graphiques en aires, graphiques en courbes, graphiques à nuages de points, graphiques d’anomalies et graphiques temporels |
|**Axe Y**     |   Permet la personnalisation des propriétés de l’axe des ordonnées (y) : <ul><li>**Étiquette** : Texte pour une étiquette personnalisée. </li><li>**Valeur maximale** : Modifiez la valeur maximale de l’axe des ordonnées (y).  </li><li>**Valeur minimale** : Modifiez la valeur minimale de l’axe des ordonnées (y).  </li></ul>      |Graphiques à barres, histogrammes, graphiques en aires, graphiques en courbes, graphiques à nuages de points, graphiques d’anomalies et graphiques temporels |
|**Axe des abscisses (x)**     |    Permet la personnalisation des propriétés de l’axe des abscisses (x) : <ul><li>**Étiquette** : Texte pour une étiquette personnalisée. </li>     | Graphiques à barres, histogrammes, graphiques en aires, graphiques en courbes, graphiques à nuages de points, graphiques d’anomalies et graphiques temporels|

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser des paramètres dans des tableaux de bord Azure Data Explorer](dashboard-parameters.md)
* [Interroger des données dans Azure Data Explorer](web-query-data.md) 