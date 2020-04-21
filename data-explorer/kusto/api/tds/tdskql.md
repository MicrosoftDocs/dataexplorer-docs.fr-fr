---
title: KQL sur TDS - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit KQL sur TDS dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2019
ms.openlocfilehash: 364db99141c528f4a822692124ebb870d7315bca
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523305"
---
# <a name="kql-over-tds"></a>KQL over TDS

Kusto permet d’utiliser le point de terminaison TDS pour exécuter les requêtes rédigées dans la langue de requête [KQL](../../query/index.md) natif. Cette fonctionnalité offre une migiration plus fluide vers Kusto. Par exemple, il est possible de créer un emploi SSIS pour interroger Kusto avec la requête KQL.

## <a name="executing-kusto-stored-functions"></a>Exécution des fonctions stockées Kusto

Kusto permet d’exécuter [des fonctions stockées](../../query/schema-entities/stored-functions.md) de la même manière que d’appeler les procédures stockées SQL.

Par exemple, la fonction stockée MyFunction :

|Nom |Paramètres|body|Dossier|DocString (en)
|---|---|---|---|---
|MyFunction |(myLimit: long)| "StormEvents &#124; limite myLimit"|MyFolder (en)|Fonction de démonstration avec paramètre||

peut être appelé comme ceci:

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

Veuillez noter que les fonctions stockées `kusto`doivent être appelées avec schéma explicite nommé, pour distinguer entre les fonctions stockées Kusto et les procédures imitées de système SQL stockées.

Les fonctions stockées Kusto peuvent également être appelées à partir de T-SQL, tout comme les fonctions tabulaires SQL :

```sql
SELECT * FROM kusto.MyFunction(10)
```

Il est recommandé de créer des requêtes KQL optimisées et de les encapsuler dans des fonctions stockées, ce qui rend le code de requête T-SQL minimal.

## <a name="executing-kql-query"></a>Exécution de la requête KQL

De même que `sp_executesql`le serveur SQL `sp_execute_kql` , Kusto a introduit la procédure stockée pour l’exécution des requêtes [KQL,](../../query/index.md) y compris les requêtes paramétrisées.

Le 1er `sp_execute_kql` paramètre est la requête KQL. Des paramètres supplémentaires peuvent être introduits et ils agiront comme [des paramètres de requête.](../../query/queryparametersstatement.md)

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

Veuillez noter qu’il n’est pas nécessaire de déclarer les paramètres lors de l’appel via TDS, car les types de paramètres sont définis par protocole.