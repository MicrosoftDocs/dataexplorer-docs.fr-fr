---
author: mgblythe
ms.service: data-explorer
ms.topic: include
ms.date: 11/14/2018
ms.author: mblythe
ms.openlocfilehash: 9624856841ec7473543575c31928c6eefd1404c1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81494333"
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