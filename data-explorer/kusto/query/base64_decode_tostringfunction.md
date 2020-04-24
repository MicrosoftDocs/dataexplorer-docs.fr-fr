---
title: base64_decode_tostring() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit base64_decode_tostring() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: a821fb07d62d5bca3982cc4c48b8e0457641f3f2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518171"
---
# <a name="base64_decode_tostring"></a>base64_decode_tostring()

Décode une chaîne base64 sur une chaîne UTF-8

**Syntaxe**

`base64_decode_tostring(`*String*`)`

**Arguments**

* *Chaîne*: Chaîne d’entrée à décoder de la gamme base64 à la chaîne UTF8-8.

**Retourne**

Retourne la chaîne UTF-8 décodée à partir de la chaîne base64.

* Pour décoder les chaînes de base64 à un tableau de longues valeurs voir [base64_decode_toarray()](base64_decode_toarrayfunction.md)
* Pour l’encodage des chaînes à base64 chaîne voir [base64_encode_tostring()](base64_encode_tostringfunction.md)

**Exemple**

```kusto
print Quine=base64_decode_tostring("S3VzdG8=")
```

|Quine|
|-----|
|Kusto|

Essayer de décoder une chaîne base64 qui a été générée à partir d’un codage UTF-8 invalide sera de retour nul:

```kusto
print Empty=base64_decode_tostring("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Vide|
|-----|
||