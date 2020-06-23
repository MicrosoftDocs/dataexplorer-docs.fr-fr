---
title: Raccourcis clavier Kusto. Explorer (touches d’accès rapide)-Azure Explorateur de données
description: Cet article décrit les raccourcis clavier Kusto. Explorer (touches d’accès rapide) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/22/2020
ms.openlocfilehash: a5bda2079adc06ea1bca5af65d89418ef5c293f6
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264672"
---
# <a name="kustoexplorer-keyboard-shortcuts-hot-keys"></a>Raccourcis clavier Kusto. Explorer (touches d’accès rapide)

## <a name="application-level"></a>Au niveau de l'application

Les raccourcis clavier suivants peuvent être utilisés à partir de n’importe quel contexte :

|Touche d’accès rapide|Description|
|-------|-----------|
|`F1`     | Ouvre l’aide|
|`F11`    | Activer/désactiver le mode d’affichage complet|
|`Ctrl`+`F1`| Activer/désactiver l’apparence du ruban |
|`Ctrl`+`+`| Augmente la police des résultats des requêtes et des données|
|`Ctrl`+`-`| Diminue la police des résultats de requête et de données|
|`Ctrl`+`0`| Réinitialise la police des résultats de requête et de données|
|`Ctrl`+`1` .. `7`| Bascule vers la requête du panneau de document avec le nombre respectif (1.. 7)|
|`Ctrl`+`F2`|Renomme l’en-tête du panneau de l’éditeur de requête|
|`Ctrl`+`N` |Ouvre un nouvel éditeur de requête|
|`Ctrl`+`O` |Ouvrir le script de l’éditeur de requête dans un nouvel éditeur de requête|
|`Ctrl`+`W` |Ferme l’éditeur de requête actif|
|`Ctrl`+`S` |Enregistre la requête dans un fichier|
|`Shift`+`F3` | Ouvre la Galerie de rapports analytiques|
|`Shift`+`F12`| Active/désactive le thème clair/sombre de l’application|
|`Ctrl`+`Shift`+`O`|Ouvre la boîte de dialogue Options et paramètres de Kusto. Explorer|
|`Esc`|Annuler l’exécution de la requête|
|`Shift`+`F5`|Annuler l’exécution de la requête|

## <a name="query-and-results-view"></a>Affichage des requêtes et des résultats

Vous pouvez utiliser les raccourcis clavier suivants avec l’éditeur de requête ou lorsque le contexte se trouve dans la vue résultats :

|Touche d’accès rapide|Description|
|-------|-----------|
|`Ctrl`+`Shift`+`C`|Copie la requête, les liens détaillés et les données dans le presse-papiers|
|`Alt`+`Shift`+`C` |Copie la requête et lie en profondeur le presse-papiers au format HTML|
|`Alt`+`Shift`+`R` |Copie les données de requête, de lien profond et place dans le presse-papiers au format de démarque|
|`Alt`+`Shift`+`M` |Copier la requête et lier en profondeur le presse-papiers au format de démarque|
|`Ctrl`+`~` |Copie la requête et les données dans le presse-papiers au format de démarque |
|`Ctrl`+`Shift`+`D`|Bascule le mode de masquage des lignes dupliquées dans la vue de données|
|`Ctrl`+`Shift`+`H`|Bascule le mode de masquage des colonnes vides dans la vue de données|
|`Ctrl`+`Shift`+`J`|Active/désactive le mode de réduction des colonnes avec une seule valeur dans la vue de données|
|`Ctrl`+`Shift`+`A`|Ouvre un outil Analyseur de requête dans un nouveau panneau de requête|
|`Alt`+`C`  |Restitue un histogramme sur des données existantes|
|`Alt`+`T`  |Restitue le graphique chronologique sur les données existantes|
|`Alt`+`A`  |Restitue le graphique de chronologie des anomalies sur les données existantes|
|`Alt`+`P`  |Restitue le graphique en secteurs sur les données existantes|
|`Alt`+`L`  |Effectue le rendu du graphique chronologique sur les données existantes|
|`Alt`+`V`  |Effectue le rendu d’un graphique croisé dynamique sur les données existantes|
|`Ctrl`+`Shift`+`V`|Affiche la chronologie pivot sur les données existantes|
|`Ctrl`+`F9`  | Bascule `show only matching rows` / `highlight matching rows` les modes pour le comportement de recherche de texte du client ( `Ctrl` + `F` ) dans la grille de données. |
|`Ctrl`+`F10` |Affiche le panneau détails-où la ligne sélectionnée est présentée comme grille des propriétés|
|`Ctrl`+`F`  | Affiche la zone de recherche pour le panneau qui est actuellement actif. Pris en charge dans `Connetions` `Data Results` les `Query Editor` panneaux, et|
|`Ctrl`+`Tab`| Affiche la boîte de dialogue Sélecteur de document de l’éditeur de requête. Vous pouvez conserver `Ctrl` et basculer entre les documents avec`Tab` |
|`Ctrl`+`J`|Active/désactive l’apparence du volet de résultats|
|`Ctrl`+`E`|Active ou désactive l’affichage de l’éditeur de requête et du panneau de résultats dans le cycle de :`Query Editor and Results` -> `Query Editor` -> `Query Editor and Results` -> `Results` |
|`Ctrl`+`Shift`+`E`|Active ou désactive l’affichage de l’éditeur de requête et du panneau de résultats dans le cycle de :`Query Editor and Results` -> `Results` -> `Query Editor and Results` -> `Query Editor` |
|`Ctrl`+`Shift`+`R` | Se concentre sur le panneau résultats |
|`Ctrl`+`Shift`+`T` | Se concentre sur le panneau connexions |
|`Ctrl`+`Shift`+`Y` | Se concentre sur l’éditeur de requête |
|`Ctrl`+`Shift`+`P` | Se concentre sur le panneau graphique |
|`Ctrl`+`Shift`+`I` | Se concentre sur le panneau informations sur la requête |
|`Ctrl`+`Shift`+`S` | Se concentre sur le panneau statistiques sur les requêtes |
|`Ctrl`+`Shift`+`K` | Se concentre sur le panneau d’erreur |
|`Alt`+`Ctrl`+`L`|Verrouille le contexte de connexion actuel dans l’éditeur de requête. par conséquent, la modification de la ligne sélectionnée dans le volet de connexion n’a aucun effet sur le contexte de l’éditeur de requête. |

## <a name="results-table-viewer"></a>Visionneuse de tables de résultats

Les raccourcis clavier suivants peuvent être utilisés lorsque la vue des résultats (table) est active sur le focus clavier :

|Touche d’accès rapide|Description|
|-----------|-----------|
|`Ctrl`+`Q` |Afficher le menu contextuel de la colonne actuelle|
|`Ctrl`+`S` |Activer/désactiver le tri de la colonne actuelle|
|`Ctrl`+`U` |Ouvre un panneau qui affiche les valeurs de la colonne actuelle avec filtrage côté client|
|`Ctrl`+`F` | Affiche la zone de recherche pour les résultats|
|`Ctrl`+`F3`| Bascule `show only matching rows` / `highlight matching rows` les modes pour le comportement de recherche de texte du client ( `Ctrl` + `F` ) dans la grille de données. |

## <a name="query-editor"></a>Éditeur de requête

Les raccourcis clavier suivants peuvent être utilisés lors de la modification d’une requête dans l’éditeur de requête :

|Touche d’accès rapide|Description|
|-------|-----------|
|`F1`|Quand le curseur pointe sur un opérateur ou une fonction, ouvre une fenêtre d’aide avec des informations sur l’opérateur ou la fonction. Si la rubrique d’aide n’est pas présente, ouvre une URL d’aide|
|`F5`|Exécuter la requête actuellement sélectionnée|
|`Shift`+`Enter`|Exécuter la requête actuellement sélectionnée|
|`F8`|Récupérez les résultats de requête à partir du cache local. Si les résultats ne sont pas présents, exécuter la requête actuellement sélectionnée|
|`F6`|Exécuter la requête actuellement sélectionnée en `Progressive Results` mode|
|`Ctrl`+`F5` | Afficher un aperçu des résultats de la requête sélectionnée (affiche peu de résultats et nombre total)|
|`Ctrl`+`Shift`+`Space`| Insérer des sélections de cellules de données comme filtres dans la requête|
|`Ctrl`+`Space`| Force la vérification des règles IntelliSense. Les options possibles seront affichées dans toutes les règles correspondantes |
|`Ctrl`+`Enter`| Ajoute `pipe` un symbole et se déplace sur une nouvelle ligne|
|`Ctrl`+`Z`| Annuler |
|`Ctrl`+`Y`| Rétablir |
|`Ctrl`+`L`| Supprime la ligne active|
|`Ctrl`+`D`| Supprime la ligne active| 
|`Ctrl`+`F`| Ouvre la `Find and Replace` boîte de dialogue |
|`Ctrl`+`G`| Ouvre la `Go-to line` boîte de dialogue |
|`Ctrl`+`F8` | Afficher mes requêtes au cours des 3 derniers jours |
|`Ctrl`+ crochet | Quand le curseur est à des signes de crochet :,,,,, `(` `)` `[` `]` `{` `}` -déplace le curseur vers le crochet ouvrant ou fermant correspondant |
|`Ctrl`+`Shift`+`Q` | Agrémenter la requête en cours |
|`Ctrl`+`Shift`+`L` | Mettre en minuscules la requête ou la sélection actuelle |
|`Ctrl`+`Shift`+`U` |  Mettre en majuscules la requête ou la sélection actuelle |
|`Ctrl`+`Mouse wheel up`| Augmente la police de l’éditeur de requête|
|`Ctrl`+`Mouse wheel down`| Diminue la police de l’éditeur de requête|
|`Alt`+`P` | Ouvre la boîte de dialogue Paramètres de la requête |
|`F2`| Ouvrir la ligne active/le texte sélectionné dans la boîte de dialogue de l’éditeur |
|`Ctrl`+`F6`| Exécute l’analyse des requêtes statiques KQL pour détecter les problèmes courants |
|`F12`| Accéder à la définition du symbole |
|`Ctrl`+`F12`| Rechercher toutes les références du symbole actuel |
|`Alt`+`Home`| Accéder à la définition du symbole |
|`Alt`+`Ctrl`+`M`| Extraire l’expression littérale ou tabulaire actuellement sélectionnée en tant qu’instruction Let |
|`Ctrl`+`.`| Extraire l’expression littérale ou tabulaire actuellement sélectionnée en tant qu’instruction Let |
|`Ctrl`+`R`, `Ctrl`+`R` | Renomme le symbole actuel |
|`Ctrl`+`K`, `Ctrl`+`D` | Insère l’horodateur actuel comme littéral DateTime |
|`Ctrl`+`K`, `Ctrl`+`R` | Insère un `range x from 1 to 1 step 1` extrait |
|`Ctrl`+`K`, `Ctrl`+`C` | Commenter la ligne active ou les lignes sélectionnées |
|`Ctrl`+`K`, `Ctrl`+`F` | Agrémenter la requête en cours |
|`Ctrl`+`K`, `Ctrl`+`V` | Dupliquer la requête en cours (l’ajouter à la fin du document de la requête active) |
|`Ctrl`+`K`, `Ctrl`+`U` | Supprimer les marques de commentaire de la ligne active ou des lignes sélectionnées |
|`Ctrl`+`K`, `Ctrl`+`S` | Transformer la ligne active ou les lignes sélectionnées en littéral de chaîne multiligne |
|`Ctrl`+`K`, `Ctrl`+`M` | Supprimer les marques de littéraux de chaîne sur plusieurs lignes (inverse de `Ctrl` + `K` , `Ctrl` + `S` ) |
|`Ctrl`+`M`, `Ctrl`+`M` | Activer/désactiver le développement du mode plan de la requête actuelle |
|`Ctrl`+`M`, `Ctrl`+`L` | Activer/désactiver le développement du mode plan de toutes les requêtes dans le document |

## <a name="json-viewer"></a>Visionneuse JSON

Les raccourcis clavier suivants peuvent être utilisés dans la visionneuse JSON résultats.
Elles s’affichent si vous double-cliquez sur une valeur de type JSON dans la cellule de la vue résultats :

|Touche d’accès rapide|Description|
|-------|-----------|
|`Ctrl`+`Up Arrow`|Accéder au parent|
|`Ctrl`+`Right Arrow`|Développer le nœud actuel (un niveau)|
|`Ctrl`+`Left Arrow`|Réduire le nœud actuel (un niveau)|
|`Ctrl`+`.`|Activer/désactiver le développement du nœud actuel (tous les niveaux enfants développés/réduits)
|`Ctrl`+`Shift`+`.`|Activer/désactiver le développement du nœud parent actuel (tous les niveaux enfants développés/réduits)|

## <a name="connection-panel"></a>Panneau de connexion

Les raccourcis clavier suivants peuvent être utilisés dans la visionneuse JSON résultats.
Elles s’affichent si vous double-cliquez sur une valeur de type JSON dans la cellule de la vue résultats :

|Touche d’accès rapide|Description|
|-------|-----------|
|`Ctrl`+`Up`Flèche|Accéder au parent|
|`Ctrl`+`Right`Flèche|Développer le nœud actuel (un niveau)|
|`Ctrl`+`Left`Flèche|Réduire le nœud actuel (un niveau)|
|`Ctrl`+`Shift`+`L`|Réduire tous les niveaux|
|`Ctrl`+`R`|Actualiser la connexion actuellement sélectionnée|
|`Insert`|Ajouter une nouvelle connexion|
|`Del`|Supprimer la connexion actuelle|
|`Ctrl`+`E`|Modifier la connexion actuellement sélectionnée|
|`Ctrl`+`T`|Ouvrir un nouvel éditeur de requête à l’aide de la connexion actuellement sélectionnée|

## <a name="diagnostics-and-monitoring"></a>Diagnostics et surveillance

Les raccourcis clavier suivants sont disponibles dans le `Monitoring` ruban.

|Touche d’accès rapide|Description|
|-----------|-----------|
|`Ctrl`+`Shift`+`F1`|Exécuter le workflow de diagnostic du cluster|
