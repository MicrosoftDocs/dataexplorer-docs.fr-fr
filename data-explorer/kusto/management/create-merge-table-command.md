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
ms.openlocfilehash: 554f6ed623b5a3be12a360fab0b1d5aa6eb4c084
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320959"
---
# <a name="create-merge-table"></a>.create-merge table

Crée une table ou étend une table existante. 

La commande doit s’exécuter dans le contexte d’une base de données spécifique. 

Nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

## <a name="syntax"></a>Syntaxe

`.create-merge``table` *TableName* ([ColumnName : ColumnType],...)  [ `with` `(` [ `docstring` `=` *Documentation*] [ `,` `folder` `=` *NomDossier*] `)` ]

Si la table n’existe pas, fonctionne exactement comme une `.create table` commande.

Si la table T existe et que vous envoyez une `.create-merge table T (<columns specification>)` commande, procédez comme suit :

* Toute colonne dans <columns specification> qui n’existait pas précédemment dans t sera ajoutée à la fin du schéma de t.
* Toute colonne dans T qui ne se trouve pas dans <columns specification> ne sera pas supprimée de t.
* Toute colonne de <columns specification> qui existe dans T, mais avec un type de données différent, entraîne l’échec de la commande.

## <a name="see-also"></a>Voir aussi

* [`.create-merge tables`](create-merge-tables-command.md)
* [`.create table`](create-table-command.md)
