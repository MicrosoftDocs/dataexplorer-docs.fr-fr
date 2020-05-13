---
title: opérateur d’analyse-Azure Explorateur de données
description: Cet article décrit l’opérateur d’analyse dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8255f3d0c3dc0006029f06c7a0da4b41dfbaa1b7
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271333"
---
# <a name="parse-operator"></a>opérateur parse

évalue une expression de chaîne et analyse sa valeur dans une ou plusieurs colonnes calculées. Pour les chaînes analysées sans succès, les colonnes calculées auront des valeurs NULL.
Consultez opérateur [Parse-WHERE](parsewhereoperator.md) qui filtre les chaînes non analysées avec succès.

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

**Syntaxe**

*T* `| parse` [ `kind=regex` [ `flags=regex_flags` ] | `simple` | `relaxed` ] *expression* `with` `*` (*StringConstant* *ColumnName* [ `:` *ColumnType*]) `*` ...

**Arguments**

* *T*: table d’entrée.
* espèces 

    * simple (valeur par défaut) : StringConstant est une valeur de chaîne normale et la correspondance est stricte, ce qui signifie que tous les délimiteurs de chaîne doivent apparaître dans la chaîne analysée et que toutes les colonnes étendues doivent correspondre aux types requis.
        
    * Regex : StringConstant peut être une expression régulière et la correspondance est stricte, ce qui signifie que tous les délimiteurs de chaîne (qui peuvent être une expression régulière pour ce mode) doivent apparaître dans la chaîne analysée et que toutes les colonnes étendues doivent correspondre aux types requis.
    
    * Flags : indicateurs à utiliser en mode Regex comme `U` (non gourmand), `m` (mode multiligne), (mettre en `s` correspondance une nouvelle ligne `\n` ), `i` (non-respect de la casse) et plus dans les [indicateurs RE2](re2.md).
        
    * souple : StringConstant est une valeur de chaîne normale et la correspondance est assouplie, ce qui signifie que tous les délimiteurs de chaînes doivent apparaître dans la chaîne analysée, mais que les colonnes étendues peuvent correspondre partiellement aux types requis (les colonnes étendues qui ne correspondent pas aux types requis obtiendront la valeur null).

* *Expression*: expression qui prend la valeur d’une chaîne.

* *ColumnName :* Nom d’une colonne pour assigner une valeur (extraite de l’expression de chaîne) à. 
  
* *ColumnType :* doit être un type scalaire facultatif qui indique le type vers lequel convertir la valeur (par défaut, il s’agit d’un type de chaîne).

**Retourne**

La table d’entrée, étendue selon la liste des colonnes fournies à l’opérateur.

**Conseils**

* Utilisez [`project`](projectoperator.md) si vous souhaitez également supprimer ou renommer certaines colonnes.

* Utilisez * dans le modèle pour ignorer les valeurs indésirables (ne peut pas être utilisé après une colonne de chaîne)

* Le modèle d’analyse peut commencer par *ColumnName* et non uniquement par *StringConstant*. 

* Si l' *expression* analysée n’est pas de type chaîne, elle sera convertie en type chaîne.

* Si le mode Regex est utilisé, il est possible d’ajouter des indicateurs Regex afin de contrôler l’intégralité de l’expression régulière utilisée dans l’analyse.

* En mode Regex, Parse convertit le modèle en Regex et utilise la [syntaxe RE2](re2.md) pour effectuer la correspondance à l’aide de groupes capturés numérotés qui sont gérés en interne.
  Par exemple, cette instruction d’analyse :
  
    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    L’expression régulière qui sera générée en interne par l’analyse est `.*?<regex1>(.*?)<regex2>(\-\d+)` .
        
    - `*`a été traduit `.*?` .
        
    - `string`a été traduit `.*?` .
        
    - `long`a été traduit `\-\d+` .

**Exemples**

L' `parse` opérateur fournit une méthode rationalisée à `extend` une table en utilisant plusieurs `extract` applications sur la même `string` expression.
Cela s’avère particulièrement utile lorsque la table comporte une `string` colonne qui contient plusieurs valeurs que vous souhaitez décomposer en colonnes individuelles, par exemple une colonne produite par une instruction de trace du développeur (« `printf` »/«» `Console.WriteLine` ).

Dans l’exemple ci-dessous, supposons que la colonne `EventText` de la table `Traces` contient des chaînes sous la forme `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` .
L’opération ci-dessous permet d’étendre la table avec 6 colonnes :,,,,, `resourceName` `totalSlices` `sliceNumber` `lockTime ` `releaseTime` `previouLockTime` `Month` et `Day` . 

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
| parse EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

pour le mode Regex :

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
| parse kind = regex EventText with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previouLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|sliceNumber|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|
|PipelineScheduler|15|02/17/2016 08:40:00, |02/17/2016 08:40:00, |2016-02-17 08:39:00.0000000|
|PipelineScheduler|23|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|PipelineScheduler|20|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|PipelineScheduler|16|02/17/2016 08:41:00, |02/17/2016 08:41:00, |2016-02-17 08:40:00.0000000|
|PipelineScheduler|22|02/17/2016 08:41:01, |02/17/2016 08:41:00, |2016-02-17 08:40:01.0000000|


pour le mode Regex utilisant des indicateurs Regex :

Si nous sommes intéressés par l’obtention du resourceName uniquement et que nous utilisons cette requête :

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

Toutefois, nous n’obtenons pas les résultats attendus, car le mode par défaut est gourmand.
ou même si nous avions des enregistrements où le resourceName apparaît parfois en minuscules, il est possible que les valeurs soient nulles pour certaines valeurs.
pour obtenir le résultat souhaité, nous pouvons exécuter celui-ci avec les indicateurs Regex non gourmands ( `U` ) et désactiver la casse ( `i` ) :

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


Si la chaîne analysée contient des nouvelles lignes, vous devez utiliser l’indicateur `s` pour analyser le texte comme prévu :

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


dans cet exemple pour le mode souple : la colonne étendue totalSlices doit être de type long, mais dans la chaîne analysée, elle a la valeur nonValidLongValue.
la colonne étendue releaseTime a le même problème, la valeur nonValidDateTime ne peut pas être analysée comme DateTime.
dans ce cas, ces deux colonnes étendues obtiennent la valeur null tandis que d’autres (comme sliceNumber) reçoivent toujours les valeurs correctes.

l’utilisation de Kind = simple pour la même requête ci-dessous donne la valeur null pour toutes les colonnes étendues, car elle est stricte sur les colonnes étendues (il s’agit de la différence entre le mode souple et le mode simple, en mode souple, les colonnes étendues peuvent être mises en correspondance partiellement).

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
| parse kind=relaxed EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *
| project-away EventText
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00||2016-02-17 08:39:00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|PipelineScheduler||20|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|PipelineScheduler||16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|
