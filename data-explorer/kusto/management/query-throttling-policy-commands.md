---
title: Commandes de la stratégie de limitation des requêtes-Azure Explorateur de données
description: Cet article décrit les commandes de la stratégie de limitation des requêtes dans Azure Explorateur de données
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
ms.date: 10/05/2020
ms.openlocfilehash: 85408aadfd15fc48f1e3e86ab01afce5dca15bce
ms.sourcegitcommit: acd8aba5ed957bee4a7361057387145f9d3ef4e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91734000"
---
# <a name="query-throttling-policy-commands"></a>Commandes de la stratégie de limitation des requêtes

La [stratégie de limitation des requêtes](query-throttling-policy.md) est une stratégie au niveau du cluster qui limite la simultanéité des requêtes dans le cluster. Ces commandes de stratégie de limitation des requêtes requièrent des autorisations [AllDatabasesAdmin](../management/access-control/role-based-authorization.md) .

## <a name="query-throttling-policy-object"></a>Objet de stratégie de limitation des requêtes

Un cluster peut avoir zéro ou une stratégie de limitation des requêtes définie.

Lorsqu’un cluster n’a pas de stratégie de limitation des requêtes définie, la stratégie par défaut s’applique. Pour plus d’informations sur la stratégie par défaut, consultez [limites de requête](../concepts/querylimits.md).

|Propriété  |Type    |Description                                                       |
|----------|--------|------------------------------------------------------------------|
|IsEnabled |`bool`  |Indique si la stratégie de limitation des requêtes est activée (true) ou désactivée (false).     |
|MaxQuantity|`int`|Nombre de requêtes simultanées que le cluster peut exécuter. Le nombre doit avoir une valeur positive. |

## `.show cluster policy querythrottling`

Retourne la [stratégie de limitation des requêtes](query-throttling-policy.md) du cluster.

### <a name="syntax"></a>Syntaxe

`.show` `cluster` `policy` `querythrottling`

### <a name="returns"></a>Retours

Retourne une table avec les colonnes suivantes :

|Colonne    |Type    |Description
|---|---|---
|PolicyName| string |Nom de la stratégie-QueryThrottlingPolicy
|EntityName| string |Vide
|Policy    | string |Objet JSON qui définit la stratégie de limitation des requêtes, mise en forme en tant qu' [objet de stratégie de limitation des requêtes](#query-throttling-policy-object)

### <a name="example"></a>Exemple

<!-- csl -->
```
.show cluster policy querythrottling 
```

Retourne les informations suivantes :

|PolicyName|EntityName|Policy|ChildEntities|EntityType|
|---|---|---|---|---|
|QueryThrottlingPolicy||{"IsEnabled" : true, "MaxQuantity" : 25}

## `.alter cluster policy querythrottling`

Définit la [stratégie de limitation des requêtes](query-throttling-policy.md) de la table spécifiée. 

### <a name="syntax"></a>Syntaxe

`.alter``cluster` `policy` `querythrottling` *QueryThrottlingPolicyObject*

### <a name="arguments"></a>Arguments

*QueryThrottlingPolicyObject* est un objet JSON pour lequel l’objet de stratégie de limitation des requêtes est défini.

### <a name="returns"></a>Retours

Définit l’objet de stratégie de limitation de requête de cluster, en remplaçant toute stratégie actuelle définie, puis retourne la sortie de la commande correspondante [. `querythrottling` afficher la stratégie de cluster](#show-cluster-policy-querythrottling) .

### <a name="example"></a>Exemple

<!-- csl -->
```
.alter cluster policy querythrottling '{"IsEnabled": true, "MaxQuantity": 25}'
```

|PolicyName|EntityName|Policy|ChildEntities|EntityType|
|---|---|---|---|---|
|QueryThrottlingPolicy||{"IsEnabled" : true, "MaxQuantity" : 25}

## `.delete cluster policy querythrottling`

Supprime l’objet de [stratégie de limitation de requête](query-throttling-policy.md) de cluster.

### <a name="syntax"></a>Syntaxe

`.delete` `cluster` `policy` `querythrottling`
