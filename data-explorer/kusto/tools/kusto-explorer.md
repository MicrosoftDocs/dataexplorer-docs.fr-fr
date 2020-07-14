---
title: Prise en main de Kusto. Explorer
description: Découvrez les fonctionnalités de Kusto. Explorer et comment il peut vous aider à explorer vos données
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: overview
ms.date: 05/19/2020
ms.openlocfilehash: 1f3b273260451dc0ce36730d20f1bc357a453397
ms.sourcegitcommit: 2126c5176df272d149896ac5ef7a7136f12dc3f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2020
ms.locfileid: "86280535"
---
# <a name="getting-started-with-kustoexplorer"></a>Prise en main de Kusto. Explorer

Kusto. Explorer est une application de bureau riche qui vous permet d’explorer vos données à l’aide du langage de requête Kusto dans une interface utilisateur facile à utiliser. Cette présentation explique comment prendre en main la configuration de votre Kusto. Explorer et explique l’interface utilisateur que vous allez utiliser.

Avec Kusto. Explorer, vous pouvez :
* [Interrogez vos données](kusto-explorer-using.md#query-mode).
* [Rechercher des données](kusto-explorer-using.md#search-mode) dans plusieurs tables.
* [Visualisez vos données](#visualizations-section) dans un large éventail de graphiques.
* [Partager des requêtes et des résultats](kusto-explorer-using.md#share-queries-and-results) par courrier électronique ou à l’aide de liens ciblés.

## <a name="installing-kustoexplorer"></a>Installation de Kusto. Explorer

* Installez l' [outil Kusto. Explorer](https://aka.ms/ke).

* Au lieu de cela, accédez à votre cluster Kusto avec votre navigateur à l’adresse suivante :`https://<your_cluster>.kusto.windows.net.`
   Remplacez &lt; your_cluster &gt; par le nom de votre cluster Azure Explorateur de données.

### <a name="using-chrome-and-kustoexplorer"></a>Utilisation de chrome et Kusto. Explorer

Si vous utilisez Chrome comme navigateur par défaut, veillez à installer l’extension ClickOnce pour Chrome :

[https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US](https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US)

## <a name="overview-of-the-user-interface"></a>Vue d’ensemble de l’interface utilisateur

L’interface utilisateur de Kusto. Explorer est conçue à l’aide d’onglets et de panneaux, similaires à ceux d’autres produits Microsoft : 

1. Parcourir les onglets du [panneau de menu](#menu-panel) pour effectuer diverses opérations
2. Gérer vos connexions dans le [panneau connexions](#connections-panel)
3. Créer des scripts à exécuter dans le panneau script
4. Afficher les résultats des scripts dans le volet de résultats

:::image type="content" source="images/kusto-explorer/ke-start.png" alt-text="Démarrage de Kusto Explorer":::

## <a name="menu-panel"></a>Panneau de menu

Le panneau de menu Kusto. Explorer comprend les onglets suivants :

* [Page d'accueil](#home-tab)
* [Fichier](#file-tab)
* [Connexions](#connections-tab)
* [Visualiser](#view-tab)
* [outils](#tools-tab)
* [Surveillance](#monitoring-tab)
* [Gestion](#management-tab)
* [Aide](#help-tab)

### <a name="home-tab"></a>Onglet Dossier de base

:::image type="content" source="images/kusto-explorer/home-tab.png" alt-text="Onglet de page d’Kusto Explorer":::

L’onglet dossier de démarrage affiche les fonctions les plus récemment utilisées, divisées en sections :

* [Requête](#query-section)
* [Partager](#share-section)
* [Visualisations](#visualizations-section)
* [Visualiser](#view-section)
* [Aide](#help-tab) 

### <a name="query-section"></a>Section de requête

:::image type="content" source="images/kusto-explorer/home-query-menu.png" alt-text="Menu requête Kusto Explorer":::

|Menu|    Comportement|
|----|----------|
|Liste déroulante mode | <ul><li>Mode requête : bascule la fenêtre de requête en [mode script](kusto-explorer-using.md#query-mode). Les commandes peuvent être chargées et enregistrées en tant que scripts (par défaut)</li> <li> Mode de recherche : un mode de requête unique où chaque commande entrée est traitée immédiatement et présente un résultat dans la fenêtre de résultats</li> <li>Mode de recherche + + : permet de rechercher un terme à l’aide de la syntaxe de recherche sur une ou plusieurs tables. En savoir plus sur l’utilisation du [mode de recherche + +](kusto-explorer-using.md#search-mode)</li></ul> |
|Nouvel onglet| Ouvre un nouvel onglet pour interroger Kusto |

### <a name="share-section"></a>Partager la section

:::image type="content" source="images/kusto-explorer/home-share-menu.png" alt-text="Menu de partage de l’Explorateur Kusto":::

|Menu|    Comportement|
|----|----------|
|Données dans le presse-papiers|    Exporte la requête et le jeu de données dans le presse-papiers. Si un graphique est présenté, il exporte le graphique en tant qu’image bitmap| 
|Résultat dans le presse-papiers| Exporte le jeu de données dans le presse-papiers. Si un graphique est présenté, il exporte le graphique en tant qu’image bitmap| 
|Requête dans le presse-papiers| Exporte la requête dans le presse-papiers|

### <a name="visualizations-section"></a>Section visualisations

:::image type="content" source="images/kusto-explorer/home-visualizations-menu.png" alt-text="Visualisations du menu Kusto Explorer":::

|Menu         | Comportement|
|-------------|---------|
|Graphique en aires      | Affiche un graphique en aires dans lequel l’axe des X est la première colonne (doit être numérique). Toutes les colonnes numériques sont mappées à différentes séries (axe Y) |
|Histogramme | Affiche un histogramme dans lequel toutes les colonnes numériques sont mappées à différentes séries (axe Y). La colonne de texte avant Numeric est l’axe des X (peut être contrôlé dans l’interface utilisateur)|
|Graphique à barres    | Affiche un graphique à barres dans lequel toutes les colonnes numériques sont mappées à différentes séries (axe X). La colonne de texte avant Numeric est l’axe Y (peut être contrôlée dans l’interface utilisateur)|
|graphique à aires empilées      | Affiche un graphique en aires empilées dans lequel l’axe des X est la première colonne (doit être numérique). Toutes les colonnes numériques sont mappées à différentes séries (axe Y) |
|Graphique chronologique   | Affiche un graphique temporel dans lequel l’axe des X est la première colonne (doit être DateTime). Toutes les colonnes numériques sont mappées à différentes séries (axe Y).|
|Graphique en courbes   | Affiche un graphique en courbes dans lequel l’axe des X est la première colonne (doit être numérique). Toutes les colonnes numériques sont mappées à différentes séries (axe Y).|
|[Graphique d’anomalies](#anomaly-chart)|    Semblable à graphique temporel, mais détecte les anomalies dans les données de séries chronologiques, à l’aide de l’algorithme Machine Learning anomalies. Pour la détection d’anomalies, Kusto. Explorer utilise la fonction [series_decompose_anomalies](../query/series-decompose-anomaliesfunction.md) .
|Graphique à secteurs    |    Affiche un graphique à secteurs dans lequel l’axe des couleurs est la première colonne. L’axe thêta (doit être une mesure, convertie en pourcentage) est la deuxième colonne.|
|Échelle de temps |    Affiche un graphique en échelle dans lequel l’axe des X correspond aux deux dernières colonnes (doit être DateTime). L’axe des Y est un composite des autres colonnes.|
|Nuage de points| Affiche un graphique à points dans lequel l’axe des X est la première colonne (doit être numérique). Toutes les colonnes numériques sont mappées à différentes séries (axe Y).|
|Graphique croisé dynamique  | Affiche un tableau croisé dynamique et un graphique croisé dynamique qui donne la flexibilité totale de la sélection de données, de colonnes, de lignes et de différents types de graphiques.| 
|Tableau croisé dynamique Time   | Navigation interactive sur la ligne de temps des événements (pivotement sur l’axe du temps)|

> [!NOTE]
> <a id="anomaly-chart">Graphique d’anomalies</a>: l’algorithme attend des données TimeSeries, qui se composent de deux colonnes :
>* Durée dans les compartiments d’intervalles fixes
>* Valeur numérique pour la détection d’anomalies afin de produire des données TimeSeries dans Kusto. Explorer, résumez par le champ d’heure et spécifiez l’emplacement de la plage de temps.

### <a name="view-section"></a>Section de vue

:::image type="content" source="images/kusto-explorer/home-view-menu.png" alt-text="Menu Affichage Explorateur Kusto":::

|Menu           | Comportement|
|---------------|---------|
|Mode d’affichage complet | Maximise l’espace de travail en masquant le menu du ruban et le panneau de connexion. Quittez le mode d’affichage complet en sélectionnant **démarrage**  >  **en mode d’affichage complet**ou en appuyant sur **F11**.|
|Masquer les colonnes vides| Supprime les colonnes vides de la grille de données|
|Réduire les colonnes singulières| Réduit les colonnes avec des valeurs singulières|
|Explorer les valeurs de colonne| Affiche la distribution des valeurs de colonne|
|Augmenter la police  | Augmente la taille de police de l’onglet de requête et de la grille de données des résultats|  
|Réduire la police  | Diminue la taille de police de l’onglet de requête et de la grille de données des résultats|

>[!NOTE]
> Paramètres de la vue de données :
>
> Kusto. Explorer effectue le suivi des paramètres utilisés par ensemble de colonnes unique. Lorsque les colonnes sont réorganisées ou supprimées, la vue de données est enregistrée et est réutilisée chaque fois que les données contenant les mêmes colonnes sont récupérées. Pour rétablir les valeurs par défaut de la vue, sélectionnez **Réinitialiser la vue**dans l’onglet **affichage** . 

## <a name="file-tab"></a>Onglet Fichier

:::image type="content" source="images/kusto-explorer/file-tab.png" alt-text="Onglet fichier de Kusto Explorer":::

|Menu| Comportement|
|---------------|---------|
||---------*Script de requête*---------|
|Nouvel onglet | Ouvre une nouvelle fenêtre d’onglet pour l’interrogation de Kusto |
|Ouvrir un fichier| Charge des données à partir d’un fichier *. kql dans le panneau de script actif|
|Enregistrer dans le fichier| Enregistre le contenu du panneau de script actif dans le fichier *. kql|
|Fermer l’onglet| Ferme la fenêtre active de l’onglet|
||---------*Enregistrer des données*---------|
|Données au format CSV       | Exporte des données vers un fichier CSV (valeurs séparées par des virgules)| 
|Données au format JSON      | Exporte des données vers un fichier au format JSON|
|Données vers Excel     | Exporte des données vers un fichier XLSX (Excel)|
|Données en texte      | Exporte des données vers un fichier TXT (texte)| 
|Script de données vers KQL| Exporte la requête dans un fichier de script| 
|Données aux résultats   | Exporte la requête et les données dans un fichier de résultats (QRES)|
|Exécuter la requête au format CSV |Exécute une requête et enregistre les résultats dans un fichier CSV local|
||---------*Charger les données*---------|
|À partir des résultats|    Charge la requête et les données à partir d’un fichier de résultats (QRES)| 
||---------*Papier*---------|
|Requête et résultats dans le presse-papiers|    Exporte la requête et le jeu de données dans le presse-papiers. Si un graphique est présenté, il exporte le graphique en tant qu’image bitmap| 
|Résultat dans le presse-papiers| Exporte le jeu de données dans le presse-papiers. Si un graphique est présenté, il exporte le graphique en tant qu’image bitmap| 
|Requête dans le presse-papiers| Exporte la requête dans le presse-papiers|
||---------*About*---------|
|Effacer le cache des résultats| Efface les résultats mis en cache des requêtes exécutées précédemment| 

## <a name="connections-tab"></a>Onglet Connexions

:::image type="content" source="images/kusto-explorer/connections-tab.png" alt-text="Onglet Connexions de l’Explorateur Kusto":::

|Menu|Comportement|
|----|----------|
||---------*Ceux*---------|
|Ajout d’un groupe| Ajoute un nouveau groupe de serveurs Kusto|
|Renommer le groupe| Renomme le groupe de serveurs Kusto existant|
|Retirer le groupe| Supprime le groupe de serveurs Kusto existant.|
||---------*Clusters*---------|
|Importer des connexions| Importe des connexions à partir d’un fichier en spécifiant des connexions|
|Exporter les connexions| Exporte des connexions dans un fichier|
|Ajouter une connexion| Ajoute une nouvelle connexion au serveur Kusto| 
|Modifier la connexion| Ouvre une boîte de dialogue pour la modification des propriétés de connexion du serveur Kusto|
|Supprimer la connexion| Supprime la connexion existante au serveur Kusto|
|Actualiser| Actualise les propriétés d’une connexion au serveur Kusto|
||---------*Caution*---------|
|Inspecter votre principal d’ajout| Affiche les détails de l’utilisateur actif actuel|
|Déconnexion d’AAD| Déconnecte l’utilisateur actuel de la connexion à AAD|
||---------*Étendue des données*---------|
|Portée de la mise en cache|<ul><li>Requêtes DataExecute à chaud uniquement sur le [cache de données à chaud](../management/cachepolicy.md)</li><li>Toutes les données : exécuter les requêtes sur toutes les données disponibles (par défaut)</li></ul> |
|Colonne DateTime| Nom de la colonne qui peut être utilisé pour le pré-filtre de l’heure|
|Filtre de temps| Valeur de l’heure avant le filtre|

## <a name="view-tab"></a>Onglet Affichage

:::image type="content" source="images/kusto-explorer/view-tab.png" alt-text="Onglet de vue de l’Explorateur Kusto":::

|Menu|Comportement|
|----|----------|
||---------*Présence*---------|
|Mode d’affichage complet | Maximise l’espace de travail en masquant le menu du ruban et le panneau de connexion|
|Augmenter la police  | Augmente la taille de police de l’onglet de requête et de la grille de données des résultats|  
|Réduire la police  | Diminue la taille de police de l’onglet de requête et de la grille de données des résultats|
|Réinitialiser la disposition|Réinitialise la disposition des contrôles et fenêtres d’ancrage de l’outil.|
|Renommer l’onglet de document |Renommer l’onglet sélectionné |
||---------*Vue de données*---------|
|Réinitialiser l’affichage| Rétablit les valeurs par défaut des paramètres de la [vue de données](#dvs) |
|Explorer les valeurs de colonne|Affiche la distribution des valeurs de colonne|
|Focus sur les statistiques sur les requêtes|Change le focus en statistiques sur les requêtes au lieu des résultats de la requête lors de la fin de la requête|
|Masquer les doublons|Active/désactive la suppression des lignes en double dans les résultats de la requête|
|Masquer les colonnes vides|Active/désactive la suppression de colonnes vides dans les résultats de la requête|
|Réduire les colonnes singulières|Active/désactive la réduction des colonnes avec une valeur singulière|
||---------*Filtrage des données*---------|
|Filtrer les lignes dans la recherche|Active ou désactive l’option pour afficher uniquement les lignes correspondantes dans la recherche des résultats de la requête (**Ctrl + F**)|
||---------*Visualisations*---------|
|Visualisations|Consultez [visualisations](#visualizations-section), ci-dessus. |

> [!NOTE]
> <a id="dvs">Paramètres de la vue de données :</a> 
>
> Kusto. Explorer effectue le suivi des paramètres utilisés par ensemble de colonnes unique. Lorsque les colonnes sont réorganisées ou supprimées, la vue de données est enregistrée et est réutilisée chaque fois que les données contenant les mêmes colonnes sont récupérées. Pour rétablir les valeurs par défaut de la vue, sélectionnez **Réinitialiser la vue**dans l’onglet **affichage** . 

## <a name="tools-tab"></a>Onglet Outils

:::image type="content" source="images/kusto-explorer/tools-tab.png" alt-text="Onglet Outils de Kusto Explorer":::

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
|Requête vers Power BI| Convertit une requête en un format adapté à l’utilisation de dans Power BI|
||---------*Options*---------|
|Réinitialiser les options| Définit les valeurs par défaut des paramètres d’application|
|Options| Ouvre un outil permettant de configurer les paramètres de l’application. En savoir plus sur les [options de Kusto. Explorer](kusto-explorer-options.md).|

## <a name="monitoring-tab"></a>Onglet analyse

:::image type="content" source="images/kusto-explorer/monitoring-tab.png" alt-text="Onglet analyse de Kusto Explorer":::

|Menu             | Comportement|
|-----------------|---------| 
||---------*Socle*---------|
|Diagnostics de cluster | Affiche un résumé de l’intégrité du groupe de serveurs actuellement sélectionné dans le panneau connexions | 
|Données les plus récentes : toutes les tables| Affiche un résumé des données les plus récentes dans toutes les tables de la base de données actuellement sélectionnée|
|Données les plus récentes : table sélectionnée|Affiche dans la barre d’état les données les plus récentes dans la table sélectionnée| 

## <a name="management-tab"></a>Onglet gestion

:::image type="content" source="images/kusto-explorer/management-tab.png" alt-text="Onglet gestion de Kusto Explorer":::

|Menu             | Comportement|
|-----------------|---------|
||---------*Principaux autorisés*---------|
|Gérer les principaux autorisés du cluster |Active la gestion des principaux d’un cluster pour les utilisateurs autorisés| 
|Gérer les principaux autorisés de la base de données | Active la gestion des principaux d’une base de données pour les utilisateurs autorisés| 
|Gérer les principaux autorisés de table | Active la gestion des principaux d’une table pour les utilisateurs autorisés| 
|Gérer les principaux autorisés de la fonction | Permet de gérer les principaux d’une fonction pour les utilisateurs autorisés| 

## <a name="help-tab"></a>Onglet aide

:::image type="content" source="images/kusto-explorer/help-tab.png" alt-text="Onglet aide de Kusto Explorer":::

|Menu             | Comportement|
|-----------------|---------|
||---------*Correspondante*---------|
|Aide-moi             | Ouvre un lien vers la documentation en ligne de Kusto  | 
|Nouveautés       | Ouvre un document qui répertorie toutes les modifications apportées à Kusto. Explorer|
|Signaler un problème      |Ouvre une boîte de dialogue avec deux options : <ul><li>Signaler des problèmes liés au service</li><li>Signaler des problèmes dans l’application cliente</li></ul> | 
|Suggérer une fonctionnalité  | Ouvre un lien vers le Forum de commentaires Kusto | 
|Vérifier les mises à jour     | Vérifie s’il existe des mises à jour de votre version de Kusto. Explorer | 

## <a name="connections-panel"></a>Panneau connexions

:::image type="content" source="images/kusto-explorer/connections-panel.png" alt-text="Panneau connexions de l’Explorateur Kusto":::

Le volet connexions affiche toutes les connexions de cluster configurées. Pour chaque cluster, les bases de données, les tables et les attributs (colonnes) qu’ils stockent sont affichés. Sélectionnez Items (qui définit un contexte implicite pour la recherche/requête dans le volet principal) ou double-cliquez sur items pour copier le nom dans le panneau de recherche/requête.

Si le schéma réel est volumineux (par exemple, une base de données contenant des centaines de tables), vous pouvez effectuer une recherche en appuyant sur **Ctrl + F** et en entrant une sous-chaîne (ne respectant pas la casse) du nom d’entité que vous recherchez.

Kusto. Explorer prend en charge le contrôle du panneau de connexion à partir de la fenêtre de requête, qui est utile pour les scripts. Par exemple, vous pouvez démarrer un fichier de script avec une commande qui indique à Kusto. Explorer de se connecter au cluster/à la base de données dont les données sont interrogées par le script, à l’aide de la syntaxe suivante :

<!-- csl -->
```kusto
#connect cluster('help').database('Samples')

StormEvents | count
```

Exécutez chaque ligne à l’aide de `F5` , ou similaire.

### <a name="control-the-user-identity-connecting-to-kustoexplorer"></a>Contrôler l’identité de l’utilisateur qui se connecte à Kusto. Explorer

Le modèle de sécurité par défaut pour les nouvelles connexions est AAD-sécurité fédérée. L’authentification s’effectue via le Azure Active Directory à l’aide de l’expérience utilisateur AAD par défaut.

Si vous avez besoin d’un contrôle plus strict sur les paramètres d’authentification, vous pouvez développer la zone d’édition « avancé : chaînes de connexion » et fournir une valeur de [chaîne de connexion Kusto](../api/connection-strings/kusto.md) valide.

Par exemple, les utilisateurs avec une présence dans plusieurs locataires AAD doivent parfois utiliser une « projection » particulière de leurs identités sur un locataire AAD spécifique. Pour ce faire, fournissez une chaîne de connexion, telle que celle ci-dessous (remplacez les mots en MAJUSCULEs par des valeurs spécifiques) :

```kusto
Data Source=https://CLUSTER_NAME.kusto.windows.net;Initial Catalog=DATABASE_NAME;AAD Federated Security=True;Authority Id=AAD_TENANT_OF_CLUSTER;User=USER_DOMAIN
```

* `AAD_TENANT_OF_CLUSTER`nom de domaine ou ID de locataire AAD (GUID) du locataire AAD dans lequel le cluster est hébergé. Il s’agit généralement du nom de domaine de l’organisation qui possède le cluster, par exemple `contoso.com` . 
* USER_DOMAIN est l’identité de l’utilisateur invité dans ce locataire (par exemple, `user@example.com` ). 

>[!Note]
> Le nom de domaine de l’utilisateur n’est pas nécessairement le même que celui du locataire qui héberge le cluster.

:::image type="content" source="images/kusto-explorer/advanced-connection-string.png" alt-text="Chaîne de connexion avancée Kusto Explorer":::

## <a name="keyboard-shortcuts"></a>Raccourcis clavier

Vous constaterez peut-être que l’utilisation de raccourcis clavier vous permet d’effectuer des opérations plus rapidement qu’avec la souris. Jetez un coup d’œil à cette [liste de raccourcis clavier Kusto. Explorer](kusto-explorer-shortcuts.md) pour en savoir plus.

## <a name="table-row-colors"></a>Couleurs des lignes de tableau

Kusto. Explorer tente d’interpréter le niveau de gravité ou de détail de chaque ligne dans le volet de résultats et de les colorer en conséquence. Pour ce faire, il met en correspondance les valeurs distinctes de chaque colonne avec un ensemble de modèles connus (« Warning », « Error », etc.).

Pour modifier le modèle de couleur de sortie, ou désactiver ce comportement, dans le menu **Outils** , sélectionnez **options**  >  **visionneuse des résultats**  >  **Commentaires schéma de couleurs**.

:::image type="content" source="images/kusto-explorer/ke-color-scheme.png" alt-text="Modification du modèle de couleurs Kusto Explorer":::

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’utilisation de Kusto. Explorer :

* [Utilisation de Kusto.Explorer](kusto-explorer-using.md)
* [Raccourcis clavier de Kusto. Explorer](kusto-explorer-shortcuts.md)
* [Options de Kusto.Explorer](kusto-explorer-options.md)
* [Résolution des problèmes de Kusto.Explorer](kusto-explorer-troubleshooting.md)

En savoir plus sur les outils et utilitaires Kusto. Explorer :
* [Analyseur de code Kusto. Explorer](kusto-explorer-code-analyzer.md)
* [Navigation dans le code Kusto. Explorer](kusto-explorer-codenav.md)
* [Refactorisation du code Kusto. Explorer](kusto-explorer-refactor.md)
* [Langage de requête Kusto (KQL)](https://docs.microsoft.com/azure/kusto/query/)
