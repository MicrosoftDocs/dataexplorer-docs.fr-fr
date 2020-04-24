---
title: parse-où l’opérateur - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’analyse-où l’opérateur dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/12/2020
ms.openlocfilehash: 0e44ab83242fc8aed0e46bdab1fa5a142992bfe5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511405"
---
# <a name="parse-where-operator"></a>Opérateur parse-where

évalue une expression de chaîne et analyse sa valeur dans une ou plusieurs colonnes calculées. Le résultat n’est que les cordes analysées avec succès.
Voir [l’opérateur d’analyse](parseoperator.md) qui produit des nulls pour les cordes analysées sans succès.

```kusto
T | parse-where Text with "ActivityName=" name ", ActivityType=" type
```

**Syntaxe**

*T* `| parse-where` `kind=regex` [`flags=regex_flags`]`simple` *Expression* `with` Expression `*` (*StringConstant* *ColumnName* [`:` `*` *ColumnType*]) ...

**Arguments**

* *T*: La table d’entrée.

* Genre: 

    * simple (la valeur par défaut) : StringConstant est une valeur de chaîne régulière et le match est strict, ce qui signifie que tous les délimitants de cordes doivent apparaître dans la chaîne analysée et toutes les colonnes étendues doivent correspondre aux types requis.
        
    * regex : StringConstant peut être une expression régulière et le match est strict, ce qui signifie que tous les délimitants de cordes (qui peuvent être un regex pour ce mode) doivent apparaître dans la chaîne analysée et toutes les colonnes étendues doivent correspondre aux types requis.
    
    * drapeaux : Drapeaux à utiliser en `U` mode regex `m` comme (Ungreedy), (mode `s` multi-lignes), (match nouvelle ligne `\n`), `i` (insensible au cas) et plus encore dans les drapeaux [RE2.](re2.md)
        
* *Expression*: Une expression qui évalue à une chaîne.

* *Nom de colonne:* Le nom d’une colonne pour attribuer une valeur (sortie de l’expression de la chaîne) à. 
  
* *ColumnType:* doit être de type scalaire facultatif qui indique le type pour convertir la valeur en (par défaut, il est de type chaîne).

**Retourne**

Le tableau d’entrée, étendu selon la liste des colonnes qui sont fournies à l’opérateur.
Seules les chaînes analysées avec succès seront dans la sortie. les chaînes qui ne correspondent pas au modèle seront filtrées.

**Conseils**

* `parse-where`analyse les cordes de la même manière que [l’analyse,](parseoperator.md) mais en plus filtres-out des chaînes qui n’ont pas été analysées avec succès.

* Utilisez [`project`](projectoperator.md) si vous voulez également laisser tomber ou renommer certaines colonnes.

* Utilisation dans le modèle afin de sauter les valeurs indésirables (ne peut pas être utilisé après la colonne de chaîne)

* Le modèle d’analyse peut commencer avec *ColumnName* et pas seulement avec *StringConstant*. 

* Si *l’expression* analysée n’est pas de chaîne de type, elle sera convertie en chaîne de type.

* Si le mode regex est utilisé, il est possible d’ajouter des drapeaux regex afin de contrôler l’ensemble du regex utilisé dans l’analyse.

* En mode regex, l’analyse traduira le modèle en un regex et utilisera la [syntaxe RE2](re2.md) afin de faire l’appariement à l’aide de groupes capturés numérotés qui sont manipulés en interne.
  
  Ainsi, par exemple, cette déclaration d’analyse :
  
    ```kusto
    parse-where kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    Le regex qui sera généré par le `.*?<regex1>(.*?)<regex2>(\-\d+)`parse interne est .
        
    - `*`a été `.*?`traduit à .
        
    - `string`a été `.*?`traduit à .
        
    - `long`a été `\-\d+`traduit à .

**Exemples**

L’opérateur `parse-where` fournit un moyen `extend` simplifié à `extract` une table `string` en utilisant plusieurs applications sur la même expression.
Ceci est plus utile lorsque `string` la table a une colonne qui contient plusieurs valeurs que vous souhaitez percer`printf`dans des`Console.WriteLine`colonnes individuelles, comme une colonne qui a été produite par une trace développeur (" "/" ") déclaration.

Dans l’exemple ci-dessous, `Traces` supposons que la `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})`colonne `EventText` de table contient des chaînes de la forme .
L’opération ci-dessous étendra la `resourceName` `totalSlices`table `sliceNumber` `lockTime `avec `releaseTime` `previouLockTime`6 colonnes: , , , , , `Month` , et `Day`. 

Ici, peu de cordes n’ont pas un match complet.
À `parse`l’aide , les colonnes calculées auront des nulls :

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
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|totalSlices|sliceNumber|lockTime|sortieTime|previouLockTime (en)|
|---|---|---|---|---|---|
|||||||
|||||||
|||||||
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


Utilisation `parse-where` de filtret-out sans succès les chaînes analysées du résultat:

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
| parse-where EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|totalSlices|sliceNumber|lockTime|sortieTime|previouLockTime (en)|
|---|---|---|---|---|---|
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


pour le mode regex à l’aide de drapeaux regex :

si nous sommes intéressés à obtenir le resouceName et totalSlices et nous utilisons cette requête:

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

Dans la requête ci-dessus, nous n’obtenons aucun résultat puisque le mode par défaut est sensible aux cas, donc aucune des cordes n’a été analysée avec succès.

Afin d’obtenir le résultat requis, `parse-where` nous pouvons exécuter le`i`drapeau de régularis cas-insensible ( ) regex.
Notez que seulement 3 cordes seront analysées avec succès et donc le résultat est de 3 enregistrements (certains totalSlices détient integers invalides): 

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


