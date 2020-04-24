---
title: extractjson() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit extraitjson () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6177a1c8a6ed4390093e6f6fd24c5f5e9fd04f8a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515332"
---
# <a name="extractjson"></a>extractjson()

Obtient un élément spécifié à partir d’un texte JSON à l’aide d’une expression de chemin. 

Convertit éventuellement la chaîne extraite un type spécifique.

```kusto
extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))
```

**Syntaxe**

`extractjson(`*jsonPath* `,` *dataSource (en)*`)` 

**Arguments**

* *jsonPath*: chaîne JsonPath qui définit un accesseur dans le document JSON.
* *dataSource*: Un document JSON.

**Retourne**

Cette fonction effectue une requête JsonPath dans dataSource qui contient une chaîne JSON valide, convertissant éventuellement cette valeur en un autre type selon le troisième argument.

**Exemple**

La `[``]` notatation de`.`parenthèse et la notation de point ( ) sont équivalentes :

```kusto
T 
| extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) 

T
| extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) 
```

### <a name="json-path-expressions"></a>Expressions de chemin JSON

|||
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