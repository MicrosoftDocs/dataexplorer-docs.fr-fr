---
title: KQL sur TDS-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit KQL sur TDS dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2019
ms.openlocfilehash: 071658bf2277dd0ddb4734aaf0b59a7a44c8fe27
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512348"
---
# <a name="kql-over-tds"></a>KQL over TDS

Kusto permet aux points de terminaison TDS d’exécuter des requêtes créées dans le langage de requête [KQL](../../query/index.md) natif. Cette fonctionnalité permet une migration plus fluide vers Kusto. Par exemple, vous pouvez créer des travaux SSIS pour interroger Kusto à l’aide d’une requête KQL.

## <a name="executing-kusto-stored-functions"></a>Exécution de fonctions stockées Kusto

Kusto permet l’exécution de [fonctions stockées](../../query/schema-entities/stored-functions.md) , comme l’appel de procédures stockées SQL.

Par exemple, la fonction stockée MyFunction :

|Nom |Paramètres|body|Dossier|DocString
|---|---|---|---|---
|MyFunction |(myLimit : long)| {StormEvents &#124; limite myLimit}|Mondossier|Fonction Demo avec un paramètre||

peut être appelée comme suit :

```csharp
  using (var connection = new SqlConnection(csb.ToString()))
  {
    await connection.OpenAsync();
    using (var command = new SqlCommand("kusto.MyFunction", connection))
    {
      command.CommandType = CommandType.StoredProcedure;
      var parameter = new SqlParameter("mylimit", SqlDbType.Int);
      command.Parameters.Add(parameter);
      parameter.Value = 3;
      using (var reader = await command.ExecuteReaderAsync())
      {
        // Read the response.
      }
    }
  }
```

> [!NOTE]
> Appelez des fonctions stockées avec un schéma explicite nommé `kusto` , pour faire la distinction entre les fonctions stockées Kusto et les procédures stockées système SQL émulées.

Vous pouvez également appeler des fonctions stockées Kusto à partir de T-SQL, comme les fonctions tabulaires SQL :

```sql
SELECT * FROM kusto.MyFunction(10)
```

Créer des requêtes KQL optimisées et les encapsuler dans des fonctions stockées, ce qui rend le code de requête T-SQL minimal.

## <a name="executing-kql-query"></a>Exécution de la requête KQL

La procédure stockée `sp_execute_kql` exécute des requêtes [KQL](../../query/index.md) (y compris des requêtes paramétrables). Cette procédure est similaire à celle de SQL Server `sp_executesql` .

Le premier paramètre de `sp_execute_kql` est la requête KQL. Vous pouvez introduire des paramètres supplémentaires et ils agiront comme des [paramètres de requête](../../query/queryparametersstatement.md).

Par exemple :

```csharp
  using (var connection = new SqlConnection(csb.ToString()))
  {
    await connection.OpenAsync();
    using (var command = new SqlCommand("sp_execute_kql", connection))
    {
      command.CommandType = CommandType.StoredProcedure;
      var query = new SqlParameter("@kql_query", SqlDbType.NVarChar);
      command.Parameters.Add(query);
      var parameter = new SqlParameter("mylimit", SqlDbType.Int);
      command.Parameters.Add(parameter);
      query.Value = "StormEvents | limit myLimit";
      parameter.Value = 3;
      using (var reader = await command.ExecuteReaderAsync())
      {
        // Read the response.
      }
    }
  }
```

> [!NOTE]
> Il n’est pas nécessaire de déclarer des paramètres lors de l’appel via TDS, car les types de paramètres sont définis via le protocole.
