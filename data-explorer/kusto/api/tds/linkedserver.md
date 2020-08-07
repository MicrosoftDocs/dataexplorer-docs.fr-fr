---
title: Kusto en tant que serveur lié à partir de SQL Server-Azure Explorateur de données
description: Cet article décrit Kusto en tant que serveur lié à partir de SQL Server dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: a6f053ac6a838d2dacd047c284b9608bdea9e5dc
ms.sourcegitcommit: bd30e24d026d8d98f9f7d8b79f18a03e295846b8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87758106"
---
# <a name="kusto-as-a-linked-server-from-the-sql-server"></a>Kusto en tant que serveur lié à partir de SQL Server

SQL Server local vous permet d’attacher un serveur lié et vous permet de créer des requêtes de jointure de données à partir de SQL Server et de serveurs liés.

Vous pouvez utiliser Kusto comme serveur lié via la connectivité ODBC.
Le SQL Server service local doit utiliser un compte Active Directory (et non le compte de service par défaut) qui lui permet de se connecter à Azure Explorateur de données à l’aide d’Azure Active Directory (Azure AD).

1. Installez le dernier pilote ODBC pour SQL Server 2017 (il est également fourni avec SSMS 18) :https://aka.ms/downloadmsodbcsql
1. Préparez la chaîne de connexion sans nom de source de données pour le pilote ODBC, pour un cluster et une base de données Azure Explorateur de données spécifiques : `Driver={ODBC Driver 17 for SQL Server};Server=<cluster>.kusto.windows.net;Database=<database>;Authentication=ActiveDirectoryIntegrated;Language=any@MaxStringSize:4000` . L’option Language est ajoutée pour paramétrer Azure Explorateur de données pour encoder les chaînes en tant que NVARCHAR (4000). Pour plus d’informations sur cette solution de contournement, consultez [ODBC](./clients.md#odbc).
1. Créez le serveur lié avec les paramètres pointés par les flèches rouges.

:::image type="content" source="../images/linkedserverconnection.png" alt-text="connexion au serveur lié":::

1. Définissez la sécurité avec le paramètre pointé par la flèche rouge. 

:::image type="content" source="../images/linkedserverlogin.png" alt-text="connexion au serveur lié":::

Pour interroger des données à partir de Kusto :

```sql
SELECT * FROM OpenQuery(LINKEDSERVER, 'SELECT * FROM <KustoStoredFunction>[(<Parameters>)]')
```

> [!NOTE]
> 1. Utilisez les [fonctions stockées](../../query/schema-entities/stored-functions.md) Kusto pour extraire des données d’Azure Explorateur de données. La fonction stockée peut inclure toute la logique nécessaire pour des requêtes efficaces à partir de Kusto, créées dans le langage [KQL](../../query/index.md) natif et contrôlées par des valeurs de paramètre spécifiées. La syntaxe T-SQL pour l’appel de la fonction stockée Kusto est identique à l’appel de la fonction tabulaire SQL.
> 1. SQL Server ne prend pas en charge l’appel de fonctions tabulaires distantes à partir de serveurs liés à l’intérieur de ses propres requêtes. La solution de contournement de cette limitation consiste à utiliser `OpenQuery` pour exécuter des requêtes distantes sur le serveur lié. De cette façon, la fonction tabulaire est appelée non sur le répertoire SQL Server, mais dans une requête exécutée sur le serveur lié. La requête T-SQL externe peut être utilisée pour effectuer une jointure entre les données sur le serveur SQL et les données retournées par la fonction stockée Kusto via `OpenQuery` .
