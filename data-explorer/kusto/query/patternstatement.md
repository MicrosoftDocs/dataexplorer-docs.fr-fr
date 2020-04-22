---
title: énoncé de modèle - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’énoncé de modèle dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d7ad021fe7b458d54beeb24b908420324b45ad39
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765669"
---
# <a name="pattern-statement"></a>énoncé de modèle

::: zone pivot="azuredataexplorer"

Un **modèle** est une construction nommée de type vue qui cartographie les tuples de chaîne prédéfinis aux corps de fonction sans paramètres. Les motifs sont uniques en deux aspects :

* Les motifs sont « invoqués » à l’aide d’une syntaxe qui ressemble à des références de table portées.
* Les modèles ont un ensemble contrôlé, rapproché, de valeurs d’argumentation qui peuvent être cartographiés, et le processus de cartographie est fait par Kusto. Cela signifie que si un modèle est déclaré mais non défini, Kusto identifier et signaler comme une erreur toutes les invocations au modèle, ce qui permet de «résoudre» ces modèles par une application de niveau intermédiaire.


## <a name="pattern-declaration"></a>Déclaration de modèle
L’énoncé de modèle est utilisé pour déclarer ou définir un modèle.
Par exemple, ce qui suit est `app` une instruction de modèle qui déclare être un modèle :

```kusto
declare pattern app;
```

Cette déclaration dit Kusto qui `app` est un modèle, mais ne dit pas Kusto comment résoudre le modèle. Par conséquent, toute tentative d’invoquer ce modèle dans la requête entraînera une erreur spécifique énumérant toutes ces invocations. Par exemple :

```kusto
declare pattern app;
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

Cette requête va générer une erreur de Kusto, indiquant que les `app("ApplicationX")["StartEvents"]` invocations de modèle suivantes ne peuvent pas être résolues: et `app("ApplicationX")["StopEvents"]`.

**Syntaxe**

`declare``pattern` *PatternName (nom du modèle)*

## <a name="pattern-definition"></a>Définition du modèle

L’énoncé de modèle peut également être utilisé pour définir un modèle. Dans une définition de modèle, toutes les invocations possibles du modèle sont explicitement énoncées, et l’expression tabulaire correspondante donnée. Lorsque Kusto exécute alors la requête, il remplace chaque invocation de modèle par le corps de modèle correspondant. Par exemple :

```kusto
declare pattern app = (applicationId:string)[eventType:string]
{
    ("ApplicationX").["StopEvents"] = { database("AppX").Events | where EventType == "StopEvent" };
    ("ApplicationX").["StartEvents"] = { database(applicationId).Events | where EventType == eventType } ;
};
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

L’expression qui est fournie pour chaque modèle étant apparié est soit un nom de table ou une référence à une [déclaration de laisser](letstatement.md).

**Syntaxe**

`declare``pattern` *PatternName* = `(`*ArgName* `:` *ArgType* [`,` ... ] `)` [`[` *PathName* `:` *PathArgType* `]`]`{`
&nbsp;&nbsp;&nbsp;&nbsp;`(` *ArgValue1* `,` [ *ArgValue2* ... ] `)` [ `.[` Expression *de* `};` &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; PathValue `]` ] `=` `{`[ `(` *ArgValue1_2* `,` ArgValue1_2 [ ArgValue2_2 ... ] *ArgValue2_2* &nbsp; `)` [ `.[` *PathValue_2* `]` ] `=` `{` *expression_2* `};` &nbsp; ... &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp; ]        `}`

* *PatternName*: Nom du mot clé du modèle. La syntaxe qui définit uniquement le mot clé est autorisée : pour détecter toutes les références de motifs avec un mot clé spécifié.
* *ArgName*: Nom de l’argument de modèle. Les modèles permettent un ou plusieurs noms d’argument.
* *ArgType*: Type de l’argument de modèle (actuellement seulement `string` est autorisé)
* *PathName*: Nom de l’argument du chemin. Les modèles permettent zéro ou un nom de chemin.
* *PathType*: Type de l’argument du chemin (actuellement seulement `string` est autorisé)
* *ArgValue1*, *ArgValue2*, ... - valeurs `string` des arguments de modèle (actuellement seulement les littérals sont autorisés)
* *PathValue* - valeur du chemin `string` de modèle (actuellement seuls les littérals sont autorisés)
* *expression*: L’expression - une expression `Logs | where Timestamp > ago(1h)`tabulaire (par exemple, ), ou une expression lambda qui fait référence à une fonction. *expression*

## <a name="pattern-invocation"></a>Invocation de modèle

La syntaxe d’invocation de modèle est semblable à la syntaxe de référence de tableau étendue :

* *PatternName* `(` *ArgValue1* [`,` *ArgValue2* ...] `).` *PathValue (en)*
* *PatternName* `(` *ArgValue1* [`,` *ArgValue2* ...] `).["` *PathValue (en)*`"]`

## <a name="remarks"></a>Notes

**Scénario**

L’énoncé de modèle est conçu pour les applications de niveau intermédiaire qui acceptent les requêtes des utilisateurs, puis envoient ces requêtes à Kusto. De telles applications préfixent souvent ces requêtes d’utilisateur avec un modèle logique de schéma (un ensemble de [déclarations de laisser](letstatement.md), peut-être suffixé par une [déclaration de restriction](restrictstatement.md)).
Dans certains cas, ces applications ont besoin d’une syntaxe qu’elles peuvent fournir aux utilisateurs pour référencer des entités qui ne peuvent pas être connues à l’avance et définies dans le schéma logique qu’elles construisent (soit parce qu’elles ne sont pas connues à l’avance, soit parce que le nombre d’entités potentielles est trop grand pour être prédéfini dans le schéma logique.

Pattern résoudre ce scénario de la manière suivante. L’application de niveau intermédiaire envoie la requête à Kusto avec tous les modèles déclarés, mais pas définis. Kusto analyse alors la requête, et s’il ya une ou plusieurs invocation de modèle en elle renvoie une erreur de retour à l’application de niveau intermédiaire avec toutes ces invocations explicitement énumérées. L’application de niveau intermédiaire peut alors résoudre chacune de ces références, et ré-exécuter la requête, cette fois préfixer avec la définition de modèle entièrement élaborée.

**Normalisations**

Kusto normalise automatiquement le modèle, ainsi par exemple les suivants sont tous les invocations du même modèle, et un seul est rapporté en arrière :

```kusto
declare pattern app;
union
  app("ApplicationX").StartEvent,
  app('ApplicationX').StartEvent,
  app("ApplicationX").['StartEvent'],
  app("ApplicationX").["StartEvent"]
```

Cela signifie également que l’on ne peut pas les définir ensemble, car ils sont considérés comme les mêmes.

**Caractères génériques**

Kusto ne traite pas les wildcards d’une manière particulière. Par exemple, dans la requête suivante :

```kusto
declare pattern app;
union app("ApplicationX").*
| count
```

Kusto signalera une seule invocation manquante de modèle : `app("ApplicationX").["*"]`.

## <a name="examples"></a>Exemples

Questions sur plus d’une invocation modèle simple:

```kusto
declare pattern A
{
    // ...
};
union (A('a1').Text), (A('a2').Text)
```

|Application|SomeText|
|---|---|
|App #1|Il s’agit d’un texte gratuit: 1|
|App #1|Il s’agit d’un texte gratuit: 2|
|App #1|Il s’agit d’un texte gratuit: 3|
|App #1|Il s’agit d’un texte gratuit: 4|
|App #1|Il s’agit d’un texte gratuit: 5|
|App #2|Il s’agit d’un texte gratuit: 9|
|App #2|Il s’agit d’un texte gratuit: 8|
|App #2|Il s’agit d’un texte gratuit: 7|
|App #2|Il s’agit d’un texte gratuit: 6|
|App #2|Il s’agit d’un texte gratuit: 5|

```kusto
declare pattern App;
union (App('a1').Text), (App('a2').Text)
```

Erreur sémantique:

     SEM0036: One or more pattern references were not declared. Detected pattern references: ["App('a1').['Text']","App('a2').['Text']"].

```kusto
declare pattern App;
declare pattern App = (applicationId:string)[scope:string]  
{
    ('a1').['Data']    = { range x from 1 to 5 step 1 | project App = "App #1", Data    = x };
    ('a1').['Metrics'] = { range x from 1 to 5 step 1 | project App = "App #1", Metrics = rand() };
    ('a2').['Data']    = { range x from 1 to 5 step 1 | project App = "App #2", Data    = 10 - x };
    ('a3').['Metrics'] = { range x from 1 to 5 step 1 | project App = "App #3", Metrics = rand() };
};
union (App('a2').Metrics), (App('a3').Metrics) 
```

Erreur sémantique retournée :

    SEM0036: One or more pattern references were not declared. Detected pattern references: ["App('a2').['Metrics']","App('a3').['Metrics']"].

::: zone-end

::: zone pivot="azuremonitor"

Ce n’est pas pris en charge dans Azure Monitor

::: zone-end
