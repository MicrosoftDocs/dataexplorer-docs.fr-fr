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
ms.openlocfilehash: 1063355f880f26a321eb6416180ae9764575f0be
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320891"
---
# <a name="disable-plugin"></a>.disable plugin

Désactive un plug-in.

Cette commande requiert une `All Databases admin` autorisation.

## <a name="syntax"></a>Syntaxe

`.disable``plugin` *PluginName*

## <a name="example"></a> Exemple
 
<!-- csl -->
```kusto
.disable plugin autocluster
``` 

## <a name="next-steps"></a>Étapes suivantes

* [`.show plugins`](show-plugins.md)
* [`.enable plugin`](enable-plugin.md)

