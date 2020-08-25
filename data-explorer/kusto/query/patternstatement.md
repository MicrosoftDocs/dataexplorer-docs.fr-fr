---
title: instruction pattern-Azure Explorateur de données
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
ms.openlocfilehash: 03d183bd042bb75d8bb44f530575bd3b91cb2102
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793809"
---
# <a name="pattern-statement"></a>Pattern (instruction)

::: zone pivot="azuredataexplorer"

Un **modèle** est une construction de type vue nommée qui mappe des tuples de chaîne prédéfinis à des corps de fonction sans paramètre. Les modèles sont uniques en deux aspects :

* Les modèles sont appelés à l’aide d’une syntaxe qui ressemble aux références de table délimitées.
* Les modèles ont un jeu de valeurs d’arguments contrôlé, fermé et terminé, qui peuvent être mappés, et le processus de mappage est effectué par Kusto. Si un modèle est déclaré mais pas défini, Kusto identifie et signale tous les appels au modèle comme des erreurs. Cette identification permet de « résoudre » ces modèles à l’aide d’une application de niveau intermédiaire.

## <a name="pattern-declaration"></a>Déclaration de modèle

L’instruction Pattern est utilisée pour déclarer ou définir un modèle.
Par exemple, une instruction de modèle qui déclare `app` comme étant un modèle.

```kusto
declare pattern app;
```

Cette instruction indique à Kusto qu’il `app` s’agit d’un modèle, mais n’indique pas à Kusto comment résoudre le modèle. Par conséquent, toute tentative d’appeler ce modèle dans la requête entraîne une erreur spécifique et répertorie tous les appels de ce type. Par exemple :

```kusto
declare pattern app;
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

Cette requête génère une erreur à partir de Kusto, ce qui indique que les appels de modèle suivants ne peuvent pas être résolus : `app("ApplicationX")["StartEvents"]` et `app("ApplicationX")["StopEvents"]` .

## <a name="syntax"></a>Syntaxe

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

## <a name="syntax"></a>Syntaxe

`declare``pattern` *PatternName*  =  PatternName `(` *ArgName* `:` *ArgType* [ `,` ...] `)` [ `[` *Nom de chemin* `:` *PathArgType* `]` ]`{`
&nbsp;&nbsp;&nbsp;&nbsp;`(` *ArgValue1* [ `,` *ArgValue2* ...] `)` [ `.[` * PathValue `]` ] `=` `{` *expression* `};` &nbsp; &nbsp; &nbsp; &nbsp; [ &nbsp; &nbsp; &nbsp; &nbsp; `(` *ArgValue1_2* [ `,` *ArgValue2_2* ...] `)` [ `.[` *PathValue_2* `]` ] `=` `{` *expression_2* `};` &nbsp; &nbsp; &nbsp; &nbsp; ... &nbsp; &nbsp; &nbsp; &nbsp; ]        `}`

* *PatternName*: nom du mot clé Pattern. La syntaxe qui définit le mot clé uniquement est autorisée : pour la détection de toutes les références de modèle avec un mot clé spécifié.
* *ArgName*: nom de l’argument de modèle. Les modèles autorisent un ou plusieurs noms d’arguments.
* *ArgType*: type de l’argument de modèle (actuellement `string` autorisé uniquement)
* *Pathname*: nom de l’argument Path. Les modèles autorisent zéro ou un nom de chemin d’accès.
* *PathType*: type de l’argument Path (actuellement uniquement `string` autorisé)
* *ArgValue1*, *ArgValue2*,...-valeurs des arguments de modèle (actuellement, seuls les `string` littéraux sont autorisés)
* *PathValue* : valeur du chemin d’accès du modèle (actuellement, seuls les `string` littéraux sont autorisés)
* *expression* *: expression tabulaire* (par exemple, `Logs | where Timestamp > ago(1h)` ) ou expression lambda qui fait référence à une fonction.

## <a name="pattern-invocation"></a>Appel de modèle

La syntaxe d’appel de modèle est similaire à la syntaxe de référence de table étendue.

* *PatternName* `(` *ArgValue1* [ `,` *ArgValue2* ...] `).` *PathValue*
* *PatternName* `(` *ArgValue1* [ `,` *ArgValue2* ...] `).["` *PathValue*`"]`

## <a name="notes"></a>Notes

**Scénario**

L’instruction Pattern est conçue pour les applications de couche intermédiaire qui acceptent les requêtes utilisateur, puis envoie ces requêtes à Kusto. Ces applications préfixent souvent ces requêtes utilisateur avec un modèle de schéma logique. Le modèle est un ensemble d' [instructions Let](letstatement.md), éventuellement suffixées par une [instruction Restrict](restrictstatement.md).

Certaines applications ont besoin d’une syntaxe qu’elles peuvent fournir aux utilisateurs. La syntaxe est utilisée pour référencer des entités définies dans le schéma logique que les applications construisent. Toutefois, les entités ne sont parfois pas connues à l’avance ou le nombre d’entités potentielles est trop important pour être prédéfinies dans le schéma logique.

Pattern résout ce scénario de la façon suivante. L’application de niveau intermédiaire envoie la requête à Kusto avec tous les modèles déclarés, mais non définis. Kusto analyse ensuite la requête. S’il existe un ou plusieurs appels de modèle, Kusto retourne une erreur à l’application de couche intermédiaire avec tous les appels répertoriés explicitement. L’application de couche intermédiaire peut ensuite résoudre chacune de ces références, puis réexécuter la requête. Cette fois-ci, préfixez-le avec la définition de modèle entièrement élaborée.

**Normalisation**

Kusto normalise automatiquement le modèle. Par exemple, les éléments suivants sont tous des appels du même modèle, et un seul est rapporté en retour.

```kusto
declare pattern app;
union
  app("ApplicationX").StartEvent,
  app('ApplicationX').StartEvent,
  app("ApplicationX").['StartEvent'],
  app("ApplicationX").["StartEvent"]
```

Cela signifie également que vous ne pouvez pas les définir ensemble, car ils sont considérés comme identiques.

**Caractères génériques**

Kusto ne traite pas les caractères génériques dans un modèle d’une manière particulière. Par exemple, dans la requête suivante.

```kusto
declare pattern app;
union app("ApplicationX").*
| count
```

Kusto signale un appel de modèle manquant unique : `app("ApplicationX").["*"]` .

## <a name="examples"></a>Exemples

Requêtes sur plus d’un appel de modèle unique.

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

**Erreur sémantique**:

> SEM0036 : une ou plusieurs références de modèle n’ont pas été déclarées. Références de modèle détectées : ["application ('a1 '). [' Text'] "," app ('a2 '). ['Text'] "].

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

**Erreur sémantique retournée**:

> SEM0036 : une ou plusieurs références de modèle n’ont pas été déclarées. Références de modèle détectées : ["app ('a2 '). [' Métriques « ] », « application » (« a3 »). ['Metrics'] "].

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
