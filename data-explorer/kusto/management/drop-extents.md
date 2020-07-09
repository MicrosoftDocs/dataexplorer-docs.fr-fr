---
title: . extensions Drop-Azure Explorateur de données
description: Cet article décrit la commande supprimer les étendues dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: dfa462ca82cd5e94adff77b3893b3b02d60c6cdc
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060638"
---
# <a name="drop-extents"></a>.drop extents

Supprime les étendues d’une base de données ou d’une table spécifiée.

Cette commande a plusieurs variantes : dans un, les étendues à supprimer sont spécifiées par une requête Kusto. Dans les autres variantes, les extensions sont spécifiées à l’aide d’un mini-langage décrit ci-dessous.

Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md) pour chaque table qui a des étendues retournées par la requête fournie.

> [!NOTE]
> Les partitions de données sont des **Extensions** appelées dans Kusto, et toutes les commandes utilisent « extent » ou « extents » comme synonyme.
> Pour plus d’informations sur les extensions, consultez [extents (Data partitions) Overview](extents-overview.md).

## <a name="drop-extents-with-a-query"></a>Supprimer des étendues à l’aide d’une requête

Supprimer les étendues spécifiées à l’aide d’une requête Kusto.
Un jeu d’enregistrements avec une colonne appelée « ExtentId » est retourné.

Si `whatif` est utilisé, les signale uniquement, sans suppression.

### <a name="syntax"></a>Syntaxe

`.drop``extents`[ `whatif` ] <| *requête*

## <a name="drop-a-specific-extent"></a>Supprimer une étendue spécifique

Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md) si le nom de table est spécifié.

Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md) si le nom de table n’est pas spécifié.

### <a name="syntax"></a>Syntaxe

`.drop``extent` *ExtentId* [ `from` *TableName*]

## <a name="drop-specific-multiple-extents"></a>Supprimer des étendues multiples spécifiques

Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md) si le nom de table est spécifié.

Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md) si le nom de table n’est pas spécifié.

### <a name="syntax"></a>Syntaxe

`.drop``extents` `(` *ExtentId1* `,` ... *ExtentIdN* `)` [ `from` *TableName*]

## <a name="drop-extents-by-specified-properties"></a>Supprimer les étendues par les propriétés spécifiées

La commande prend en charge le mode d’émulation qui produit une sortie comme si la commande avait été exécutée, mais sans réellement l’exécuter. Utilisez `.drop-pretend` à la place de `.drop`.

Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md) si le nom de table est spécifié.

Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md) si le nom de table n’est pas spécifié.

### <a name="syntax"></a>Syntaxe

`.drop``extents`[ `older` *N* ( `days`  |  `hours` )] `from` (*TableName*  |  `all` `tables` ) [ `trim` `by` ( `extentsize`  |  `datasize` ) *n* () `MB`  |  `GB`  |  `bytes` ] [ `limit` *LimitCount*]

* `older`: Seules les extensions datant de plus de *N* jours/heures seront supprimées.
* `trim`: L’opération supprimera les données dans la base de données jusqu’à ce que la somme des étendues corresponde à la taille requise (MaxSize).
* `limit`: L’opération sera appliquée aux premières étendues *LimitCount* .

## <a name="examples"></a>Exemples

### <a name="remove-all-extents-by-time-created"></a>Supprimer toutes les étendues par heure de création

Supprimer toutes les étendues créées il y a plus de 10 jours, à partir de toutes les tables de la base de données`MyDatabase`

```kusto
.drop extents <| .show database MyDatabase extents | where CreatedOn < now() - time(10d)
```

### <a name="remove-some-extents-by-time-created"></a>Supprimer des extensions par heure de création

Supprimer toutes les étendues dans les tables `Table1` et `Table2` dont l’heure de création a eu lieu il y a plus de 10 jours

```kusto
.drop extents older 10 days from tables (Table1, Table2)
```

### <a name="emulation-mode-show-which-extents-would-be-removed-by-the-command"></a>Mode d’émulation : afficher les étendues qui seront supprimées par la commande

>[!NOTE]
>Le paramètre d’ID d’étendue n’est pas applicable pour cette commande.

```kusto
.drop-pretend extents older 10 days from all tables
```

### <a name="remove-all-extents-from-testtable"></a>Supprimer toutes les étendues de « TestTable »

```kusto
.drop extents from TestTable
```

## <a name="return-output"></a>Sortie de retour

|Paramètre de sortie |Type |Description 
|---|---|---
|ExtentId |String |ExtentId qui a été supprimé en raison de la commande
|TableName |String |Nom de la table, où l’étendue appartenait  
|Created |DateTime |Horodateur qui contient des informations sur la création initiale de l’extension
 
## <a name="sample-output"></a>Exemple de sortie

|ID d’étendue |Nom de la table |Créée le 
|---|---|---
|43c6e03f-1713-4ca7-a52a-5db8a4e8b87d |TestTable |2015-01-12 12:48:49.4298178
