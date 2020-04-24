---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493405"
---
### <a name="event-system-properties-mapping"></a>Mappage des propriétés du système d’événements

> [!Note]
> * Les propriétés système sont prises en charge pour les événements à enregistrement unique.
> * Pour un mappage `csv`, des propriétés sont ajoutées au début de l’enregistrement. Pour un mappage `json`, des propriétés sont ajoutées en fonction du nom qui s’affiche dans la liste déroulante.

Si vous avez sélectionné **Propriétés du système d’événements** dans la section **Source de données** de la table, vous devez inclure les propriétés suivantes dans le schéma et le mappage de table.

**Exemple schéma de table**

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

Des données sont ajoutées en utilisant les noms de propriétés système tels qu’ils apparaissent dans la liste **Propriétés système d’événement** du panneau **Connexion de données**. Exécutez ces commandes :

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
