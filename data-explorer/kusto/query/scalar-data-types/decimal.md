---
title: Le type de données décimales - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le type de données décimales dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 62fd745c804ad4a50652e09cd722c17a4a61daac
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509892"
---
# <a name="the-decimal-data-type"></a>Le type de données décimales

Le `decimal` type de données représente un nombre décimal de 128 bits de large.

Les littérals du `decimal` type de données ont la même représentation que . NET `System.Data.SqlTypes.SqlDecimal`.

`decimal(1.0)`, `decimal(0.1)`, `decimal(1e5)` et sont tous `decimal`les littérals de type .

Il existe plusieurs formes littérales spéciales :
* `decimal(null)`: C’est la [valeur nulle](null-values.md).