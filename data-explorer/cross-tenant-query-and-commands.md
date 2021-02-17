---
title: Autoriser les requêtes et les commandes entre locataires dans Azure Data Explorer
description: Découvrez comment autoriser des requêtes ou des commandes à partir de plusieurs locataires dans Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: reference
ms.date: 01/06/2021
ms.openlocfilehash: e675fd0a3a50fa1959e4fb3a6a660546adcb4a36
ms.sourcegitcommit: ec290d02974b4bb28e44c8c4a981fb32b1f945a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98124403"
---
# <a name="allow-cross-tenant-queries-and-commands"></a>Autoriser les requêtes et les commandes interlocataires 

Plusieurs locataires peuvent exécuter des requêtes et des commandes dans un seul cluster Azure Data Explorer. Dans cet article, vous allez apprendre à accorder au cluster l’accès aux principaux à partir d’un autre locataire.

Les propriétaires de cluster peuvent protéger leur cluster contre les requêtes et les commandes d’autres locataires. Pour ce faire, il suffit de gérer la propriété de cluster `TrustedExternalTenants`. La propriété `trustedExternalTenants` définit explicitement les locataires autorisés à exécuter des requêtes et des commandes sur le cluster. Pour plus d’informations, consultez [Corps de la demande de cluster Azure Data Explorer](/rest/api/azurerekusto/clusters/createorupdate#request-body).

> [!NOTE]
> Le principal qui exécutera des requêtes ou des commandes doit également avoir un rôle de base de données approprié. Pour plus d’informations, consultez [Autorisation basée sur les rôles](./kusto/management/access-control/role-based-authorization.md). La validation des rôles corrects a lieu après la validation des locataires externes approuvés.

## <a name="syntax"></a>Syntaxe

**Définir des locataires spécifiques**

`trustedExternalTenants``: [ {"`*value*`": "`*tenantId1*`" }, { "`*value*`": "`*tenantId2*`" }, ... ]`

**Autoriser tous les locataires**

Le tableau trustedExternalTenants prend également en charge la notation étoile (« * ») de tous les locataires, qui autorise les requêtes et les commandes de tous les locataires. 

`trustedExternalTenants``: [ { "`*value*`": "`*`" }]`

> [!NOTE]
> La valeur par défaut de `trustedExternalTenants` est tous les locataires : `[ { "value": "*" }]`. Si le tableau de locataires externes n’a pas été défini lors de la création du cluster, il peut être remplacé par une opération de mise à jour de cluster. Un tableau vide n’est pas accepté.

## <a name="example"></a> Exemple

Mettez à jour le cluster en effectuant l’opération suivante :

```http
PATCH https://management.azure.com/subscriptions/12345678-1234-1234-1234-123456789098/resourceGroups/kustorgtest/providers/Microsoft.Kusto/clusters/kustoclustertest?api-version=2020-09-18
```

### <a name="request-body-specific-tenants"></a>Locataires spécifiques du corps de la demande

```json
{
              "properties": { 
                           "trustedExternalTenants": [
                                         { "value": "tenantId1" }, 
                                         { "value": "tenantId2" }, 
                                         ...
                           ]
              }
}
```

### <a name="request-body-all-tenants"></a>Tous les locataires du corps de la demande

```json
{
              "properties": { 
                           "trustedExternalTenants": [  { "value": "*" }  ]
              }
}

```
