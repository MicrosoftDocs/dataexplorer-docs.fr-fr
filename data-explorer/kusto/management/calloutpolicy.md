---
title: Stratégie de légende-Azure Explorateur de données
description: Cet article décrit la stratégie de légende dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 809088f35567f85444755d89ab30e02fad46abaf
ms.sourcegitcommit: 313a91d2a34383b5a6e39add6c8b7fabb4f8d39a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90680675"
---
# <a name="callout-policy"></a>Stratégie de légende

Les clusters Explorateur de données Azure peuvent communiquer avec des services externes dans de nombreux scénarios différents.
Les administrateurs de cluster peuvent gérer les domaines autorisés pour les appels externes, en mettant à jour la stratégie de légende du cluster.

Les stratégies de légende sont gérées au niveau du cluster et sont classées dans les types suivants.
* `kusto` -Contrôle les requêtes entre clusters Azure Explorateur de données.
* `sql` -Contrôle le [plug-in SQL](../query/sqlrequestplugin.md).
* `cosmosdb` -Contrôle le [plug-in CosmosDB](../query/cosmosdb-plugin.md).
* `webapi` -Contrôle d’autres appels Web externes.
* `sandbox_artifacts`-Contrôle les plug-ins sandbox ([python](../query/pythonplugin.md)  |  [R](../query/rplugin.md)).
* `external_data` -Contrôle l’accès aux données externes via des [tables externes](../query/schema-entities/externaltables.md) ou un opérateur [ExternalData](../query/externaldata-operator.md) .

La stratégie de légende est composée des éléments suivants.

* **CalloutType** : définit le type de la légende et peut être `kusto` , `sql` ou `webapi`
* **CalloutUriRegex** : spécifie l’expression régulière autorisée du domaine de la légende
* **CanCall** : indique si la légende est autorisée par des appels externes.

## <a name="predefined-callout-policies"></a>Stratégies de légende prédéfinies

Le tableau suivant présente un ensemble de stratégies de légende prédéfinies qui sont préconfigurées sur tous les clusters Azure Explorateur de données pour permettre aux légendes de sélectionner les services.

|Service      |Cloud        |Désignation  |Domaines autorisés |
|-------------|-------------|-------------|-------------|
|Kusto |`Public Azure` |Requêtes entre clusters |`^[^.]*\.kusto\.windows\.net$` <br> `^[^.]*\.kustomfa\.windows\.net$` |
|Kusto |`Black Forest` |Requêtes entre clusters |`^[^.]*\.kusto\.cloudapi\.de$` <br> `^[^.]*\.kustomfa\.cloudapi\.de$` |
|Kusto |`Fairfax` |Requêtes entre clusters |`^[^.]*\.kusto\.usgovcloudapi\.net$` <br> `^[^.]*\.kustomfa\.usgovcloudapi\.net$` |
|Kusto |`Mooncake` |Requêtes entre clusters |`^[^.]*\.kusto\.chinacloudapi\.cn$` <br> `^[^.]*\.kustomfa\.chinacloudapi\.cn$` |
|BD Azure |`Public Azure` |Requêtes SQL |`^[^.]*\.database\.windows\.net$` <br> `^[^.]*\.databasemfa\.windows\.net$` |
|BD Azure |`Black Forest` |Requêtes SQL |`^[^.]*\.database\.cloudapi\.de$` <br> `^[^.]*\.databasemfa\.cloudapi\.de$` |
|BD Azure |`Fairfax` |Requêtes SQL |`^[^.]*\.database\.usgovcloudapi\.net$` <br> `^[^.]*\.databasemfa\.usgovcloudapi\.net$` |
|BD Azure |`Mooncake` |Requêtes SQL |`^[^.]*\.database\.chinacloudapi\.cn$` <br> `^[^.]*\.databasemfa\.chinacloudapi\.cn$` |
|Service de comparaison |Cloud public Azure |Demandes de comparaison |`baseliningsvc-int.azurewebsites.net` <br> `baseliningsvc-ppe.azurewebsites.net` <br> `baseliningsvc-prod.azurewebsites.net` |

## <a name="control-commands"></a>Commandes de contrôle

Les commandes requièrent des autorisations [AllDatabasesAdmin](access-control/role-based-authorization.md) .

**Afficher toutes les stratégies de légende configurées**

```kusto
.show cluster policy callout
```

**Stratégies ALTER Callout**

```kusto
.alter cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}]'
```

**Ajouter un ensemble de légendes autorisées**

```kusto
.alter-merge cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}, {"CalloutType": "webapi","CalloutUriRegex": "bing\\.com","CanCall": true}]'
```

**Supprimer toutes les stratégies de légende non immuables**

```kusto
.delete cluster policy callout
```
