---
title: Options de l’Explorateur Kusto-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les options de Kusto Explorer dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 5b1bb73778858998f835f1e8718afbfbd6b69443
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108386"
---
# <a name="kusto-explorer-options"></a>Options Kusto Explorer

Les tableaux suivants décrivent les options de personnalisation du comportement de Kusto. Explorer à partir de la boîte de dialogue**options** des **Outils** > .

## <a name="general-settings"></a>Paramètres généraux

| Option | Description |
|---------|--------------|
| Mode outil | Active les fonctionnalités de l’application bêta, alpha et expérimentales. La valeur par défaut est none.|
| Accessibilité étendue | Lorsque cette option est activée, l’application envoie des événements d’accessibilité utilisés par les outils d’accessibilité. Peut affecter les performances si elles sont activées. Par défaut, elle est désactivée. |
| Autoriser l’envoi de données de télémétrie | Lorsqu’elle est activée, l’application envoie des données de télémétrie lorsque des erreurs se produisent ou lorsque l’application se bloque. |
| Message de bienvenue | Lorsque cette option est activée, l’application affiche le message de bienvenue au démarrage. Par défaut, l’utilisation est activée.|
| Afficher le thème | Modèle de couleurs de l’interface utilisateur de l’application : clair ou sombre.|
  
## <a name="query-editor"></a>Éditeur de requête

| Option | Description |
|---------|--------------|
| Enregistrement automatique des requêtes | Lorsque cette option est activée, l’application enregistre automatiquement les documents ouverts. La modification de ce paramètre requiert le redémarrage de l’application pour prendre effet. Option activée par défaut.|
| Suivre les modifications | Lorsque cette option est activée, l’application suit les modifications apportées aux scripts de requête sur le disque par d’autres applications. Si le script de requête est modifié en externe, vous êtes averti et vous êtes invité à le recharger. La modification de ce paramètre requiert le redémarrage de l’application. Option activée par défaut.|
| Utiliser les sessions de modification | Lorsqu’il est activé, chaque processus d’application possède son propre ensemble de documents. Désactivé par défaut.|
| Taille de police | Taille de police utilisée dans l’éditeur de requête.|
| Sélectionner l’arrière-plan de la commande | Couleur d’arrière-plan utilisée pour mettre en surbrillance la commande actuellement sélectionnée.|
| Remplacer les tabulations par des espaces | Lorsque cette option est activée, les onglets sont automatiquement remplacés par des espaces.|
| Désactiver l’injection de paramètre de fonction | Quand cette option est activée, l’injection de paramètres de fonction à partir du presse-papiers est désactivée.|
| Désactiver l’injection de paramètre de requête | Quand cette option est activée, l’injection des paramètres de requête est désactivée.|
| Désactiver les déclencheurs d’exécution de requête sauf F5 | Lorsque cette option est activée, seule la touche F5 déclenche l’exécution des requêtes.|
| Afficher l’aide à l’intérieur de l’application | Quand cette option est activée, les rubriques d’aide sont affichées à l’intérieur de l’application. Lorsque des rubriques d’aide désactivées sont ouvertes dans un navigateur.|
| Limite de longueur du paramètre de requête | Longueur maximale d’une chaîne qui peut être utilisée comme paramètre de requête. La définition de cette valeur sur plus de 128K peut entraîner des problèmes de performances. La valeur par défaut est 64 Ko.|

## <a name="intellisense"></a>IntelliSense

| Option | Description |
|---------|--------------|
| Version IntelliSense | Version du moteur IntelliSense utilisée. *V1* est le moteur classique. *V2* est le moteur moderne. La valeur par défaut est *v2*. |
| IntelliSense : commandes de contrôle | Version d’IntelliSense pour les commandes de contrôle. *V1* est le moteur classique. *V2* est le moteur moderne. La valeur par défaut est *v2*. | 
| IntelliSense | Active ou désactive IntelliSense. Par défaut, l’utilisation est activée.|
| Mise en surbrillance syntaxique | Active ou désactive la mise en surbrillance de la syntaxe. Par défaut, l’utilisation est activée.|
| Outils-conseils | Active ou désactive les info-bulles qui s’affichent lorsque la souris pointe sur les zones de la requête sélectionnée. Par défaut, l’utilisation est activée.|

## <a name="formatter"></a>Formateur

| Option | Description |
|---------|--------------|
| Version | Version du module de formatage utilisée lorsque l’outil de requête agrémenter est appliqué à la requête actuelle. *V1* est le formateur classique. *V2* est le formateur moderne. La valeur par défaut est *v2*.|
| Indentation | Nombre d’espaces pour les éléments mis en retrait.|
| Accolades de fonction | Emplacement des accolades de la fonction. *Vertical* place les accolades ouvrantes et fermantes sur de nouvelles lignes. *Horizontal* laisse l’accolade ouvrante sur sa ligne d’origine et place l’accolade fermante sur une nouvelle ligne. *Aucun* conserve toutes les accolades comme c’est le cas.|
| Opérateur de canal | Emplacement du caractère de barre verticale qui existe entre les opérateurs de requête tabulaire. *Nouvelle ligne* place tous les caractères de barre verticale sur une nouvelle ligne. *Smart* place tous les caractères de barre verticale sur une nouvelle ligne si la requête s’étend sur plusieurs lignes. *Aucun* conserve tous les caractères de la barre verticale.|
| Point-virgule | Emplacement des points-virgules qui terminent les instructions de requête. *Nouvelle ligne* place les points-virgules sur une nouvelle ligne, mis en retrait. *Smart* place les points-virgules sur une nouvelle ligne si l’instruction elle-même s’étend sur plusieurs lignes.  *None* laisse les points-virgules tels quels.
| Insérer une syntaxe manquante | Ajoute des signes de ponctuation manquants, tels que des virgules et des points-virgules pour lesquels vous obtenez des messages d’erreur.|

## <a name="results-viewer"></a>Visionneuse des résultats

| Option | Description |
|---------|--------------|
| Taille de police | Taille de police utilisée par la grille de la table de données, où les résultats de la requête sont affichés.|
| Mode de couleur de commentaires | Sélectionne le modèle de couleurs pour la base de mise en forme de ligne sur le niveau de détail détecté automatiquement.|
| Masquer les colonnes vides | Quand cette option est activée, les colonnes vides dans la vue qui affiche les données sont masquées.  Par défaut, elle est désactivée.|
| Réduire les colonnes à valeur unique| Quand cette option est activée, elle permet de réduire automatiquement les colonnes à valeur unique dans la vue qui affiche les données. Les colonnes avec des valeurs uniques non vides sont affichées sous forme de groupes. Par défaut, elle est désactivée.|
| Trier automatiquement les résultats par colonnes DateTime | Lorsque cette option est activée, les lignes sont triées automatiquement par colonnes DateTime. Par défaut, l’utilisation est activée.|
| Plage visible par colonne | Quantité maximale de caractères à afficher dans une colonne. La valeur par défaut est 2048.|
| Visualiser JSON en tant que texte | Lorsque cette option est activée, les valeurs JSON sont visualisées sous forme de texte. Par défaut, elle est désactivée.|
| Taille maximale des détails du texte | Nombre maximal de caractères qui s’affichent dans le volet d’informations détaillé lorsque l’utilisateur double-clique sur la cellule. La valeur par défaut est 32KO.|

## <a name="connections"></a>Connexions

| Option | Description |
|---------|--------------|
| Trier les colonnes de table par ordre alphabétique | Lorsque cette option est activée, les colonnes qui apparaissent sous les nœuds de la table dans le panneau connexions sont triées par ordre alphabétique.|
| Délai d’expiration du serveur de requêtes | Délai d’attente du serveur pour l’exécution de la requête.|
| Avertir lors du verrouillage de la connexion | Lorsque cette option est activée, l’application vous avertit du verrouillage de la connexion chaque fois qu’un changement de connexion est tenté. Par défaut, l’utilisation est activée.|
| Exploration de schéma différée | Lorsqu’il est activé, le panneau connexions récupère et affiche uniquement le schéma de base de données lorsque le nœud de base de données est développé.|
| Afficher les objets système masqués | Lorsque cette option est activée, les objets système masqués s’affichent si l’utilisateur dispose des autorisations appropriées.|
| Cohérence faible des requêtes | Lorsqu’elle est activée, la cohérence faible sera utilisée pour les requêtes.|
| Version de l’analyseur Kusto | Version de l’analyseur utilisée pour exécuter des requêtes. *V1* est l’analyseur classique. *V2* est l’analyseur moderne. La valeur par défaut est *v1*.|

## <a name="diagnostic-tracing"></a>Suivi de diagnostic

| Option | Description |
|---------|--------------|
| Activer le suivi | Active le suivi.|
| Commentaires de trace | Définit le niveau de détail du suivi.|
| Emplacement des traces | Emplacement dans lequel les traces sont enregistrées.|
| PlatformTraceVerbosity | Définit le niveau de détail du suivi pour la plateforme.| 