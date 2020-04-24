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
ms.openlocfilehash: 73b65cc59ff98bc658c542d690812ccf5ad3895a
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108454"
---
# <a name="cluster-follower-commands"></a>Commandes du suiveur de cluster

Les commandes de contrôle pour la gestion de la configuration du cluster de suivi sont répertoriées ci-dessous. Ces commandes s’exécutent de façon synchrone, mais elles sont appliquées lors de l’actualisation périodique suivante du schéma. Par conséquent, il peut s’écouler quelques minutes avant que la nouvelle configuration ne soit appliquée.

Les commandes de suivi incluent des commandes [au niveau de la base de données](#database-level-commands) et [au niveau](#table-level-commands)de la table.

## <a name="database-level-commands"></a>Commandes au niveau de la base de données

### <a name="show-follower-database"></a>. afficher la base de données de suivi

Affiche une base de données (ou des bases de données) suivie d’un autre cluster leader, avec un ou plusieurs remplacements de niveau base de données configurés.


**Syntaxe**

`.show``follower` *DatabaseName* DatabaseName `database`

`.show``follower` `,`DatabaseName1... *DatabaseName1* `databases` `(` `,` *DatabaseNameN*`)`

**Sortie** 

| Paramètre de sortie                     | Type    | Description                                                                                                        |
|--------------------------------------|---------|--------------------------------------------------------------------------------------------------------------------|
| nom_base_de_données                         | String  | Nom de la base de données en cours de suivi.                                                                           |
| LeaderClusterMetadataPath            | String  | Chemin d’accès au conteneur de métadonnées du cluster de leader.                                                               |
| CachingPolicyOverride                | String  | Une stratégie de mise en cache de substitution pour la base de données, sérialisée au format JSON ou null.                                         |
| AuthorizedPrincipalsOverride         | String  | Collection de substitution des principaux autorisés pour la base de données, sérialisée au format JSON ou null.                    |
| AuthorizedPrincipalsModificationKind | String  | Type de modification à appliquer à l’aide`none`de `union` AuthorizedPrincipalsOverride `replace`(, ou).                  |
| CachingPoliciesModificationKind      | String  | Le type de modification à appliquer à l’aide de la base de données ou de la`none`stratégie `union` de `replace`mise en cache au niveau de la table remplace (, ou). |
| IsAutoPrefetchEnabled                | Boolean | Si les nouvelles données sont préalablement extraites à chaque actualisation du schéma.        |
| TableMetadataOverrides               | String  | Sérialisation JSON des substitutions de propriété au niveau de la table (si elles sont définies).                                      |

### <a name="alter-follower-database-policy-caching"></a>. Alter suiver la mise en cache de la stratégie de base de données

Modifie une stratégie de mise en cache de la base de données, afin de remplacer celle définie sur la base de données source dans le cluster leader. Il nécessite des [autorisations DatabaseAdmin](../management/access-control/role-based-authorization.md).



**Remarques**

* La valeur `modification kind` par défaut pour les `union`stratégies de mise en cache est. Pour modifier le `modification kind` , utilisez la commande [. Alter suiveur Database Caching-Policies-modification-Kind](#alter-follower-database-caching-policies-modification-kind) .
* L’affichage de la stratégie ou des stratégies effectives après la modification peut `.show` être effectué à l’aide des commandes suivantes :
    * [. afficher la conservation de la stratégie de base de données](../management/retention-policy.md#show-retention-policy)
    * [. afficher les détails de la base de données](../management/show-databases.md)
    * [.show table details](show-tables-command.md)
* L’affichage des paramètres de remplacement sur la base de données du suiveur une fois la modification effectuée peut être effectué à l’aide [de. afficher la base de données du suiveur](#show-follower-database)

**Syntaxe**

`.alter``follower` `=` *HotDataSpan* *DatabaseName* HotDataSpan DatabaseName `policy` `caching` `hot` `database`



**Exemple**



```kusto
.alter follower database MyDb policy caching hot = 7d
```

### <a name="delete-follower-database-policy-caching"></a>. supprimer la mise en cache de la stratégie de base de données

Supprime une stratégie de mise en cache de remplacement de la base de données. En conséquence, la stratégie définie sur la base de données source dans le cluster de leader est la même que celle qui est effective.
Il nécessite des [autorisations DatabaseAdmin](../management/access-control/role-based-authorization.md). 

**Remarques**

* L’affichage de la stratégie ou des stratégies effectives après la modification peut `.show` être effectué à l’aide des commandes suivantes :
    * [. afficher la conservation de la stratégie de base de données](../management/retention-policy.md#show-retention-policy)
    * [. afficher les détails de la base de données](../management/show-databases.md)
    * [.show table details](show-tables-command.md)
* L’affichage des paramètres de remplacement sur la base de données du suiveur après la modification peut être effectué à l’aide [de. afficher la base de données du suiveur](#show-follower-database)

**Syntaxe**

`.delete``follower` `policy` *DatabaseName* DatabaseName `database``caching`

**Exemple**

```kusto
.delete follower database MyDB policy caching
```

### <a name="add-follower-database-principals"></a>. ajouter des principaux de base de données de suivi

Ajoute le ou les principaux autorisés à la collection de bases de données de la base de données principale des principaux autorisés de remplacement. Elle requiert l' [autorisation DatabaseAdmin](../management/access-control/role-based-authorization.md).



**Remarques**

* La valeur `modification kind` par défaut pour ces principaux autorisés `none`est. Pour modifier l' `modification kind` [instruction USE ALTER suivra Database principals-modification-genre](#alter-follower-database-principals-modification-kind).
* L’affichage de la collection effective des principaux après la modification peut être effectué à `.show` l’aide des commandes suivantes :
    * [. afficher les principaux de la base de données](../management/security-roles.md#managing-database-security-roles)
    * [. afficher les détails de la base de données](../management/show-databases.md)
* L’affichage des paramètres de remplacement sur la base de données du suiveur après la modification peut être effectué à l’aide [de. afficher la base de données du suiveur](#show-follower-database)

**Syntaxe**

`.add``follower` `admins` |  *DatabaseName* *principal1*Principal1 rôle DatabaseName (`users` | ) | ..`monitors``viewers` `(` `database` `,` `,` *principaln* `)` [`'`*notes*remarques`'`]




**Exemple**

```kusto
.add follower database MyDB viewers ('aadgroup=mygroup@microsoft.com') 'My Group'
```

```kusto

```

### <a name="drop-follower-database-principals"></a>. supprimer les principaux principaux de la base de données

Supprime le (s) principal (s) autorisé (s) de la collection de bases de données de suivi des principaux autorisés de remplacement.
Il nécessite des [autorisations DatabaseAdmin](../management/access-control/role-based-authorization.md).

**Remarques**

* L’affichage de la collection effective des principaux après la modification peut être effectué à `.show` l’aide des commandes suivantes :
    * [. afficher les principaux de la base de données](../management/security-roles.md#managing-database-security-roles)
    * [. afficher les détails de la base de données](../management/show-databases.md)
* L’affichage des paramètres de remplacement sur la base de données du suiveur après la modification peut être effectué à l’aide [de. afficher la base de données du suiveur](#show-follower-database)

**Syntaxe**

`.drop``follower` `monitors` *DatabaseName* `(` *principal1*DatabaseName (`admins` | `users`) principal1`,`. | .. `database` `viewers` |  `,` *principaln*`)`

**Exemple**

```kusto
.drop follower database MyDB viewers ('aadgroup=mygroup@microsoft.com')
```

### <a name="alter-follower-database-principals-modification-kind"></a>. Alter de la base de données principale-modification-type

Modifie le type de modification du principal de la base de données. Il nécessite des [autorisations DatabaseAdmin](../management/access-control/role-based-authorization.md).

**Remarques**

* L’affichage de la collection effective des principaux après la modification peut être effectué à `.show` l’aide des commandes suivantes :
    * [. afficher les principaux de la base de données](../management/security-roles.md#managing-database-security-roles)
    * [. afficher les détails de la base de données](../management/show-databases.md)
* L’affichage des paramètres de remplacement sur la base de données du suiveur après la modification peut être effectué à l’aide [de. afficher la base de données du suiveur](#show-follower-database)

**Syntaxe**

`.alter``follower`  |  *DatabaseName*  | DatabaseName`replace`= (`none`) `database` 
 `principals-modification-kind` `union`



**Exemple**

```kusto
.alter follower database MyDB principals-modification-kind = union
```

### <a name="alter-follower-database-caching-policies-modification-kind"></a>. Alter de la base de données de suivi-stratégies-modification-genre

Modifie le type de modification de la base de données et des stratégies de mise en cache de table. Il nécessite des [autorisations DatabaseAdmin](../management/access-control/role-based-authorization.md).

**Remarques**

* L’affichage de la collection efficace de stratégies de mise en cache au niveau de la table ou de la base `.show` de données après la modification peut être effectué à l’aide des commandes standard :
    * [. afficher les détails des tables](show-tables-command.md)
    * [. afficher les détails de la base de données](../management/show-databases.md)
* L’affichage des paramètres de remplacement sur la base de données du suiveur après la modification peut être effectué à l’aide [de. afficher la base de données du suiveur](#show-follower-database)

**Syntaxe**

`.alter``follower` `union` *DatabaseName* `replace`DatabaseName = ()`none` |  `database` `caching-policies-modification-kind`  | 



**Exemple**

```kusto
.alter follower database MyDB caching-policies-modification-kind = union
```



## <a name="table-level-commands"></a>Commandes au niveau de la table

### <a name="alter-follower-table-policy-caching"></a>. modifier la mise en cache de la stratégie de table

Modifie une stratégie de mise en cache au niveau de la table sur la base de données de suivi, pour remplacer la stratégie définie sur la base de données source dans le cluster leader.
Il nécessite des [autorisations DatabaseAdmin](../management/access-control/role-based-authorization.md). 



**Remarques**

* L’affichage de la stratégie ou des stratégies effectives après la modification peut `.show` être effectué à l’aide des commandes suivantes :
    * [. afficher la conservation de la stratégie de base de données](../management/retention-policy.md#show-retention-policy)
    * [. afficher les détails de la base de données](../management/show-databases.md)
    * [.show table details](show-tables-command.md)
* L’affichage des paramètres de remplacement sur la base de données du suiveur après la modification peut être effectué à l’aide [de. afficher la base de données du suiveur](#show-follower-database)

**Syntaxe**





`.alter``follower` `=` *HotDataSpan* *DatabaseName* `caching` *TableName* DatabaseName table TableName `policy` HotDataSpan `database` `hot`

`.alter``follower` `(` *DatabaseName* `,`Tables DatabaseName *TableName1*. `database` .. `,` *TableNameN* TableNameN`)` *HotDataSpan* HotDataSpan `policy` `caching` `hot` `=`

**Exemple**



```kusto
.alter follower database MyDb tables (Table1, Table2) policy caching hot = 7d
```

### <a name="delete-follower-table-policy-caching"></a>. supprimer la mise en cache de la stratégie de table du suiveur

Supprime une stratégie de mise en cache au niveau de la table de substitution sur la base de données du suiveur, en faisant de la stratégie définie sur la base de données source dans le cluster de leader la stratégie effective. Requiert des [autorisations DatabaseAdmin](../management/access-control/role-based-authorization.md). 

**Remarques**

* L’affichage de la stratégie ou des stratégies effectives après la modification peut `.show` être effectué à l’aide des commandes suivantes :
    * [. afficher la conservation de la stratégie de base de données](../management/retention-policy.md#show-retention-policy)
    * [. afficher les détails de la base de données](../management/show-databases.md)
    * [.show table details](show-tables-command.md)
* L’affichage des paramètres de remplacement sur la base de données du suiveur après la modification peut être effectué à l’aide [de. afficher la base de données du suiveur](#show-follower-database)

**Syntaxe**

`.delete``follower` `table` *TableName* *DatabaseName* DatabaseName, `policy` table `database``caching`

`.delete``follower` `,` *DatabaseName* *TableName1*TableName1 DatabaseName `tables` . `(`.. `database` `,``)` *TableNameN* TableNameN `policy``caching`

**Exemple**

```kusto
.delete follower database MyDB tables (Table1, Table2) policy caching
```

## <a name="sample-configuration"></a>Exemple de configuration

Voici quelques exemples de procédure de configuration d’une base de données de suivi.

Dans cet exemple :

* Notre cluster de suivi, `MyFollowerCluster` sera suivi de la `MyDatabase` base de données du cluster `MyLeaderCluster`leader,.
    * `MyDatabase`contient `N` des tables `MyTable1`: `MyTable2`, `MyTable3`,,... `MyTableN` (`N` > 3).
    * Sur `MyLeaderCluster`:
    
    | `MyTable1`stratégie de mise en cache | `MyTable2`stratégie de mise en cache | `MyTable3`... `MyTableN` stratégie de mise en cache   | `MyDatabase`Principaux autorisés                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | étendue de données à chaud =`7d`      | étendue de données à chaud =`30d`     | étendue de données à chaud =`365d`                   | *Visionneuses* = `aadgroup=scubadivers@contoso.com`; *Administrateurs* = `aaduser=jack@contoso.com` |
     
    * Sur `MyFollowerCluster` nous voulons :
    
    | `MyTable1`stratégie de mise en cache | `MyTable2`stratégie de mise en cache | `MyTable3`... `MyTableN` stratégie de mise en cache   | `MyDatabase`Principaux autorisés                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | étendue de données à chaud =`1d`      | étendue de données à chaud =`3d`      | étendue de données à `0d` chaud = (rien n’est mis en cache) | *Admins* = Administrateurs`aaduser=jack@contoso.com`, *visionneuses* = `aaduser=jill@contoso.com`         |

> [!IMPORTANT] 
> `MyFollowerCluster` Et `MyLeaderCluster` doivent se trouver dans la même région.

### <a name="steps-to-execute"></a>Étapes à exécuter

*Condition préalable :* Configurer le `MyFollowerCluster` cluster pour suivre `MyDatabase` la base `MyLeaderCluster`de données du cluster.

> [!NOTE]
> Le principal qui exécute les commandes de contrôle est censé être `DatabaseAdmin` un sur `MyDatabase`la base de données.

#### <a name="show-the-current-configuration"></a>Afficher la configuration actuelle

Consultez la configuration actuelle en fonction de `MyDatabase` laquelle est suivi `MyFollowerCluster`:

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| Colonne                              | Value                                                    |
|-------------------------------------|----------------------------------------------------------|
|nom_base_de_données                         | Mabdd                                               |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.windows.net/cluster` |
|CachingPolicyOverride                | null                                                     |
|AuthorizedPrincipalsOverride         | []                                                       |
|AuthorizedPrincipalsModificationKind | None                                                     |
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

Seuls les deux principaux spécifiques sont autorisés à accéder `MyDatabase` à sur`MyFollowerCluster`

```kusto
.show database MyDatabase principals
```

| Rôle                       | PrincipalType | PrincipalDisplayName                        | PrincipalObjectId                    | PrincipalFQN                                                                      | Notes |
|----------------------------|---------------|---------------------------------------------|--------------------------------------|-----------------------------------------------------------------------------------|-------|
| Administration de base de données MyDatabase  | Utilisateur AAD      | Kusto Jack (UPN : jack@contoso.com)       | 12345678-ABCD-efef-1234-350bf486087b | aaduser = 87654321-ABCD-efef-1234-350bf486087b ; 55555555-4444-3333-2222-2d7cd011db47 |       |
| Visionneuse de base de données mabdd | Utilisateur AAD      | Jill Kusto (UPN : jack@contoso.com)       | abcdefab-abcd-efef-1234-350bf486087b | aaduser = 54321789-ABCD-efef-1234-350bf486087b ; 55555555-4444-3333-2222-2d7cd011db47 |       |

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

Remplacez la collection de stratégies de base de données et de mise `MyDatabase` en `MyFollowerCluster` cache au niveau de la table pour on en définissant toutes les tables de sorte que `MyTable1`leurs `MyTable2` données *ne soient pas* mises en cache, à l’exception de `3d`deux tables spécifiques,-dont les données seront mises en cache pour les périodes de `1d` et, respectivement :

```kusto
.alter follower database MyDatabase policy caching hot = 0d

.alter follower database MyDatabase table MyTable1 policy caching hot = 1d

.alter follower database MyDatabase table MyTable2 policy caching hot = 3d

.alter follower database MyDatabase caching-policies-modification-kind = replace
```

Seules ces deux tables spécifiques ont des données mises en cache, et les autres tables ont une période de données à `0d`chaud de :

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

Consultez la configuration actuelle où `MyDatabase` est suivi `MyFollowerCluster`:

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| Colonne                              | Value                                                                                                                                                                           |
|-------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|nom_base_de_données                         | Mabdd                                                                                                                                                                      |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.windows.net/cluster`                                                                                                                        |
|CachingPolicyOverride                | {"DataHotSpan" : {"value" : "00:00:00"}, "IndexHotSpan" : {"value" : "00:00:00"}}                                                                                                        |
|AuthorizedPrincipalsOverride         | [{"Principal" : {"FullyQualifiedName" : "aaduser = 87654321-ABCD-efef-1234-350bf486087b",...}, {"principal" : {"FullyQualifiedName" : "aaduser = 54321789-ABCD-efef-1234-350bf486087b",...}] |
|AuthorizedPrincipalsModificationKind | Replace                                                                                                                                                                         |
|IsAutoPrefetchEnabled                | False                                                                                                                                                                           |
|TableMetadataOverrides               | {"MyTargetTable" : {"CachingPolicyOverride" : {"DataHotSpan" : {"value" : "3.00:00:00"}...}, "MySourceTable" : {"CachingPolicyOverride" : {"DataHotSpan" : {"value" : "1.00:00:00"},...}}}       |
|CachingPoliciesModificationKind      | Replace                                                                                                                                                                         |