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
ms.openlocfilehash: 19dc7db9e344a516b5c92917dccbf8362b1ca858
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102872"
---
# <a name="create-merge-table"></a>.create-merge table

Crée une table ou étend une table existante. 

La commande doit s’exécuter dans le contexte d’une base de données spécifique. 

Nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

## <a name="syntax"></a>Syntaxe

`.create-merge``table` *TableName* ([ColumnName : ColumnType],...)  [ `with` `(` [ `docstring` `=` *Documentation*] [ `,` `folder` `=` *NomDossier*] `)` ]

Si la table n’existe pas, fonctionne exactement comme une commande « . Create table ».

Si la table T existe et que vous envoyez une commande « . Create-Merge table T ( <columns specification> ) », procédez comme suit :

* Toute colonne dans <columns specification> qui n’existait pas précédemment dans t sera ajoutée à la fin du schéma de t.
* Toute colonne dans T qui ne se trouve pas dans <columns specification> ne sera pas supprimée de t.
* Toute colonne de <columns specification> qui existe dans T, mais avec un type de données différent, entraîne l’échec de la commande.

## <a name="see-also"></a>Voir aussi

* [.create-merge tables](create-merge-tables-command.md)
* [. Create table](create-table-command.md)
