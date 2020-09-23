---
title: ALTER MATERIALIZED VIEW-Azure Explorateur de données
description: Cet article explique comment modifier les vues matérialisées dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: ac9fb575f46bb60e313da4fa2b3c023ac826daec
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057169"
---
# <a name="alter-materialized-view"></a>. ALTER MATERIALIZED-VIEW

La modification de la [vue matérialisée](materialized-view-overview.md) peut être utilisée pour modifier la requête d’une vue matérialisée, tout en conservant les données existantes dans la vue.

La modification de la vue matérialisée requiert des autorisations d' [administrateur de base de données](../access-control/role-based-authorization.md) ou un administrateur de la vue matérialisée. Pour plus d’informations, consultez [gestion des rôles de sécurité](../security-roles.md).

> [!WARNING]
> Soyez très prudent lors de la modification d’une vue matérialisée. Une utilisation incorrecte peut entraîner une perte de données.

## <a name="syntax"></a>Syntaxe

`.alter` `materialized-view`  
[ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ]  
*ViewName* `on table` *SourceTableName*  
`{`  
    &nbsp;&nbsp;&nbsp;&nbsp;*Demande*  
`}`

## <a name="arguments"></a>Arguments

|Argument|Type|Description
|----------------|-------|---|
|NomVue|Chaîne|Nom de la vue matérialisée.|
|SourceTableName|Chaîne|Nom de la table source sur laquelle la vue est définie.|
|Requête|Chaîne|Requête de vue matérialisée.|

## <a name="properties"></a>Propriétés

`dimensionTables`Est la seule propriété prise en charge dans la commande matérialisée-View ALTER. Cette propriété doit être utilisée au cas où la requête fait référence à des tables de dimension. Pour plus d’informations, consultez la commande [. Créez une vue matérialisée](materialized-view-create.md) .

## <a name="use-cases"></a>Cas d'utilisation

* Ajoutez des agrégations à la vue, par exemple ajouter une `avg` agrégation à `T | summarize count(), min(Value) by Id` , en modifiant afficher la requête en `T | summarize count(), min(Value), avg(Value) by Id` .
* Modifiez les opérateurs autres que l’opérateur de synthèse. Par exemple, filtrez certains enregistrements en modifiant  `T | summarize arg_max(Timestamp, *) by User` sur `T | where User != 'someone' | summarize arg_max(Timestamp, *) by User` .
* ALTER sans modification de la requête en raison d’une modification dans la table source. Prenons l’exemple d’une vue de `T | summarize arg_max(Timestamp, *) by Id` , qui n’est pas définie sur `autoUpdateSchema` (voir. créer une commande de [vue matérialisée](materialized-view-create.md) ). Si une colonne est ajoutée ou supprimée de la table source de la vue, la vue est automatiquement désactivée. Exécutez la commande ALTER, avec la même requête, pour modifier le schéma de la vue matérialisée afin de l’aligner avec le nouveau schéma de la table. La vue doit toujours être activée explicitement après la modification, à l’aide de la commande [activer la vue matérialisée](materialized-view-enable-disable.md) .

## <a name="alter-materialized-view-limitations"></a>Modifier les limitations de vue matérialisée

* **Modifications non prises en charge :**
    * La modification du type de colonne n’est pas prise en charge.
    * Le changement de nom des colonnes n’est pas pris en charge. Par exemple, la modification d’une vue de `T | summarize count() by Id` pour `T | summarize Count=count() by Id` supprimera la colonne `count_` et créera une nouvelle colonne `Count` , qui ne contient initialement que des valeurs NULL.
    * Les modifications apportées à la vue matérialisée Group by expressions ne sont pas prises en charge.

* **Impact sur les données existantes :**
    * La modification de la vue matérialisée n’a aucun impact sur les données existantes.
    * Les nouvelles colonnes recevront des valeurs NULL pour tous les enregistrements existants jusqu’à ce que les enregistrements ingérés publient la commande ALTER. modifiez les valeurs NULL.
        * Par exemple : si une vue de `T | summarize count() by bin(Timestamp, 1d)` est modifiée en `T | summarize count(), sum(Value) by bin(Timestamp, 1d)` , puis pour un particulier `Timestamp=T` pour lequel des enregistrements ont déjà été traités avant la modification de la vue, la `sum` colonne contient des données partielles. Cette vue comprend uniquement les enregistrements traités après l’exécution de la modification.
    * L’ajout de filtres à la requête ne modifie pas les enregistrements qui ont déjà été matérialisés. Le filtre s’applique uniquement aux enregistrements nouvellement ingérés.
