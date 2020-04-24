---
title: Gestion des requêtes - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la gestion des requêtes dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: b888aa004b4c8b02cd4e1d130bb0b5319af018b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520517"
---
# <a name="queries-management"></a>Gestion des requêtes

## <a name="show-queries"></a>.show requêtes

La `.show` `queries` commande renvoie une liste de requêtes qui ont atteint un état final, et que l’utilisateur invoquant la commande a accès pour voir:


* Un [administrateur de base de données ou un moniteur de base de données](../management/access-control/role-based-authorization.md) peut voir n’importe quelle commande qui a été invoquée dans leur base de données.
* D’autres utilisateurs ne peuvent voir que les requêtes qui ont été invoquées par eux.

**Syntaxe**

`.show` `queries`

## <a name="show-running-queries"></a>.show requêtes en cours d’exécution

`.show` `running` La `queries` commande renvoie une liste de requêtes actuellement exécutantes par l’utilisateur, ou par un autre utilisateur, ou par tous les utilisateurs.

**Syntaxe**

```kusto
.show running queries
```

* (1) renvoie les requêtes actuellement exécutantes par l’utilisateur invoquant (nécessite un accès à la lecture).

## <a name="cancel-query"></a>.annuler la requête

La `.cancel` `query` commande commence une tentative de meilleur effort pour annuler une requête spécifique précédemment commencée par le même utilisateur.

* Les administrateurs de cluster peuvent annuler n’importe quelle requête en cours d’exécution.
* Les administrateurs de base de données peuvent annuler toute requête en cours d’exécution qui a été invoquée sur une base de données sur laquelle ils ont accès à l’administration.
* D’autres utilisateurs ne peuvent annuler les requêtes qu’ils ont commencées. 

**Syntaxe**

`.cancel``query` *ClientRequestId (en)*

* *ClientRequestId* est la valeur des requêtes originales ClientRequestId champ, comme un `string` littéral.

**Exemple**

```kusto
.cancel query "KE.RunQuery;8f70e9ab-958f-4955-99df-d2a288b32b2c"
```

