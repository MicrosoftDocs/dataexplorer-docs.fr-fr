---
title: .afficher les bases de données des clusters - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .show bases de données cluster dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f354862df1bc9bef352819832125cf6f82ba0ae4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519990"
---
# <a name="show-cluster-databases"></a>.afficher les bases de données cluster

Renvoie un tableau montrant toutes les bases de données attachées au cluster et auxquelles l’utilisateur invoquant la commande a accès. Si des noms de base de données spécifiques sont utilisés, seules ces bases de données seront incluses.

**Syntaxe**

`.show` `cluster` `databases` [`details` | `identity` | `policies` | `datastats`]

`.show``cluster` `,` base de`,` données12 ... `databases` `(` databaseN`)`

**Sortie**
 
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