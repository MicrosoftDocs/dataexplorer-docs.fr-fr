---
title: plug-in cosmosdb_sql_request-Azure Explorateur de données
description: Cet article décrit le plug-in cosmosdb_sql_request dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
ms.date: 09/11/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 7c95f7676ecdc88deefeae5db3f904dd5048a5ba
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94417589"
---
# <a name="cosmosdb_sql_request-plugin"></a>cosmosdb_sql_request, plug-in

::: zone pivot="azuredataexplorer"

Le `cosmosdb_sql_request` plug-in envoie une requête SQL à un point de terminaison de réseau Cosmos DB SQL et retourne les résultats de la requête. Ce plug-in est principalement conçu pour l’interrogation de petits jeux de données, par exemple, l’enrichissement de données avec des données de référence stockées dans [Azure Cosmos DB](/azure/cosmos-db/).

## <a name="syntax"></a>Syntaxe

`evaluate``cosmosdb_sql_request` `(` *ConnectionString* `,` *SqlQuery* [ `,` *SqlParameters* [ `,` *options* ]]`)`

## <a name="arguments"></a>Arguments

|Nom de l’argument | Description | Obligatoire ou facultatif | 
|---|---|---|
| *ConnectionString* | `string`Littéral indiquant la chaîne de connexion qui pointe vers la collection de Cosmos dB à interroger. Il doit inclure *AccountEndpoint* , *Database* et *collection*. Elle peut inclure *AccountKey* si une clé principale est utilisée pour l’authentification. <br> **Exemple :** `'AccountEndpoint=https://cosmosdbacc.documents.azure.com:443/ ;Database=MyDatabase;Collection=MyCollection;AccountKey=' h'R8PM...;'`| Obligatoire |
| *SqlQuery*| `string`Littéral indiquant la requête à exécuter. | Obligatoire |
| *SqlParameters* | Valeur constante de type `dynamic` qui contient des paires clé-valeur à passer comme paramètres avec la requête. Les noms de paramètres doivent commencer par `@` . | Facultatif |
| *Options* | Valeur constante de type `dynamic` qui contient des paramètres plus avancés en tant que paires clé-valeur. | Facultatif |
|| ----*Les paramètres d’options pris en charge sont les suivants :*-----
|      `armResourceId` | Récupérer la clé API à partir de la Azure Resource Manager <br> **Exemple :** `/subscriptions/a0cd6542-7eaf-43d2-bbdd-b678a869aad1/resourceGroups/ cosmoddbresourcegrouput/providers/Microsoft.DocumentDb/databaseAccounts/cosmosdbacc`| 
|  `token` | Fournissez le jeton d’accès Azure AD utilisé pour l’authentification auprès du Azure Resource Manager.
| `preferredLocations` | Contrôle de la région à partir de laquelle les données sont interrogées. <br> **Exemple :** `['East US']` | |  

## <a name="set-callout-policy"></a>Définir la stratégie de légende

Le plug-in fait des légendes sur le Cosmos DB. Assurez-vous que la [stratégie de légende](../management/calloutpolicy.md) du cluster autorise les appels de type `cosmosdb` au *CosmosDbUri* cible.

L’exemple suivant montre comment définir la stratégie de légende pour Cosmos DB. Il est recommandé de le limiter à des points de terminaison spécifiques ( `my_endpoint1` , `my_endpoint2` ).

```kusto
[
  {
    "CalloutType": "CosmosDB",
    "CalloutUriRegex": "my_endpoint1\\.documents\\.azure\\.com",
    "CanCall": true
  },
  {
    "CalloutType": "CosmosDB",
    "CalloutUriRegex": "my_endpoint2\\.documents\\.azure\\.com",
    "CanCall": true
  }
]
```

L’exemple suivant illustre une commande de stratégie ALTER Callout pour `cosmosdb` *CalloutType*

```kusto
.alter cluster policy callout @'[{"CalloutType": "cosmosdb", "CalloutUriRegex": "\\.documents\\.azure\\.com", "CanCall": true}]'
```

## <a name="examples"></a>Exemples

### <a name="query-cosmos-db"></a>Cosmos DB de requête

L’exemple suivant utilise le plug-in *cosmosdb_sql_request* pour envoyer une requête SQL afin d’extraire des données de Cosmos dB à l’aide de son API SQL.

```kusto
evaluate cosmosdb_sql_request(
  'AccountEndpoint=https://cosmosdbacc.documents.azure.com:443/;Database=MyDatabase;Collection=MyCollection;AccountKey=' h'R8PM...;',
  'SELECT * from c')
```

### <a name="query-cosmos-db-with-parameters"></a>Cosmos DB de requête avec des paramètres

L’exemple suivant utilise des paramètres de requête SQL et interroge les données d’une autre région. Pour plus d’informations, consultez [`preferredLocations`](/azure/cosmos-db/tutorial-global-distribution-sql-api?tabs=dotnetv2%2Capi-async#preferred-locations).

```kusto
evaluate cosmosdb_sql_request(
    'AccountEndpoint=https://cosmosdbacc.documents.azure.com:443/;Database=MyDatabase;Collection=MyCollection;AccountKey=' h'R8PM...;',
    "SELECT c.id, c.lastName, @param0 as Column0 FROM c WHERE c.dob >= '1970-01-01T00:00:00Z'",
    dynamic({'@param0': datetime(2019-04-16 16:47:26.7423305)}),
    dynamic({'preferredLocations': ['East US']}))
| where lastName == 'Smith'
```

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
