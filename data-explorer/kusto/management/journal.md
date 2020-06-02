---
title: Gestion des journaux-Azure Explorateur de données
description: Cet article décrit la gestion des journaux dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: 64b0f8179a328ce811747b05a90516fd8b6029be
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294404"
---
# <a name="journal-management"></a>Gestion des journaux

 `Journal`contient des informations sur les opérations de métadonnées effectuées sur la base de données Azure Explorateur de données.

Les opérations de métadonnées peuvent résulter d’une commande de contrôle qu’un utilisateur a exécutée, ou de commandes de contrôle internes exécutées par le système, telles que les étendues de suppression par rétention.

> [!NOTE]
> * Les opérations de métadonnées qui englobent l' *Ajout* de nouvelles étendues, telles que `.ingest` , `.append` `.move` et d’autres, n’auront pas d’événements correspondants affichés dans le `Journal` .
> * Les données dans les colonnes de l’ensemble de résultats, ainsi que le format dans lequel elles sont présentées, ne sont pas contractuels. 
  Il n’est pas recommandé d’établir une dépendance sur ces dernières.

|Événement        |EventTimestamp     |Base de données  |EntityName|UpdatedEntityName|EntityVersion|EntityContainerName|
|-------------|-------------------|----------|----------|-----------------|-------------|-------------------|
|CRÉER UNE TABLE |2017-01-05 14:25:07|InternalDb|MyTable1  |MyTable1         |7.0         |InternalDb         |
|RENAME-TABLE |2017-01-13 10:30:01|InternalDb|MyTable1  |MyTable2         |v 8.0         |InternalDb         |  

|OriginalEntityState|UpdatedEntityState                                              |ChangeCommand                                                                                                          |Principal            |
|-------------------|----------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------|
|.                  |Nom : MyTable1, attributs : Name = ' [MyTable1]. [col1] ', type = 'i32 '|. Create table MyTable1 (col1 : int)                                                                                      |imike@fabrikam.com
|.                  |Propriétés de la base de données (trop longue pour être affichée ici)         |. Create Database TestDB Persist (@ " https://imfbkm.blob.core.windows.net/md ", @ " https://imfbkm.blob.core.windows.net/data ")|ID d’application Azure AD = 76263cdb-ABCD-545644e9c404
|Nom : MyTable1, attributs : Name = ' [MyTable1]. [col1] ', type = 'i32 '|Nom : MyTable2, attributs : Name = ' [MyTable1]. [col1] ', type = 'i32 '|. Renommez la table MyTable1 en MyTable2|rdmik@fabrikam.com

|Élément                 |Description                                                              |                                
|---------------------|-------------------------------------------------------------------------|
|Événement                |Nom de l’événement de métadonnées                                                  |
|EventTimestamp       |L’horodateur de l’événement                                                      |                        
|Base de données             |Les métadonnées de cette base de données ont été modifiées après l’événement                |
|EntityName           |Nom de l’entité sur laquelle l’opération a été exécutée avant la modification    |
|UpdatedEntityName    |Nouveau nom d’entité après la modification                                     |
|EntityVersion        |La nouvelle version des métadonnées (DB/cluster) qui suit la modification               |
|EntityContainerName  |Nom du conteneur d’entités (Entity = colonne, Container = table)               |
|OriginalEntityState  |État de l’entité (propriétés de l’entité) avant la modification            |
|UpdatedEntityState   |Nouvel état après la modification                                           |
|ChangeCommand        |Commande de contrôle exécutée qui a déclenché la modification des métadonnées          |
|Principal            |Principal (utilisateur/application) qui a exécuté la commande de contrôle               |
    
## <a name="show-journal"></a>. afficher le journal

La `.show journal` commande retourne une liste de modifications de métadonnées sur les bases de données ou le cluster auquel l’utilisateur a accès administrateur.

**autorisations**

Tout le monde (accès au cluster) peut exécuter la commande. 

Les résultats retournés sont les suivants : 
- Toutes les entrées de journal de l’utilisateur qui exécute la commande. 
- Toutes les entrées de journal des bases de données auxquelles l’utilisateur qui exécute la commande ont un accès administrateur à. 
- Toutes les entrées de journal de cluster si l’utilisateur qui exécute la commande est un administrateur de cluster. 

## <a name="show-database-databasename-journal"></a>. afficher le journal de base de données *DatabaseName* 

La `.show` `database` commande *DatabaseName* `journal` retourne le journal des modifications de métadonnées de base de données spécifiques.

**autorisations**

Tout le monde (accès au cluster) peut exécuter la commande. Les résultats retournés sont les suivants : 
- Toutes les entrées de journal de la base de données *DatabaseName* si l’utilisateur qui exécute la commande est un administrateur de base de données dans *DatabaseName*. 
- Dans le cas contraire, toutes les entrées de journal de la base de données `DatabaseName` et de l’utilisateur exécutent la commande. 
