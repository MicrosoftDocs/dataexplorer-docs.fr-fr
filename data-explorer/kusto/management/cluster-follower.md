---
title: Commandes du suiveur de cluster-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les commandes du suiveur de cluster dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: e05f8204ba1e81b9391b6b63f190b81e1db73338
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321044"
---
# <a name="cluster-follower-commands"></a>Commandes du suiveur de cluster

Les commandes de contrôle pour la gestion de la configuration du cluster de suivi sont répertoriées ci-dessous. Ces commandes s’exécutent de façon synchrone, mais elles sont appliquées lors de l’actualisation périodique suivante du schéma. Par conséquent, il peut s’écouler quelques minutes avant que la nouvelle configuration ne soit appliquée.

Les commandes de suivi incluent des commandes [au niveau de la base de données](#database-level-commands) et [au niveau](#table-level-commands)de la table.

## <a name="database-level-commands"></a>Commandes au niveau de la base de données

### <a name="show-follower-database"></a>. afficher la base de données de suivi

Affiche une base de données (ou des bases de données) suivie d’un autre cluster leader, avec un ou plusieurs remplacements de niveau base de données configurés.

**Syntaxe**

`.show` `follower` `database` *nom_base_de_données*

`.show``follower` `databases` `(` *DatabaseName1* `,` ...`,` *DatabaseNameN*`)`

**Sortie** 

| Paramètre de sortie                     | Type    | Description                                                                                                        |
|--------------------------------------|---------|--------------------------------------------------------------------------------------------------------------------|
| nom_base_de_données                         | String  | Nom de la base de données en cours de suivi.                                                                           |
| LeaderClusterMetadataPath            | String  | Chemin d’accès au conteneur de métadonnées du cluster de leader.                                                               |
| CachingPolicyOverride                | String  | Une stratégie de mise en cache de substitution pour la base de données, sérialisée au format JSON ou null.                                         |
| AuthorizedPrincipalsOverride         | String  | Collection de substitution des principaux autorisés pour la base de données, sérialisée au format JSON ou null.                    |
| AuthorizedPrincipalsModificationKind | String  | Type de modification à appliquer à l’aide de AuthorizedPrincipalsOverride ( `none` , `union` ou `replace` ).                  |
| CachingPoliciesModificationKind      | String  | Le type de modification à appliquer à l’aide de la base de données ou de la stratégie de mise en cache au niveau de la table remplace ( `none` , `union` ou `replace` ). |
| IsAutoPrefetchEnabled                | Boolean | Si les nouvelles données sont préalablement extraites à chaque actualisation du schéma.        |
| TableMetadataOverrides               | String  | Sérialisation JSON des substitutions de propriété au niveau de la table (si elles sont définies).                                      |

### <a name="alter-follower-database-policy-caching"></a>. Alter suiver la mise en cache de la stratégie de base de données

Modifie une stratégie de mise en cache de la base de données, afin de remplacer celle définie sur la base de données source dans le cluster leader. Il nécessite des [autorisations DatabaseAdmin](../management/access-control/role-based-authorization.md).

**Remarques**

* La valeur par défaut `modification kind` pour les stratégies de mise en cache est `union` . Pour modifier le `modification kind` , utilisez la [`.alter follower database caching-policies-modification-kind`](#alter-follower-database-caching-policies-modification-kind) commande.
* L’affichage de la stratégie ou des stratégies effectives après la modification peut être effectué à l’aide des `.show` commandes suivantes :
    * [`.show database policy retention`](../management/retention-policy.md#show-retention-policy)
    * [`.show database details`](../management/show-databases.md)
    * [`.show table details`](show-tables-command.md)
* L’affichage des paramètres de remplacement sur la base de données du suiveur une fois la modification effectuée peut être effectué à l’aide de [`.show follower database`](#show-follower-database)

**Syntaxe**

`.alter``follower` `database` *DatabaseName* `policy` `caching` `hot` `=` *HotDataSpan* DatabaseName

**Exemple**

```kusto
.alter follower database MyDb policy caching hot = 7d
```

### <a name="delete-follower-database-policy-caching"></a>. supprimer la mise en cache de la stratégie de base de données

Supprime une stratégie de mise en cache de remplacement de la base de données. En conséquence, la stratégie définie sur la base de données source dans le cluster de leader est la même que celle qui est effective.
Il nécessite des [autorisations DatabaseAdmin](../management/access-control/role-based-authorization.md). 

**Remarques**

* L’affichage de la stratégie ou des stratégies effectives après la modification peut être effectué à l’aide des `.show` commandes suivantes :
    * [`.show database policy retention`](../management/retention-policy.md#show-retention-policy)
    * [`.show database details`](../management/show-databases.md)
    * [`.show table details`](show-tables-command.md)
* L’affichage des paramètres de remplacement sur la base de données du suiveur après la modification peut être effectué à l’aide de [`.show follower database`](#show-follower-database)

**Syntaxe**

`.delete` `follower` `database` *nom_base_de_données* `policy` `caching`

**Exemple**

```kusto
.delete follower database MyDB policy caching
```

### <a name="add-follower-database-principals"></a>. ajouter des principaux de base de données de suivi

Ajoute le ou les principaux autorisés à la collection de bases de données de la base de données principale des principaux autorisés de remplacement. Elle requiert l' [autorisation DatabaseAdmin](../management/access-control/role-based-authorization.md).

**Remarques**

* La valeur par défaut `modification kind` pour ces principaux autorisés est `none` . Pour modifier l' `modification kind`  [instruction USE ALTER suivra Database principals-modification-genre](#alter-follower-database-principals-modification-kind).
* L’affichage de la collection effective des principaux après la modification peut être effectué à l’aide des `.show` commandes suivantes :
    * [`.show database principals`](../management/security-roles.md#managing-database-security-roles)
    * [`.show database details`](../management/show-databases.md)
* L’affichage des paramètres de remplacement sur la base de données du suiveur après la modification peut être effectué à l’aide [de. afficher la base de données du suiveur](#show-follower-database)

**Syntaxe**

`.add``follower` `database` *DatabaseName* `admins`  |  `users`  |  `viewers`  |  `monitors` Principal1 rôle DatabaseName () `(` *principal1* `,` ... `,` *principaln* `)` [ `'` *Remarques* `'` ]

**Exemple**

```kusto
.add follower database MyDB viewers ('aadgroup=mygroup@microsoft.com') 'My Group'
```

### <a name="drop-follower-database-principals"></a>. supprimer les principaux principaux de la base de données

Supprime le (s) principal (s) autorisé (s) de la collection de bases de données de suivi des principaux autorisés de remplacement.
Il nécessite des [autorisations DatabaseAdmin](../management/access-control/role-based-authorization.md).

**Remarques**

* L’affichage de la collection effective des principaux après la modification peut être effectué à l’aide des `.show` commandes suivantes :
    * [`.show database principals`](../management/security-roles.md#managing-database-security-roles)
    * [`.show database details`](../management/show-databases.md)
* L’affichage des paramètres de remplacement sur la base de données du suiveur après la modification peut être effectué à l’aide de [`.show follower database`](#show-follower-database)

**Syntaxe**

`.drop``follower` `database` *DatabaseName* ( `admins`  |  `users`  |  `viewers`  |  `monitors` ) `(` *principal1* `,` ... `,` *principaln*`)`

**Exemple**

```kusto
.drop follower database MyDB viewers ('aadgroup=mygroup@microsoft.com')
```

### <a name="alter-follower-database-principals-modification-kind"></a>. Alter de la base de données principale-modification-type

Modifie le type de modification du principal de la base de données. Il nécessite des [autorisations DatabaseAdmin](../management/access-control/role-based-authorization.md).

**Remarques**

* L’affichage de la collection effective des principaux après la modification peut être effectué à l’aide des `.show` commandes suivantes :
    * [`.show database principals`](../management/security-roles.md#managing-database-security-roles)
    * [`.show database details`](../management/show-databases.md)
* L’affichage des paramètres de remplacement sur la base de données du suiveur après la modification peut être effectué à l’aide [de. afficher la base de données du suiveur](#show-follower-database)

**Syntaxe**

`.alter``follower` `database` *DatabaseName* 
 `principals-modification-kind` = ( `none`  |  `union`  |  `replace` )

**Exemple**

```kusto
.alter follower database MyDB principals-modification-kind = union
```

### <a name="alter-follower-database-caching-policies-modification-kind"></a>. Alter de la base de données de suivi-stratégies-modification-genre

Modifie le type de modification de la base de données et des stratégies de mise en cache de table. Il nécessite des [autorisations DatabaseAdmin](../management/access-control/role-based-authorization.md).

**Remarques**

* L’affichage de la collection efficace de stratégies de mise en cache au niveau de la table ou de la base de données après la modification peut être effectué à l’aide des `.show` commandes standard :
    * [`.show tables details`](show-tables-command.md)
    * [`.show database details`](../management/show-databases.md)
* L’affichage des paramètres de remplacement sur la base de données du suiveur après la modification peut être effectué à l’aide de [`.show follower database`](#show-follower-database)

**Syntaxe**

`.alter``follower` `database` *DatabaseName* `caching-policies-modification-kind` = ( `none`  |  `union`  |  `replace` )

**Exemple**

```kusto
.alter follower database MyDB caching-policies-modification-kind = union
```

### <a name="alter-follower-database-prefetch-extents"></a>. Alter de la base de données du suiveur-étendues

Le cluster de suivi peut attendre que les nouvelles données soient extraites du stockage sous-jacent vers le disque SSD (cache) des nœuds avant de rendre ces données interrogeables.

La commande suivante modifie la configuration de la base de données de suivi pour la pré-récupération de nouvelles étendues à chaque actualisation du schéma. Cette commande requiert des [autorisations DatabaseAdmin](../management/access-control/role-based-authorization.md).

> [!WARNING]
> * Ce paramètre peut dégrader l’actualisation des données dans la base de données de suivi.
> * La configuration par défaut est `false` , et il est recommandé d’utiliser la valeur par défaut.
> * Lorsque vous choisissez de modifier le paramètre `true` , évaluez attentivement l’impact sur l’actualisation d’une période après la modification de la configuration.

**Syntaxe**

`.alter``follower` `database` *DatabaseName* `prefetch-extents` = ( `true`  |  `false` )

**Exemple**

<!-- csl -->
```
.alter follower database MyDB prefetch-extents = false
```

## <a name="table-level-commands"></a>Commandes au niveau de la table

### <a name="alter-follower-table-policy-caching"></a>. modifier la mise en cache de la stratégie de table

Modifie une stratégie de mise en cache au niveau de la table sur la base de données de suivi, pour remplacer la stratégie définie sur la base de données source dans le cluster leader.
Il nécessite des [autorisations DatabaseAdmin](../management/access-control/role-based-authorization.md). 

**Remarques**

* L’affichage de la stratégie ou des stratégies effectives après la modification peut être effectué à l’aide des `.show` commandes suivantes :
    * [`.show database policy retention`](../management/retention-policy.md#show-retention-policy)
    * [`.show database details`](../management/show-databases.md)
    * [`.show table details`](show-tables-command.md)
* L’affichage des paramètres de remplacement sur la base de données du suiveur après la modification peut être effectué à l’aide de [`.show follower database`](#show-follower-database)

**Syntaxe**

`.alter``follower` `database` *DatabaseName* table *TableName* `policy` `caching` `hot` `=` *HotDataSpan*

`.alter``follower` `database` *DatabaseName* Tables DatabaseName `(` *TableName1* `,` ... `,` *TableNameN* `)` `policy` `caching` `hot` `=` *HotDataSpan*

**Exemple**

```kusto
.alter follower database MyDb tables (Table1, Table2) policy caching hot = 7d
```

### <a name="delete-follower-table-policy-caching"></a>. supprimer la mise en cache de la stratégie de table du suiveur

Supprime une stratégie de mise en cache au niveau de la table de substitution sur la base de données du suiveur, en faisant de la stratégie définie sur la base de données source dans le cluster de leader la stratégie effective. Requiert des [autorisations DatabaseAdmin](../management/access-control/role-based-authorization.md). 

**Remarques**

* L’affichage de la stratégie ou des stratégies effectives après la modification peut être effectué à l’aide des `.show` commandes suivantes :
    * [`.show database policy retention`](../management/retention-policy.md#show-retention-policy)
    * [`.show database details`](../management/show-databases.md)
    * [`.show table details`](show-tables-command.md)
* L’affichage des paramètres de remplacement sur la base de données du suiveur après la modification peut être effectué à l’aide de [`.show follower database`](#show-follower-database)

**Syntaxe**

`.delete``follower` `database` *DatabaseName* , `table` *TableName* table `policy``caching`

`.delete``follower` `database` *DatabaseName* `tables` `(` *TableName1* DatabaseName `,` ... `,` *TableNameN* `)` `policy``caching`

**Exemple**

```kusto
.delete follower database MyDB tables (Table1, Table2) policy caching
```

## <a name="sample-configuration"></a>Exemple de configuration

Voici quelques exemples de procédure de configuration d’une base de données de suivi.

Dans cet exemple :

* Notre cluster de suivi, `MyFollowerCluster` sera suivi de la base de données `MyDatabase` du cluster leader, `MyLeaderCluster` .
    * `MyDatabase` contient `N` des tables : `MyTable1` , `MyTable2` , `MyTable3` ,... `MyTableN` ( `N` > 3).
    * Sur `MyLeaderCluster`:
    
    | `MyTable1` stratégie de mise en cache | `MyTable2` stratégie de mise en cache | `MyTable3`...`MyTableN` stratégie de mise en cache   | `MyDatabase` Principaux autorisés                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | étendue de données à chaud = `7d`      | étendue de données à chaud = `30d`     | étendue de données à chaud = `365d`                   | *Visionneuses*  =  `aadgroup=scubadivers@contoso.com` ; *Administrateurs* = `aaduser=jack@contoso.com` |
     
    * Sur `MyFollowerCluster` nous voulons :
    
    | `MyTable1` stratégie de mise en cache | `MyTable2` stratégie de mise en cache | `MyTable3`...`MyTableN` stratégie de mise en cache   | `MyDatabase` Principaux autorisés                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | étendue de données à chaud = `1d`      | étendue de données à chaud = `3d`      | étendue de données à chaud = `0d` (rien n’est mis en cache) | *Administrateurs*  =  `aaduser=jack@contoso.com` , *visionneuses* = `aaduser=jill@contoso.com`         |

> [!IMPORTANT] 
> `MyFollowerCluster`Et `MyLeaderCluster` doivent se trouver dans la même région.

### <a name="steps-to-execute"></a>Étapes à exécuter

*Condition préalable :* Configurer le cluster `MyFollowerCluster` pour suivre la base de données `MyDatabase` du cluster `MyLeaderCluster` .

> [!NOTE]
> Le principal qui exécute les commandes de contrôle est censé être un `DatabaseAdmin` sur la base de données `MyDatabase` .

#### <a name="show-the-current-configuration"></a>Afficher la configuration actuelle

Consultez la configuration actuelle en fonction de laquelle `MyDatabase` est suivi `MyFollowerCluster` :

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| Colonne                              | Valeur                                                    |
|-------------------------------------|----------------------------------------------------------|
|nom_base_de_données                         | Mabdd                                               |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.windows.net/cluster` |
|CachingPolicyOverride                | null                                                     |
|AuthorizedPrincipalsOverride         | []                                                       |
|AuthorizedPrincipalsModificationKind | Aucun                                                     |
|IsAutoPrefetchEnabled                | False                                                    |
|TableMetadataOverrides               |                                                          |
|CachingPoliciesModificationKind      | Union                                                    |                                                                                                                      |

#### <a name="override-authorized-principals"></a>Remplacer les principaux autorisés

Remplacez la collection de principaux autorisés pour `MyDatabase` on `MyFollowerCluster` par un regroupement qui comprend un seul utilisateur AAD en tant qu’administrateur de la base de données et un utilisateur AAD comme visionneuse de base de données :

```kusto
.add follower database MyDatabase admins ('aaduser=jack@contoso.com')

.add follower database MyDatabase viewers ('aaduser=jill@contoso.com')

.alter follower database MyDatabase principals-modification-kind = replace
```

Seuls les deux principaux spécifiques sont autorisés à accéder à `MyDatabase` sur `MyFollowerCluster`

```kusto
.show database MyDatabase principals
```

| Rôle                       | PrincipalType | PrincipalDisplayName                        | PrincipalObjectId                    | PrincipalFQN                                                                      | Notes |
|----------------------------|---------------|---------------------------------------------|--------------------------------------|-----------------------------------------------------------------------------------|-------|
| Administration de base de données MyDatabase  | Utilisateur AAD      | Kusto Jack (UPN : jack@contoso.com )       | 12345678-ABCD-efef-1234-350bf486087b | aaduser = 87654321-ABCD-efef-1234-350bf486087b ; 55555555-4444-3333-2222-2d7cd011db47 |       |
| Visionneuse de base de données mabdd | Utilisateur AAD      | Jill Kusto (UPN : jack@contoso.com )       | abcdefab-abcd-efef-1234-350bf486087b | aaduser = 54321789-ABCD-efef-1234-350bf486087b ; 55555555-4444-3333-2222-2d7cd011db47 |       |

```kusto
.show follower database MyDatabase
| mv-expand parse_json(AuthorizedPrincipalsOverride)
| project AuthorizedPrincipalsOverride.Principal.FullyQualifiedName
```

| AuthorizedPrincipalsOverride_Principal_FullyQualifiedName                         |
|-----------------------------------------------------------------------------------|
| aaduser = 87654321-ABCD-efef-1234-350bf486087b ; 55555555-4444-3333-2222-2d7cd011db47 |
| aaduser = 54321789-ABCD-efef-1234-350bf486087b ; 55555555-4444-3333-2222-2d7cd011db47 |

#### <a name="override-caching-policies"></a>Remplacer les stratégies de mise en cache

Remplacez la collection de stratégies de base de données et de mise en cache au niveau de la table pour `MyDatabase` on `MyFollowerCluster` en définissant toutes les tables de sorte que leurs données *ne soient pas* mises en cache, à l’exception de deux tables spécifiques `MyTable1` , `MyTable2` -dont les données seront mises en cache pour les périodes de `1d` et `3d` , respectivement :

```kusto
.alter follower database MyDatabase policy caching hot = 0d

.alter follower database MyDatabase table MyTable1 policy caching hot = 1d

.alter follower database MyDatabase table MyTable2 policy caching hot = 3d

.alter follower database MyDatabase caching-policies-modification-kind = replace
```

Seules ces deux tables spécifiques ont des données mises en cache, et les autres tables ont une période de données à chaud de `0d` :

```kusto
.show tables details
| summarize TableNames = make_list(TableName) by CachingPolicy
```
| CachingPolicy                                                                | TableNames                  |
|------------------------------------------------------------------------------|-----------------------------|
| {"DataHotSpan" : {"value" : "1.00:00:00"}, "IndexHotSpan" : {"value" : "1.00:00:00"}} | ["MyTable1"]                |
| {"DataHotSpan" : {"value" : "3.00:00:00"}, "IndexHotSpan" : {"value" : "3.00:00:00"}} | ["MyTable2"]                |
| {"DataHotSpan" : {"value" : "0.00:00:00"}, "IndexHotSpan" : {"value" : "0.00:00:00"}} | ["MyTable3",..., "MyTableN"] |

```kusto
.show follower database MyDatabase
| mv-expand parse_json(TableMetadataOverrides)
| project TableMetadataOverrides
```

| TableMetadataOverrides                                                                                              |
|---------------------------------------------------------------------------------------------------------------------|
| {"MyTable1" : {"CachingPolicyOverride" : {"DataHotSpan" : {"value" : "1.00:00:00"}, "IndexHotSpan" : {"value" : "1.00:00:00"}}}} |
| {"MyTable2" : {"CachingPolicyOverride" : {"DataHotSpan" : {"value" : "3.00:00:00"}, "IndexHotSpan" : {"value" : "3.00:00:00"}}}} |

#### <a name="summary"></a>Résumé

Consultez la configuration actuelle où `MyDatabase` est suivi `MyFollowerCluster` :

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| Colonne                              | Valeur                                                                                                                                                                           |
|-------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|nom_base_de_données                         | Mabdd                                                                                                                                                                      |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.windows.net/cluster`                                                                                                                        |
|CachingPolicyOverride                | {"DataHotSpan" : {"value" : "00:00:00"}, "IndexHotSpan" : {"value" : "00:00:00"}}                                                                                                        |
|AuthorizedPrincipalsOverride         | [{"Principal" : {"FullyQualifiedName" : "aaduser = 87654321-ABCD-efef-1234-350bf486087b",...}, {"principal" : {"FullyQualifiedName" : "aaduser = 54321789-ABCD-efef-1234-350bf486087b",...}] |
|AuthorizedPrincipalsModificationKind | Replace                                                                                                                                                                         |
|IsAutoPrefetchEnabled                | False                                                                                                                                                                           |
|TableMetadataOverrides               | {"MyTargetTable" : {"CachingPolicyOverride" : {"DataHotSpan" : {"value" : "3.00:00:00"}...}, "MySourceTable" : {"CachingPolicyOverride" : {"DataHotSpan" : {"value" : "1.00:00:00"},...}}}       |
|CachingPoliciesModificationKind      | Replace                                                                                                                                                                         |
