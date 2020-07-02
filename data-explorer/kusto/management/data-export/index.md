---
title: Exportation de données – Azure Data Explorer
description: Cet article décrit l’exportation de données dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/09/2019
ms.openlocfilehash: e6afcf86a02f1f74fe024c1b94d7f9458a72a4e7
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763430"
---
# <a name="data-export"></a>Exportation de données

L’exportation de données est le processus qui exécute une requête Kusto et en écrit les résultats. Les résultats de la requête sont disponibles pour une inspection ultérieure.

Il existe plusieurs méthodes d’exportation de données :

## <a name="client-side-export"></a>Exportation côté client
  Dans sa forme la plus simple, l’exportation de données peut être effectuée côté client. Le client exécute une requête sur le service, lit les résultats, puis les écrit.
Cette forme d’exportation de données dépend de l’outil client utilisé pour effectuer l’exportation, généralement vers le système de fichiers local où l’outil s’exécute. Parmi les outils qui prennent en charge ce modèle, citons [Kusto.Explorer](../../tools/kusto-explorer.md) et l’[interface utilisateur web](../../../web-query-data.md).

## <a name="service-side-export-pull"></a>Exportation côté service – Tirage (pull)
  Si la cible de l’exportation est une table située dans un cluster ou une base de données identique ou différent de la requête, utilisez « ingérer à partir de la requête » sur la table cible. Dans ce flux, une requête est exécutée et ses résultats sont immédiatement ingérés dans une table. Pour plus d’informations, consultez [Ingérer à partir d’une requête](../../management/data-ingestion/ingest-from-query.md).

## <a name="service-side-export-push"></a>Exportation côté service – Envoi (push)
  Les méthodes ci-dessus, [Exportation côté client](#client-side-export) et [Exportation côté service – Tirage (pull)](#service-side-export-pull), sont limitées. Les résultats de la requête doivent transiter par une même connexion réseau entre le producteur effectuant la requête et le consommateur qui en écrit les résultats.
Pour une exportation de données scalable, utilisez le modèle d’exportation « push » dans lequel le service qui exécute la requête écrit aussi ses résultats de manière optimisée. Ce modèle est exposé à travers un ensemble de commandes de contrôle `.export`, qui prennent en charge l’exportation de résultats de requête vers une [table externe](export-data-to-an-external-table.md), une [table SQL](export-data-to-sql.md) ou un [stockage d’objets blob externe](export-data-to-storage.md).
  
  Les commandes d’exportation côté service sont limitées par la capacité d’exportation des données disponibles du cluster.
Vous pouvez exécuter la [commande d’affichage de la capacité](../../management/diagnostics.md#show-capacity) pour voir la capacité d’exportation totale, consommée et restante du cluster.

## <a name="recommendations-for-secret-management-when-using-data-export-commands"></a>Recommandations liées à la gestion des secrets lors de l’utilisation de commandes d’exportation de données

Dans l’idéal, exportez les données vers une cible distante, telle que Stockage Blob Azure ou Azure SQL Database. Utilisez implicitement les informations d’identification du principal de sécurité qui exécute la commande d’exportation de données. Cette méthode n’est pas possible dans certains scénarios. Par exemple, Stockage Blob Azure ne prend pas en charge la notion de principal de sécurité, mais uniquement ses propres jetons.
Cette fonctionnalité prend en charge l’introduction des informations d’identification nécessaires dans le cadre de la commande de contrôle d’exportation de données.

Pour effectuer l’exportation de manière sécurisée :

* Utilisez des [littéraux de chaîne masqués](../../query/scalar-data-types/string.md#obfuscated-string-literals), comme `h@"..."`, au moment d’envoyer des secrets. Les secrets seront nettoyés pour éviter qu’ils apparaissent dans une trace émise en interne.

* Faites en sorte que les mots de passe et les secrets similaires soient stockés de manière sécurisée et tirés (« pull ») à l’aide de l’application, selon les besoins.
