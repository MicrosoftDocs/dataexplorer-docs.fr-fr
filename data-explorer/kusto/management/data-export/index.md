---
title: Exportation de données - Azure Data Explorer | Microsoft Docs
description: Cet article décrit l’exportation de données dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/09/2019
ms.openlocfilehash: 88bb9e6541d9dc5c934affc8f777f836aad86ae1
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373490"
---
# <a name="data-export"></a>Exportation de données

L’exportation de données est le processus qui exécute une requête Kusto et en écrit les résultats, rendant ces derniers disponibles à des fins d’inspection ultérieure.

Il existe plusieurs méthodes d’exportation de données :

* **Exportation côté client** : Dans sa forme la plus simple, l’exportation de données peut s’effectuer côté client (le client exécute une requête auprès du service, lit les résultats, puis les écrit quelque part). Cette forme d’exportation de données dépend de l’outil client utilisé pour effectuer l’exportation, le plus souvent vers le système de fichiers local où l’outil s’exécute. Parmi les outils qui prennent en charge ce modèle, citons [Kusto.Explorer](../../tools/kusto-explorer.md), l’[interface utilisateur web](../../../web-query-data.md) et autres.

* **Exportation côté service - tirage (pull)**  : Si la cible de l’exportation est une table Kusto (sur le même cluster/la même base de données que la requête ou sur en/une autre), utilisez le flux « ingestion à partir d’une requête » sur la table cible. Dans ce flux, une requête est exécutée et ses résultats sont immédiatement ingérés dans une table Kusto. Consultez [Ingestion des données](../data-ingestion/index.md).



* **Exportation côté service - envoi (push)**  : Les méthodes ci-dessus sont relativement limitées, car les résultats de la requête doivent être diffusés par le biais d’une seule connexion réseau entre le producteur qui effectue la requête et le consommateur qui écrit ses résultats. Pour que l’exportation de données soit scalable, Kusto propose un modèle d’exportation « push » dans lequel le service qui exécute la requête écrit également ses résultats de manière optimisée. Ce modèle est exposé par le biais d’un ensemble de commandes de contrôle `.export`, prenant en charge l’exportation des résultats de la requête vers une [table externe](export-data-to-an-external-table.md), une [table SQL](export-data-to-sql.md) ou un [stockage d’objets blob externe](export-data-to-storage.md).
  
  Les commandes d’exportation côté service sont limitées par la capacité d’exportation des données disponibles du cluster. 
  Vous pouvez exécuter la [commande d’affichage de la capacité](../../management/diagnostics.md#show-capacity) pour voir la capacité d’exportation totale, consommée et restante du cluster.

## <a name="recommendations-for-secret-management-when-using-data-export-commands"></a>Recommandations liées à la gestion des secrets lors de l’utilisation de commandes d’exportation de données

Idéalement, l’exportation de données vers une cible distante (comme Stockage Blob Azure et Azure SQL Database) s’effectue en utilisant implicitement les informations d’identification du principal de sécurité qui exécute la commande d’exportation de données. Ce n’est pas possible dans certains scénarios (par exemple, Stockage Blob Azure ne prend pas en charge la notion de principal de sécurité, mais uniquement ses propres jetons). Ainsi, Kusto prend en charge l’introduction des informations d’identification nécessaires dans le cadre de la commande de contrôle d’exportation de données. Voici quelques recommandations pour vous assurer que l’opération s’effectue de façon sécurisée :

Utilisez des [littéraux de chaîne masqués](../../query/scalar-data-types/string.md#obfuscated-string-literals) (comme `h@"..."`) pour envoyer des secrets à Kusto.
Kusto les lit alors à vitesse variable pour qu’ils n’apparaissent dans aucune trace émise en interne.

De plus, les mots de passe et secrets similaires doivent être stockés de manière sécurisée et « tirés (pull) » par l’application en fonction des besoins.
