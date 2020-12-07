---
title: . afficher la base de données-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit. afficher la base de données dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: eb3e1dd16d36af5d92019b710f3d5790a24b1296
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320483"
---
# <a name="show-database"></a>.show database

Retourne une table qui affiche les propriétés de la base de données de contexte.

Pour retourner une table dans laquelle chaque enregistrement correspond à une base de données du cluster à laquelle l’utilisateur a accès, consultez [`.show databases`](show-databases.md) .

**Syntaxe**

`.show` `database` [`details` | `identity` | `policies` | `datastats`]

L’appel par défaut sans options spécifié est égal à l’option « Identity ».

**Sortie de l’option « Identity »**
 
|Paramètre de sortie |Type |Description 
|---|---|---
|nom_base_de_données  |String |Nom de la base de données. Les noms des bases de données respectent la casse. 
|PersistentStorage  |String |URI de stockage persistant dans lequel la base de données est stockée. (Ce champ est vide pour les bases de données éphémères.) 
|Version  |String |Numéro de version de la base de données. Ce nombre est mis à jour pour chaque opération de modification dans la base de données (par exemple, l’ajout de données et la modification du schéma). 
|IsCurrent  |Boolean |True si la base de données est celle vers laquelle pointe la connexion active. 
|DatabaseAccessMode  |String |Comment le cluster est attaché à la base de données. Par exemple, si la base de données est attachée en mode ReadOnly, le cluster échouera toutes les demandes de modification de la base de données de quelque manière que ce soit. 
|PrettyName |String |Nom convivial de la base de données.
|CurrentUserIsUnrestrictedViewer |Boolean | Spécifie si l’utilisateur actuel est une visionneuse non restreinte sur la base de données.
|DatabaseId |Guid |ID unique de la base de données.

**Sortie de l’option « Détails »**
 
|Paramètre de sortie |Type |Description 
|---|---|---
|nom_base_de_données  |String |Nom de la base de données. Les noms des bases de données respectent la casse. 
|PersistentStorage  |String |URI de stockage persistant dans lequel la base de données est stockée. (Ce champ est vide pour les bases de données éphémères.) 
|Version  |String |Numéro de version de la base de données. Ce nombre est mis à jour pour chaque opération de modification dans la base de données (par exemple, l’ajout de données et la modification du schéma). 
|IsCurrent  |Boolean |True si la base de données est celle vers laquelle pointe la connexion active. 
|DatabaseAccessMode  |String |Comment le cluster est attaché à la base de données. Par exemple, si la base de données est attachée en mode ReadOnly, le cluster échouera toutes les demandes de modification de la base de données de quelque manière que ce soit. 
|PrettyName |String |Nom convivial de la base de données.
|AuthorizedPrincipals |String | Collection de principaux autorisés de la base de données (sérialisée au format JSON).
|RetentionPolicy |String | Stratégie de rétention de la base de données (sérialisée au format JSON).
|MergePolicy |String | La stratégie de fusion d’étendues de la base de données (sérialisée au format JSON).
|CachingPolicy |String | Stratégie de mise en cache de la base de données (sérialisée au format JSON).
|ShardingPolicy |String | La stratégie partitionnement de la base de données (sérialisée au format JSON).
|StreamingIngestionPolicy |String | La stratégie d’ingestion de diffusion en continu de la base de données (sérialisée au format JSON).
|IngestionBatchingPolicy |String | La stratégie de traitement par lot de l’ingestion de la base de données (sérialisée au format JSON).
|TotalSize |Real | La taille totale des étendues de la base de données.
|DatabaseId |Guid |ID unique de la base de données.

**Sortie de l’option « stratégies »**
 
|Paramètre de sortie |Type |Description 
|---|---|---
|nom_base_de_données  |String |Nom de la base de données. Les noms des bases de données respectent la casse. 
|PersistentStorage  |String |URI de stockage persistant dans lequel la base de données est stockée. (Ce champ est vide pour les bases de données éphémères.) 
|Version  |String |Numéro de version de la base de données. Ce nombre est mis à jour pour chaque opération de modification dans la base de données (par exemple, l’ajout de données et la modification du schéma). 
|IsCurrent  |Boolean |True si la base de données est celle vers laquelle pointe la connexion active. 
|DatabaseAccessMode  |String |Comment le cluster est attaché à la base de données. Par exemple, si la base de données est attachée en mode ReadOnly, le cluster échouera toutes les demandes de modification de la base de données de quelque manière que ce soit. 
|PrettyName |String |Nom convivial de la base de données.
|DatabaseId |Guid |ID unique de la base de données.
|AuthorizedPrincipals |String | Collection de principaux autorisés de la base de données (sérialisée au format JSON).
|RetentionPolicy |String | Stratégie de rétention de la base de données (sérialisée au format JSON).
|MergePolicy |String | La stratégie de fusion d’étendues de la base de données (sérialisée au format JSON).
|CachingPolicy |String | Stratégie de mise en cache de la base de données (sérialisée au format JSON).
|ShardingPolicy |String | La stratégie partitionnement de la base de données (sérialisée au format JSON).
|StreamingIngestionPolicy |String | La stratégie d’ingestion de diffusion en continu de la base de données (sérialisée au format JSON).
|IngestionBatchingPolicy |String | La stratégie de traitement par lot de l’ingestion de la base de données (sérialisée au format JSON).

**Sortie de l’option « datastats »**

|Paramètre de sortie |Type |Description 
|---|---|---
|nom_base_de_données  |String |Nom de la base de données. Les noms des bases de données respectent la casse.
|PersistentStorage  |String |URI de stockage persistant dans lequel la base de données est stockée. (Ce champ est vide pour les bases de données éphémères.) 
|Version  |String |Numéro de version de la base de données. Ce nombre est mis à jour pour chaque opération de modification dans la base de données (par exemple, l’ajout de données et la modification du schéma). 
|IsCurrent  |Boolean |True si la base de données est celle vers laquelle pointe la connexion active. 
|DatabaseAccessMode  |String |Comment le cluster est attaché à la base de données. Par exemple, si la base de données est attachée en mode ReadOnly, le cluster échouera toutes les demandes de modification de la base de données de quelque manière que ce soit. 
|PrettyName |String |Nom convivial de la base de données.
|DatabaseId |Guid |ID unique de la base de données.
|OriginalSize |Real | La taille totale d’origine des étendues de la base de données.
|ExtentSize |Real | La taille totale des étendues de la base de données (données + index).
|CompressedSize |Real | La taille totale compressée des données étendues de la base de données.
|IndexSize |Real | La taille totale de l’index des étendues de la base de données.
|RowCount |Long | Nombre total de lignes de la base de données.
|HotOriginalSize |Real | Taille d’origine totale des extensions de la base de données.
|HotExtentSize |Real | Taille totale des extensions actives de la base de données (données + index).
|HotCompressedSize |Real | Taille totale compressée des données d’étendues de la base de données.
|HotIndexSize |Real | La taille totale de l’index des étendues de la base de données.
|HotRowCount |Long | Nombre total de lignes par étendues de la base de données.