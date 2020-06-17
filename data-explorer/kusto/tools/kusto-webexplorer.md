---
title: Kusto. webexplorer-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Kusto. webexplorer dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: d53f12c4a0c4dd2bce669dbe004b8f325db27af5
ms.sourcegitcommit: 4986354cc1ba25c584e4f3c7eac7b5ff499f0cf1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2020
ms.locfileid: "84856391"
---
# <a name="kustowebexplorer"></a>Kusto. webexplorer

Kusto. webexplorer est une application Web qui peut être utilisée pour envoyer des requêtes et contrôler des commandes à un service Kusto. L’application est hébergée https://dataexplorer.azure.com/ par https://aka.ms/kwe .



Kusto. webexplorer peut également être hébergé par d’autres portails Web dans un IFRAME HTML.
(Par exemple, cette opération est effectuée par l' [portail Azure](https://portal.azure.com).) Pour plus d’informations sur l’hébergement et l’éditeur Monaco utilisé, consultez [IDE de Monaco](../api/monaco/monaco-kusto.md) .

## <a name="connect-to-multiple-clusters"></a>Se connecter à plusieurs clusters

Vous pouvez maintenant connecter plusieurs clusters et basculer entre les bases de données et les clusters.
L’outil est conçu pour identifier facilement le cluster et la base de données auxquels vous êtes connecté.

![texte de remplacement](./Images/KustoTools-WebExplorer/AddingCluster.gif "AddingCluster")

## <a name="recall-results"></a>Résultats du rappel

Souvent, lors de l’analyse, nous exécutons plusieurs requêtes et vous devrez peut-être revisiter les résultats des requêtes précédentes. Vous pouvez utiliser cette fonctionnalité pour rappeler vos résultats sans avoir à réexécuter la requête. Les données sont traitées à partir du cache côté client local.

![texte de remplacement](./Images/KustoTools-WebExplorer/RecallResults.gif "RecallResults")

## <a name="enhanced-results-grid-control"></a>Contrôle de grille des résultats améliorés

La grille de table vous permet de sélectionner plusieurs lignes, colonnes et cellules. Calculez des agrégats en sélectionnant plusieurs cellules (comme Excel) et faites pivoter les données.

![texte de remplacement](./Images/KustoTools-WebExplorer/EnhancedGrid.gif "EnhancedGrid")

## <a name="intellisense--formatting"></a>Mise en forme des & IntelliSense

Vous pouvez utiliser le format d’impression simple en utilisant la touche de raccourci « Maj + Alt + F », le repli de code (mode plan) et IntelliSense.

![texte de remplacement](./Images/KustoTools-WebExplorer/Formating.gif "Mise en forme")

## <a name="deep-linking"></a>Liaison profonde

Vous pouvez copier uniquement le lien profond ou le lien profond et la requête. Vous pouvez également mettre en forme l’URL pour inclure le cluster, la base de données et la requête à l’aide du modèle suivant :

`https://dataexplorer.azure.com/`[ `clusters/` *Cluster* [ `/databases/` *Database* [ `?` *options*]]]

Les options suivantes peuvent être spécifiées :

* `workspace=empty`: Indique de créer un nouvel espace de travail vide (aucun rappel des clusters, onglets et requêtes précédents n’est effectué).



![texte de remplacement](./Images/KustoTools-WebExplorer/DeepLink.gif "DeepLink")

## <a name="feedback"></a>Commentaires

Vous pouvez envoyer vos commentaires via l’outil.
![texte de remplacement](./Images/KustoTools-WebExplorer/Feedback.gif "Commentaires")
