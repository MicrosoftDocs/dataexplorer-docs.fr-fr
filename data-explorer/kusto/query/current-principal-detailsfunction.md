---
title: current_principal_details() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit current_principal_details() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/08/2019
ms.openlocfilehash: 5418647c811b034bb5790dfff3fd17f500c52db0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516777"
---
# <a name="current_principal_details"></a>current_principal_details()

Retourne les détails du principal exécutant la requête.

**Syntaxe**

`current_principal_details()`

**Retourne**

Les détails du principal `dynamic`actuel en tant que .

**Exemple**

```kusto
print d=current_principal_details()
```

|d|
|---|
|{<br>  "UserPrincipalName":user@fabrikam.com"<br>  "IdentityProvider":https://sts.windows.net"<br>  "Autorité": "72f988bf-86f1-41af-91ab-2d7cd011db47",<br>  "Mfa": "Vrai",<br>  "Type": "AadUser",<br>  "DisplayName": "James Smith (upn: user@fabrikam.com)",<br>  "ObjectId": "346e950e-4a62-42bf-96f5-4cf4eac3f11e",<br>  "FQN": nul,<br>  "Notes": nul<br>}|
