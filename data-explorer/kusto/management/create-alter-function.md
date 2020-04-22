---
title: .créer ou modifier la fonction - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la fonction .create-or-alter dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 9e9c24f7fda44d6c44b8f78d8622b525268a341a
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744270"
---
# <a name="create-or-alter-function"></a>.create-or-alter function

Crée une fonction stockée ou modifie une fonction existante et la stocke à l’intérieur des métadonnées de base de données.

```kusto
.create-or-alter function [with (docstring = '<description>', folder='<name>')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```

Si la fonction avec le *FunctionName* fourni n’existe pas dans les métadonnées de base de données, la commande crée une nouvelle fonction. Si la fonction existe déjà, cette fonction sera modifiée.

**Exemple**

```kusto
.create-or-alter function  with (docstring = 'Demo function with parameter', folder='MyFolder') TestFunction(myLimit:int)
{
    StormEvents | take myLimit 
} 
```

|Nom|Paramètres|body|Dossier|DocString (en)|
|---|---|---|---|---|
|TestFunction|(myLimit:int)|StormEvents &#124; prendre myLimit|MyFolder (en)|Fonction de démonstration avec paramètre|
