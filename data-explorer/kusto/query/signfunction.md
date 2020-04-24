---
title: signe() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le signe () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 86a57ed2fb7d43daf300731fe48233eca318bded
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507563"
---
# <a name="sign"></a>sign()

Signe d’une expression numérique

**Syntaxe**

`sign(`*X*`)`

**Arguments**

* *x*: Un vrai nombre.

**Retourne**

* Le signe positif (1), zéro (0) ou négatif (-1) de l’expression spécifiée. 

**Exemples**

```kusto
print s1 = sign(-42), s2 = sign(0), s3 = sign(11.2)

```

|s1|s2|s3|
|---|---|---|
|-1|0|1|