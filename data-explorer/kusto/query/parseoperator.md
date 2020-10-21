---
title: opérateur d’analyse-Azure Explorateur de données
description: Cet article décrit l’opérateur d’analyse dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a942015908c9608a76d3c49c411de9d17d6e70f5
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248617"
---
# <a name="parse-operator"></a>opérateur parse

évalue une expression de chaîne et analyse sa valeur dans une ou plusieurs colonnes calculées. Les colonnes calculées auront des valeurs NULL, pour les chaînes analysées sans succès.
Pour plus d’informations, consultez l' [opérateur parse-WHERE](parsewhereoperator.md).

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

## <a name="syntax"></a>Syntaxe

*T* `| parse` [ `kind=regex` [ `flags=regex_flags` ] | `simple` | `relaxed` ] *expression* `with` `*` (*StringConstant* *ColumnName* [ `:` *ColumnType*]) `*` ...

## <a name="arguments"></a>Arguments

* *T*: table d’entrée.
* espèces

    * simple (valeur par défaut) : StringConstant est une valeur de chaîne normale et la correspondance est stricte. Tous les délimiteurs de chaîne doivent apparaître dans la chaîne analysée, et toutes les colonnes étendues doivent correspondre aux types requis.
        
    * Regex : StringConstant peut être une expression régulière et la correspondance est stricte. Tous les délimiteurs de chaînes, qui peuvent être une expression régulière pour ce mode, doivent apparaître dans la chaîne analysée, et toutes les colonnes étendues doivent correspondre aux types requis.
    
    * Flags : indicateurs à utiliser en mode Regex comme `U` (non gourmand), `m` (mode multiligne), (mettre en `s` correspondance une nouvelle ligne `\n` ), `i` (non-respect de la casse) dans les [indicateurs RE2](re2.md).
        
    * souple : StringConstant est une valeur de chaîne normale et la correspondance est assouplie. Tous les délimiteurs de chaîne doivent apparaître dans la chaîne analysée, mais les colonnes étendues peuvent correspondre partiellement aux types requis. Les colonnes étendues qui ne correspondent pas aux types requis obtiendront la valeur null.

* *Expression*: expression qui prend la valeur d’une chaîne.

* *ColumnName :* Nom d’une colonne à laquelle une valeur doit être assignée, extraite de l’expression de chaîne. 
  
* *ColumnType :* Facultatif. Valeur scalaire qui indique le type vers lequel convertir la valeur. La valeur par défaut est le `string` type.

## <a name="returns"></a>Retours

La table d’entrée, étendue selon la liste des colonnes fournies à l’opérateur.

**Conseils**

* Utilisez [`project`](projectoperator.md) si vous souhaitez également supprimer ou renommer certaines colonnes.

* Utilisez * dans le modèle pour ignorer les valeurs indésirables. 

    > [!NOTE] 
    > Le `*` ne peut pas être utilisé après une `string` colonne de type.

* Le modèle d’analyse peut commencer par *ColumnName* et non uniquement par *StringConstant*.

* Si l' *expression* analysée n’est pas de type `string` , elle sera convertie en type `string` .

* Si le mode Regex est utilisé, il est possible d’ajouter des indicateurs Regex pour contrôler l’intégralité de l’expression régulière utilisée dans l’analyse.

* En mode Regex, parse traduira le modèle en une expression régulière. Utilisez la [syntaxe RE2](re2.md) pour effectuer la correspondance et utilisez des groupes capturés numérotés qui sont gérés en interne.
    Par exemple :

    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    Dans l’instruction Parse, l’expression régulière générée en interne par l’analyse est `.*?<regex1>(.*?)<regex2>(\-\d+)` .
        
    * `*` a été traduit `.*?` .
        
    * `string` a été traduit `.*?` .
        
    * `long` a été traduit `\-\d+` .

## <a name="examples"></a>Exemples

L' `parse` opérateur fournit une méthode rationalisée à `extend` une table en utilisant plusieurs `extract` applications sur la même `string` expression. Ce résultat est utile lorsque la table comporte une `string` colonne qui contient plusieurs valeurs que vous souhaitez décomposer en colonnes individuelles. Par exemple, une colonne qui a été produite par une instruction de trace de développement (« `printf` »/«» `Console.WriteLine` ).

Dans l’exemple ci-dessous, supposons que la colonne `EventText` de la table `Traces` contient des chaînes sous la forme `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` .
L’opération va étendre la table avec six colonnes :,,,,,, `resourceName` `totalSlices` `sliceNumber` `lockTime ` `releaseTime` `previousLockTime` `Month` et `Day` . 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

**Pour le mode Regex**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse kind = regex EventText with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previousLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|sliceNumber|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|
|PipelineScheduler|15|02/17/2016 08:40:00, |02/17/2016 08:40:00, |2016-02-17 08:39:00.0000000|
|PipelineScheduler|23|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|PipelineScheduler|20|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|PipelineScheduler|16|02/17/2016 08:41:00, |02/17/2016 08:41:00, |2016-02-17 08:40:00.0000000|
|PipelineScheduler|22|02/17/2016 08:41:01, |02/17/2016 08:41:00, |2016-02-17 08:40:01.0000000|

**Pour le mode Regex à l’aide d’indicateurs Regex**

Si vous souhaitez obtenir le resourceName uniquement, utilisez la requête suivante :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind = regex  EventText with * "resourceName=" resourceName ',' *
| project resourceName
```

|resourceName|
|---|
|PipelineScheduler, totalSlices = 27, sliceNumber = 23, lockTime = 02/17/2016 08:40:01, releaseTime = 02/17/2016 08:40:01|
|PipelineScheduler, totalSlices = 27, sliceNumber = 15, lockTime = 02/17/2016 08:40:00, releaseTime = 02/17/2016 08:40:00|
|PipelineScheduler, totalSlices = 27, sliceNumber = 20, lockTime = 02/17/2016 08:40:01, releaseTime = 02/17/2016 08:40:01|
|PipelineScheduler, totalSlices = 27, sliceNumber = 22, lockTime = 02/17/2016 08:41:01, releaseTime = 02/17/2016 08:41:00|
|PipelineScheduler, totalSlices = 27, sliceNumber = 16, lockTime = 02/17/2016 08:41:00, releaseTime = 02/17/2016 08:41:00|

Vous n’obtiendrez pas les résultats attendus, car le mode par défaut est gourmand.
Si vous avez des enregistrements où le *resourceName*  apparaît parfois en minuscules et parfois en majuscules, vous pouvez obtenir des valeurs NULL pour certaines valeurs.

Pour obtenir le résultat souhaité, exécutez la requête avec le non gourmand `U` et désactivez les indicateurs Regex respectant la casse `i` .

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind = regex flags = Ui EventText with * "RESOURCENAME=" resourceName ',' *
| project resourceName
```

|resourceName|
|---|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|

Si la chaîne analysée contient des nouvelles lignes, utilisez l’indicateur `s` pour analyser le texte.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=23\nlockTime=02/17/2016 08:40:01\nreleaseTime=02/17/2016 08:40:01\npreviousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=15\nlockTime=02/17/2016 08:40:00\nreleaseTime=02/17/2016 08:40:00\npreviousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=20\nlockTime=02/17/2016 08:40:01\nreleaseTime=02/17/2016 08:40:01\npreviousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=22\nlockTime=02/17/2016 08:41:01\nreleaseTime=02/17/2016 08:41:00\npreviousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=16\nlockTime=02/17/2016 08:41:00\nreleaseTime=02/17/2016 08:41:00\npreviousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind=regex flags=s EventText with * "resourceName=" resourceName:string "(.*?)totalSlices=" totalSlices:long "(.*?)lockTime=" lockTime:datetime "(.*?)releaseTime=" releaseTime:datetime "(.*?)previousLockTime=" previousLockTime:datetime "\\)" 
| project-away EventText
```

|resourceName|totalSlices|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|
|PipelineScheduler<br>|27|2016-02-17 08:40:00.0000000|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:41:00.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:41:01.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

**Mode souple**

Dans cet exemple pour le mode souple, la colonne étendue *totalSlices* doit être de type `long` . Toutefois, dans la chaîne analysée, elle a la valeur *nonValidLongValue*.
Dans la colonne étendue *releaseTime* , la valeur *nonValidDateTime* ne peut pas être analysée comme *DateTime*.
Ces deux colonnes étendues obtiennent la valeur null tandis que d’autres, telles que *sliceNumber*, obtiennent toujours les valeurs correctes.

Si vous utilisez option *genre = simple* pour la même requête ci-dessous, vous obtenez la valeur null pour toutes les colonnes étendues. Cette option est stricte sur les colonnes étendues et est la différence entre le mode souple et le mode simple.

 > [!NOTE] 
 > En mode souple, les colonnes étendues peuvent être partiellement mises en correspondance.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=nonValidDateTime 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=nonValidDateTime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=nonValidLongValue, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=nonValidDateTime 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=nonValidLongValue, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind=relaxed EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *
| project-away EventText
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00||2016-02-17 08:39:00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|PipelineScheduler||20|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|PipelineScheduler||16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|
 
