---
title: Options Kusto Explorer - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les options Kusto Explorer dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: dee96dd937b4258525673ced10f206836f6e51c2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524002"
---
# <a name="kusto-explorer-options"></a>Options Kusto Explorer

Les tableaux suivants décrivent les options pour personnaliser le comportement de Kusto.Explorer à partir de la boîte de dialogue **Tools** > **Options.**

![> 'Outils' > 'Options'](\images\Kusto-Tools-Explorer-Options\tools-options.png)

## <a name="general-settings"></a>Paramètres généraux

| Option | Description |
|---------|--------------|
| Mode outil | Permet des fonctionnalités d’application bêta, alpha et expérimentale. La valeur par défaut est none.|
| Accessibilité étendue | Lorsqu’elle est activée, l’application envoie des événements d’accessibilité utilisés par les outils d’accessibilité. Peut affecter les performances si activé. Le défaut est désactivé. |
| Autoriser l’envoi de données de télémétrie | Lorsqu’elle est activée, l’application envoie des données de télémétrie lorsque des erreurs se produisent ou que l’application se bloque. |
| Message de bienvenue | Lorsque activé, l’application affiche le message de bienvenue sur le début. Le défaut est activé.|
| Thème d’affichage | Le schéma de couleur de l’interface utilisateur d’application : clair ou foncé.|
  
## <a name="query-editor"></a>Éditeur de requête

| Option | Description |
|---------|--------------|
| Requêtes auto-sauvegarde | Lorsqu’elle est activée, l’application enregistre automatiquement les documents ouverts. Pour modifier ce paramètre, il faut redémarrer l’application pour prendre effet. Option activée par défaut.|
| Suivre les modifications | Lorsqu’elle est activée, l’application suit les modifications apportées aux scripts de requête sur disque par d’autres applications. Si le script de requête est modifié à l’extérieur, vous serez avisé et invité à le recharger. Pour modifier ce paramètre, il faut redémarrer l’application. Option activée par défaut.|
| Utiliser Edit Sessions | Lorsqu’il est activé, chaque processus de demande possède son propre ensemble de documents. Désactivé par défaut.|
| Taille de police | La taille de police utilisée dans l’éditeur de requête.|
| Sélectionner l’arrière-plan de commande | La couleur de fond utilisée pour mettre en évidence la commande actuellement sélectionnée.|
| Remplacer les onglets par des espaces | Lorsqu’ils sont activés, les onglets sont automatiquement remplacés par des espaces.|
| Injection de paramètre de fonction désactivable | Lorsqu’elle est activée, l’injection de paramètres de fonction à partir du presse-papiers est désactivée.|
| Injection de paramètre de requête désactivable | Lorsqu’elle est activée, l’injection de paramètres de requête est désactivée.|
| Déclencheurs de requêtes invalidants à l’exception de F5 | Lorsqu’elle est activée, seule la clé F5 déclenchera des requêtes.|
| Montrez de l’aide à l’intérieur de l’application | Lorsqu’ils sont activés, les sujets d’aide sont affichés à l’intérieur de l’application. Lorsque les sujets d’aide désactivés sont ouverts à l’intérieur d’un navigateur.|
| Limite de longueur des paramètres de requête | La longueur maximale d’une chaîne qui peut être utilisée comme paramètre de requête. La définition de cette valeur à plus de 128 000 personnes peut causer des problèmes de performance. Par défaut, c’est 64K.|

## <a name="intellisense"></a>IntelliSense

| Option | Description |
|---------|--------------|
| IntelliSense Version | La version du moteur IntelliSense utilisée. *V1* est le moteur classique. *V2* est le moteur moderne. Par défaut est *V2*. |
| IntelliSense: Commandes de contrôle | La version d’IntelliSense pour les commandes de contrôle. *V1* est le moteur classique. *V2* est le moteur moderne. Par défaut est *V2*. | 
| IntelliSense | Permet ou désactive IntelliSense. Le défaut est activé.|
| Mise en évidence de la syntaxe | Permet ou désactive la mise en évidence de la syntaxe. Le défaut est activé.|
| Conseils d’outils | Permet ou désactive les conseils d’outils qui apparaissent lorsque la souris plane sur les zones de la requête sélectionnée. Le défaut est activé.|

## <a name="formatter"></a>Formateur

| Option | Description |
|---------|--------------|
| Version | La version du formateur utilisée lorsque l’outil Prettify Query est appliqué à la requête actuelle. *V1* est le formateur classique. *V2* est le formateur moderne. Par défaut est *V2*.|
| Indentation | Nombre d’espaces pour les objets en retrait.|
| Fonction Braces | Le placement des accolades de fonction. Les places *verticales* s’ouvrent et ferment les accolades bouclées sur de nouvelles lignes. *Horizontal* laisse l’accolade ouverte sur sa ligne d’origine et place l’accolade rapprochée sur une nouvelle ligne. *Aucun ne* laisse toutes les accolades telles qu’elles sont.|
| Opérateur de tuyauterie | Le placement du caractère de tuyau (bar) qui existe entre les opérateurs de requête tabulaire. *New Line* place tous les personnages de tuyaux sur une nouvelle ligne. *Smart* place tous les personnages de tuyaux sur une nouvelle ligne si la requête s’étend déjà sur plus d’une ligne. *Aucun ne* laisse tous les personnages de pipe tel qu’il est.|
| Point-virgule | Le placement de semi-colons qui terminent les énoncés de requête. *New Line* place les semi-colons sur une nouvelle ligne, en retrait. *Smart* place des semi-colons sur une nouvelle ligne si la déclaration elle-même s’étend sur plus d’une ligne.  *Aucun ne* quitte les semi-colons tel qu’il est.
| Insérer la Syntaxe manquante | Ajoute la ponctuation manquante comme (comme les virgules et les semi-colons) que vous obtenez des messages d’erreur pour.|

## <a name="results-viewer"></a>Résultats Viewer

| Option | Description |
|---------|--------------|
| Taille de police | La taille de police utilisée par la grille de la table de données, où les résultats des requêtes sont affichés.|
| Schéma de couleur de verbosité | Sélectionne le schéma de couleurs pour la base de formatage de ligne sur le niveau de verbosité auto-détecté.|
| Masquer les colonnes vides | Lorsqu’elles sont activées, les colonnes vides dans la vue montrant les données seront cachées.  Le défaut est désactivé.|
| Effondrement des colonnes à valeur unique| Lorsqu’il est activé, les colonnes d’auto-effondrement d’une seule valeur dans la vue montrant les données. Les colonnes avec des valeurs non vides simples seront affichées en groupes. Le défaut est désactivé.|
| Résultats de tri automatique par colonnes d’heure de date | Lorsqu’elles sont activées, les lignes seront auto-triées par des colonnes d’heure de date. Le défaut est activé.|
| Gamme visible de colonne | La quantité maximale de caractères à afficher dans une colonne. Par défaut, c’est 2048.|
| Visualisez JSON comme texte | Lorsqu’elles sont activées, les valeurs JSON sont visualisées sous forme de texte. Le défaut est désactivé.|
| Taille maximale des détails de texte | Le nombre maximum de caractères qui seront affichés dans le panneau d’information détaillé lorsque la cellule est à double clic. Par défaut, c’est 32KB.|

## <a name="connections"></a>Connexions

| Option | Description |
|---------|--------------|
| Trier les colonnes de table par ordre alphabétique | Lorsqu’elles sont activées, les colonnes apparaissant sous les nœuds de table dans le panneau de connexions seront triées par ordre alphabétique.|
| Temps d’arrêt du serveur de requête | Le délai d’attente du serveur pour l’exécution des requêtes.|
| Avertir sur le verrou de connexion | Lorsqu’elle est activée, l’application avertit de verrouillage de connexion chaque fois qu’un commutateur de connexion est tenté. Le défaut est activé.|
| Exploration de schéma paresseux | Lorsqu’il est activé, le panneau de connexions ne cherchera et affiche le schéma de base de données que lorsque le nœud de base de données est élargi.|
| Afficher les objets du système caché | Lorsqu’elles sont activées, des objets système cachés seront affichés si l’utilisateur dispose d’autorisations appropriées.|
| Requête faible cohérence | Lorsqu’elle est activée, une faible consistance sera utilisée pour les requêtes.|
| Version kusto parser | La version du analyseur utilisée pour exécuter les requêtes. *V1* est le parseur classique. *V2* est le parseur moderne. Par défaut est *V1*.|

## <a name="diagnostic-tracing"></a>Traçage diagnostique

| Option | Description |
|---------|--------------|
| Activer le suivi | Permet le traçage.|
| Verbosity trace | Définit la verbosité du traçage.|
| Emplacement des traces | L’endroit où les traces sont enregistrées.|
| PlateformeTraceVerbosity | Définit la verbosité du traçage de la plate-forme.| 