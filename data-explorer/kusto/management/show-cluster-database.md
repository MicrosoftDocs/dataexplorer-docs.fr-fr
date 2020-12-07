---
title: . afficher les bases de données de cluster-Azure Explorateur de données
description: Cet article décrit. afficher les bases de données de cluster dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c6d25380a44a2195f407c52a2224ee28fad9f8bb
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320500"
---
# <a name="show-cluster-databases"></a>.show cluster databases

Retourne une table qui affiche toutes les bases de données attachées au cluster et auxquelles l’utilisateur qui appelle la commande a accès. Si des noms de base de données spécifiques sont utilisés, seules ces bases de données sont incluses.

**Syntaxe**

`.show` `cluster` `databases` [`details` | `identity` | `policies` | `datastats`]

`.show``cluster` `databases` `(` Database1 `,` Database2 `,` ... base de données`)`

**Sortie**
 
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