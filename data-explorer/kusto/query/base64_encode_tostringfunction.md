---
title: base64_encode_tostring() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit base64_encode_tostring() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: a80b0aa0e3f7e5f330da87f93bbad44e587bcdaf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518052"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

Code une chaîne comme chaîne base64

**Syntaxe**

`base64_encode_tostring(`*String*`)`

**Arguments**

* *Chaîne*: Chaîne d’entrée à encoder sous forme de chaîne base64.

**Retourne**

Retourne la chaîne codée sous forme de corde base64.

* Pour décoder les cordes de base64 à une chaîne UTF-8 voir [base64_decode_tostring()](base64_decode_tostringfunction.md)
* Pour décoder les chaînes de base64 à un tableau de longues valeurs voir [base64_decode_toarray()](base64_decode_toarrayfunction.md)


**Exemple**

```kusto
print Quine=base64_encode_tostring("Kusto")
```

|Quine   |
|--------|
|S3VzdG8|
