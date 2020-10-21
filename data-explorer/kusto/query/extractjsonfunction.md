---
title: extractjson ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit extractjson () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4891e3b906de540fb2b4939e65359829fd442f7f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247502"
---
# <a name="extractjson"></a>extractjson()

Obtient un élément spécifié à partir d’un texte JSON à l’aide d’une expression de chemin. 

Convertit éventuellement la chaîne extraite un type spécifique.

```kusto
extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))
```

## <a name="syntax"></a>Syntaxe

`extractjson(`*jsonPath* `,` *source de source*`)` 

## <a name="arguments"></a>Arguments

* *jsonPath*: jsonPath chaîne qui définit un accesseur dans le document JSON.
* *DataSource*: document JSON.

## <a name="returns"></a>Retours

Cette fonction effectue une requête JsonPath dans dataSource qui contient une chaîne JSON valide, convertissant éventuellement cette valeur en un autre type selon le troisième argument.

## <a name="example"></a>Exemple

La `[` notation entre crochets `]` notatation et point ( `.` ) est équivalente :

```kusto
T 
| extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) 

T
| extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) 
```

### <a name="json-path-expressions"></a>Expressions de chemin JSON

|Expression de chemin|Description|
|---|---|
|`$`|Objet racine|
|`@`|Objet en cours|
|`.` ou `[ ]` | Enfant|
|`[ ]`|Indice de tableau|

*(Actuellement, nous n’implémentons pas les caractères génériques, la récursivité, l’union ou les tranches.)*


**Conseils sur les performances**

* Appliquez les clauses where avant d’utiliser `extractjson()`
* Utilisez plutôt une correspondance d’expression régulière avec [extract](extractfunction.md) . L’exécution peut être beaucoup plus rapide, et elle est efficace si le JSON est généré à partir d’un modèle.
* Utilisez `parse_json()` si vous devez extraire plusieurs valeurs de JSON.
* Envisagez d’analyser le JSON lors de l’ingestion en déclarant le type de la colonne comme étant dynamique.