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
ms.openlocfilehash: a4da3848fa459cf5fae8a7a73f8b1f318ce7e858
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321469"
---
# <a name="enable-plugin"></a>.enable plugin

Active un plug-in.

Cette commande requiert une `All Databases admin` autorisation.

## <a name="syntax"></a>Syntaxe

`.enable``plugin` *PluginName*

## <a name="example"></a> Exemple

<!-- csl -->
```kusto
.enable plugin autocluster
``` 

## <a name="next-steps"></a>Étapes suivantes

* [`.disable plugin`](disable-plugin.md)
* [`.show plugins`](show-plugins.md)

