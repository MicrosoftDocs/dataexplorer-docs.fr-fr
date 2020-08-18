---
title: Fichier include
description: Fichier include
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 08/12/2020
ms.author: orspodek
ms.reviewer: tzgitlin
ms.custom: include file
ms.openlocfilehash: f63288ad25363f1d32184e45efb21b69a894da0c
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201729"
---
|**Propriété** | **Description de la propriété**|
|---|---|
| `rawSizeBytes` | Taille des données brutes (non compressées). Pour Avro/ORC/Parquet, il s’agit de la taille avant l’application de la compression spécifique au format. Indiquez la taille des données d’origine en affectant à cette propriété la taille des données non compressées en octets.|
| `kustoTable` |  Nom de la table cible existante. Remplace le paramètre `Table` défini dans le panneau `Data Connection`. |
| `kustoDataFormat` |  Format de données. Remplace le paramètre `Data format` défini dans le panneau `Data Connection`. |
| `kustoIngestionMappingReference` |  Nom du mappage d’ingestion existant à utiliser. Remplace le paramètre `Column mapping` défini dans le panneau `Data Connection`.|
| `kustoIgnoreFirstRecord` | Si la valeur définie est `true`, Kusto ignore la première ligne de l’objet blob. Utilisez dans les données au format tabulaire (CSV, TSV ou similaire) pour ignorer les en-têtes. |
| `kustoExtentTags` | Chaîne représentant les [balises](../kusto/management/extents-overview.md#extent-tagging) qui seront attachées à l’étendue résultante. |
| `kustoCreationTime` |  Remplace [$IngestionTime](../kusto/query/ingestiontimefunction.md?pivots=azuredataexplorer) pour l’objet blob, au format d’une chaîne ISO 8601. À utiliser pour le renvoi. |
