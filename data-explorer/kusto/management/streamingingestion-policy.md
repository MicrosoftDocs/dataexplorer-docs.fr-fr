---
title: Gestion des politiques d’ingestion en continu - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la gestion des politiques d’ingestion en continu dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: b0b1a76e52688dcc88ca87023309f9c970b4c702
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519616"
---
# <a name="streaming-ingestion-policy-management"></a>Gestion des politiques d’ingestion en continu

La politique d’ingestion en continu peut être jointe à une base de données ou à un tableau pour permettre l’ingestion en continu vers ces emplacements. La politique définit également les magasins en rangée utilisés pour l’ingestion en continu.

Pour plus d’informations sur l’ingestion en streaming voir [l’ingestion en streaming (aperçu)](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming). Pour en savoir plus sur la politique d’ingestion en continu, consultez la [politique d’ingestion de streaming](streamingingestionpolicy.md).

## <a name="show-policy-streamingingestion"></a>.show politique streamingingestion

La `.show policy streamingingestion` commande affiche la politique d’ingestion en continu de la base de données ou du tableau.

**Syntaxe**

`.show``database` `policy` `streamingingestion` 
 `.show` MyDatabase `table` MyTable `policy``streamingingestion`

**Retourne**

Cette commande renvoie une table avec les colonnes suivantes :

|Colonne    |Type    |Description
|---|---|---
|PolicyName|`string`|Le nom de la politique - StreamingIngestionPolicy
|EntityName|`string`|Base de données ou nom de table
|Stratégie    |`string`|Un objet JSON qui définit la politique d’ingestion en streaming, formaté comme [objet de politique d’ingestion en streaming](#streaming-ingestion-policy-object)

**Exemple**

```kusto
.show database DB1 policy streamingingestion 
.show table T1 policy streamingingestion 
```

|PolicyName|EntityName|Stratégie|Entités enfantin|EntityType|
|---|---|---|---|---|
|StreamingIngestionPolicy|DB1|"NumberOfRowStores": 4

### <a name="streaming-ingestion-policy-object"></a>Objet politique d’ingestion en continu

|Propriété  |Type    |Description                                                       |
|----------|--------|------------------------------------------------------------------|
|NumberOfRowStores (en) |`int`  |Le nombre de magasins en rangée attribués à l’entité|
|SealIntervalLimit|`TimeSpan?`|Limite facultative pour les intervalles entre les opérations de joint sur la table. La plage valide se situe entre 1 et 24 heures. Par défaut : 24 heures.|
|SealThresholdBytes|`int?`|Limite facultative pour la quantité de données à prendre pour une seule opération de joint sur la table. La plage valide pour la valeur se situe entre 10 et 200 MBs. Défaut : 200 MBs.|

## <a name="alter-policy-streamingingestion"></a>.modifier la politique de streamingingestion

La `.alter policy streamingingestion` commande définit la politique de diffusion en continu de la base de données ou de la table.

**Syntaxe**

* `.alter``database` MyDatabase `policy` `streamingingestion` *StreamingIngestionPolicyObject*

* `.alter``database` MyData `policy` `streamingingestion` base`enable`

* `.alter``database` MyData `policy` `streamingingestion` base`disable`

* `.alter``table` MyTable `policy` `streamingingestion` *StreamingIngestionPolicyObject*

* `.alter``table` MyTable `policy` `streamingingestion` (en)`enable`

* `.alter``table` MyTable `policy` `streamingingestion` (en)`disable`

**Remarques**

1. *StreamingIngestionPolicyObject* est un objet JSON qui a défini l’objet de la politique d’ingestion en streaming.

2. `enable`- définir la politique d’ingestion en continu à 4 étages si la politique n’est pas définie ou déjà définie avec 0 rames, sinon la commande ne fera rien.

3. `disable`- définir la politique d’ingestion en continu à être avec 0 rowstores si la politique est déjà définie avec des rowstores positifs, sinon la commande ne fera rien.

**Exemple**

```kusto
.alter database MyDatabase policy streamingingestion '{  "NumberOfRowStores": 4}'

.alter table MyTable policy streamingingestion '{  "NumberOfRowStores": 4}'
```

## <a name="delete-policy-streamingingestion"></a>.supprimer la politique de streamingingestion

La `.delete policy streamingingestion` commande supprime la politique de diffusion de la base de données ou du tableau.

**Syntaxe** 

`.delete``database` MyData `policy` base`streamingingestion`

`.delete``table` MyTable `policy` (en)`streamingingestion`

**Retourne**

La commande supprime l’objet de stratégie de streaming de table ou de base de données, puis renvoie la sortie de la commande correspondante [de streaming de la politique .show.](#show-policy-streamingingestion)

**Exemple**

```kusto
.delete database MyDatabase policy streamingingestion 
```