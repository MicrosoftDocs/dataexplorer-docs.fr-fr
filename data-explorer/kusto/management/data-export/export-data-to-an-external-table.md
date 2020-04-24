---
title: Les données d’exportation vers une table externe - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les données d’exportation vers un tableau externe d’Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: e26d1ae163b69f3a04c52538c245ea446dc11199
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521639"
---
# <a name="export-data-to-an-external-table"></a>Données d’exportation vers un tableau externe

Vous pouvez exporter des données en définissant une [table externe](../externaltables.md) et en y exportant des données.
Les propriétés de table sont spécifiées lors [de la création de la table externe,](../externaltables.md#create-or-alter-external-table)par conséquent, vous n’avez pas besoin d’intégrer les propriétés de la table dans le commandement d’exportation. Le commandement d’exportation fait référence à la table externe par son nom.
Les données d’exportation nécessitent [l’autorisation de l’administration de base de données](../access-control/role-based-authorization.md).

> [!NOTE] 
> * L’exportation vers une `impersonate` table externe avec chaîne de connexion n’est pas actuellement prise en charge.

**Syntaxe :**

`.export`[`async` `to` `table` ] *ExternalTableName* <br>
[`with` `(` *PropertyName* `=` *PropertyValue*`,`... `)`] <*Requête*

**Output:**

|Paramètre de sortie |Type |Description
|---|---|---
|ExternalTableName (en)  |String |Le nom de la table externe.
|Path|String|Chemin de sortie.
|NumRecords|String| Nombre de documents exportés sur le chemin.

**Remarques :**
* Le schéma de sortie de requête à l’exportation doit correspondre au schéma de la table externe, y compris toutes les colonnes définies par les partitions. Par exemple, si le tableau est divisé par *DateTime*, le schéma de sortie de requête doit inclure une colonne Timestamp qui correspond au *TimestampColumnName* défini dans la définition de partition de table externe.

* Il n’est pas possible de remplacer les propriétés de table externes à l’aide de la commande d’exportation.
 Par exemple, vous ne pouvez pas exporter de données en format Parquet vers une table externe dont le format de données est CSV.

* Les propriétés suivantes sont prises en charge dans le cadre du commandement d’exportation. Voir la section [exportation vers le stockage](export-data-to-storage.md) pour plus de détails : 
   * `sizeLimit`, `parquetRowGroupSize`, `distributed`.

* Si la table externe est cloisonnée, les artefacts exportés seront écrits à leurs répertoires respectifs, selon les définitions de partition comme on le voit dans [l’exemple](#partitioned-external-table-example). 

* Le nombre de fichiers écrits par partition dépend des éléments suivants :
   * Si le tableau externe comprend uniquement les partitions de date, ou pas de partitions du tout - le nombre de fichiers écrits `sizeLimit` (pour chaque partition, s’il existe) doit être autour du nombre de nœuds dans le cluster (ou plus, si elle est atteinte). C’est parce que l’opération d’exportation est distribuée, de sorte que tous les nœuds dans l’exportation de cluster en même temps. 
   Pour désactiver la distribution, de sorte que seul un `distributed` nœud effectue les écrits, mis à faux. Cela créera moins de fichiers, mais diminuera les performances à l’exportation.

   * Si le tableau externe comprend une partition par une colonne de chaîne, le nombre `sizeLimit` de fichiers exportés doit être un fichier unique par partition (ou plus, si elle est atteinte). Tous les nœuds participent toujours à l’exportation (l’opération est distribuée), mais chaque partition est attribuée à un nœud spécifique. Le `distributed` fait de se tromper dans ce cas, ne fera qu’exécuter l’exportation d’un seul nœud, mais le comportement restera le même (un seul fichier écrit par partition).

## <a name="examples"></a>Exemples

### <a name="non-partitioned-external-table-example"></a>Exemple de table externe non divisé

ExternalBlob est une table externe non cloisonnée. 
```kusto
.export to table ExternalBlob <| T
```

|ExternalTableName (en)|Path|NumRecords|
|---|---|---|
|ExternalBlob (en)|http://storage1.blob.core.windows.net/externaltable1cont1/1_58017c550b384c0db0fea61a8661333e.csv|10|

### <a name="partitioned-external-table-example"></a>Exemple de table externe divisé

PartitionedExternalBlob est une table externe, définie comme suit : 

```
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

```
.export to table PartitionedExternalBlob <| T
```

|ExternalTableName (en)|Path|NumRecords|
|---|---|---|
|ExternalBlob (en)|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer1/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_1_58017c550b384c0db0fea61a8661333e.csv|10|
|ExternalBlob (en)|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer2/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_2_b785beec2c004d93b7cd531208424dc9.csv|10|

Si la commande est exécutée asynchrone `async` (en utilisant le mot clé), la sortie est disponible à l’aide de la commande [des détails de l’opération d’exposition.](../operations.md#show-operation-details)