---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: 40334f81e39317839c05ce09a2e4923be4e0747c
ms.sourcegitcommit: f2f9cc0477938da87e0c2771c99d983ba8158789
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2020
ms.locfileid: "89502656"
---
### <a name="schema-mapping-examples"></a>Exemples de mappage de schéma

**Exemple de mappage de schéma de table**

Si vos données comprennent trois colonnes (`Timespan`, `Metric`et `Value`) et que les propriétés que vous incluez sont `x-opt-enqueued-time` et `x-opt-offset`, créez ou modifiez le schéma de table à l’aide de la commande suivante :

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Exemple de mappage CSV**

Exécutez les commandes suivantes pour ajouter des données au début de l’enregistrement. Notez les valeurs ordinales.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**Exemple de mappage JSON**

Les données sont ajoutées à l’aide du mappage des propriétés système. Exécutez les commandes suivantes :

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```
