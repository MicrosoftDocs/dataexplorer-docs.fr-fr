---
title: instruction pattern-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’instruction pattern dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 97fc8361feb3d8dddb7d0722ce741ef44bd4cec6
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737264"
---
# <a name="pattern-statement"></a>Pattern (instruction)

::: zone pivot="azuredataexplorer"

Un **modèle** est une construction de type vue nommée qui mappe des tuples de chaîne prédéfinis à des corps de fonction sans paramètre. Les modèles sont uniques en deux aspects :

* Les modèles sont appelés à l’aide d’une syntaxe qui ressemble aux références de table délimitées.
* Les modèles ont un jeu de valeurs d’arguments contrôlé, fermé et terminé, qui peuvent être mappés, et le processus de mappage est effectué par Kusto. Cela signifie que si un modèle est déclaré mais pas défini, Kusto identifie et signale comme une erreur tous les appels au modèle, ce qui permet de « résoudre » ces modèles par une application de couche intermédiaire.


## <a name="pattern-declaration"></a>Déclaration de modèle
L’instruction Pattern est utilisée pour déclarer ou définir un modèle.
Par exemple, voici une instruction pattern qui déclare `app` comme étant un modèle :

```kusto
declare pattern app;
```

Cette instruction indique à Kusto `app` qu’il s’agit d’un modèle, mais il n’indique pas à Kusto comment résoudre le modèle. Par conséquent, toute tentative d’appel de ce modèle dans la requête entraînera l’affichage d’une erreur spécifique qui répertorie tous les appels de ce type. Par exemple :

```kusto
declare pattern app;
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

Cette requête génère une erreur à partir de Kusto, ce qui indique que les appels de modèle suivants ne peuvent `app("ApplicationX")["StartEvents"]` pas `app("ApplicationX")["StopEvents"]`être résolus : et.

**Syntaxe**

`declare``pattern` *PatternName*

## <a name="pattern-definition"></a>Définition de modèle

L’instruction pattern peut également être utilisée pour définir un modèle. Dans une définition de modèle, tous les appels possibles du modèle sont explicitement disposés, et l’expression tabulaire correspondante donnée. Quand Kusto exécute ensuite la requête, il remplace chaque appel de modèle par le corps de modèle correspondant. Par exemple :

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

L’expression fournie pour chaque modèle mis en correspondance est un nom de table ou une référence à une [instruction Let](letstatement.md).

**Syntaxe**

`declare``pattern` *PatternName*PatternName = *ArgName* ArgName `:` *ArgType* [`,` ..`(`.] `)` [`[` *Nom de chemin* `:` *PathArgType* `]`]`{`
&nbsp;&nbsp;&nbsp;&nbsp;`(` *ArgValue1* [`,` *ArgValue2* ...] `)` `.[` `]` `=` `{` *Expression* `(` *ArgValue1_2* *ArgValue2_2* [* PathValue] [ &nbsp; ArgValue1_2 [`,` ArgValue2_2.. &nbsp; .] &nbsp; &nbsp; `};` &nbsp; &nbsp; &nbsp; &nbsp; `)` [ `.[` *PathValue_2* `{` *expression_2* ] `=` expression_2`};` .. &nbsp; . `]` &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp; ]        `}`

* *PatternName*: nom du mot clé Pattern. La syntaxe qui définit le mot clé uniquement est autorisée : pour la détection de toutes les références de modèle avec un mot clé spécifié.
* *ArgName*: nom de l’argument de modèle. Les modèles autorisent un ou plusieurs noms d’arguments.
* *ArgType*: type de l’argument de modèle (actuellement `string` autorisé uniquement)
* *Pathname*: nom de l’argument Path. Les modèles autorisent zéro ou un nom de chemin d’accès.
* *PathType*: type de l’argument Path (actuellement uniquement `string` autorisé)
* *ArgValue1*, *ArgValue2*,...-valeurs des arguments de modèle (actuellement, `string` seuls les littéraux sont autorisés)
* *PathValue* : valeur du chemin d’accès du modèle ( `string` actuellement, seuls les littéraux sont autorisés)
* *expression*: expression tabulaire (par exemple, `Logs | where Timestamp > ago(1h)`) ou expression lambda qui fait *référence à une* fonction.

## <a name="pattern-invocation"></a>Appel de modèle

La syntaxe d’appel de modèle est similaire à la syntaxe de référence de table étendue :

* *PatternName* `(` *ArgValue1* [`,` *ArgValue2* ...] `).` *PathValue*
* *PatternName* `(` *ArgValue1* [`,` *ArgValue2* ...] `).["` *PathValue*`"]`

## <a name="remarks"></a>Notes 

**Scénario**

L’instruction Pattern est conçue pour les applications de couche intermédiaire qui acceptent les requêtes utilisateur, puis envoie ces requêtes à Kusto. Ces applications préfixent souvent ces requêtes utilisateur avec un modèle de schéma logique (un ensemble d' [instructions Let](letstatement.md), éventuellement suffixées par une [instruction Restrict](restrictstatement.md)).
Dans certains cas, ces applications ont besoin d’une syntaxe qu’elles peuvent fournir aux utilisateurs pour référencer des entités qui ne peuvent pas être connues à l’avance et définies dans le schéma logique qu’elles construisent (parce qu’elles ne sont pas connues à l’avance, car le nombre d’entités potentielles est trop important pour être prédéfinies dans le schéma logique.

Le modèle permet de résoudre ce scénario de la façon suivante. L’application de niveau intermédiaire envoie la requête à Kusto avec tous les modèles déclarés, mais non définis. Kusto analyse ensuite la requête et, s’il y a un ou plusieurs appels de modèle dans, retourne une erreur à l’application de niveau intermédiaire avec tous les appels explicitement listés. L’application de couche intermédiaire peut ensuite résoudre chacune de ces références et réexécuter la requête, cette fois la préfixe avec la définition de modèle entièrement élaborée.

**Normalisation**

Kusto normalise automatiquement le modèle. par exemple, les éléments suivants sont tous des appels du même modèle, et un seul est rapporté :

```kusto
declare pattern app;
union
  app("ApplicationX").StartEvent,
  app('ApplicationX').StartEvent,
  app("ApplicationX").['StartEvent'],
  app("ApplicationX").["StartEvent"]
```

Cela signifie également qu’il est impossible de les définir ensemble, car ils sont considérés comme identiques.

**Caractères génériques**

Kusto ne traite pas les caractères génériques dans un modèle d’une manière particulière. Par exemple, dans la requête suivante :

```kusto
declare pattern app;
union app("ApplicationX").*
| count
```

Kusto signale un appel de modèle manquant unique : `app("ApplicationX").["*"]`.

## <a name="examples"></a>Exemples

Requêtes sur plus d’un appel de modèle unique :

```kusto
declare pattern A
{
    // ...
};
union (A('a1').Text), (A('a2').Text)
```

|Application|SomeText|
|---|---|
|#1 d’application|Il s’agit d’un texte libre : 1|
|#1 d’application|Il s’agit d’un texte libre : 2|
|#1 d’application|Il s’agit d’un texte libre : 3|
|#1 d’application|Il s’agit d’un texte libre : 4|
|#1 d’application|Il s’agit d’un texte libre : 5|
|#2 d’application|Il s’agit d’un texte libre : 9|
|#2 d’application|Il s’agit d’un texte libre : 8|
|#2 d’application|Il s’agit d’un texte libre : 7|
|#2 d’application|Il s’agit d’un texte libre : 6|
|#2 d’application|Il s’agit d’un texte libre : 5|

```kusto
declare pattern App;
union (App('a1').Text), (App('a2').Text)
```

Erreur sémantique :

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

Erreur sémantique retournée :

    SEM0036: One or more pattern references were not declared. Detected pattern references: ["App('a2').['Metrics']","App('a3').['Metrics']"].

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
