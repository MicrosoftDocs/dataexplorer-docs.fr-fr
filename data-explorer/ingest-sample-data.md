---
title: Ingérer des exemples de données dans l’Explorateur de données Azure
description: Découvrez comment ingérer (charger) des exemples de données météorologiques dans l’Explorateur de données Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 08/12/2019
ms.openlocfilehash: 3ece5a9d225e48654a0a3a96c3b7b78327565841
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81491914"
---
# <a name="quickstart-ingest-sample-data-into-azure-data-explorer"></a>Démarrage rapide : Ingérer des exemples de données dans l’Explorateur de données Azure

Cet article vous montre comment ingérer (charger) des exemples de données dans une base de données de l’Explorateur de données Azure. Il existe [plusieurs façons d’ingérer des données](ingest-data-overview.md) ; cet article se concentre sur une approche de base qui convient à des fins de test.

> [!NOTE]
> Vous avez déjà ces données si vous avez terminé [Ingérer des données à l’aide de la bibliothèque Python dans Azure Data Explorer](python-ingest-data.md).

## <a name="prerequisites"></a>Prérequis

[Un cluster et une base de données de test](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Réception de données

L’exemple de jeu de données **StormEvents** contient des données météorologiques des [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

1. Connectez-vous à [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Dans la partie supérieure gauche de l’application, sélectionnez **Ajouter un cluster**.

1. Dans la boîte de dialogue **Ajouter un cluster**, entrez l’URL de votre cluster sous la forme `https://<ClusterName>.<Region>.kusto.windows.net/`, puis sélectionnez **Ajouter**.

1. Collez la commande suivante, puis sélectionnez **Exécuter** pour créer une table StormEvents.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```
1. Collez la commande suivante, puis sélectionnez **Exécuter** pour ingérer des données dans la table StormEvents.

    ```Kusto
    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. Une fois l’ingestion terminée, collez la requête suivante, sélectionnez la requête dans la fenêtre, puis **Exécuter**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    La requête retourne les résultats suivants à partir des exemples de données ingérés.

    ![Résultats de la requête](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Étapes suivantes

* [Ingestion de données Azure Data Explorer](ingest-data-overview.md) pour en savoir plus sur les méthodes d’ingestion.
* [Démarrage rapide : Interroger des données dans l’interface utilisateur web Azure Data Explorer](web-query-data.md).
* [Écrire des requêtes](write-queries.md) avec le langage de requête Kusto.
