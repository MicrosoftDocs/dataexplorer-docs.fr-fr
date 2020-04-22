---
title: Politique Callout - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique Callout dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: df57c4901cef74d574108d2c6e75672d1faba75c
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744515"
---
# <a name="callout-policy"></a>Stratégie de légende

## <a name="overview"></a>Vue d’ensemble

Les clusters Azure Data Explorer peuvent communiquer avec les services externes dans de nombreux scénarios différents.
Les administrateurs de cluster peuvent gérer les domaines autorisés pour les appels externes en mettant à jour la politique d’appel du cluster.

Les politiques d’appel sont gérées au niveau des grappes et sont classées dans les types suivants :
* `kusto`- contrôle les requêtes à clusters croisés Azure Data Explorer.
* `sql`- contrôle le [plugin SQL](../query/sqlrequestplugin.md).


* `webapi`- contrôle d’autres appels Web externes.
* `sandbox_artifacts`- contrôle les plugins sandboxed ([python](../query/pythonplugin.md) | [R](../query/rplugin.md)).

La politique d’appel est composée des éléments suivants :
* **CalloutType** définit le type de l’appel, et peut être l’un des éléments suivants: kusto, sql, ou webapi
* **CalloutUriRegex** précise le Regex autorisé du domaine de l’appel
* **CanCall** indique si l’appel est autorisé les appels externes.

## <a name="predefined-callout-policies"></a>Politiques Prédéfinies Callout

Il existe un ensemble de politiques d’appel prédéfinis immuablement préconfigurées sur tous les clusters Azure Data Explorer pour faciliter les appels à sélectionner les services.

|Service      |Cloud        |Désignation  |Domaines autorisés |
|-------------|-------------|-------------|-------------|
|Kusto |Cloud public Azure |Requêtes de clusters croisés |`^[^.]*\.kusto\.windows\.net$` <br> `^[^.]*\.kustomfa\.windows\.net$` |
|Kusto |Forêt Noire |Requêtes de clusters croisés |`^[^.]*\.kusto\.cloudapi\.de$` <br> `^[^.]*\.kustomfa\.cloudapi\.de$` |
|Kusto |Fairfax |Requêtes de clusters croisés |`^[^.]*\.kusto\.usgovcloudapi\.net$` <br> `^[^.]*\.kustomfa\.usgovcloudapi\.net$` |
|Kusto |Mooncake |Requêtes de clusters croisés |`^[^.]*\.kusto\.chinacloudapi\.cn$` <br> `^[^.]*\.kustomfa\.chinacloudapi\.cn$` |
|Azure DB |Cloud public Azure |Demandes SQL |`^[^.]*\.database\.windows\.net$` <br> `^[^.]*\.databasemfa\.windows\.net$` |
|Azure DB |Forêt Noire |Demandes SQL |`^[^.]*\.database\.cloudapi\.de$` <br> `^[^.]*\.databasemfa\.cloudapi\.de$` |
|Azure DB |Fairfax |Demandes SQL |`^[^.]*\.database\.usgovcloudapi\.net$` <br> `^[^.]*\.databasemfa\.usgovcloudapi\.net$` |
|Azure DB |Mooncake |Demandes SQL |`^[^.]*\.database\.chinacloudapi\.cn$` <br> `^[^.]*\.databasemfa\.chinacloudapi\.cn$` |
|Service de baselining |Cloud public Azure |Demandes de base |`baseliningsvc-int.azurewebsites.net` <br> `baseliningsvc-ppe.azurewebsites.net` <br> `baseliningsvc-prod.azurewebsites.net` |


## <a name="control-commands"></a>Commandes de contrôle

Les commandes nécessitent des [autorisations AllDatabasesAdmin.](access-control/role-based-authorization.md)

**Afficher toutes les stratégies d’appel configurées**

```kusto
.show cluster policy callout
```

**Modifier les politiques de sortie des appels**

```kusto
.alter cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}]'
```

**Ajouter un ensemble d’appels autorisés**

```kusto
.alter-merge cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}, {"CalloutType": "webapi","CalloutUriRegex": "bing\\.com","CanCall": true}]'
```

**Supprimer toutes les stratégies d’appel non immuables**

```kusto
.delete cluster policy callout
```
