---
title: Ingestion des données - Azure Data Explorer | Microsoft Docs
description: Cet article décrit l’ingestion des données dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: f41304ae4ac51081cd61c41856ed5e7e08ed6f7a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490376"
---
# <a name="data-ingestion"></a>Ingestion de données

Processus par lequel les données sont ajoutées à une table et deviennent disponibles pour la requête.
Selon que la table existe déjà, le processus nécessite des [autorisations d’administrateur de base de données, d’ingesteur de base de données, d’utilisateur de base de données ou d’administrateur de table](../access-control/role-based-authorization.md).

Le processus d’ingestion des données comprend plusieurs étapes :

1. Récupération des données auprès de la source de données.
1. Analyse et validation des données.
1. Mise en correspondance du schéma des données avec le schéma de la table Kusto cible ou création de la table cible si elle n’existe pas déjà.
1. Organisation des données dans des colonnes.
1. Indexation des données.
1. Encodage et compression des données.
1. Persistance dans le stockage des artefacts de stockage Kusto obtenus.
1. Exécution de toutes les stratégies de mise à jour pertinentes, le cas échéant.
1. « Validation » de l’ingestion des données, pour qu’elle devienne disponible pour la requête.

> [!NOTE]
> Certaines des étapes ci-dessus peuvent être ignorées, selon le scénario spécifique.
> Par exemple, les données ingérées via le point de terminaison d’ingestion de streaming ignorent les étapes 4, 5, 6 et 9. Ces opérations sont effectuées en arrière-plan pendant le « nettoyage » des données.
> Autre exemple : si la source de données est le résultat d’une requête Kusto sur le même cluster, il n’est pas nécessaire d’analyser et de valider les données.

> [!WARNING]
> Les données ingérées dans une table dans Kusto sont soumises à la **stratégie de conservation** effective de la table.
> À moins d’être explicitement définie sur une table, la stratégie de conservation effective découle de la stratégie de conservation de la base de données. Par conséquent, lorsque vous ingérez des données dans Kusto, vérifiez que la stratégie de conservation de la base de données est adaptée à vos besoins. Si ce n’est pas le cas, remplacez-la explicitement au niveau de la table. Si vous ne le faites pas, vous risquez d’exposer vos données à une suppression « silencieuse » due à la stratégie de conservation de la base de données. Pour plus d’informations, consultez [Stratégie de rétention](https://kusto.azurewebsites.net/docs/concepts/retentionpolicy.html).

Pour découvrir les propriétés d’ingestion des données, consultez [Propriétés d’ingestion des données](https://docs.microsoft.com/azure/data-explorer/ingestion-properties).
Pour obtenir la liste des formats pris en charge pour l’ingestion des données, consultez [Formats de données](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats).



## <a name="ingestion-methods"></a>Méthodes d’ingestion

Il existe plusieurs méthodes d’ingestion des données, chacune avec ses propres caractéristiques :

1. **Ingestion inline (push)**  : Une commande de contrôle ([.ingest inline](./ingest-inline.md)) est envoyée au moteur, avec les données à ingérer intégrées dans le texte de la commande.
   Cette méthode est utilisée principalement à des fins de test ad hoc et ne doit pas être utilisée à des fins de production.
1. **Ingestion à partir d’une requête** : Une commande de contrôle [(.set, .append, .set-or-append ou .set-or-replace](./ingest-from-query.md)) est envoyée au moteur, avec les données spécifiées indirectement sous forme de résultats d’une requête ou d’une commande.
   Cette méthode s’avère utile pour générer des tables de rapports à partir de tables de données brutes ou pour créer de petites tables temporaires à des fins d’analyse plus poussée.
1. **Ingestion à partir du stockage - tirage (pull)**  : Une commande de contrôle ([.ingest into](./ingest-from-storage.md)) est envoyée au moteur, avec les données stockées dans un stockage externe (par exemple, Stockage Blob Azure) accessible par le moteur et vers lequel la commande pointe.
   Cette méthode permet une ingestion en bloc des données efficace, mais elle pèse sur le client qui effectue l’ingestion car il ne doit pas surtaxer le cluster avec des ingestions simultanées (ni risquer de consommer toutes les ressources de cluster avec l’ingestion de données, réduisant ainsi les performances des requêtes).
1. **Ingestion en file d’attente** : Les données sont chargées dans un stockage externe (par exemple, Stockage Blob Azure). Une notification est envoyée à une file d’attente (par exemple, une file d’attente Azure ou un hub d’événements).
   Il s’agit de la méthode qui est principalement utilisée en production, car sa disponibilité est très haute. En outre, elle n’oblige pas le client à gérer lui-même la capacité et elle gère bien les ajouts en bloc. Cette méthode est parfois appelée « ingestion native ».


|Méthode             |Latence                 |Production|Bloc|Disponibilité|Synchronicité|
|-------------------|------------------------|----------|----|------------|-------------|
|Ingestion inline   |Secondes + durée d’ingestion   |Non        |Non  |Moteur Kusto|Synchrone  |
|Ingestion à partir d’une requête  |Durée de requête + durée d’ingestion|Oui       |Oui |Moteur Kusto|Synchrone  |
|Ingestion à partir du stockage|Secondes + durée d’ingestion   |Oui       |Oui |Moteur Kusto|Les deux         |
|Ingestion en file d’attente   |Minutes                 |Oui       |Oui |Stockage     |Asynchrone |

## <a name="validation-policy-during-ingestion"></a>Stratégie de validation pendant l’ingestion

Pendant l’ingestion à partir du stockage, la source de données est validée dans le cadre de l’analyse.
La stratégie de validation indique comment réagir aux échecs d’analyse. Elle comprend deux propriétés :

* `ValidationOptions`: Ici, `0` signifie qu’aucune validation ne doit être effectuée, `1` valide que tous les enregistrements ont le même nombre de champs (utile pour les fichiers CSV et similaires) et `2` indique d’ignorer les champs qui ne sont pas entre guillemets doubles.

* `ValidationImplications` : `0` indique que les échecs de validation doivent faire échouer la totalité de l’ingestion et `1` indique que les échecs de validation doivent être ignorés.