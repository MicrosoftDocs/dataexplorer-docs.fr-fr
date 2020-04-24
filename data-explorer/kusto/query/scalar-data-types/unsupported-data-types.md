---
title: Types de données scalaires non pris en jeu - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les types de données scalaires non pris en compte dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 76a548abdf05cec57e4d0558e5d98ab0f7cbdc3e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509586"
---
# <a name="unsupported-scalar-data-types"></a>Types de données scalaires non pris en soutien

Tous les **types de données scalaires** sans papiers sont considérés comme non pris en compte à Kusto.

Parmi les types non pris en soutien sont les suivants. Certains types ont déjà été pris en charge :

| Type       | Nom(s) supplémentaire(s)   | Type .NET équivalent              | Type de stockage (nom interne)|
| ---------- | -------------------- | --------------------------------- | ----------------------------|
| `float`    |                      | `System.Single`                   | `R32`                       |
| `int16`    |                      | `System.Int16`                    | `I16`                       |
| `uint16`   |                      | `System.UInt16`                   | `UI16`                      |
| `uint32`   | `uint`               | `System.UInt32`                   | `UI32`                      |
| `uint64`   | `ulong`              | `System.UInt64`                   | `UI64`                      |
| `uint8`    | `byte`               | `System.Byte`                     | `UI8`                       |