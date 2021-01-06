---
title: 'Démarrage rapide : Interroger des données dans l’interface utilisateur web Azure Data Explorer'
description: Dans ce guide de démarrage rapide, vous allez apprendre à interroger et à partager des données dans l’interface utilisateur web d’Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: olgolden
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/22/2020
ms.localizationpriority: high
ms.openlocfilehash: b20d9a3e6c01f59a9cde44d6462ffeb0072473ed
ms.sourcegitcommit: 1530a38181ec92ed1c2c1f3aa2a75f69bd3e9045
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822880"
---
# <a name="quickstart-query-data-in-azure-data-explorer-web-ui"></a>Démarrage rapide : Interroger des données dans l’interface utilisateur web Azure Data Explorer

Azure Data Explorer est un service d’analytique données rapide et entièrement managé dédié à l’analyse en temps réel de volumes importants de données. Azure Data Explorer fournit une expérience web qui vous permet de vous connecter à vos clusters Azure Data Explorer et d’écrire, d’exécuter et de partager des requêtes et des commandes de langage de requête Kusto. L’expérience web est disponible dans le portail Azure et en tant qu’application web autonome, l’[interface utilisateur web Azure Data Explorer](https://dataexplorer.azure.com). L’interface utilisateur web Azure Data Explorer peut également être hébergée par d’autres portails web dans un iframe HTML. Pour plus d’informations sur l’hébergement de l’interface utilisateur web et de l’éditeur Monaco, consultez [Intégration de l’IDE Monaco](kusto/api/monaco/monaco-kusto.md).
Dans ce guide de démarrage rapide, vous allez travailler dans l’interface utilisateur web autonome d’Azure Data Explorer.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’en avez pas, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Cluster et base de données avec des données. [Créez votre propre cluster](create-cluster-database-portal.md) ou utilisez le cluster d’aide Azure Data Explorer.

## <a name="sign-in-to-the-application"></a>Se connecter à l’application

Connectez-vous à [l’application](https://dataexplorer.azure.com/).

## <a name="add-clusters"></a>Ajouter des clusters

Lorsque vous ouvrez l’application la première fois, vous n’avez aucune connexion de cluster.

![Ajouter un cluster](media/web-query-data/add-cluster.png)

Avant de commencer à exécuter des requêtes, vous devez ajouter une connexion. Dans cette section, vous allez ajouter des connexions au cluster d’**aide**  Azure Data Explorer et tester le cluster que vous avez créé dans la section [Prérequis](#prerequisites).

### <a name="add-help-cluster"></a>Ajouter un cluster d’aide

1. Dans la partie supérieure gauche de l’application, sélectionnez **Ajouter un cluster**.

1. Dans la boîte de dialogue **Ajouter un cluster**, entrez l’URI https://help.kusto.windows.net, puis sélectionnez **Ajouter**.
   
1. Dans le volet gauche, vous devriez maintenant voir le cluster **help**. Développez la base de données **Samples** et ouvrez le dossier **Tables** pour consulter les exemples de tables auxquels vous avez accès.

    :::image type="content" source="media/web-query-data/help-cluster.png" alt-text="Rechercher une table dans le cluster d’aide":::

Nous utilisons la table **StormEvents** plus loin dans ce guide de démarrage rapide et dans d’autres articles de l’Explorateur de données Azure. 

### <a name="add-your-cluster"></a>Ajouter votre cluster

Ajoutez maintenant le cluster de test que vous avez créé.

1. Sélectionnez **Ajouter un cluster**.

1. Dans la boîte de dialogue **Ajouter un cluster**, entrez l’URL de votre cluster de test sous la forme `https://<ClusterName>.<Region>.kusto.windows.net/`, puis sélectionnez **Ajouter**. Par exemple, `https://mydataexplorercluster.westus.kusto.windows.net` comme dans l’image suivante :

    :::image type="content" source="media/web-query-data/server-uri.png" alt-text="Entrer l’URL du cluster de test":::
    
1. Dans l’exemple ci-dessous, vous voyez le cluster **help** et un nouveau cluster, **docscluster.westus** (l’URL complète est `https://docscluster.westus.kusto.windows.net/`).

    ![Cluster de test](media/web-query-data/test-cluster.png)

## <a name="run-queries"></a>Exécuter des requêtes

Vous pouvez maintenant exécuter des requêtes sur les deux clusters (en supposant que vous avez des données dans votre cluster de test). Dans cet article, nous allons nous concentrer sur le cluster d’**aide**.

1. Dans le volet gauche, sous le cluster **help**, sélectionnez la base de données **Samples**.

1. Copiez et collez la requête ci-après dans la fenêtre de requête. En haut de la fenêtre, sélectionnez **Exécuter**.

    ```kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    
    Cette requête retourne les 10 enregistrements les plus récents dans la table **StormEvents**. Le résultat doit ressembler à la table suivante.

    :::image type="content" source="media/web-query-data/result-set-take-10.png" alt-text="Capture d’écran d’une table qui répertorie les données pour 10 événements orageux." border="false":::

    L’image suivante montre l’état de l’application, avec le cluster ajouté, ainsi qu’une requête avec les résultats.

    :::image type="content" source="media/web-query-data/webui-take10.png" alt-text="plein écran":::

1. Copiez et collez la requête ci-après dans la fenêtre de requête, sous la première requête. Notez qu’elle ne se présente pas sur des lignes distinctes comme la première requête.

    ```kusto
    StormEvents | sort by StartTime desc 
    | project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative | take 10
    ```

1. Sélectionnez la nouvelle requête. Appuyez sur *Maj+Alt+F* pour mettre en forme la requête afin qu’elle ressemble à la requête suivante.

    ![Requête mise en forme](media/web-query-data/formatted-query.png)

1. Sélectionnez **Exécuter** ou appuyez sur *Maj + Entrée* pour exécuter une requête. Cette requête retourne les mêmes enregistrements que la première, mais contient uniquement les colonnes spécifiées dans l’instruction `project`. Le résultat doit ressembler à la table suivante.

    :::image type="content" source="media/web-query-data/result-set-project.png" alt-text="Capture d’écran d’un tableau listant l’heure de début, l’heure de fin, l’état, le type d’événement, les dommages et la description des épisodes pour 10 événements orageux." border="false":::

    > [!TIP]
    > Sélectionnez **Rappeler** en haut de la fenêtre de la requête pour afficher le jeu de résultats de la première requête sans devoir réexécuter la requête. Souvent, lors de l’analyse, vous exécutez plusieurs requêtes et l’option **Rappel** vous permet de récupérer les résultats des requêtes précédentes.

1. Exécutons une autre requête pour voir un autre type de sortie.

    ```kusto
    StormEvents
    | summarize event_count=count(), mid = avg(BeginLat) by State
    | sort by mid
    | where event_count > 1800
    | project State, event_count
    | render columnchart
    ```

    Le résultat doit ressembler au graphique suivant.

    ![Histogramme](media/web-query-data/column-chart.png)

    > [!NOTE]
    > Des lignes vides dans l’expression de requête peuvent avoir une incidence sur la partie de la requête qui est exécutée.
    > * Si aucun texte n’est sélectionné, l’hypothèse est que la requête ou la commande est séparée par des lignes vides.
    > * Si du texte est sélectionné, le texte sélectionné est exécuté.

## <a name="work-with-the-table-grid"></a>Utiliser la grille de table

Maintenant que vous avez vu comment fonctionnent les requêtes simples, voyons comment vous pouvez utiliser la grille de table pour personnaliser les résultats et approfondir les analyses.

1. Réexécutez la première requête. Placez le curseur de la souris sur la colonne **État**, sélectionnez le menu et **Grouper par état**.

    ![Grouper par état](media/web-query-data/group-by.png)

1. Dans la grille, double-cliquez sur **California** pour développer et afficher les enregistrements de cet État. Ce type de regroupement peut être utile lors d’une analyse exploratoire.

    :::image type="content" source="media/web-query-data/group-expanded.png" alt-text="Capture d’écran d’une grille de résultats de requête avec le groupe Californie développé" border="false":::

1. Placez le curseur de la souris sur la colonne **Grouper**, puis sélectionnez **Réinitialiser les colonnes**. Ce paramètre rétablit la grille à son état d’origine.

    ![Rétablir les colonnes](media/web-query-data/reset-columns.png)

1. Exécutez la requête suivante.

    ```kusto
    StormEvents
    | sort by StartTime desc
    | where DamageProperty > 5000
    | project StartTime, State, EventType, DamageProperty, Source
    | take 10
    ```

1. Dans la grille de résultats, sélectionnez quelques cellules numériques. La grille de table vous permet de sélectionner plusieurs lignes, colonnes et cellules et de calculer les agrégations associées. L’interface utilisateur web prend actuellement en charge les fonctions suivantes pour les valeurs numériques : **Moyenne**, **Nombre**, **Min**, **Max** et **Somme**.

    :::image type="content" source="media/web-query-data/select-stats.png" alt-text="sélection de fonctions"::: 

1. Sur le côté droit de la grille, sélectionnez **Colonnes** pour voir le panneau d’outil de la table. Ce panneau fonctionne comme la liste des champs des tableaux croisés dynamiques dans Excel, ce qui vous permet d’effectuer davantage d’analyses dans la grille elle-même.

    ![Panneau d’outil de la table](media/web-query-data/tool-panel.png)

1. Sélectionnez **Mode Pivot**, puis faites glisser les colonnes comme suit : **State** sur **Groupes de lignes**, **DamageProperty** sur **Valeurs** et **EventType** sur **Étiquettes de colonne**.  

    ![Mode Pivot](media/web-query-data/pivot-mode.png)

    Le résultat doit ressembler au tableau croisé dynamique suivant.

    ![Tableau croisé dynamique](media/web-query-data/pivot-table.png)

    Notez que Vermont et Alabama ont chacun deux événements sous la même catégorie, tandis que Texas présente deux événements sous des catégories différentes. Les tableaux croisés dynamiques sont un outil formidable pour une analyse rapide, car ils vous permettent de repérer rapidement ces différences.

## <a name="search-in-the-results-table"></a>Rechercher dans la table des résultats

Vous pouvez rechercher une expression spécifique dans une table de résultats.

1.  Exécutez la requête suivante :

    ```Kusto
    StormEvents
    | where DamageProperty > 5000
    | take 1000
    ```

1. Cliquez sur le bouton **Rechercher** à droite et tapez *« Wabash »* .

    :::image type="content" source="media/web-query-data/search.png" alt-text="Rechercher dans la table":::

1. Toutes les mentions de votre expression recherchée sont maintenant mises en surbrillance dans la table. Vous pouvez naviguer entre elles en cliquant sur *Entrée* pour avancer ou *Maj + Entrée* pour reculer, ou vous pouvez utiliser les boutons *haut* et *bas* en regard de la zone de recherche.

    :::image type="content" source="media/web-query-data/search-results.png" alt-text="Parcourir les résultats de la recherche":::

## <a name="share-queries"></a>Partager des requêtes

Souvent, vous souhaitez partager les requêtes que vous créez. 

1. Dans la fenêtre de requête, sélectionnez la première requête que vous avez copiée.

1. En haut de la fenêtre de la requête, sélectionnez **Partager**. 

:::image type="content" source="media/web-query-data/share-menu.png" alt-text="Menu Partager":::

Les options suivantes sont disponibles dans le menu déroulant :
* Lien dans le Presse-papiers
* [Lien et requête dans le Presse-papiers](#provide-a-deep-link)
* Lien, requête et résultats dans le Presse-papiers
* [Épingler au tableau de bord](#pin-to-dashboard)
* [Requête vers Power BI](power-bi-imported-query.md)

### <a name="provide-a-deep-link"></a>Fournir un lien ciblé

Vous pouvez fournir un lien ciblé afin que les utilisateurs avec un accès au cluster puissent exécuter les requêtes.

1. Dans **Partager**, sélectionnez **Lien et requête dans le Presse-papiers**.

1. Copiez le lien et la requête dans un fichier texte.

1. Collez le lien dans une nouvelle fenêtre de navigateur. Le résultat doit ressembler à ce qui suit :

    :::image type="content" source="media/web-query-data/shared-query.png" alt-text="Lien ciblé d’une requête partagée":::

### <a name="pin-to-dashboard"></a>Épingler au tableau de bord

Quand vous effectuez une exploration des données à l’aide de requêtes dans l’interface utilisateur web et que vous trouvez les données dont vous avez besoin, vous pouvez les épingler à un tableau de bord pour une supervision continue. 

Pour épingler une requête :

1. Dans **Partager**, sélectionnez **Épingler au tableau de bord**.

1. Dans le volet **Épingler au tableau de bord** :
    1. Fournissez un **Nom de requête**.
    1. Sélectionnez **Utiliser l’existant** ou **Créer un nouveau**.
    1. Fournissez le **Nom du tableau de bord**.
    1. Cochez la case **Afficher le tableau de bord après sa création** (s’il s’agit d’un nouveau tableau de bord).
    1. Sélectionnez **Épingler**.

    :::image type="content" source="media/web-query-data/pin-to-dashboard.png" alt-text="Volet Épingler au tableau de bord":::

> [!NOTE]
> **Épingler au tableau de bord** épingle uniquement la requête sélectionnée. Pour créer la source de données du tableau de bord et traduire des commandes de rendu en visuel dans le tableau de bord, vous devez sélectionner la base de données appropriée dans la liste de bases de données.

## <a name="export-query-results"></a>Exporter des résultats de requête

Pour exporter les résultats de la requête dans un fichier CSV, sélectionnez **Fichier** > **Exporter au format CSV**.

:::image type="content" source="media/web-query-data/export-results.png" alt-text="Exporter les résultats dans un fichier CSV":::

## <a name="settings"></a>Paramètres

Sous l’onglet **Paramètres**, vous pouvez :

* [Exporter les paramètres d’environnement](#export-environment-settings)
* [Importer les paramètres d’environnement](#import-environment-settings)
* [Surligner les niveaux d’erreur](#highlight-error-levels)
* [Effacer l’état local](#clean-up-resources)

Sélectionnez l’icône des paramètres :::image type="icon" source="media/web-query-data/settings-icon.png" border="false"::: en haut à droite pour ouvrir la fenêtre **Paramètres**.

:::image type="content" source="media/web-query-data/settings.png" alt-text="Fenêtre Paramètres":::

### <a name="export-and-import-environment-settings"></a>Exporter et importer des paramètres d’environnement

Les actions d’exportation et d’importation vous aident à protéger votre environnement de travail et à le déplacer vers d’autres navigateurs et appareils. L’action Exporter va exporter tous vos paramètres, connexions de cluster et onglets de requête vers un fichier JSON qui peut être importé dans un autre navigateur ou appareil.

#### <a name="export-environment-settings"></a>Exporter les paramètres d’environnement

1. Dans la fenêtre **Paramètres** > **Général**, sélectionnez **Exporter**.
1. Le fichier **adx-export.json** sera téléchargé dans votre stockage local.
1. Sélectionnez **Effacer l’état local** pour rétablir l’état d’origine de votre environnement. Ce paramètre supprime toutes vos connexions au cluster et ferme les onglets ouverts.

> [!NOTE]
> L’**exportation** exporte uniquement les données liées aux requêtes. Aucune donnée du tableau de bord n’est exportée dans le fichier **adx-export.json**.

#### <a name="import-environment-settings"></a>Importer les paramètres d’environnement

1. Dans la fenêtre **Paramètres** > **Général**, sélectionnez **Importer**. Ensuite, dans fenêtre contextuelle **Avertissement**, sélectionnez **Importer**.

    :::image type="content" source="media/web-query-data/import.png" alt-text="Avertissement d’importation":::

1. Localisez votre fichier **adx-export.json** dans votre stockage local et ouvrez-le.
1. Vos connexions de cluster précédentes et les onglets ouverts sont à présent disponibles.

> [!NOTE]
> L’**importation** remplace les données et paramètres d’environnement existants.

### <a name="highlight-error-levels"></a>Mise en surbrillance des niveaux d’erreur

Kusto tente d’interpréter le niveau de gravité ou de détail de chaque ligne dans le volet de résultats et de les colorer en conséquence. Pour ce faire, il met en correspondance les valeurs distinctes de chaque colonne avec un ensemble de modèles connus (« Avertissement », « Erreur », etc.). 

Pour activer la mise en surbrillance des niveaux d’erreur :

1. Sélectionnez l’icône **Paramètres** à côté de votre nom d’utilisateur.
1. Sélectionnez l’onglet **Apparence** et basculez l’option **Activer la mise en surbrillance du niveau d’erreur** à droite. 

:::image type="content" source="media/web-query-data/enable-error-highlighting.gif" alt-text="Image GIF animée montrant comment activer la mise en surbrillance du niveau d’erreur dans les paramètres":::

Jeu de couleurs du niveau d’erreur en mode **Clair** | Jeu de couleurs du niveau d’erreur en mode **Foncé**
|---|---|
:::image type="content" source="media/web-query-data/light-mode.png" alt-text="Capture d’écran de la légende de couleur en mode clair"::: | :::image type="content" source="media/web-query-data/dark-mode.png" alt-text="Capture d’écran de la légende de couleur en mode foncé":::

## <a name="provide-feedback"></a>Fournir des commentaires

1. En haut à droite de l’application, sélectionnez l’icône de commentaires :::image type="icon" source="media/web-query-data/icon-feedback.png" border="false":::.

1. Entrez vos commentaires et sélectionnez **Envoyer**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Vous n’avez pas créé aucune ressource dans ce guide de démarrage rapide, mais si vous souhaitez supprimer un ou les deux clusters de l’application, cliquez avec le bouton droit sur le cluster et sélectionnez **Supprimer la connexion**.
Une autre option consiste à sélectionner l’option **Effacer l’état local** de l’onglet **Paramètres** > **Général**. Cette action supprime toutes les connexions de cluster et ferme tous les onglets de requête ouverts.

## <a name="next-steps"></a>Étapes suivantes

[Écrire des requêtes pour l’Explorateur de données Azure](write-queries.md)
