---
title: Kusto en tant que serveur lié du serveur SQL - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Kusto comme serveur lié du serveur SQL dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 77db975f2bd7c5c1d747902248070664cd020e39
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523458"
---
# <a name="kusto-as-linked-server-from-sql-server"></a>Kusto en tant que serveur lié à partir du serveur SQL

Le serveur SQL sur place permet d’attacher le serveur lié. Cette fonctionnalité permet de créer des requêtes joignant des données à partir du serveur SQL et à partir de serveurs liés.

Il est possible d’utiliser Kusto comme serveur lié via la connectivité ODBC.

1. Le service SQL Server (sur site) doit utiliser un compte d’annuaire actif (et non le compte Service par défaut) qui permet de se connecter à Kusto à l’aide d’AAD.
2. Installer le dernier pilote ODBC pour SQL Server 2017 (il est également livré avec SSMS 18):https://www.microsoft.com/download/details.aspx?id=56567
3. Préparer la chaîne de connexion DSN moins pour le pilote ODBC `Driver={ODBC Driver 17 for SQL Server};Server=<cluster>.kusto.windows.net;Database=<database>;Authentication=ActiveDirectoryIntegrated;Language=any@MaxStringSize:4000`pour un cluster et une base de données Kusto spécifiques : . L’option linguistique est ajoutée pour accorder Kusto pour coder les cordes comme NVARCHAR(4000). Voir plus de détails sur cette solution de contournement à [ODBC](./clients.md#odbc).
4. Créez le serveur linked avec les 3 paramètres suivants définis : ![texte alt](../images/linkedserverconnection.png "connexion de serveur liée")
5. L’onglet Sécurité doit être défini avec ce paramètre : ![texte alt](../images/linkedserverlogin.png "connexion de serveur lié")

Maintenant, il est possible d’interroger les données de Kusto, comme ceci:
```sql
SELECT * FROM OpenQuery(LINKEDSERVER, 'SELECT * FROM <KustoStoredFunction>[(<Parameters>)]')
```

Remarques :
1. Il est recommandé d’utiliser des [fonctions stockées Kusto](../../query/schema-entities/stored-functions.md) pour extraire des données de Kusto. La fonction stockée peut inclure toute la logique nécessaire pour une requête efficace de Kusto, rédigée en langue [KQL](../../query/index.md) indigène, et contrôlée par des valeurs de paramètres spécifiées. La syntaxe T-SQL pour appeler la fonction stockée Kusto est identique à l’appel de la fonction tabulaire SQL.
2. Le serveur SQL ne prend pas en charge l’appel de fonctions tabulaires à distance à partir de serveurs liés à l’intérieur de ses propres requêtes. La solution de contournement `OpenQuery` de cette limitation est d’utiliser pour l’exécution de requêtes à distance sur le serveur lié. De cette façon, la fonction tabulaire n’est pas appelée sur le serveur SQL directy, mais dans la requête qui est exécuté sur le serveur lié. La requête extérieure T-SQL peut être utilisée pour rejoindre entre les données sur le `OpenQuery`serveur SQL et les données retournées de la fonction stockée Kusto via .