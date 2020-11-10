---
title: Désactiver les commandes de plug-in-Azure Explorateur de données
description: Cet article décrit la commande de gestion des plug-ins. désactivez le plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/02/2020
ms.openlocfilehash: 1dd2649d4746506f0ef2c08d4615260babd594e1
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422075"
---
# <a name="disable-plugin"></a>. désactiver le plug-in

Désactive un plug-in.

Cette commande requiert une `All Databases admin` autorisation.

## <a name="syntax"></a>Syntaxe

`.disable``plugin` *PluginName*

## <a name="example"></a>Exemple
 
<!-- csl -->
```kusto
.disable plugin autocluster
``` 

## <a name="next-steps"></a>Étapes suivantes

* [. afficher les plug-ins](show-plugins.md)
* [. activer le plug-in](enable-plugin.md)

