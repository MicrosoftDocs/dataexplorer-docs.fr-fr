---
title: Gestion de journal - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la gestion du Journal dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: bbe5ab4bda42fdfd9382c7e71da85789c13f6987
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520789"
---
# <a name="journal-management"></a>Gestion des journaux

 `Journal`contient des informations sur les opérations de métadonnées effectuées dans la base de données Kusto.

Les opérations de métadonnées pourraient être le résultat d’une commande de contrôle exécutée par l’utilisateur ou des commandes de contrôle interne exécutées par le système (comme les étendues de chute par rétention).

**Remarques :**

- Les opérations de métadonnées qui `.ingest` `.append`englobent *l’ajout* de nouvelles étendues (comme, et `.move` d’autres) n’auront pas d’événements correspondants montrés dans le `Journal`.
- Les données dans les colonnes de l’ensemble de résultats, ainsi que le format dans lequel il est présenté, ne sont *pas* contractuelles, et donc prendre une dépendance à leur égard *n’est pas* recommandée.

|Événement        |EventTimestamp     |Base de données  |EntityName|Mise à jourEntityName|EntityVersion|EntityContainerName|
|-------------|-------------------|----------|----------|-----------------|-------------|-------------------|
|TABLEAU DE CRÉATION |2017-01-05 14:25:07|InterneDb (interne)|MyTable1 (en)  |MyTable1 (en)         |v7.0         |InterneDb (interne)         |
|RENAME-TABLE |2017-01-13 10:30:01|InterneDb (interne)|MyTable1 (en)  |MyTable2 (en)         |v8.0         |InterneDb (interne)         |  

|OriginalEntityState (en)|Mise à jourEntityState                                              |ChangeCommand (en)                                                                                                          |Principal            |
|-------------------|----------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------|
|.                  |Nom: MyTable1, Attributs: Name'[MyTable1]. [col1]', Type’I32'|.créer la table MyTable1 (col1:int)                                                                                      |imike@fabrikam.com
|.                  |Les propriétés db (trop longtemps pour être affichées ici)               |.créer la base dehttps://imfbkm.blob.core.windows.net/mddonnées TestDB persister (" ' ' 'https://imfbkm.blob.core.windows.net/data' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' '|Application AAD id-76263cdb-abcd-545644e9c404
|Nom: MyTable1, Attributs: Name'[MyTable1]. [col1]', Type’I32'|Nom: MyTable2, Attributs: Name'[MyTable1]. [col1]', Type’I32'|.rename table MyTable1 à MyTable2|rdmik@fabrikam.com


Événement - le nom de l’événement métadonnées.

EventTimestamp - l’ampoule de l’événement.

Base de données - Les métadonnées de cette base de données ont été modifiées à la suite de l’événement.

EntityName - le nom de l’entité, l’opération a été exécutée, avant le changement.

UpdatedEntityName - le nouveau nom de l’entité après le changement.

EntityVersion - la nouvelle version métadonnées (db/cluster) après le changement.

EntityContainerName - le nom du conteneur de l’entité (c.-à-d. : entité-colonne, table de conteneur).

OriginalEntityState - l’état de l’entité (propriétés d’entité) avant le changement.

UpdatedEntityState - le nouvel État après le changement.

ChangeCommand - la commande de contrôle exécutée qui a déclenché le changement de métadonnées.

Principal - le principal (utilisateur/application) qui a exécuté la commande de contrôle.
                    
## <a name="show-journal"></a>.voir journal

La `.show journal` commande renvoie une liste de modifications de métadonnées, sur les bases de données/clusters que l’utilisateur a accès à l’administration.

**autorisations**

Tout le monde (accès par cluster) peut exécuter la commande. 

Les résultats retournés comprendront : 
1. Toutes les entrées de journal de l’utilisateur exécutant la commande. 
2. Toutes les entrées de journaux des bases de données auxquelles l’utilisateur exécutant la commande a accès à l’administration. 
3. Toutes les entrées de journal de cluster si l’utilisateur exécutant la commande est un administrateur de cluster. 

## <a name="show-database-databasename-journal"></a>.afficher la base de données *DatabaseName* journal 

La `.show` `database` commande *DatabaseName renvoie* `journal` la revue pour les modifications spécifiques des métadonnées de base de données.

**autorisations**

Tout le monde (accès par cluster) peut exécuter la commande. Les résultats retournés comprendront : 
1. Toutes les entrées journal de base de données *DatabaseName* si l’utilisateur exécutant la commande est un administrateur de base de données dans *DatabaseName*. 
2. Sinon, toutes les entrées de journal de base de données *DatabaseName* et de l’utilisateur exécutant la commande. 

