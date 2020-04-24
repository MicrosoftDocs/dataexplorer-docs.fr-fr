---
title: assert() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit assert () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 97f01df7bc85171eefabeb2ed835109f0faef81e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518409"
---
# <a name="assert"></a>assert()

Vérifie une condition. Si la condition est fausse, les sorties de messages d’erreur et échouent à la requête.

**Syntaxe**

`assert(`*condition*`, `*message* de condition`)`

**Arguments**

* *condition*: L’expression conditionnelle à évaluer. Si la `false`condition est, le message spécifié est utilisé pour signaler une erreur. Si la `true`condition est, il revient `true` comme un résultat d’évaluation. L’état doit être évalué à constante pendant la phase d’analyse de requête.
* *message*: Le message utilisé si `false`l’affirmation est évaluée à . Le *message* doit être un littératie de chaîne.


**Retourne**

* `true`- si la condition est`true`
* Soulève une erreur sémantique si `false`la condition est évaluée à .

**Remarques**

* `condition`doivent être évalués à la constante pendant la phase d’analyse de requête. En d’autres termes, il peut être construit à partir d’autres expressions faisant référence à des constantes, et ne peut pas être lié à la ligne-contexte.

**Exemples**

La requête suivante définit `checkLength()` une fonction qui vérifie `assert` la longueur des chaînes d’entrée, et utilise pour valider le paramètre de longueur d’entrée (vérifie qu’il est supérieur à zéro).

```kusto
let checkLength = (len:long, s:string)
{
    assert(len > 0, "Length must be greater than zero") and 
    strlen(s) > len
};
datatable(input:string)
[
    '123',
    '4567'
]
| where checkLength(len=long(-1), input)
```

Exécution de cette requête donne une erreur:  
`assert() has failed with message: 'Length must be greater than zero'`


Exemple d’exécution `len` avec entrée valide :

```kusto
let checkLength = (len:long, s:string)
{
    assert(len > 0, "Length must be greater than zero") and strlen(s) > len
};
datatable(input:string)
[
    '123',
    '4567'
]
| where checkLength(len=3, input)
```

|entrée|
|---|
|4567|