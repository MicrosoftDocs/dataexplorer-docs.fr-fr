---
title: Propriétés d’ingestion des données pour Azure Data Explorer
description: Découvrez les différentes propriétés d’ingestion des données prises en charge par Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: c66d7e11b3f64633a0dda33f7a3fa2f974536caa
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373724"
---
# <a name="azure-data-explorer-data-ingestion-properties"></a>Propriétés d’ingestion des données Azure Data Explorer 

L’ingestion des données est le processus par lequel les données sont ajoutées à une table et deviennent disponibles pour une requête dans Azure Data Explorer. Vous ajoutez des propriétés à la commande d’ingestion après le mot clé `with`.

## <a name="ingestion-properties"></a>Propriétés d’ingestion

Le tableau suivant liste les propriétés prises en charge par Azure Data Explorer, les décrit et fournit des exemples : 

|Propriété              |Description                                              |Exemple                                             |
|----------------------|---------------------------------------------------------|----------------------------------------------------|
|`ingestionMapping`    |Valeur de chaîne qui indique comment mapper les données du fichier source aux colonnes réelles de la table. Définissez la valeur `format` avec le type de mappage approprié. Consultez [Mappages de données](kusto/management/mappings.md).|`with (format="json", ingestionMapping = "[{\"column\":\"rownumber\", \"Properties\":{\"Path\":\"$.RowNumber\"}}, {\"column\":\"rowguid\", \"Properties\":{\"Path\":\"$.RowGuid\"}}]")`<br>(sont dépréciés : `avroMapping`, `csvMapping`, `jsonMapping`) |
|`ingestionMappingReference`|Valeur de chaîne qui indique comment mapper les données du fichier source aux colonnes réelles de la table en utilisant un objet de stratégie de mappage nommé. Définissez la valeur `format` avec le type de mappage approprié. Consultez [Mappages de données](kusto/management/mappings.md).|`with (format="csv", ingestionMappingReference = "Mapping1")`<br>(sont dépréciés : `avroMappingReference`, `csvMappingReference`, `jsonMappingReference`)|
|`creationTime` |Valeur DateHeure (sous forme de chaîne ISO8601) à utiliser comme heure de création des étendues de données ingérées. Si elle n’est pas spécifiée, la valeur actuelle (`now()`) est utilisée. En cas d’ingestion de données anciennes, vous pouvez remplacer les valeurs par défaut pour que la stratégie de conservation soit appliquée correctement.|`with (creationTime="2017-02-13T11:09:36.7992775Z")`|
|`extend_schema`|Valeur booléenne qui, si elle est spécifiée, indique à la commande d’étendre le schéma de la table (la valeur par défaut est `false`). Cette option s’applique uniquement aux commandes `.append` et `.set-or-append`. Les seules extensions de schéma autorisées sont celles pour lesquelles des colonnes supplémentaires sont ajoutées à la fin de la table.|Si le schéma de la table d’origine est `(a:string, b:int)`, une extension de schéma valide est `(a:string, b:int, c:datetime, d:string)` et non `(a:string, c:datetime)`|
|`folder` |Pour les commandes [ingest-from-query](kusto/management/data-ingestion/ingest-from-query.md), dossier à attribuer à la table. Si la table existe déjà, cette propriété remplace le dossier de la table.|`with (folder="Tables/Temporary")`|
|`format` |Format des données (voir [formats de données pris en charge](ingestion-supported-formats.md)).|`with (format="csv")`|
|`ingestIfNotExists`|Valeur de chaîne qui, si elle est spécifiée, empêche l’ingestion de s’effectuer correctement si la table a déjà des données balisées avec une balise `ingest-by:` de la même valeur. Cela garantit une ingestion des données idempotent. Pour plus d’informations, consultez [Étiquettes ingest-by:](kusto/management/extents-overview.md#ingest-by-extent-tags).|Les propriétés `with (ingestIfNotExists='["Part0001"]', tags='["ingest-by:Part0001"]')` indiquent que si des données existent déjà avec l’étiquette `ingest-by:Part0001`, vous ne devez pas effectuer l’ingestion actuelle. Si elles n’existent pas déjà, l’étiquette doit être définie dans la nouvelle ingestion (au cas où une future ingestion tente d’ingérer une nouvelle fois les mêmes données).|
|`ignoreFirstRecord` |Valeur booléenne qui, si elle a la valeur `true`, indique que l’ingestion doit ignorer le premier enregistrement de chaque fichier. Cette propriété est utile pour les fichiers au format `CSV` (et similaires) si le premier enregistrement dans le fichier représente les noms de colonne. Par défaut, la valeur `false` est supposée.|`with (ignoreFirstRecord=false)`|
|`persistDetails` |Valeur booléenne qui, si elle est spécifiée, indique que la commande doit rendre persistants les résultats détaillés (même en cas de réussite) afin que la commande [.show operation details](kusto/management/operations.md#show-operation-details) puisse les récupérer. La valeur par défaut est `false`.|`with (persistDetails=true)`|
|`policy_ingestiontime`|Valeur booléenne qui, si elle est spécifiée, indique d’activer ou non la [stratégie de durée d’ingestion](kusto/management/ingestiontimepolicy.md) sur une table créée par cette commande. Par défaut, il s’agit de `true`.|`with (policy_ingestiontime=false)`|
|`recreate_schema` |Valeur booléenne qui, si elle est spécifiée, indique si la commande peut recréer ou non le schéma de la table. Cette propriété s’applique uniquement à la commande `.set-or-replace`. Cette propriété est prioritaire sur la propriété `extend_schema` si les deux sont définies.|`with (recreate_schema=true)`|
|`tags`|Liste d’[étiquettes](kusto/management/extents-overview.md#extent-tagging) à associer aux données ingérées, sous forme de chaîne JSON |`with (tags="['Tag1', 'Tag2']")`|
|`validationPolicy`|Chaîne JSON qui indique les validations à exécuter pendant l’ingestion. Consultez [Ingestion des données](kusto/management/data-ingestion/index.md) pour avoir une explication des différentes options.| `with (validationPolicy='{"ValidationOptions":1, "ValidationImplications":1}')` (stratégie par défaut)|
|`zipPattern`|Utilisez cette propriété en cas d’ingestion des données à partir d’un stockage qui a une archive ZIP. Il s’agit d’une valeur de chaîne indiquant l’expression régulière à utiliser pour sélectionner les fichiers de l’archive ZIP à ingérer.  Tous les autres fichiers de l’archive sont ignorés.|`with (zipPattern="*.csv")`|

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l’[ingestion des données](ingest-data-overview.md)
* En savoir plus sur les [formats de données pris en charge](ingestion-supported-formats.md)
