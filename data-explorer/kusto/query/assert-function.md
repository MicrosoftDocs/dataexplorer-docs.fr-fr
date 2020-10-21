---
title: Assert ()-Azure Explorateur de données
description: Cet article décrit Assert () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 442fbec2742a4d1edc7a9ad03a81db27e6d18574
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252784"
---
# <a name="assert"></a>assert()

Vérifie une condition. Si la condition est false, génère des messages d’erreur et fait échouer la requête.

## <a name="syntax"></a>Syntaxe

`assert(`*condition* `, ` *message*`)`

## <a name="arguments"></a>Arguments

* *condition*: expression conditionnelle à évaluer. Si la condition est `false` , le message spécifié est utilisé pour signaler une erreur. Si la condition est `true` , elle retourne `true` en tant que résultat d’évaluation. La condition doit être évaluée à constant au cours de la phase d’analyse de la requête.
* *message*: message utilisé si l’assertion est évaluée à `false` . Le *message* doit être un littéral de chaîne.

> [!NOTE]
> `condition` doit être évaluée à constant pendant la phase d’analyse de la requête. En d’autres termes, elle peut être construite à partir d’autres expressions référençant des constantes et ne peut pas être liée à un contexte de ligne.

## <a name="returns"></a>Retours

* `true` -Si la condition est `true`
* Génère une erreur sémantique si la condition est évaluée à `false` .

## <a name="examples"></a>Exemples

La requête suivante définit une fonction `checkLength()` qui vérifie la longueur des chaînes d’entrée et utilise `assert` pour valider le paramètre de longueur d’entrée (vérifie qu’elle est supérieure à zéro).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

L’exécution de cette requête génère une erreur :  
`assert() has failed with message: 'Length must be greater than zero'`


Exemple d’exécution avec une `len` entrée valide :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
