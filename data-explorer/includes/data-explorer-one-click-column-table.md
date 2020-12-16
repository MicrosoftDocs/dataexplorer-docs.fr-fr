---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 06/11/2020
ms.author: orspodek
ms.openlocfilehash: 3d366a824ecfdf89c56b1049b70df664573cd7e0
ms.sourcegitcommit: 4d5628b52b84f7564ea893f621bdf1a45113c137
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "97371653"
---
Les modifications que vous pouvez apporter dans une table dépendent des paramètres suivants :
* Si le type de la **table** est nouveau ou existant
* Si le type du **mappage** est nouveau ou existant

Type de la table | Type de mappage | Ajustements disponibles|
|---|---|---|
|Nouvelle table   | Nouveau mappage |Modifier le type de données, Renommer la colonne, Nouvelle colonne, Supprimer la colonne, Mettre à jour la colonne, Trier par ordre croissant, Trier par ordre décroissant  |
|Table existante  | Nouveau mappage | Nouvelle colonne (vous pourrez ensuite modifier le type de données, la renommer ou la mettre à jour) <br> Mettre à jour la colonne, Tri croissant, Tri décroissant  |
| | Mappage existant | Tri croissant, Tri décroissant

> [!NOTE]
> Lorsque vous ajoutez une nouvelle colonne ou mettez à jour une colonne, vous pouvez modifier les transformations de mappage. Pour plus d’informations, consultez [Transformations de mappage](../ingest-data-one-click.md#mapping-transformations).