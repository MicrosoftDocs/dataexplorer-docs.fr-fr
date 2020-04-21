---
title: Raccourcis clavier Kusto.Explorer (clés chaudes) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les raccourcis clavier Kusto.Explorer (hot-keys) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/22/2020
ms.openlocfilehash: 81d99d14831905681c2dbbc45aea4b63134a06a8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523917"
---
# <a name="kustoexplorer-keyboard-shortcuts-hot-keys"></a>Raccourcis clavier Kusto.Explorer (clés chaudes)

## <a name="application-level"></a>Au niveau de l'application

Les raccourcis clavier suivants peuvent être utilisés à partir de n’importe quel contexte :

|Clé chaude|Description|
|-------|-----------|
|`F1`     | Ouvre l’aide|
|`F11`    | Mode de basculement plein-vue|
|`Ctrl`+`F1`| Aspect de ruban de bascule |
|`Ctrl`+`+`| Augmente la police des requêtes et des résultats de données|
|`Ctrl`+`-`| Diminue la police des requêtes et des résultats de données|
|`Ctrl`+`0`| Réinitialisation des requêtes et des résultats de données police|
|`Ctrl`+`1` .. `7`| Commutateurs au panneau de documents de requête avec le nombre respectif (1..7)|
|`Ctrl`+`F2`|Renomme l’en-tête du panel De l’éditeur De Requête|
|`Ctrl`+`N` |Ouvre un nouvel éditeur de requêtes|
|`Ctrl`+`O` |Script d’éditeur de requête ouverte dans un nouvel éditeur de requête|
|`Ctrl`+`W` |Ferme l’éditeur de requêtes actives|
|`Ctrl`+`S` |Enregistre la requête dans un fichier|
|`Shift`+`F3` | Ouvre la Galerie de rapport analytique|
|`Shift`+`F12`| Toggles Light/Dark thème de l’application|
|`Ctrl`+`Shift`+`O`|Ouvre le dialogue sur les options et les paramètres kusto.Explorer|
|`Esc`|Annuler la requête en cours d’exécution|
|`Shift`+`F5`|Annuler la requête en cours d’exécution|

## <a name="query-and-results-view"></a>Vue des requêtes et des résultats

Les raccourcis clavier suivants peuvent être utilisés lors de l’édition d’une requête dans l’éditeur de requête ou lorsque le contexte est dans la vue des résultats:

|Clé chaude|Description|
|-------|-----------|
|`Ctrl`+`Shift`+`C`|Copies de requêtes, de lien profond et de données au presse-papiers|
|`Alt`+`Shift`+`C` |Copies requête et lien profond le presse-papiers en format HTML|
|`Alt`+`Shift`+`R` |Copies requête, lien profond et données du presse-papiers au format Markdown|
|`Alt`+`Shift`+`M` |Copies requête et lien profond le presse-papiers dans le format Markdown|
|`Ctrl`+`~` |Copies de requêtes et de données au presse-papiers en format de réduction |
|`Ctrl`+`Shift`+`D`|Toggles mode de cacher des lignes en double dans la vue des données|
|`Ctrl`+`Shift`+`H`|Toggles mode de cacher des colonnes vides dans la vue de données|
|`Ctrl`+`Shift`+`J`|Toggles mode de colonnes s’effondre avec une valeur unique dans la vue de données|
|`Ctrl`+`Shift`+`A`|Ouvre un outil d’analyseur de requête dans un nouveau panneau de requête|
|`Alt`+`C`  |Rend l’organigramme de colonnes sur les données existantes|
|`Alt`+`T`  |Rend l’organigramme des données existantes|
|`Alt`+`A`  |Rend le tableau de la chronologie des anomalies sur les données existantes|
|`Alt`+`P`  |Rend le graphique à secteurs sur les données existantes|
|`Alt`+`L`  |Rend l’organigramme de l’échelle par-dessus les données existantes|
|`Alt`+`V`  |Rend le graphique Pivot sur les données existantes|
|`Ctrl`+`Shift`+`V`|Affiche le pivot de la chronologie par rapport aux données existantes|
|`Ctrl`+`F9`  | Toggles `show only matching rows` / `highlight matching rows` modes pour la`Ctrl`+`F`recherche de texte client ( ) le comportement dans la grille de données. |
|`Ctrl`+`F10` |Affiche le panneau de détails - où la rangée sélectionnée est présentée comme grille de propriété|
|`Ctrl`+`F`  | Affiche la boîte de recherche pour le panneau qui est actuellement au point. Pris `Connetions`en `Data Results`charge `Query Editor` dans , , et les panneaux|
|`Ctrl`+`Tab`| Affiche le dialogue du sélectionneur de documents de l’éditeur de requête. Vous pouvez `Ctrl` tenir et se tenir entre les documents avec`Tab` |
|`Ctrl`+`J`|Toggles apparence du panneau de résultat|
|`Ctrl`+`E`|Toggles apparence de l’éditeur de requête et tableau de résultat dans le cycle de:`Query Editor and Results` -> `Query Editor` -> `Query Editor and Results` -> `Results` |
|`Ctrl`+`Shift`+`E`|Toggles apparence de l’éditeur de requête et tableau de résultat dans le cycle de:`Query Editor and Results` -> `Results` -> `Query Editor and Results` -> `Query Editor` |
|`Ctrl`+`Shift`+`R` | Se concentre sur le panel de résultats |
|`Ctrl`+`Shift`+`T` | Focus sur le panel Connections |
|`Ctrl`+`Shift`+`Y` | Se concentre sur l’éditeur de Requête |
|`Ctrl`+`Shift`+`P` | Se concentre sur le panneau graphique |
|`Ctrl`+`Shift`+`I` | Se concentre sur le panel d’information sur les requêtes |
|`Ctrl`+`Shift`+`S` | Se concentre sur le panel de statistiques de requête |
|`Ctrl`+`Shift`+`K` | Se concentre sur le panneau d’erreur |
|`Alt`+`Ctrl`+`L`|Verrouille le contexte de connexion actuel à l’éditeur de requête, de sorte que le changement de ligne sélectionnée dans le panel Connetion n’a aucun effet sur le contexte de l’éditeur de requête. |

## <a name="results-table-viewer"></a>Résultats Table Viewer

Les raccourcis clavier suivants peuvent être utilisés lorsque la vue des résultats (table) est mise au point active du clavier :

|Clé chaude|Description|
|-----------|-----------|
|`Ctrl`+`Q` |Afficher le menu de contexte de colonne actuelle|
|`Ctrl`+`S` |Basculement du tri de colonne de courant|
|`Ctrl`+`U` |Ouvre un panneau montrant les valeurs actuelles de colonne avec le filtrage côté client|
|`Ctrl`+`F` | Affiche la boîte de recherche pour les résultats|
|`Ctrl`+`F3`| Toggles `show only matching rows` / `highlight matching rows` modes pour la`Ctrl`+`F`recherche de texte client ( ) le comportement dans la grille de données. |

## <a name="query-editor"></a>Éditeur de requête

Les raccourcis clavier suivants peuvent être utilisés lors de l’édition d’une requête dans l’éditeur de requête:

|Clé chaude|Description|
|-------|-----------|
|`F1`|Lorsque le curseur pointe vers un opérateur ou une fonction - ouvre une fenêtre d’aide avec des informations sur l’opérateur ou la fonction. Si le sujet d’aide n’est pas présent - ouvre une URL d’aide|
|`F5`|Exécuter des requêtes actuellement sélectionnées|
|`Shift`+`Enter`|Exécuter des requêtes actuellement sélectionnées|
|`F8`|Obtenez les résultats de la requête à partir du cache local. Si les résultats ne sont pas présents - exécuter actuellement des requêtes sélectionnées|
|`F6`|Exécuter des requêtes `Progressive Results` actuellement sélectionnées en mode|
|`Ctrl`+`F5` | Aperçu des résultats de la requête sélectionnée (montre peu de résultats et de nombre total)|
|`Ctrl`+`Shift`+`Space`| Insérer les sélections de cellules de données sous forme de filtres dans la requête|
|`Ctrl`+`Space`| Force IntelliSense règles vérifier. Les options possibles seront affichées dans n’importe quelle règle |
|`Ctrl`+`Enter`| Ajoute `pipe` le symbole et passe à une nouvelle ligne|
|`Ctrl`+`Z`| Annuler |
|`Ctrl`+`Y`| Refaire |
|`Ctrl`+`L`| Supprime la ligne actuelle|
|`Ctrl`+`D`| Supprime la ligne actuelle| 
|`Ctrl`+`F`| Ouvre `Find and Replace` le dialogue |
|`Ctrl`+`G`| Ouvre `Go-to line` le dialogue |
|`Ctrl`+`F8` | Afficher mes requêtes passées 3 jours |
|`Ctrl`- support | Lorsque le curseur est `(` aux `)` `[` symboles de parenthèse : , , , `]` , `{` , `}` - déplace le curseur à la support d’ouverture ou de fermeture assortie |
|`Ctrl`+`Shift`+`Q` | Prétifier la requête actuelle |
|`Ctrl`+`Shift`+`L` | Faire des requêtes ou des choix inférieurs |
|`Ctrl`+`Shift`+`U` |  Faire des requêtes ou des choix en cours |
|`Ctrl`+`Mouse wheel up`| Augmente la police de l’éditeur qupery| 
|`Ctrl`+`Mouse wheel down`| Diminution de la police de l’éditeur de requête|
|`Alt`+`P` | Ouvre le dialogue sur les paramètres de requête |
|`F2`| Ligne de courant ouverte / texte sélectionné dans le dialogue de l’éditeur |
|`Ctrl`+`F6`| Exécute l’analyse statique de requête de KQL pour détecter des problèmes communs |
|`F12`| Naviguez vers la définition du symbole |
|`Ctrl`+`F12`| Trouver tous les référents du symbole actuel |
|`Alt`+`Home`| Naviguez vers la définition du symbole |
|`Alt`+`Ctrl`+`M`| Extrait de l’expression littérale ou tabulaire actuellement sélectionnée comme déclaration de laisser |
|`Ctrl`+`.`| Extrait de l’expression littérale ou tabulaire actuellement sélectionnée comme déclaration de laisser |
|`Ctrl`+`R`, `Ctrl`+`R` | Renomme le symbole actuel |
|`Ctrl`+`K`, `Ctrl`+`D` | Insère l’horodatage actuel comme littéral de detatime |
|`Ctrl`+`K`, `Ctrl`+`R` | Inserts `range x from 1 to 1 step 1` extraits |
|`Ctrl`+`K`, `Ctrl`+`C` | Commentez la ligne actuelle ou les lignes sélectionnées |
|`Ctrl`+`K`, `Ctrl`+`F` | Prétifier la requête actuelle |
|`Ctrl`+`K`, `Ctrl`+`V` | Requête en cours en double (l’annexer à la fin du document de requête en cours) |
|`Ctrl`+`K`, `Ctrl`+`U` | Ligne actuelle non comcomment ou lignes sélectionnées |
|`Ctrl`+`K`, `Ctrl`+`S` | Transformez la ligne de courant ou certaines lignes en chaîne littérale multi-lignes |
|`Ctrl`+`K`, `Ctrl`+`M` | Supprimer les marques littérales à remues multi-lignes `Ctrl` + `K`(revers de , `Ctrl` + `S`) |
|`Ctrl`+`M`, `Ctrl`+`M` | Basculement décrivant l’expansion de la requête actuelle |
|`Ctrl`+`M`, `Ctrl`+`L` | Soulignant l’expansion de toutes les requêtes dans le document |

## <a name="json-viewer"></a>Visionneuse JSON

Les shorcuts de clavier suivants peuvent être utilisés à partir de l’intérieur des résultats JSON visualiseur (affiché quand un double-clics sur une valeur JSON-like dans la cellule de vue des résultats):

|Clé chaude|Description|
|-------|-----------|
|`Ctrl`+`Up Arrow`|Naviguer vers le parent|
|`Ctrl`+`Right Arrow`|Élargir le nœud courant (un niveau)|
|`Ctrl`+`Left Arrow`|Nœud courant d’effondrement (un niveau)|
|`Ctrl`+`.`|Élargissement de la nœud actuelle (tous les niveaux d’enfants élargis/effondrés)
|`Ctrl`+`Shift`+`.`|Élargissement de l’expansion du parent nœud actuel (tous les niveaux d’enfants élargis/effondrés)|

## <a name="connection-panel"></a>Panneau de connexion

Les shorcuts de clavier suivants peuvent être utilisés à partir de l’intérieur des résultats JSON visualiseur (affiché quand un double-clics sur une valeur JSON-like dans la cellule de vue des résultats):

|Clé chaude|Description|
|-------|-----------|
|`Ctrl`+`Up`Flèche|Naviguer vers le parent|
|`Ctrl`+`Right`Flèche|Élargir le nœud courant (un niveau)|
|`Ctrl`+`Left`Flèche|Nœud courant d’effondrement (un niveau)|
|`Ctrl`+`Shift`+`L`|Effondrement de tous les niveaux|
|`Ctrl`+`R`|Rafraîchir la connexion actuellement sélectionnée|
|`Insert`|Ajouter une nouvelle connexion|
|`Del`|Supprimer la connexion actuelle|
|`Ctrl`+`E`|Modifier la connexion actuellement sélectionnée|
|`Ctrl`+`T`|Ouvrez un nouvel éditeur de requêtes à l’aide d’une connexion actuellement sélectionnée|

## <a name="diagnostics-and-monitoring"></a>Diagnostics et surveillance

Les raccourcis clavier suivants `Monitoring` sont disponibles à partir du ruban.

|Clé chaude|Description|
|-----------|-----------|
|`Ctrl`+`Shift`+`F1`|Flux diagnostique de cluster d’exécution|