---
title: Outil Kusto. Explorer-Azure Explorateur de données
description: Cet article décrit l’outil Kusto. Explorer dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 60a414ff871d88de041e8b76671b73d98854fba0
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374069"
---
# <a name="kustoexplorer-tool"></a>Outil Kusto. Explorer

Kusto. Explorer est une application de bureau riche qui vous permet d’explorer vos données à l’aide du langage de requête Kusto.

## <a name="getting-the-tool"></a>Obtention de l’outil

* Installer l' [outil Kusto. Explorer](https://aka.ms/Kusto.Explorer)

* Vous pouvez également accéder à votre cluster Kusto à l’aide de votre navigateur à l’adresse suivante : `https://<your_cluster>.kusto.windows.net` . Remplacez <your_cluster> par le nom de votre cluster Azure Explorateur de données.



## <a name="using-chrome-and-kustoexplorer"></a>Utilisation de chrome et Kusto. Explorer

Si vous utilisez Chrome comme navigateur par défaut, veillez à installer l’extension ClickOnce pour Chrome :

[https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US](https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US)



## <a name="overview-of-the-user-experience"></a>Vue d’ensemble de l’expérience utilisateur

La fenêtre de l’Explorateur Kusto comporte plusieurs parties de l’interface utilisateur :

1. [Panneau de menu](#menu-panel)
2. [Panneau connexions](#connections-panel)
3. Panneau script
4. Panneau Résultats

![Démarrage de Kusto. Explorer](./Images/KustoTools-KustoExplorer/ke-start.png "ke-Start")

### <a name="keyboard-shortcuts"></a>Raccourcis clavier

Vous constaterez peut-être que l’utilisation de raccourcis clavier vous permet d’effectuer des opérations plus rapidement qu’avec la souris. Jetez un coup d’œil à cette [liste de raccourcis clavier Kusto. Explorer](kusto-explorer-shortcuts.md).

### <a name="menu-panel"></a>Panneau de menu

Le panneau de menu Kusto. Explorer comprend les onglets suivants :

* [Page d'accueil](#home-tab)
* [File](#file-tab)
* [Connexions](#connections-tab)
* [Afficher](#view-tab)
* [outils](#tools-tab)

* [Gestion](#management-tab)
* [Aide](#help-tab)

### <a name="home-tab"></a>Onglet Dossier de base

![Page d’Kusto. Explorer](./Images/KustoTools-KustoExplorer/home-tab.png "home-tab")

L’onglet dossier de démarrage affiche les fonctions les plus récemment utilisées, divisées en sections :

* [Requête](#query-section)
* [Partager](#share-section)
* [Visualisations](#visualizations-section)
* [Afficher](#view-section)
* [Aide](#help-tab) 

#### <a name="query-section"></a>Section de requête

![Menu de requête Kusto. Explorer](./Images/KustoTools-KustoExplorer/home-query-menu.png "menu requête")

|Menu|    Comportement|
|----|----------|
|Liste déroulante mode | <ul><li>Mode requête : bascule la fenêtre de requête en [mode script](#query-mode). Les commandes peuvent être chargées et enregistrées en tant que scripts (par défaut)</li> <li> Mode de recherche : un mode de requête unique où chaque commande entrée est traitée immédiatement et présente un résultat dans la fenêtre de résultats</li> <li>Mode de recherche + + : permet de rechercher un terme à l’aide de la syntaxe de recherche sur une ou plusieurs tables. En savoir plus sur l’utilisation du [mode de recherche + +](kusto-explorer.md#search-mode)</li></ul> |
|Nouvel onglet| Ouvre un nouvel onglet pour interroger Kusto |

#### <a name="share-section"></a>Partager la section

![Section de partage Kusto. Explorer](./Images/KustoTools-KustoExplorer/home-share-menu.png "menu de partage")

|Menu|    Comportement|
|----|----------|
|Données dans le presse-papiers|    Exporte la requête et le jeu de données dans le presse-papiers. Si un graphique est présenté, il exporte le graphique en tant qu’image bitmap| 
|Résultat dans le presse-papiers| Exporte le jeu de données dans le presse-papiers. Si un graphique est présenté, il exporte le graphique en tant qu’image bitmap| 
|Requête dans le presse-papiers| Exporte la requête dans le presse-papiers|

#### <a name="visualizations-section"></a>Section visualisations

![texte de remplacement](./Images/KustoTools-KustoExplorer/home-visualizations-menu.png "menu-visualisations")

|Menu         | Comportement|
|-------------|---------|
|Graphique en aires      | Affiche un graphique en aires dans lequel l’axe des X est la première colonne (doit être numérique) et toutes les colonnes numériques sont mappées à différentes séries (axe Y). |
|Histogramme | Affiche un histogramme dans lequel toutes les colonnes numériques sont mappées à différentes séries (axe Y) et la colonne de texte avant la valeur numérique est l’axe des abscisses (peut être contrôlé dans l’interface utilisateur)|
|Graphique à barres    | Affiche un graphique à barres dans lequel toutes les colonnes numériques sont mappées à différentes séries (axe des X) et la colonne de texte avant la valeur numérique est l’axe des Y (peut être contrôlée dans l’interface utilisateur)|
|graphique à aires empilées      | Affiche un graphique en aires empilées dans lequel l’axe des X est la première colonne (doit être numérique) et toutes les colonnes numériques sont mappées à différentes séries (axe Y). |
|Graphique chronologique   | Affiche un graphique temporel dans lequel l’axe des X est la première colonne (doit être DateTime) et toutes les colonnes numériques sont mappées à différentes séries (axe Y).|
|Graphique en courbes   | Affiche un graphique en courbes dans lequel l’axe des X est la première colonne (doit être numérique) et toutes les colonnes numériques sont mappées à différentes séries (axe Y).|
|Graphique d’anomalies|    Semblable à graphique temporel, mais détecte les anomalies dans les données de séries chronologiques, à l’aide de l’algorithme Machine Learning anomalies. Pour la détection d’anomalies, Kusto. Explorer utilise la fonction [series_decompose_anomalies](../query/series-decompose-anomaliesfunction.md) . (*) 
|Graphique à secteurs    |    Affiche un graphique à secteurs dans lequel l’axe des couleurs est la première colonne et l’axe du thêta (doit être une mesure, converti en pourcentage) est la deuxième colonne.|
|Graphique d’échelle |    Affiche un graphique d’échelle dans lequel l’axe des X est les deux dernières colonnes (doit être DateTime) et l’axe des Y est un composite des autres colonnes.|
|Nuage de points| Affiche un graphique à points dans lequel l’axe des X est la première colonne (doit être numérique) et toutes les colonnes numériques sont mappées à différentes séries (axe Y).|
|Graphique croisé dynamique  | Visionner un tableau croisé dynamique et un graphique croisé dynamique qui donne la flexibilité totale de la sélection de données, de colonnes, de lignes et de différents types de graphiques.| 
|Tableau croisé dynamique Time   | Navigation interactive sur la ligne de temps des événements (pivotement sur l’axe du temps)|

(*) Graphique d’anomalies : l’algorithme attend des données TimeSeries, qui se composent de deux colonnes :
1. Durée dans les compartiments d’intervalles fixes
2. Valeur numérique pour la détection d’anomalies pour produire cela dans Kusto. Explorer, résumez par le champ d’heure et spécifiez la plage de temps.

#### <a name="view-section"></a>Section de vue

![texte de remplacement](./Images/KustoTools-KustoExplorer/home-view-menu.png "menu Affichage")

|Menu           | Comportement|
|---------------|---------|
|Mode d’affichage complet | Maximise l’espace de travail en masquant le menu du ruban et le panneau de connexion|
|Masquer les colonnes vides| Supprime les colonnes vides de la grille de données|
|Réduire les colonnes singulières| Réduit les colonnes avec des valeurs singulières|
|Explorer les valeurs de colonne| Affiche la distribution des valeurs de colonne|
|Augmenter la police  | Augmente la taille de police de l’onglet de requête et de la grille de données des résultats|  
|Réduire la police  | Diminue la taille de police de l’onglet de requête et de la grille de données des résultats|

(*) Paramètres d’affichage de données : Kusto. Explorer effectue le suivi des paramètres utilisés par ensemble de colonnes. Ainsi, lorsque les colonnes sont réorganisées ou supprimées, la vue de données est enregistrée et est réutilisée chaque fois que les données contenant les mêmes colonnes sont récupérées. Pour rétablir les valeurs par défaut de la vue, sélectionnez **Réinitialiser la vue**dans l’onglet **affichage** . 

### <a name="file-tab"></a>Onglet Fichier

![Fichier Kusto. Explorer](./Images/KustoTools-KustoExplorer/file-tab.png "onglet fichier")

|Menu| Comportement|
|---------------|---------|
||---------*Script de requête*---------|
|Nouvel onglet | Ouvre une nouvelle fenêtre d’onglet pour l’interrogation de Kusto |
|Ouvrir un fichier| Charge des données à partir d’un fichier *. kql dans le panneau de script actif|
|Enregistrer dans le fichier| Enregistre le contenu du panneau de script actif dans le fichier *. kql|
|Fermer l’onglet| Ferme la fenêtre active de l’onglet|
||---------*Enregistrer des données*---------|
|Au format CSV       | Exporte des données vers un fichier CSV (valeurs séparées par des virgules)| 
|En JSON      | Exporte des données vers un fichier au format JSON|
|Vers Excel     | Exporte des données vers un fichier XLSX (Excel)|
|En texte      |    Exporte des données vers un fichier TXT (texte)| 
|Au script CSL|    Exporte la requête dans un fichier de script| 
|Aux résultats   |    Exporte la requête et les données dans un fichier de résultats (QRES)|
||---------*Charger les données*---------|
|À partir des résultats|    Charge la requête et les données à partir d’un fichier de résultats (QRES)| 
||---------*Papier*---------|
|Données dans le presse-papiers|    Exporte la requête et le jeu de données dans le presse-papiers. Si un graphique est présenté, il exporte le graphique en tant qu’image bitmap| 
|Résultat dans le presse-papiers| Exporte le jeu de données dans le presse-papiers. Si un graphique est présenté, il exporte le graphique en tant qu’image bitmap| 
|Requête dans le presse-papiers| Exporte la requête dans le presse-papiers|
||---------*About*---------|
|Effacer le cache des résultats| Efface les résultats mis en cache des requêtes exécutées précédemment| 

### <a name="connections-tab"></a>Onglet Connexions

![Onglet Connexions Kusto. Explorer](./Images/KustoTools-KustoExplorer/connections-tab.png "connexions-onglet")

|Menu|Comportement|
|----|----------|
||---------*Ceux*---------|
|Ajouter un groupe| Ajoute un nouveau groupe de serveurs Kusto|
|Renommer le groupe| Renomme le groupe de serveurs Kusto existant|
|Supprimer le groupe| Supprime le groupe de serveurs Kusto existant.|
||---------*Clusters*---------|
|Connexions d’importation| Importe des connexions à partir d’un fichier en spécifiant des connexions|
|Exporter les connexions| Exporte des connexions dans un fichier|
|Ajouter une connexion| Ajoute une nouvelle connexion au serveur Kusto| 
|Modifier la connexion| Ouvre une boîte de dialogue pour la modification des propriétés de connexion du serveur Kusto|
|Supprimer la connexion| Supprime la connexion existante au serveur Kusto|
|Actualiser| Actualise les propriétés d’une connexion au serveur Kusto|
||---------*Fournisseurs d’identité*---------|
|Inspecter le principal de connexion| Affiche les détails de l’utilisateur actif actuel|
|Déconnexion d’AAD| Déconnecte l’utilisateur actuel de la connexion à AAD|
||---------*Étendue des données*---------|
|Portée de la mise en cache|<ul><li>Requêtes DataExecute à chaud uniquement sur le [cache de données à chaud](../management/cachepolicy.md)</li><li>Toutes les données : exécuter les requêtes sur toutes les données disponibles (par défaut)</li></ul> |
|Colonne DateTime| Nom de la colonne qui peut être utilisé pour le pré-filtre de l’heure|
|Filtre de temps| Valeur de l’heure avant le filtre|

### <a name="view-tab"></a>Onglet vue

![onglet vue](./Images/KustoTools-KustoExplorer/view-tab.png "onglet vue")

|Menu|Comportement|
|----|----------|
||---------*Présence*---------|
|Mode d’affichage complet | Maximise l’espace de travail en masquant le menu du ruban et le panneau de connexion|
|Augmenter la police  | Augmente la taille de police de l’onglet de requête et de la grille de données des résultats|  
|Réduire la police  | Diminue la taille de police de l’onglet de requête et de la grille de données des résultats|
|Réinitialiser la disposition|Réinitialise la disposition des contrôles et fenêtres d’ancrage de l’outil.|
||---------*Vue de données*---------|
|Réinitialiser l’affichage| Réinitialise les paramètres de la vue de données (*)|
|Explorer les valeurs de colonne|Affiche la distribution des valeurs de colonne|
|Focus sur les statistiques sur les requêtes|Change le focus en statistiques sur les requêtes au lieu des résultats de la requête lors de la fin de la requête|
|Masquer les doublons|Active/désactive la suppression des lignes en double dans les résultats de la requête|
|Masquer les colonnes vides|Active/désactive la suppression de colonnes vides dans les résultats de la requête|
|Réduire les colonnes singulières|Active/désactive la réduction des colonnes avec une valeur singulière|
||---------*Filtrage des données*---------|
|Filtrer les lignes dans la recherche|Active ou désactive l’option pour afficher uniquement les lignes correspondantes dans la recherche des résultats de la requête (Ctrl + F)|
||---------*Visualisations*---------|
|Visualisations|Consultez [visualisations](#visualizations-section), ci-dessus. |

(*) Paramètres d’affichage de données : Kusto. Explorer effectue le suivi des paramètres utilisés par ensemble de colonnes. Ainsi, lorsque les colonnes sont réorganisées ou supprimées, la vue de données est enregistrée et est réutilisée chaque fois que les données contenant les mêmes colonnes sont récupérées. Pour rétablir les valeurs par défaut de la vue, sélectionnez **Réinitialiser la vue**dans l’onglet **affichage** . 

### <a name="tools-tab"></a>Onglet Outils

![onglet Outils](./Images/KustoTools-KustoExplorer/tools-tab.png "outils-onglet")

|Menu|Comportement|
|----|----------|
||---------*Semi*---------|
|Activer IntelliSense| Active et désactive IntelliSense dans le panneau script|
||---------*Analyse*---------|
|Analyseur de requêtes| Lance l’outil Analyseur de requêtes|
|Vérificateur de requête | Analyse la requête actuelle et génère un ensemble de recommandations d’amélioration applicables|
|Calculatrice| Lance la calculatrice|
||---------*Analyse*---------|
|Rapports analytiques| Ouvre un tableau de bord avec plusieurs rapports prédéfinis pour l’analyse des données|
||---------*Traduire*---------|
|Requête à Power BI| Convertit une requête en un format adapté à l’utilisation de dans Power BI|
||---------*Options*---------|
|Réinitialiser les options| Définit les valeurs par défaut des paramètres d’application|
|Options| Ouvre un outil permettant de configurer les paramètres de l’application. [Détails](kusto-explorer-options.md)|



### <a name="management-tab"></a>Onglet gestion

![onglet gestion](./Images/KustoTools-KustoExplorer/management-tab.png "onglet gestion")

|Menu             | Comportement|
|-----------------|---------|
||---------*Principaux autorisés*---------|
|Gérer les principaux autorisés du cluster |Active la gestion des principaux d’un cluster pour les utilisateurs autorisés| 
|Gérer les principaux autorisés de la base de données | Active la gestion des principaux d’une base de données pour les utilisateurs autorisés| 
|Gérer les principaux autorisés de table | Active la gestion des principaux d’une table pour les utilisateurs autorisés| 
|Gérer les principaux autorisés de la fonction | Permet de gérer les principaux d’une fonction pour les utilisateurs autorisés| 

### <a name="help-tab"></a>Onglet aide

![onglet aide](./Images/KustoTools-KustoExplorer/help-tab.png "onglet aide")

|Menu             | Comportement|
|-----------------|---------|
||---------*Correspondante*---------|
|Aide             | Ouvre un lien vers la documentation en ligne de Kusto  | 
|Nouveautés       | Ouvre un document qui répertorie toutes les modifications apportées à Kusto. Explorer|
|Signaler un problème      |Ouvre une boîte de dialogue avec deux options : <ul><li>Signaler des problèmes liés au service</li><li>Signaler des problèmes dans l’application cliente</li></ul> | 
|Suggérer une fonctionnalité  | Ouvre un lien vers le Forum de commentaires Kusto | 
|Vérifier les mises à jour     | Vérifie s’il existe des mises à jour de votre version de Kusto. Explorer | 

## <a name="connections-panel"></a>Panneau connexions

![texte de remplacement](./Images/KustoTools-KustoExplorer/connectionsPanel.png "connexions-panneau") 

Le volet gauche de Kusto. Explorer affiche toutes les connexions de cluster avec lesquelles le client est configuré. Pour chaque cluster, il affiche les bases de données, les tables et les attributs (colonnes) qu’ils stockent. Le panneau connexions vous permet de sélectionner des éléments (qui définit un contexte implicite pour la recherche/requête dans le volet principal) ou de double-cliquer sur des éléments pour copier le nom dans le panneau recherche/requête.

Si le schéma réel est volumineux (par exemple, une base de données contenant des centaines de tables), il est possible de rechercher le schéma en appuyant sur CTRL + F et en entrant une sous-chaîne (ne respectant pas la casse) du nom de l’entité que vous recherchez.

Kusto. Explorer prend en charge le contrôle du panneau de connexion à partir de la fenêtre de requête.
Cela est très utile pour les scripts. Par exemple, en démarrant un fichier de script avec une commande qui indique à Kusto. Explorer de se connecter au cluster/à la base de données dont les données sont interrogées par le script est possible en utilisant la syntaxe suivante. Comme d’habitude, vous devrez exécuter chaque ligne à l’aide de `F5` ou de la même manière :

```kusto
#connect cluster('help').database('Samples')

StormEvents | count
```

### <a name="controlling-the-user-identity-used-for-connecting-to-kusto"></a>Contrôle de l’identité de l’utilisateur utilisée pour la connexion à Kusto

Lors de l’ajout d’une nouvelle connexion, le modèle de sécurité par défaut utilisé est la sécurité Azure-Federated, dans laquelle l’authentification est effectuée via le Azure Active Directory à l’aide de l’expérience utilisateur AAD par défaut.

Dans certains cas, vous pouvez avoir besoin d’un contrôle plus fin sur les paramètres d’authentification que ceux disponibles dans AAD. Dans ce cas, il est possible de développer la zone d’édition « avancé : chaînes de connexion » et de fournir une valeur de [chaîne de connexion Kusto](../api/connection-strings/kusto.md) valide.

Par exemple, les utilisateurs qui ont une présence dans plusieurs locataires AAD doivent parfois utiliser une « projection » particulière de leurs identités sur un locataire AAD spécifique. Pour ce faire, vous pouvez fournir une chaîne de connexion telle que celle ci-dessous (remplacez les mots en MAJUSCULEs par des valeurs spécifiques) :

```
Data Source=https://CLUSTER_NAME.kusto.windows.net;Initial Catalog=DATABASE_NAME;AAD Federated Security=True;Authority Id=AAD_TENANT_OF_CLUSTER;User=USER_DOMAIN
```

Ce qui est unique, c’est qu’il s’agit d' `AAD_TENANT_OF_CLUSTER` un nom de domaine ou d’un ID de locataire AAD (Guid) du locataire AAD dans lequel le cluster est hébergé (généralement le nom de domaine de l’organisation qui possède le cluster, tel que `contoso.com` ), et USER_DOMAIN est l’identité de l’utilisateur invité dans ce locataire (par exemple, `joe@fabrikam.com` ). 

>[!Note]
> Le nom de domaine de l’utilisateur n’est pas nécessairement le même que celui du locataire qui héberge le cluster.

## <a name="table-row-colors"></a>Couleurs des lignes de tableau

Kusto. Explorer tente de « deviner » le niveau de gravité ou de détail de chaque ligne dans le volet de résultats et de le colorier en conséquence. Pour ce faire, il met en correspondance les valeurs distinctes de chaque colonne avec un ensemble de modèles connus (« Warning », « Error », etc.).

Pour modifier le modèle de couleur de sortie, ou désactiver ce comportement, dans le menu **Outils** , sélectionnez **options**  >  **visionneuse des résultats**  >  **Commentaires schéma de couleurs**.

![texte de remplacement](./Images/KustoTools-KustoExplorer/ke-color-scheme.png)

## <a name="search-mode"></a>Mode de recherche + +

1. Dans l’onglet dossier de démarrage, dans la liste déroulante requête, sélectionnez « Rechercher + + ».
2. Sélectionnez **plusieurs tables** , puis, sous **choisir des tables**, définissez les tables à rechercher.
3. Dans la zone d’édition, entrez votre expression de recherche et sélectionnez **Go**
4. Une carte thermique de la grille de l’emplacement de table/heure affiche le terme qui apparaît et l’endroit où ils apparaissent
5. Sélectionnez une cellule dans la grille et sélectionnez **afficher les détails** pour afficher les entrées appropriées

![texte de remplacement](./Images/KustoTools-KustoExplorer/ke-search-beta.jpg "ke-Search-version bêta") 

## <a name="query-mode"></a>Mode de requête

Kusto. Explorer dispose d’un puissant mode de script qui vous permet d’écrire, de modifier et d’exécuter des requêtes ad hoc. Le mode de script est fourni avec la mise en surbrillance de la syntaxe et IntelliSense, ce qui vous permet de progresser rapidement jusqu’au langage Kusto CSL.

### <a name="basic-queries"></a>Requêtes de base

Si vous avez des journaux de table, vous pouvez commencer à les explorer en entrant ce qui suit :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | count 
```

Quand votre curseur est positionné sur cette ligne, sa couleur est grisée. Appuyez sur F5 pour exécuter la requête. 

Voici quelques exemples de requêtes :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Take 10 lines from the table. Useful to get familiar with the data
StormEvents | limit 10 
```

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Filter by EventType == 'Flood' and State == 'California' (=~ means case insensitive) 
// and take sample of 10 lines
StormEvents 
| where EventType == 'Flood' and State =~ 'California'
| limit 10
```

## <a name="importing-a-local-file-into-a-kusto-table"></a>Importation d’un fichier local dans une table Kusto

Kusto. Explorer offre un moyen pratique de charger des fichiers à partir de votre ordinateur dans une table Kusto.

1. Assurez-vous que vous avez créé la table avec un schéma qui correspond à votre fichier (par exemple, à l’aide de la commande [. Create table](../management/tables.md) )

1. Assurez-vous que l’extension de fichier est adaptée au contenu du fichier. Par exemple :
    * Si votre fichier contient des valeurs séparées par des virgules, assurez-vous que votre fichier a une extension. csv.
    * Si votre fichier contient des valeurs séparées par des tabulations, assurez-vous que votre fichier a une extension. TSV.

1. Cliquez avec le bouton droit sur la base de données cible dans le [panneau connexions](#connections-panel), puis sélectionnez **Actualiser**pour que votre table s’affiche.

    ![texte de remplacement](./Images/KustoTools-KustoExplorer/right-click-refresh-schema.png "Cliquez avec le bouton droit sur-Refresh-Schema")

1. Cliquez avec le bouton droit sur la table cible dans le [panneau connexions](#connections-panel), puis sélectionnez **importer des données à partir de fichiers locaux**.

    ![texte de remplacement](./Images/KustoTools-KustoExplorer/right-click-import-local-file.png "Cliquez avec le bouton droit sur-Import-local-file")

1. Sélectionnez le ou les fichiers à charger, puis sélectionnez **ouvrir**.

    ![texte de remplacement](./Images/KustoTools-KustoExplorer/import-local-file-choose-files.png "Import-local-file-Choose-Files")

    La barre de progression affiche la progression et une boîte de dialogue s’affiche lorsque l’opération se termine

    ![texte de remplacement](./Images/KustoTools-KustoExplorer/import-local-file-progress.png "importation-local-fichier-progression")

    ![texte de remplacement](./Images/KustoTools-KustoExplorer/import-local-file-complete.png "importation-fichier local-complet")

1. Interrogez les données de votre table (double-cliquez sur la table dans le [panneau connexions](#connections-panel)).

## <a name="managing-authorized-principals"></a>Gestion des principaux autorisés

Kusto. Explorer offre un moyen pratique de gérer les principaux autorisés de cluster, de base de données, de table ou de fonction.

> [!Note]
> Seuls les [administrateurs](../management/access-control/role-based-authorization.md) peuvent ajouter ou supprimer des principaux autorisés dans leur propre étendue.

1. Cliquez avec le bouton droit sur l’entité cible dans le [panneau connexions](#connections-panel), puis sélectionnez **gérer les principaux autorisés**. (Vous pouvez également le faire à partir du menu gestion).

    ![texte de remplacement](./Images/KustoTools-KustoExplorer/right-click-manage-authorized-principals.png "Cliquez avec le bouton droit sur-Manage-Authorized-principal")

    ![texte de remplacement](./Images/KustoTools-KustoExplorer/manage-authorized-principals-window.png "Manage-Authorized-principaux-Window")

1. Pour ajouter un nouveau principal autorisé, sélectionnez **Ajouter un principal**, fournissez les détails du principal et confirmez l’action.

    ![texte de remplacement](./Images/KustoTools-KustoExplorer/add-authorized-principals-window.png "Add-Authorized-principaux-Window")

    ![texte de remplacement](./Images/KustoTools-KustoExplorer/confirm-add-authorized-principals.png "confirmer-Add-Authorized-principal")

1. Pour supprimer un principal autorisé existant, sélectionnez **Drop principal** et confirmez l’action.

    ![texte de remplacement](./Images/KustoTools-KustoExplorer/confirm-drop-authorized-principals.png "confirmer la suppression des principaux autorisés")

## <a name="sharing-queries-and-results-by-email"></a>Partage de requêtes et de résultats par courrier électronique

Kusto. Explorer offre un moyen pratique de partager des requêtes et des résultats de requête par courrier électronique. Sélectionnez **Exporter dans le presse-papiers**, et Kusto. Explorer copie les éléments suivants dans le presse-papiers :
1. Votre requête
1. Résultats de la requête (tableau ou graphique)
1. Détails de connexion pour le cluster et la base de données Kusto
1. Un lien qui réexécute automatiquement la requête

Fonctionnement de l’opération :

1. Exécuter une requête dans Kusto. Explorer
1. Sélectionnez **Exporter dans le presse-papiers** (ou appuyez sur `Ctrl+Shift+C` )

    ![texte de remplacement](./Images/KustoTools-KustoExplorer/menu-export.png "menu-exporter")

1. Ouvrez, par exemple, un nouveau message Outlook.

    ![texte de remplacement](./Images/KustoTools-KustoExplorer/share-results.png "partage-résultats")
    
1. Collez le contenu du presse-papiers dans le message Outlook.

    ![texte de remplacement](./Images/KustoTools-KustoExplorer/share-results-2.png "partage-résultats-2")

## <a name="client-side-query-parametrization"></a>Parametrization de requête côté client

> [!WARNING]
> Il existe deux types de techniques de requête parametrization dans Kusto :
> * La [parametrization de requête intégrée au langage](../query/queryparametersstatement.md) est implémentée dans le cadre du moteur de requête et destinée à être utilisée par les applications qui interrogent le service par programme.
>
> * La requête côté client parametrization, décrite ci-dessous, est une fonctionnalité de l’application Kusto. Explorer uniquement. Cela équivaut à utiliser des opérations de remplacement de chaîne sur les requêtes avant de les envoyer à exécuter par le service. La syntaxe décrite ci-dessous ne fait pas partie du langage de requête lui-même et ne peut pas être utilisée lors de l’envoi de requêtes au service par d’autres moyens que Kusto. Explorer.

Si vous envisagez d’utiliser la même valeur dans plusieurs requêtes ou dans plusieurs onglets, il sera difficile de la modifier. Toutefois, Kusto. Explorer prend en charge les paramètres de requête. Les paramètres sont dénotés par des {} crochets. Par exemple : `{parameter1}`

L’éditeur de script met en surbrillance les paramètres de requête :

![texte de remplacement](./Images/KustoTools-KustoExplorer/parametrized-query-1.png "paramétrée-requête-1")

Vous pouvez facilement définir et modifier des paramètres de requête existants :

![texte de remplacement](./Images/KustoTools-KustoExplorer/parametrized-query-2.png "paramétrée-requête-2")

![texte de remplacement](./Images/KustoTools-KustoExplorer/parametrized-query-3.png "paramétrée-requête-3")

L’éditeur de script dispose également d’IntelliSense pour les paramètres de requête qui sont déjà définis :

![texte de remplacement](./Images/KustoTools-KustoExplorer/parametrized-query-4.png "paramétrée-requête-4")

Il peut y avoir plusieurs ensembles de paramètres (répertoriés dans la zone de liste déroulante **Parameters Set** ).
Utilisez les paramètres **Ajouter nouveau** et **supprimer le en cours** pour manipuler la liste des jeux de paramètres.

![texte de remplacement](./Images/KustoTools-KustoExplorer/parametrized-query-5.png "paramétrée-requête-5")

Les paramètres de requête sont partagés entre les onglets, afin qu’ils puissent être facilement réutilisés.

## <a name="deep-linking-queries"></a>Requêtes de liaison profonde

### <a name="overview"></a>Vue d’ensemble
Vous pouvez créer un URI qui, lorsqu’il est ouvert dans un navigateur, Kusto. Explorer démarre localement et exécute une requête spécifique sur une base de données Kusto spécifiée.

### <a name="limitations"></a>Limitations
Les requêtes sont limitées à environ 2000 caractères en raison des limitations d’Internet Explorer (la limitation est approximative, car elle dépend de la longueur du nom de la base de données et du cluster) https://support.microsoft.com/kb/208427 pour réduire les chances que vous atteigniez la limite de caractères, consultez la section [obtenir des liens plus courts](#getting-shorter-links), ci-dessous.

Le format de l’URI est : https:// <ClusterCname> . Kusto.Windows.NET/ <DatabaseName> ? Query =<QueryToExecute>

Par exemple :  https://help.kusto.windows.net/Samples?query=StormEvents+%7c+limit+10
 
Cet URI ouvre Kusto. Explorer, se connecte au `help` cluster Kusto et exécute la requête spécifiée sur la `Samples` base de données. Si une instance de Kusto. Explorer est déjà en cours d’exécution, l’instance en cours d’exécution ouvrira un nouvel onglet et exécutera la requête dans celle-ci.)

**Note de sécurité**: pour des raisons de sécurité, la liaison profonde est désactivée pour les commandes de contrôle.



### <a name="creating-a-deep-link"></a>Création d’un lien profond
Le moyen le plus simple de créer un lien profond consiste à créer votre requête dans Kusto. Explorer, puis à utiliser exporter dans le presse-papiers pour copier la requête (y compris le lien profond et les résultats) dans le presse-papiers. Vous pouvez ensuite le partager par courrier électronique.
        
En cas de copie dans un message électronique, le lien profond est affiché dans les petites polices ; par exemple :

https://help.kusto.windows.net:443/Samples[[Cliquez pour exécuter la requête](https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d)] 

Le premier lien ouvre Kusto. Explorer et définit le contexte du cluster et de la base de données de manière appropriée.
Le deuxième lien (Cliquer pour exécuter la requête) est le lien profond. Si vous accédez au lien vers un message électronique et que vous appuyez sur CTRL-K, vous pouvez voir l’URL réelle :

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

### <a name="deep-links-and-parametrized-queries"></a>Liens profond et requêtes paramétrée

Vous pouvez utiliser des requêtes paramétrée avec des liens approfondis.

1. Créer une requête à former en tant que requête paramétrée (par exemple, `KustoLogs | where Timestamp > ago({Period}) | count` ) 
2. Fournissez un paramètre pour chaque paramètre de requête dans l’URI dans le cas suivant :

`https://mycluster.kusto.windows.net/MyDatabase?web=0&query=KustoLogs+%7c+where+Timestamp+>+ago({Period})+%7c+count&Period=1h`

### <a name="getting-shorter-links"></a>Obtenir des liens plus courts

Les requêtes peuvent devenir longues. Pour réduire le risque que la requête dépasse la longueur maximale, utilisez la `String Kusto.Data.Common.CslCommandGenerator.EncodeQueryAsBase64Url(string query)` méthode disponible dans la bibliothèque cliente Kusto. Cette méthode produit une version plus compacte de la requête. Le format plus petit est également reconnu par Kusto. Explorer.

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

La requête est rendue plus compacte en appliquant la transformation suivante :

```csharp
 UrlEncode(Base64Encode(GZip(original query)))
```

## <a name="kustoexplorer-command-line-arguments"></a>Arguments de ligne de commande Kusto. Explorer

Kusto. Explorer prend en charge plusieurs arguments de ligne de commande dans la syntaxe suivante (l’ordre est important) :

[*LocalScriptFile*] [*QueryString*]

Où :
* *LocalScriptFile* est le nom d’un fichier de script sur l’ordinateur local qui doit avoir l’extension `.kql` . Si un tel fichier existe, Kusto. Explorer charge automatiquement ce fichier lors de son démarrage.
* *QueryString* est une chaîne formatée à l’aide de la mise en forme de chaîne de requête http. Cette méthode fournit des propriétés supplémentaires, comme décrit dans le tableau ci-dessous.

Par exemple, pour démarrer Kusto. Explorer avec un fichier de script nommé `c:\temp\script.kql` et configuré pour communiquer avec `help` le cluster, base de données `Samples` , utilisez la commande suivante :

```
Kusto.Explorer.exe c:\temp\script.kql uri=https://help.kusto.windows.net/Samples;Fed=true&name=Samples
```

|Argument  |Description                                                               |
|----------|--------------------------------------------------------------------------|
|**Requête à exécuter**                                                                 |
|`query`   |Requête à exécuter (encodée en base64). S’il est vide, utilisez `querysrc` .          |
|`querysrc`|URL d’un fichier ou d’un objet blob contenant la requête à exécuter (si `query` est vide).|
|**Connexion au cluster Kusto**                                                  |
|`uri`     |Chaîne de connexion du cluster Kusto auquel se connecter.                 |
|`name`    |Nom complet de la connexion au cluster Kusto.                  |
|**Groupe de connexions**                                                                 |
|`path`    |URL d’un fichier de groupe de connexions à télécharger (encodé URL).             |
|`group`   |Nom du groupe de connexions.                                         |
|`filename`|Fichier local contenant le groupe de connexions.                              |

## <a name="kustoexplorer-connection-files"></a>Fichiers de connexion Kusto. Explorer

Kusto. Explorer conserve ses paramètres de connexion dans le `%LOCALAPPDATA%\Kusto.Explorer` dossier.
Une liste de groupes de connexions est conservée dans `%LOCALAPPDATA%\Kusto.Explorer\UserConnectionGroups.xml` , et chaque groupe de connexions est conservé dans un fichier dédié sous `%LOCALAPPDATA%\Kusto.Explorer\Connections\` .

### <a name="format-of-connection-group-files"></a>Format des fichiers de groupe de connexions

L’emplacement du fichier est `%LOCALAPPDATA%\Kusto.Explorer\UserConnectionGroups.xml` .  

Il s’agit d’une sérialisation XML d’un tableau d' `ServerGroupDescription` objets avec les propriétés suivantes :

```
  <ServerGroupDescription>
    <Name>`Connection Group name`</Name>
    <Details>`Full path to XML file containing the list of connections`</Details>
  </ServerGroupDescription>
```

Exemple :

```
<?xml version="1.0"?>
<ArrayOfServerGroupDescription xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
  <ServerGroupDescription>
    <Name>Connections</Name>
    <Details>C:\Users\alexans\AppData\Local\Kusto.Explorer\UserConnections.xml</Details>
  </ServerGroupDescription>
</ArrayOfServerGroupDescription>  
```

### <a name="format-of-connection-list-files"></a>Format des fichiers de liste de connexions

L’emplacement du fichier est : `%LOCALAPPDATA%\Kusto.Explorer\Connections\` .

Il s’agit d’une sérialisation XML d’un tableau d' `ServerDescriptionBase` objets avec les propriétés suivantes :

```
   <ServerDescriptionBase xsi:type="ServerDescription">
    <Name>`Connection name`</Name>
    <Details>`Details as shown in UX, usually full URI`</Details>
    <ConnectionString>`Full connection string to the cluster`</ConnectionString>
  </ServerDescriptionBase>
```

Exemple :

```
<?xml version="1.0"?>
<ArrayOfServerDescriptionBase xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
  <ServerDescriptionBase xsi:type="ServerDescription">
    <Name>Help</Name>
    <Details>https://help.kusto.windows.net</Details>
    <ConnectionString>Data Source=https://help.kusto.windows.net:443;Federated Security=True</ConnectionString>
  </ServerDescriptionBase>
</ArrayOfServerDescriptionBase>
```

## <a name="resetting-kustoexplorer"></a>Réinitialisation de Kusto. Explorer

Si nécessaire, vous pouvez réinitialiser complètement Kusto. Explorer. Utilisez la procédure suivante pour réinitialiser progressivement Kusto. Explorer déployé sur votre ordinateur, jusqu’à ce qu’il soit complètement supprimé et qu’il soit nécessaire de l’installer à partir de zéro.

1. Dans Windows, ouvrez **modifier ou supprimer des programmes** (également appelés **programmes et fonctionnalités**).
1. Sélectionnez tous les éléments dont le nom commence par `Kusto.Explorer` .
1. Sélectionner **Désinstaller**.

   En cas d’échec de désinstallation de l’application (problème connu parfois avec les applications ClickOnce), consultez [cet article de dépassement de capacité](https://stackoverflow.com/questions/10896223/how-do-i-completely-uninstall-a-clickonce-application-from-my-computer) de la pile qui explique comment procéder.

1. Supprimez le dossier `%LOCALAPPDATA%\Kusto.Explorer` . Cela supprime toutes les connexions, l’historique, etc.

1. Supprimez le dossier `%APPDATA%\Kusto` . Cela supprime le cache de jetons Kusto. Explorer. Vous devrez vous authentifier de nouveau auprès de tous les clusters.

Il est également possible de revenir à une version spécifique de Kusto. Explorer :

1. Exécutez `appwiz.cpl`.
1. Sélectionnez **Kusto. Explorer** et sélectionnez **Désinstaller/Modifier**.
3. Sélectionnez **restaurer l’application à son état précédent**.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="kustoexplorer-fails-to-start"></a>Kusto. Explorer ne parvient pas à démarrer

#### <a name="kustoexplorer-shows-error-dialog-during-or-after-start-up"></a>Kusto. Explorer affiche la boîte de dialogue d’erreur pendant ou après le démarrage

**Symptôme :**

Au démarrage, Kusto. Explorer affiche une `InvalidOperationException` erreur.

**Solution possible :**

Cette erreur peut suggérer que le système d’exploitation est endommagé ou qu’il manque certains modules essentiels.
Pour vérifier les fichiers système manquants ou endommagés, suivez les étapes décrites ici :   
[https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system](https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system)

### <a name="kustoexplorer-always-downloads-even-when-there-are-no-updates"></a>Kusto. Explorer télécharge toujours même lorsqu’il n’y a aucune mise à jour

**Symptôme :**

Chaque fois que vous ouvrez Kusto. Explorer, vous êtes invité à installer une nouvelle vue. Kusto. Explorer télécharge le package entier, sans réellement mettre à jour la version déjà installée.

**Solution possible :**

Cela peut être dû à un endommagement dans votre magasin ClickOnce local. Vous pouvez effacer le magasin ClickOnce local en exécutant la commande suivante, dans une invite de commandes avec élévation de privilèges.
> [!Important]
> 1. S’il existe d’autres instances d’applications ClickOnce ou de `dfsvc.exe` , terminez-les avant d’exécuter cette commande.
> 2. Toutes les applications ClickOnce sont réinstallées automatiquement la prochaine fois que vous les exécutez, à condition que vous ayez accès à l’emplacement d’installation d’origine stocké dans le raccourci de l’application. Les raccourcis de l’application ne seront pas supprimés.

```
rd /q /s %userprofile%\appdata\local\apps\2.0
```

Essayez d’installer Kusto. Explorer à nouveau à partir de l’un des [miroirs d’installation](#getting-the-tool).

#### <a name="clickonce-error-cannot-start-application"></a>Erreur ClickOnce : impossible de démarrer l’application

**Dysfonctionnement**  

* Le programme ne parvient pas à démarrer et affiche une erreur contenant les éléments suivants :`External component has thrown an exception`
* Le programme ne parvient pas à démarrer et affiche une erreur contenant les éléments suivants :`Value does not fall within the expected range`
* Le programme ne parvient pas à démarrer et affiche une erreur contenant les éléments suivants :`The application binding data format is invalid.` 
* Le programme ne parvient pas à démarrer et affiche une erreur contenant les éléments suivants :`Exception from HRESULT: 0x800736B2`

Vous pouvez explorer les détails de l’erreur en cliquant `Details` dans la boîte de dialogue d’erreur suivante :

![texte de remplacement](./Images/KustoTools-KustoExplorer/clickonce-err-1.jpg "ClickOnce-ERR-1")

```
Following errors were detected during this operation.
    * System.ArgumentException
        - Value does not fall within the expected range.
        - Source: System.Deployment
        - Stack trace:
            at System.Deployment.Application.NativeMethods.CorLaunchApplication(UInt32 hostType, String applicationFullName, Int32 manifestPathsCount, String[] manifestPaths, Int32 activationDataCount, String[] activationData, PROCESS_INFORMATION processInformation)
            at System.Deployment.Application.ComponentStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.SubscriptionStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.Activate(DefinitionAppId appId, AssemblyManifest appManifest, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.ProcessOrFollowShortcut(String shortcutFile, String& errorPageUrl, TempFile& deployFile)
            at System.Deployment.Application.ApplicationActivator.PerformDeploymentActivation(Uri activationUri, Boolean isShortcut, String textualSubId, String deploymentProviderUrlFromExtension, BrowserSettings browserSettings, String& errorPageUrl)
            at System.Deployment.Application.ApplicationActivator.ActivateDeploymentWorker(Object state)
```

**Étapes de la solution proposées :**

1. Désinstallez l’application Kusto. Explorer à l’aide `Programs and Features` de ( `appwiz.cpl` ).

1. Essayez d’exécuter `CleanOnlineAppCache` , puis réessayez d’installer Kusto. Explorer. À partir d’une invite de commandes avec élévation de privilèges : 
    
    ```
    rundll32 %windir%\system32\dfshim.dll CleanOnlineAppCache
    ```

    Installez Kusto. Explorer à nouveau à partir de l’un des [miroirs d’installation](#getting-the-tool).

1. En cas d’échec, supprimez le magasin ClickOnce local. Toutes les applications ClickOnce sont réinstallées automatiquement la prochaine fois que vous les exécutez, à condition que vous ayez accès à l’emplacement d’installation d’origine stocké dans le raccourci de l’application. Les raccourcis de l’application ne sont pas supprimés.

À partir d’une invite de commandes avec élévation de privilèges :

    ```
    rd /q /s %userprofile%\appdata\local\apps\2.0
    ```

    Install Kusto.Explorer again from one of the [installation mirrors](#getting-the-tool)

1. En cas d’échec, supprimez les fichiers de déploiement temporaires et renommez le dossier Kusto. Explorer local AppData.

    À partir d’une invite de commandes avec élévation de privilèges :

    ```
    rd /s/q %userprofile%\AppData\Local\Temp\Deployment
    ren %LOCALAPPDATA%\Kusto.Explorer Kusto.Explorer.bak
    ```

    Installer Kusto. Explorer à nouveau à partir de l’un des [miroirs d’installation](#getting-the-tool)

1. Pour restaurer vos connexions à partir de Kusto. Explorer. bak, à partir d’une invite de commandes avec élévation de privilèges :

    ```
    copy %LOCALAPPDATA%\Kusto.Explorer.bak\User*.xml %LOCALAPPDATA%\Kusto.Explorer
    ```

1. Si elle échoue toujours, activez la journalisation ClickOnce en créant une valeur de chaîne LogVerbosityLevel de 1 sous :

`HKEY_CURRENT_USER\Software\Classes\Software\Microsoft\Windows\CurrentVersion\Deployment`, reproduisez-le, puis envoyez la sortie détaillée à KEBugReport@microsoft.com . 

#### <a name="clickonce-error-your-administrator-has-blocked-this-application-because-it-potentially-poses-a-security-risk-to-your-computer"></a>Erreur ClickOnce : votre administrateur a bloqué cette application, car elle risque de poser un problème de sécurité sur votre ordinateur

**Symptôme :**  
Le programme ne parvient pas à s’installer avec l’une des erreurs suivantes :
* `Your administrator has blocked this application because it potentially poses a security risk to your computer`.
* `Your security settings do not allow this application to be installed on your computer.`

**Solution :**

1. Cela peut être dû au fait qu’une autre application remplace le comportement par défaut de l’invite d’approbation ClickOnce. Vous pouvez afficher vos paramètres de configuration par défaut, les comparer aux valeurs réelles sur votre ordinateur et les réinitialiser si nécessaire, comme expliqué [dans cet article de procédure](https://docs.microsoft.com/visualstudio/deployment/how-to-configure-the-clickonce-trust-prompt-behavior).

#### <a name="cleanup-application-data"></a>Nettoyer les données d’application

Parfois, lorsque les étapes de dépannage précédentes n’ont pas permis d’obtenir Kusto. Explorer pour démarrer, le nettoyage des données stockées localement peut être utile.

Les données stockées par l’application Kusto. Explorer se trouvent ici : `C:\Users\\[your alias]\AppData\Local\Kusto.Explorer` .

> [!NOTE]
> Le nettoyage des données entraînera la perte des onglets ouverts (dossier de récupération), des connexions enregistrées (dossier des connexions) et des paramètres d’application (dossier UserSettings).