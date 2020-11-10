---
title: Commande plugins afficher les plug-ins-Azure Explorateur de données
description: Cet article décrit les commandes de gestion des plug-ins dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/02/2020
ms.openlocfilehash: 1db761d8c290c7aaea452cd0cb3d85f2f648221c
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422078"
---
# <a name="show-plugins"></a>. afficher les plug-ins


Répertorie tous les plug-ins du cluster.

## <a name="syntax"></a>Syntaxe

`.show` `plugins`

## <a name="output"></a>Output

Retourne une table qui contient les champs suivants :
* **PluginName** : nom du plug-in
* **IsEnabled** : valeur booléenne qui indique si le plug-in est activé

## <a name="example"></a>Exemple

<!-- csl -->
```kusto
.show plugins
``` 

| PluginName | IsEnabled |
|---|---|
| autocluster | false |
| basket      | true  |

## <a name="next-steps"></a>Étapes suivantes

* [. désactiver le plug-in](disable-plugin.md)
* [. activer le plug-in](enable-plugin.md)
