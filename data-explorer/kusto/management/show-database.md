---
title: .afficher la base de données - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .show base de données dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 95d90377afd33971052dd00cd6ab3d662130824c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519871"
---
# <a name="show-database"></a>.afficher la base de données

Retourne un tableau indiquant les propriétés de la base de données contextuelle.

Pour retourner un tableau dans lequel chaque enregistrement correspond à une base de données dans le cluster auquel l’utilisateur a accès, voir [.afficher les bases de données](show-databases.md).

**Syntaxe**

`.show` `database` [`details` | `identity` | `policies` | `datastats`]

L’appel par défaut sans aucune option spécifiée est égal à l’option « identité ».

**Sortie pour option «identité»**
 
|Paramètre de sortie |Type |Description 
|---|---|---
|nom_base_de_données  |String |Nom de la base de données. Les noms de base de données sont sensibles aux cas. 
|PersistantStorage  |String |L’URI de stockage persistant dans lequel la base de données est stockée. (Ce champ est vide pour les bases de données éphémères.) 
|Version  |String |Numéro de version de la base de données. Ce nombre est mis à jour pour chaque opération de modification dans la base de données (comme l’ajout de données et la modification du schéma). 
|IsCurrent  |Boolean |Vrai si la base de données est celle que la connexion actuelle indique. 
|Base de donnéesAccessMode  |String |Comment le cluster est attaché à la base de données. Par exemple, si la base de données est jointe en mode ReadOnly, le cluster échouera à toutes les demandes de modification de la base de données de quelque manière que ce soit. 
|PrettyName (En) |String |La jolie base de données.
|CurrentUserIsUnrestrictedViewer |Boolean | Précise si l’utilisateur actuel est un spectateur sans restriction sur la base de données.
|DatabaseId |Guid |L’ID unique de la base de données.

**Sortie pour option «détails»**
 
|Paramètre de sortie |Type |Description 
|---|---|---
|nom_base_de_données  |String |Nom de la base de données. Les noms de base de données sont sensibles aux cas. 
|PersistantStorage  |String |L’URI de stockage persistant dans lequel la base de données est stockée. (Ce champ est vide pour les bases de données éphémères.) 
|Version  |String |Le numéro de version de base de données. Ce nombre est mis à jour pour chaque opération de modification dans la base de données (comme l’ajout de données et la modification du schéma). 
|IsCurrent  |Boolean |Vrai si la base de données est celle que la connexion actuelle indique. 
|Base de donnéesAccessMode  |String |Comment le cluster est attaché à la base de données. Par exemple, si la base de données est jointe en mode ReadOnly, le cluster échouera à toutes les demandes de modification de la base de données de quelque manière que ce soit. 
|PrettyName (En) |String |La jolie base de données.
|Principes autorisés |String | La collection de la base de données de directeurs autorisés (sérialisé en format JSON).
|RétentionPolicy |String | La politique de rétention de la base de données (sérialisée en format JSON).
|FusionPolicy |String | La politique de fusion d’étendues de la base de données (sérialisée en format JSON).
|CachingPolicy |String | La politique de cachage de la base de données (sérialisée en format JSON).
|ShardingPolicy (ShardingPolicy) |String | La politique de Sharding de la base de données (sérialisée en format JSON).
|StreamingIngestionPolicy |String | La politique d’ingestion en continu de la base de données (sérialisée en format JSON).
|IngestionBatchingPolicy |String | La politique d’ingestion de la base de données (sérialisée en format JSON).
|TotalSize |Real | La taille totale de la base de données.
|DatabaseId |Guid |L’ID unique de la base de données.

**Sortie pour option «politiques»**
 
|Paramètre de sortie |Type |Description 
|---|---|---
|nom_base_de_données  |String |Nom de la base de données. Les noms de base de données sont sensibles aux cas. 
|PersistantStorage  |String |L’URI de stockage persistant dans lequel la base de données est stockée. (Ce champ est vide pour les bases de données éphémères.) 
|Version  |String |Le numéro de version de base de données. Ce nombre est mis à jour pour chaque opération de modification dans la base de données (comme l’ajout de données et la modification du schéma). 
|IsCurrent  |Boolean |Vrai si la base de données est celle que la connexion actuelle indique. 
|Base de donnéesAccessMode  |String |Comment le cluster est attaché à la base de données. Par exemple, si la base de données est jointe en mode ReadOnly, le cluster échouera à toutes les demandes de modification de la base de données de quelque manière que ce soit. 
|PrettyName (En) |String |Le joli nom de la base de données.
|DatabaseId |Guid |L’ID unique de la base de données.
|Principes autorisés |String | La collection de la base de données de directeurs autorisés (sérialisé en format JSON).
|RétentionPolicy |String | La politique de rétention de la base de données (sérialisée en format JSON).
|FusionPolicy |String | La politique de fusion d’étendues de la base de données (sérialisée en format JSON).
|CachingPolicy |String | La politique de cachage de la base de données (sérialisée en format JSON).
|ShardingPolicy (ShardingPolicy) |String | La politique de Sharding de la base de données (sérialisée en format JSON).
|StreamingIngestionPolicy |String | La politique d’ingestion en continu de la base de données (sérialisée en format JSON).
|IngestionBatchingPolicy |String | La politique d’ingestion de la base de données (sérialisée en format JSON).

**Sortie de l’option 'datastats'**

|Paramètre de sortie |Type |Description 
|---|---|---
|nom_base_de_données  |String |Nom de la base de données. Les noms de base de données sont sensibles aux cas.
|PersistantStorage  |String |L’URI de stockage persistant dans lequel la base de données est stockée. (Ce champ est vide pour les bases de données éphémères.) 
|Version  |String |Le numéro de version de base de données. Ce nombre est mis à jour pour chaque opération de modification dans la base de données (comme l’ajout de données et la modification du schéma). 
|IsCurrent  |Boolean |Vrai si la base de données est celle que la connexion actuelle indique. 
|Base de donnéesAccessMode  |String |Comment le cluster est attaché à la base de données. Par exemple, si la base de données est jointe en mode ReadOnly, le cluster échouera à toutes les demandes de modification de la base de données de quelque manière que ce soit. 
|PrettyName (En) |String |Le joli nom de la base de données.
|DatabaseId |Guid |L’ID unique de la base de données.
|OriginalSize |Real | La taille totale de l’original de la base de données.
|ExtentSize (en) |Real | La taille totale de la base de données (données et indices).
|CompressésSize |Real | La taille totale des données compressées de la base de données.
|IndexSize |Real | La taille totale de l’index de la base de données.
|RowCount |Long | Les étendues de la base de données comptent au total des rangs.
|HotOriginalSize |Real | La taille d’origine de la base de données est chaude.
|HotExtentSize |Real | La taille totale de la base de données (données et indices).
|HotCompressedSize |Real | La taille compressée des données totales de la base de données.
|HotIndexSize (en) |Real | La taille de l’index total de la base de données.
|HotRowCount (en) |Long | Le nombre de lignes à secteurs chauds de la base de données.