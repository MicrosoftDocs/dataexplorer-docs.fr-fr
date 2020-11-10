---
title: Commande Activer le plug-in-Azure Explorateur de données
description: Cet article décrit la commande de gestion des plug-ins. activez le plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/02/2020
ms.openlocfilehash: a44ebec6774374f4d38dfda3babe42f2f5e07ac6
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422070"
---
# <a name="enable-plugin"></a>. activer le plug-in

Active un plug-in.

Cette commande requiert une `All Databases admin` autorisation.

## <a name="syntax"></a>Syntaxe

`.enable``plugin` *PluginName*

## <a name="example"></a>Exemple

<!-- csl -->
```kusto
.enable plugin autocluster
``` 

## <a name="next-steps"></a>Étapes suivantes

* [. désactiver le plug-in](disable-plugin.md)
* [. afficher les plug-ins](show-plugins.md)

