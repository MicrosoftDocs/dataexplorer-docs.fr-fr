---
title: bag_merge ()-Azure Explorateur de données
description: Cet article décrit bag_merge () dans Azure Explorateur de données.
services: data-explorer
author: elgevork
ms.author: elgevork
ms.reviewer: ''
ms.service: data-explorer
ms.topic: reference
ms.date: 06/14/2020
ms.openlocfilehash: f5ca4888b0c3aad976d78c10bbbfa0810483c75b
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84784597"
---
# <a name="bag_merge"></a>bag_merge ()

Fusionne les objets d’entrée de jeu de propriétés dynamiques en un objet de conteneur de propriétés dynamique.

**Syntaxe**

`bag_merge(`*objet dynamique* `,` *objet dynamique*`)`

**Arguments**

* Entrez les objets dynamiques en les séparant par des virgules. La fonction prend en charge entre 2 et 64 objets dynamiques d’entrée.

**Retourne**

Retourne un `dynamic` conteneur de propriétés. Résultats de la fusion de tous les objets de jeu de propriétés d’entrée.
Si une clé apparaît dans plusieurs objets d’entrée, une valeur arbitraire (parmi les valeurs possibles pour cette clé) est choisie.

**Exemple**

Expression :

`print result = bag_merge(dynamic({ 'A1':12, 'B1':2, 'C1':3}), dynamic({ 'A2':81, 'B2':82, 'A1':1}))`

Prend la valeur :

`{
  "A1": 12,
  "B1": 2,
  "C1": 3,
  "A2": 81,
  "B2": 82
}`
