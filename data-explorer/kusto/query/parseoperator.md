---
title: opérateur d’analyse - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur d’analyse dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: beb51ac80810e5d14013e9b3571d759bb7b09125
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511507"
---
# <a name="parse-operator"></a>opérateur parse

évalue une expression de chaîne et analyse sa valeur dans une ou plusieurs colonnes calculées. Pour les chaînes analysées sans succès, les colonnes calculées auront des nulls.
Voir [l’analyse-où](parsewhereoperator.md) l’opérateur qui filtre les cordes analysées sans succès.

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

**Syntaxe**

*T* `| parse` `kind=regex` [`flags=regex_flags`]`simple` | `*` `:` `*` *Expression* `with` *StringConstant* *ColumnName* *ColumnType*] Expression ( StringConstant ColumnName [ ColumnType ]) ... `relaxed`

**Arguments**

* *T*: La table d’entrée.
* Genre: 

    * simple (la valeur par défaut) : StringConstant est une valeur de chaîne régulière et le match est strict, ce qui signifie que tous les délimitants de cordes doivent apparaître dans la chaîne analysée et toutes les colonnes étendues doivent correspondre aux types requis.
        
    * regex : StringConstant peut être une expression régulière et le match est strict, ce qui signifie que tous les délimitants de cordes (qui peuvent être un regex pour ce mode) doivent apparaître dans la chaîne analysée et toutes les colonnes étendues doivent correspondre aux types requis.
    
    * drapeaux : Drapeaux à utiliser en `U` mode regex `m` comme (Ungreedy), (mode `s` multi-lignes), (match nouvelle ligne `\n`), `i` (insensible au cas) et plus encore dans les drapeaux [RE2.](re2.md)
        
    * détendu : StringConstant est une valeur de chaîne régulière et le match est détendu, ce qui signifie que tous les délimitants de cordes doivent apparaître dans la chaîne analysée, mais les colonnes étendues peuvent correspondre partiellement aux types requis (colonnes étendues qui ne correspondent pas aux types requis obtiendront la valeur nulle).

* *Expression*: Une expression qui évalue à une chaîne.

* *Nom de colonne:* Le nom d’une colonne pour attribuer une valeur (sortie de l’expression de la chaîne) à. 
  
* *ColumnType:* doit être de type scalaire facultatif qui indique le type pour convertir la valeur en (par défaut, il est de type chaîne).

**Retourne**

Le tableau d’entrée, étendu selon la liste des colonnes qui sont fournies à l’opérateur.

**Conseils**

* Utilisez [`project`](projectoperator.md) si vous voulez également laisser tomber ou renommer certaines colonnes.

* Utilisation dans le modèle afin de sauter les valeurs indésirables (ne peut pas être utilisé après la colonne de chaîne)

* Le modèle d’analyse peut commencer avec *ColumnName* et pas seulement avec *StringConstant*. 

* Si *l’expression* analysée n’est pas de chaîne de type, elle sera convertie en chaîne de type.

* Si le mode regex est utilisé, il est possible d’ajouter des drapeaux regex afin de contrôler l’ensemble du regex utilisé dans l’analyse.

* En mode regex, l’analyse traduira le modèle en un regex et utilisera la [syntaxe RE2](re2.md) afin de faire l’appariement à l’aide de groupes capturés numérotés qui sont manipulés en interne.
  Ainsi, par exemple, cette déclaration d’analyse :
  
    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    Le regex qui sera généré par le `.*?<regex1>(.*?)<regex2>(\-\d+)`parse interne est .
        
    - `*`a été `.*?`traduit à .
        
    - `string`a été `.*?`traduit à .
        
    - `long`a été `\-\d+`traduit à .

**Exemples**

L’opérateur `parse` fournit un moyen `extend` simplifié à `extract` une table `string` en utilisant plusieurs applications sur la même expression.
Ceci est plus utile lorsque `string` la table a une colonne qui contient plusieurs valeurs que vous souhaitez percer`printf`dans des`Console.WriteLine`colonnes individuelles, comme une colonne qui a été produite par une trace développeur (" "/" ") déclaration.

Dans l’exemple ci-dessous, `Traces` supposons que la `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})`colonne `EventText` de table contient des chaînes de la forme .
L’opération ci-dessous étendra la `resourceName` `totalSlices`table `sliceNumber` `lockTime `avec `releaseTime` `previouLockTime`6 colonnes: , , , , , `Month` , et `Day`. 


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

|resourceName|totalSlices|sliceNumber|lockTime|sortieTime|previouLockTime (en)|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

pour le mode regex :

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

|resourceName|sliceNumber|lockTime|sortieTime|previouLockTime (en)|
|---|---|---|---|---|
|PipelineScheduler|15|02/17/2016 08:40:00, |02/17/2016 08:40:00, |2016-02-17 08:39:00.0000000|
|PipelineScheduler|23|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|PipelineScheduler|20|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|PipelineScheduler|16|02/17/2016 08:41:00, |02/17/2016 08:41:00, |2016-02-17 08:40:00.0000000|
|PipelineScheduler|22|02/17/2016 08:41:01, |02/17/2016 08:41:00, |2016-02-17 08:40:01.0000000|


pour le mode regex à l’aide de drapeaux regex :

si nous sommes intéressés à obtenir la ressourceName seulement et nous utilisons cette requête:

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
|PipelineScheduler, totalSlices-27, sliceNumber-23, lockTime-02/17/2016 08:40:01, releaseTime-02/17/2016 08:40:01|
|PipelineScheduler, totalSlices-27, sliceNumber-15, lockTime-02/17/2016 08:40:00, releaseTime-02/17/2016 08:40:00|
|PipelineScheduler, totalSlices-27, sliceNumber-20, lockTime-02/17/2016 08:40:01, releaseTime-02/17/2016 08:40:01|
|PipelineScheduler, totalSlices-27, sliceNumber-22, lockTime-02/17/2016 08:41:01, releaseTime-02/17/2016 08:41:00|
|PipelineScheduler, totalSlices-27, sliceNumber-16, lockTime-02/17/2016 08:41:00, releaseTime-02/17/2016 08:41:00|

Mais nous n’obtenons pas les résultats attendus puisque le mode par défaut est gourmand.
ou même si nous avions peu d’enregistrements où la ressourceName apparaît parfois plus bas cas parfois cas supérieur de sorte que nous pouvons obtenir des nulls pour certaines valeurs.
Afin d’obtenir le résultat recherché, nous pouvons exécuter celui-ci avec le non-gourmand (`U`) et désactiver les cas sensibles (`i`) drapeaux regex:

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


Si la chaîne analysée a de nouvelles lignes, vous devez utiliser le drapeau `s` pour analyser le texte comme prévu :

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

|resourceName|totalSlices|lockTime|sortieTime|previousLockTime (en)|
|---|---|---|---|---|
|PipelineScheduler<br>|27|2016-02-17 08:40:00.0000000|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:41:00.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:41:01.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


dans cet exemple pour le mode détendu: totalSlices colonne étendue est nécessaire pour être de type long, mais dans la chaîne analysée, il a la valeur nonValidLongValue.
versionTime colonne étendue a le même problème, la valeur nonValidDateTime ne peut pas être analysé comme date.
dans ce cas, ces deux colonnes étendues obtiendront la valeur nulle tandis que les autres (comme sliceNumber) obtient toujours les valeurs correctes.

en utilisant le type - simple pour la même requête ci-dessous donne nul pour toutes les colonnes étendues, car il est strict sur les colonnes étendues (c’est la différence entre le mode détendu et simple, en mode détendu, colonnes étendues peuvent être assorties partiellement).

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

|resourceName|totalSlices|sliceNumber|lockTime|sortieTime|previouLockTime (en)|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00||2016-02-17 08:39:00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|PipelineScheduler||20|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|PipelineScheduler||16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|