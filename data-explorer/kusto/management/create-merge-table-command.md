---
title: . Create-fusionner une table-Azure Explorateur de données
description: Cet article décrit la création d’une table de fusion dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/05/2020
ms.openlocfilehash: afe5011717fd77d654eaf6c2b70e9ffbdea87128
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967623"
---
# <a name="create-merge-table"></a>. Create-fusionner la table

Crée une table ou étend une table existante. 

La commande doit s’exécuter dans le contexte d’une base de données spécifique. 

Nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe**

`.create-merge``table` *TableName* ([ColumnName : ColumnType],...)  [ `with` `(` [ `docstring` `=` *Documentation*] [ `,` `folder` `=` *NomDossier*] `)` ]

Si la table n’existe pas, fonctionne exactement comme une commande « . Create table ».

Si la table T existe et que vous envoyez une commande « . Create-Merge table T ( <columns specification> ) », procédez comme suit :

* Toute colonne dans <columns specification> qui n’existait pas précédemment dans t sera ajoutée à la fin du schéma de t.
* Toute colonne dans T qui ne se trouve pas dans <columns specification> ne sera pas supprimée de t.
* Toute colonne de <columns specification> qui existe dans T, mais avec un type de données différent, entraîne l’échec de la commande.

**Voir aussi**

* [.create-merge tables](create-merge-tables-command.md)
* [.create table](create-table-command.md)
