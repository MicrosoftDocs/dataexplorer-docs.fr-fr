---
title: Kusto.WebExplorer - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Kusto.WebExplorer dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 1f6926df09a207cfea2b9201ef57f36932a63f74
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523866"
---
# <a name="kustowebexplorer"></a>Kusto.WebExplorer Kusto.WebExplorer

Kusto.WebExplorer est une application web qui peut être utilisée pour envoyer des requêtes et des commandes de contrôle à un service Kusto. L’application est hébergée à [https://dataexplorer.azure.com/]https://aka.ms/kweet court-lié par [ ].



Kusto.WebExplorer peut également être hébergé par d’autres portails Web dans un IFRAME HTML.
(Par exemple, cela est fait par le [portail Azure](https://portal.azure.com).) Voir [Monaco IDE](../api/monaco/monaco-kusto.md) pour plus de détails sur la façon de l’accueillir et l’éditeur monégasque qu’il utilise.

## <a name="connect-to-multiple-clusters"></a>Connectez-vous à plusieurs clusters

Vous pouvez désormais connecter plusieurs clusters et basculer entre les bases de données et les clusters.
L’outil est conçu pour identifier facilement le cluster et la base de données auxquels vous êtes connecté.

![texte de remplacement](./Images/KustoTools-WebExplorer/AddingCluster.gif "AjoutCluster")

## <a name="recall-results"></a>Résultats de rappel

Souvent, au cours de l’analyse, nous 2000 requêtes et pouvons avoir à revoir les résultats des requêtes précédentes. Vous pouvez utiliser cette fonctionnalité pour rappeler vos résultats sans avoir à réexécuter la requête. Les données sont servies à partir du cache local du côté client.

![texte de remplacement](./Images/KustoTools-WebExplorer/RecallResults.gif "RappelResults")

## <a name="enhanced-results-grid-control"></a>Amélioration du contrôle de la grille de résultats

La grille de table vous permet de sélectionner plusieurs lignes, colonnes et cellules. Calculez les agrégats en sélectionnant plusieurs cellules (comme Excel) et pivotez les données.

![texte de remplacement](./Images/KustoTools-WebExplorer/EnhancedGrid.gif "EnhancedGrid")

## <a name="intellisense--formatting"></a>Intellisense & Formatting

Vous pouvez utiliser le format joliment imprimé en utilisant la clé de raccourci "Shift 'Alt 'F", le pliage du code (décrivant) et IntelliSense.

![texte de remplacement](./Images/KustoTools-WebExplorer/Formating.gif "Formatage")

## <a name="deep-linking"></a>Lien profond

Vous pouvez copier juste le lien profond ou le lien profond et la requête. Vous pouvez également formater l’URL pour inclure le cluster, la base de données et la requête en utilisant le modèle suivant :

`https://dataexplorer.azure.com/`[`clusters/` *Cluster* Cluster`/databases/` [ *Base de données* `?` *[Options]]*

Les options suivantes peuvent être spécifiées :

* `workspace=empty`: Indique la création d’un nouvel espace de travail vide (aucun rappel des clusters, onglets et requêtes précédents ne sera effectué).



![texte de remplacement](./Images/KustoTools-WebExplorer/DeepLink.gif "DeepLink")

## <a name="feedback"></a>Commentaires

Vous pouvez soumettre vos commentaires via l’outil.
![texte de remplacement](./Images/KustoTools-WebExplorer/Feedback.gif "Commentaires")