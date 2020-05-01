---
title: Exporter des données vers une table externe-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’exportation de données vers une table externe dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 56150c480d0d5ecfd4d428e51f7bdb4b68e36b0c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617695"
---
# <a name="export-data-to-an-external-table"></a>Exporter des données vers une table externe

Vous pouvez exporter des données en définissant une [table externe](../externaltables.md) et en y exportant des données.
Les propriétés de la table sont spécifiées lors de la [création de la table externe](../externaltables.md#create-or-alter-external-table). par conséquent, vous n’avez pas besoin d’incorporer les propriétés de la table dans la commande d’exportation. La commande Export fait référence à la table externe par son nom.
L’exportation de données nécessite une [autorisation d’administrateur de base de données](../access-control/role-based-authorization.md).

> [!NOTE] 
> * L’exportation vers une table externe `impersonate` avec la chaîne de connexion n’est pas prise en charge actuellement.

**Syntaxe :**

`.export`[`async`] `to` `table` *ExternalTableName* <br>
[`with` `(` *PropertyName* PropertyName `=` *PropertyValue*PropertyValue`,`... `)`] <| *Requête*

**Output:**

|Paramètre de sortie |Type |Description
|---|---|---
|ExternalTableName  |String |Nom de la table externe.
|Path|String|Chemin de sortie.
|NumRecords|String| Nombre d’enregistrements exportés dans le chemin d’accès.

**Remarques :**
* Le schéma de sortie de la requête d’exportation doit correspondre au schéma de la table externe, y compris toutes les colonnes définies par les partitions. Par exemple, si la table est partitionnée par *DateTime*, le schéma de sortie de la requête doit inclure une colonne timestamp qui correspond à la *TimestampColumnName* définie dans la définition de partitionnement de table externe.

* Il n’est pas possible de remplacer les propriétés de la table externe à l’aide de la commande exporter.
 Par exemple, vous ne pouvez pas exporter des données au format parquet vers une table externe dont le format de données est CSV.

* Les propriétés suivantes sont prises en charge dans le cadre de la commande d’exportation. Pour plus d’informations, consultez la section [Exporter vers le stockage](export-data-to-storage.md) : 
   * `sizeLimit`, `parquetRowGroupSize`, `distributed`.

* Si la table externe est partitionnée, les artefacts exportés sont écrits dans leurs répertoires respectifs, en fonction des définitions de partition, comme indiqué dans l' [exemple](#partitioned-external-table-example). 

* Le nombre de fichiers écrits par partition dépend des éléments suivants :
   * Si la table externe comprend uniquement des partitions DateTime, ou aucune partition, le nombre de fichiers écrits (pour chaque partition, le cas échéant) doit être le nombre de nœuds du cluster (ou plus, si `sizeLimit` est atteint). Cela est dû au fait que l’opération d’exportation est distribuée, de sorte que tous les nœuds du cluster sont exportés simultanément. 
   Pour désactiver la distribution, afin qu’un seul nœud effectue les écritures, affectez `distributed` la valeur false. Cela créera moins de fichiers, mais réduira les performances d’exportation.

   * Si la table externe comprend une partition par une colonne de type chaîne, le nombre de fichiers exportés doit être un fichier unique par partition ( `sizeLimit` ou plus, si est atteint). Tous les nœuds participent toujours à l’exportation (l’opération est distribuée), mais chaque partition est assignée à un nœud spécifique. Si `distributed` vous affectez la valeur false dans ce cas, vous n’aurez qu’un seul nœud à effectuer l’exportation, mais le comportement restera le même (un fichier unique écrit par partition).

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

Si la commande est exécutée de façon asynchrone (à `async` l’aide du mot clé), la sortie est disponible à l’aide de la commande [afficher les détails](../operations.md#show-operation-details) de l’opération.
