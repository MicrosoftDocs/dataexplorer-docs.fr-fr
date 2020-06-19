---
title: opérateur d’analyse WHERE-Azure Explorateur de données
description: Cet article décrit l’opérateur parse-Where dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/12/2020
ms.openlocfilehash: 48231d24ca1e49938629dd9912804c5858d11ae1
ms.sourcegitcommit: f9d3f54114fb8fab5c487b6aea9230260b85c41d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "85071885"
---
# <a name="parse-where-operator"></a>Opérateur parse-where

Évalue une expression de chaîne et analyse sa valeur dans une ou plusieurs colonnes calculées. Le résultat est uniquement les chaînes analysées avec succès. 

Consultez l' [opérateur parse](parseoperator.md), qui produit des valeurs NULL pour les chaînes analysées sans succès.

```kusto
T | parse-where Text with "ActivityName=" name ", ActivityType=" type
```

**Syntaxe**

*T* `| parse-where` [ `kind=regex` [ `flags=regex_flags` ] | `simple` ] *expression* `with` `*` (*StringConstant* *ColumnName* [ `:` *ColumnType*]) `*` ...

**Arguments**

* *T*: table d’entrée.

* *genre*: 

    * *simple* (par défaut) : StringConstant est une valeur de chaîne normale et la correspondance est stricte. Tous les délimiteurs de chaîne doivent apparaître dans la chaîne analysée, et toutes les colonnes étendues doivent correspondre aux types requis.
        
    * *Regex*: StringConstant peut être une expression régulière, et la correspondance est stricte. Tous les délimiteurs de chaîne doivent apparaître dans la chaîne analysée, et toutes les colonnes étendues doivent correspondre aux types requis. Les délimiteurs de chaîne peuvent être une expression régulière pour ce mode.
    
    * *Flags*: indicateurs à utiliser en mode Regex : `U` (non gourmand), `m` (mode multiligne), (mettre en `s` correspondance une nouvelle ligne `\n` ), `i` (non-respect de la casse), d’autres indicateurs se trouvent dans les [indicateurs RE2](re2.md).
        
* *Expression*: expression qui prend la valeur d’une chaîne.

* *ColumnName :* Nom d’une colonne qui est assignée à une valeur extraite de l’expression de chaîne. 
  
* *ColumnType :* doit être un type scalaire facultatif qui indique le type vers lequel convertir la valeur. La valeur par défaut est type de chaîne.

**Retourne**

La table d’entrée, qui est étendue en fonction de la liste des colonnes fournies à l’opérateur.

> [!Note] 
> Seules les chaînes analysées avec succès seront dans la sortie. Les chaînes qui ne correspondent pas au modèle seront filtrées.

**Conseils**

* `parse-where`analyse les chaînes de la même façon que l' [analyse](parseoperator.md)et filtre les chaînes qui n’ont pas été analysées avec succès.

* Utilisez [Project](projectoperator.md) si vous souhaitez également supprimer ou renommer certaines colonnes.

* Utilisez * dans le modèle pour ignorer les valeurs indésirables. Cette valeur ne peut pas être utilisée après une colonne de chaîne.

* Le modèle d’analyse peut commencer par *ColumnName*, en plus de *StringConstant*. 

* Si l' *expression* analysée n’est pas de type chaîne, elle sera convertie en type chaîne.

* Si le mode Regex est utilisé, vous pouvez ajouter des indicateurs Regex pour contrôler l’intégralité de l’expression régulière utilisée dans parse.

* En mode Regex, Parse convertit le modèle en Regex et utilise la [syntaxe RE2](re2.md) pour effectuer la correspondance à l’aide de groupes capturés numérotés qui sont gérés en interne.
  
  Par exemple, cette instruction d’analyse :
  
    ```kusto
    parse-where kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    L’expression régulière qui sera générée en interne par l’analyse est `.*?<regex1>(.*?)<regex2>(\-\d+)` .
        
    - `*`a été traduit `.*?` .
        
    - `string`a été traduit `.*?` .
        
    - `long`a été traduit `\-\d+` .

## <a name="examples"></a>Exemples

L' `parse-where` opérateur fournit une méthode rationalisée à `extend` une table en utilisant plusieurs `extract` applications sur la même `string` expression. Cela s’avère particulièrement utile lorsque la table comporte une `string` colonne qui contient plusieurs valeurs que vous souhaitez décomposer en colonnes individuelles. Par exemple, vous pouvez diviser une colonne qui a été générée par une instruction de trace de développement (« `printf` »/«» `Console.WriteLine` ).

### <a name="using-parse"></a>Utilisation de `parse`

Dans l’exemple ci-dessous, la colonne `EventText` de la table `Traces` contient des chaînes au format `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` . L’opération ci-dessous étend la table avec six colonnes :,,,,, `resourceName` `totalSlices` `sliceNumber` `lockTime ` `releaseTime` `previouLockTime` , `Month` et `Day` . 

Certaines chaînes n’ont pas de correspondance complète.

À l’aide `parse` de, les colonnes calculées auront des valeurs NULL.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=invalid_number, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=invalid_datetime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=invalid_number, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|---|
|||||||
|||||||
|||||||
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

### <a name="using-parse-where"></a>Utilisation de `parse-where` 

L’utilisation de « parse-WHERE » filtre les chaînes analysées sans succès à partir du résultat.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=invalid_number, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=invalid_datetime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=invalid_number, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse-where EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


### <a name="regex-mode-using-regex-flags"></a>Mode Regex à l’aide d’indicateurs Regex

Pour obtenir les informations resourceName et totalSlices, utilisez la requête suivante :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=11, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=44, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse-where kind = regex EventText with * "RESOURCENAME=" resourceName "," * "totalSlices=" totalSlices:long "," *
| project resourceName, totalSlices
```

### <a name="parse-where-with-case-insensitive-regex-flag"></a>`parse-where`avec l’indicateur Regex ne respectant pas la casse

Dans la requête ci-dessus, le mode par défaut était sensible à la casse, de sorte que les chaînes ont été analysées avec succès. Aucun résultat n’a été obtenu.

Pour obtenir le résultat requis, exécutez `parse-where` avec un indicateur Regex qui ne respecte pas la casse ( `i` ).

Seules trois chaînes seront analysées avec succès. le résultat est donc trois enregistrements (certains totalSlices contiennent des entiers non valides).

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=11, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=44, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse-where kind = regex flags=i EventText with * "RESOURCENAME=" resourceName "," * "totalSlices=" totalSlices:long "," *
| project resourceName, totalSlices
```

|resourceName|totalSlices|
|---|---|
|PipelineScheduler|27|
|PipelineScheduler|27|
|PipelineScheduler|27|
