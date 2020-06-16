---
title: Exporter des données vers une table externe-Azure Explorateur de données
description: Cet article décrit l’exportation de données vers une table externe dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: ebead1ee5dbe458fc9c517d6bf20fc99ca27dd66
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780675"
---
# <a name="export-data-to-an-external-table"></a>Exporter des données vers une table externe

Vous pouvez exporter des données en définissant une [table externe](../externaltables.md) et en y exportant des données.
Les propriétés de la table sont spécifiées lors [de la création de la table externe](../external-tables-azurestorage-azuredatalake.md#create-or-alter-external-table). Vous n’avez pas besoin d’incorporer les propriétés de la table dans la commande d’exportation. La commande Export fait référence à la table externe par son nom. L’exportation de données nécessite une [autorisation d’administrateur de base de données](../access-control/role-based-authorization.md).

**Syntaxe :**

`.export`[ `async` ] `to` `table` *ExternalTableName* <br>
[ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] <| *Requête*

**Output:**

|Paramètre de sortie |Type |Description
|---|---|---
|ExternalTableName  |String |Nom de la table externe.
|Path|String|Chemin de sortie.
|NumRecords|String| Nombre d’enregistrements exportés dans le chemin d’accès.

**Remarques :**
* Le schéma de sortie de la requête d’exportation doit correspondre au schéma de la table externe, y compris toutes les colonnes définies par les partitions. Par exemple, si la table est partitionnée par *DateTime*, le schéma de sortie de la requête doit avoir une colonne timestamp correspondant à *TimestampColumnName*. Ce nom de colonne est défini dans la définition de partitionnement de table externe.

* Il n’est pas possible de remplacer les propriétés de la table externe à l’aide de la commande exporter.
 Par exemple, vous ne pouvez pas exporter des données au format parquet vers une table externe dont le format de données est CSV.

* Les propriétés suivantes sont prises en charge dans le cadre de la commande d’exportation. Pour plus d’informations, consultez la section [Exporter vers le stockage](export-data-to-storage.md) : 
   * `sizeLimit`, `parquetRowGroupSize`, `distributed`.

* Si la table externe est partitionnée, les artefacts exportés sont écrits dans leurs répertoires respectifs conformément aux définitions de partition, comme indiqué dans l' [exemple de table externe partitionnée](#partitioned-external-table-example). 
  * Si une valeur de partition est null/vide ou qu’il s’agit d’une valeur de répertoire non valide, selon les définitions du stockage cible, la valeur de partition est remplacée par la valeur par défaut `__DEFAULT_PARTITION__` . 

* Le nombre de fichiers écrits par partition dépend des paramètres suivants :
   * Si la table externe comprend uniquement des partitions DateTime, ou aucune partition, le nombre de fichiers écrits (pour chaque partition, le cas échéant) doit être similaire au nombre de nœuds dans le cluster (ou plus, si `sizeLimit` est atteint). Lorsque l’opération d’exportation est distribuée, tous les nœuds du cluster sont exportés simultanément. Pour désactiver la distribution, afin qu’un seul nœud effectue les écritures, affectez la valeur `distributed` false à. Ce processus va créer moins de fichiers, mais réduira les performances d’exportation.

   * Si la table externe comprend une partition par une colonne de type chaîne, le nombre de fichiers exportés doit être un fichier unique par partition (ou plus, si `sizeLimit` est atteint). Tous les nœuds participent toujours à l’exportation (l’opération est distribuée), mais chaque partition est assignée à un nœud spécifique. Si `distributed` vous définissez la valeur sur false, un seul nœud peut effectuer l’exportation, mais le comportement reste le même (un fichier unique écrit par partition).

## <a name="examples"></a>Exemples

### <a name="non-partitioned-external-table-example"></a>Exemple de table externe non partitionnée

ExternalBlob est une table externe non partitionnée. 

```kusto
.export to table ExternalBlob <| T
```

|ExternalTableName|Path|NumRecords|
|---|---|---|
|ExternalBlob|http://storage1.blob.core.windows.net/externaltable1cont1/1_58017c550b384c0db0fea61a8661333e.csv|10|

### <a name="partitioned-external-table-example"></a>Exemple de table externe partitionnée

PartitionedExternalBlob est une table externe, définie comme suit : 

```kusto
.create external table PartitionedExternalBlob (Timestamp:datetime, CustomerName:string) 
kind=blob
partition by 
   "CustomerName="CustomerName,
   bin(Timestamp, 1d)
dataformat=csv
( 
   h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

```kusto
.export to table PartitionedExternalBlob <| T
```

|ExternalTableName|Path|NumRecords|
|---|---|---|
|ExternalBlob|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer1/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_1_58017c550b384c0db0fea61a8661333e.csv|10|
|ExternalBlob|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer2/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_2_b785beec2c004d93b7cd531208424dc9.csv|10|

Si la commande est exécutée de façon asynchrone (à l’aide du `async` mot clé), la sortie est disponible à l’aide de la commande [afficher les détails](../operations.md#show-operation-details) de l’opération.
