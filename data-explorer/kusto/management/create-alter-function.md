---
title: . Create-or-ALTER, fonction-Azure Explorateur de données
description: Cet article décrit la fonction. Create-or-ALTER dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: f19ca38f344f10b9dd8e4491b467eaad5ca022bc
ms.sourcegitcommit: a034b6a795ed5e62865fcf9340906f91945b3971
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85197240"
---
# <a name="create-or-alter-function"></a>.create-or-alter function

Crée une fonction stockée ou modifie une fonction existante et la stocke dans les métadonnées de la base de données.

```kusto
.create-or-alter function [with (docstring = '<description>', folder='<name>')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```

Si la fonction avec l' *nomfonction* fourni n’existe pas dans les métadonnées de la base de données, la commande crée une nouvelle fonction. Sinon, cette fonction sera modifiée.

**Exemple**

```kusto
.create-or-alter function  with (docstring = 'Demo function with parameter', folder='MyFolder') TestFunction(myLimit:int)
{
    StormEvents | take myLimit 
} 
```

|Nom|Paramètres|Corps|Dossier|DocString|
|---|---|---|---|---|
|TestFunction|(myLimit : int)|{StormEvents &#124; Take myLimit}|Mondossier|Fonction Demo avec un paramètre|
