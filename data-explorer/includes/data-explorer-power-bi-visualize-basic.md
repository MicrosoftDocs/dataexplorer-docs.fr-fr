---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/14/2018
ms.author: orspodek
ms.openlocfilehash: 13601823f9bff9b00d285a215a6cef5160203b37
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87374742"
---
Maintenant que vous avez des données dans Power BI Desktop, vous pouvez créer des rapports basés sur ces données. Vous allez créer un rapport simple avec un histogramme qui montre les dégâts aux cultures par état.

1. Sur le côté gauche de la fenêtre principale de Power BI, sélectionnez la vue Rapport.

    ![Vue Rapport](media/data-explorer-power-bi-visualize-basic/report-view.png)

1. Dans le volet **VISUALISATIONS**, sélectionnez l’histogramme groupé.

    ![Ajouter un histogramme](media/data-explorer-power-bi-visualize-basic/add-column-chart.png)

    Un graphique vide est ajouté au canevas.

    ![Graphique vide](media/data-explorer-power-bi-visualize-basic/blank-chart.png)

1. Dans la liste **CHAMPS**, sélectionnez **DamageCrops** (Dégâts aux cultures) et **State** (État).

    ![Sélectionner des champs](media/data-explorer-power-bi-visualize-basic/select-fields.png)

    Vous avez maintenant un graphique qui montre les dommages subis par les cultures pour les 1 000 premières lignes de la table.

    ![Dommages aux cultures par état](media/data-explorer-power-bi-visualize-basic/damage-column-chart.png)

1. Enregistrez le rapport.