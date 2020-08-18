---
title: dynamic_to_json ()-Azure Explorateur de données
description: Cet article décrit dynamic_to_json () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 08/05/2020
ms.openlocfilehash: 010610eb8cf6727c752f04564e8b3d1f71a405ab
ms.sourcegitcommit: 31ebf208d6bfd901f825d048ea69c9bb3d8b87af
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88502153"
---
# <a name="dynamic_to_json"></a>dynamic_to_json ()

Convertit l' `dynamic` entrée en une représentation sous forme de chaîne.
Si l’entrée est un conteneur de propriétés, la chaîne de sortie imprime son contenu trié par les clés de manière récursive. Dans le cas contraire, la sortie est similaire à la sortie de la `tostring` fonction.

## <a name="syntax"></a>Syntaxe

`dynamic_to_json(Expr)`

## <a name="arguments"></a>Arguments

* *Expr*: `dynamic` entrée. La fonction accepte un seul argument.

## <a name="returns"></a>Retours

Retourne une représentation sous forme de chaîne de l' `dynamic` entrée. Si l’entrée est un conteneur de propriétés, la chaîne de sortie imprime son contenu trié par les clés de manière récursive.

## <a name="examples"></a>Exemples

Expression :

  Laissez BAG1 = dynamic_to_json (dynamique ({'Y10 ' :d ynamique ({}), 'x8 ' : Dynamic ({'C3 ' : 1, d 8 ' : 5, 'a4 ' : 6}), 1 ' : 114, 'a1 ' : 12, 'B1 ' : 2, 'C1 ' : 3, 'A14 ' : [15, 13, 18]})); imprimer BAG1
  
Résultat :

"{" "A1" " : 12," "A14" " : [15, 13, 18]," "B1" " : 2," "C1" " : 3," D1 "" : 114, "" x8 "" : {"" C3 "" : 1, "" D8 "" : 5, "" a4 "" : 6}, "" Y10 "" : {} } "

Expression :

 Laissez BAG2 = dynamic_to_json (dynamique ({'x8 ' : dynamique ({'a4 ' : 6, 'C3 ' : 1, d 8 ' : 5}), 'A14 ' : [15, 13, 18], 'C1 ' : 3, 'B1 ' : 2, 'Y10 ' : dynamique ({}), 'a1 ' : 12, 1 ' : 114}); imprimer BAG2
 
Résultat :

{"A1" : 12, "A14" : [15, 13, 18], "B1" : 2, "C1" : 3, "D1" : 114, "x8" : {"a4" : 6, "C3" : 1, "D8" : 5}, "Y10" : {} }
