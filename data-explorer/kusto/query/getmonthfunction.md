---
title: getmonth() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit getmonth() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: b0224d8ea9c99ce72604a5b7df248394bc6317fa
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514414"
---
# <a name="getmonth"></a>getmonth()

Obtient le numéro du mois (1 à 12) à partir d’une valeur datetime.

Un autre alias: monthoyear()

**Exemple**

```kusto
print month = getmonth(datetime(2015-10-12))
```