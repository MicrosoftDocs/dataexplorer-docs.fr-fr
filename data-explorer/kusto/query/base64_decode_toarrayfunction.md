---
title: base64_decode_toarray() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit base64_decode_toarray() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 80a702f112fd4d7b88b4011b3f615cf34acff62c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518188"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

Décode une chaîne base64 à un éventail de valeurs longues.

**Syntaxe**

`base64_decode_toarray(`*String*`)`

**Arguments**

* *Chaîne*: Chaîne d’entrée à décoder de la gamme base64 à la chaîne UTF8-8.

**Retourne**

Retourne la gamme de valeurs longues écoded de la chaîne base64.

* Pour décoder les cordes de base64 à une chaîne UTF-8 voir [base64_decode_tostring()](base64_decode_tostringfunction.md)
* Pour l’encodage des chaînes à base64 chaîne voir [base64_encode_tostring()](base64_encode_tostringfunction.md)

**Exemple**

```kusto
print Quine=base64_decode_toarray("S3VzdG8=")  
// 'K', 'u', 's', 't', 'o'
```

|Quine|
|-----|
|[75,117,115,116,111]|

Essayer de décoder une chaîne base64 qui a été générée à partir d’un codage UTF-8 invalide sera de retour nul:

```kusto
print Empty=base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Vide|
|-----|
||