---
title: Authentification sur HTTPs-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’authentification sur HTTPs dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: e0910089d87d6bce6124cb7e4560c2fa7b92b847
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617984"
---
# <a name="authentication-over-https"></a>Authentification sur HTTPs

Lors de l’utilisation du protocole HTTPs, le service `Authorization` prend en charge l’en-tête HTTP standard pour effectuer l’authentification.

Les méthodes d’authentification HTTP prises en charge sont les suivantes :

* **Azure Active Directory**, via la `bearer` méthode.

Lors de l’authentification à l' `Authorization` aide de Azure ad, l’en-tête a le format suivant :

```txt
Authorization: bearer TOKEN
```

Où `TOKEN` est le jeton d’accès que l’appelant acquiert en communiquant avec le service Azure ad. Le jeton a les propriétés suivantes :

* La ressource est l’URI du service (par exemple `https://help.kusto.windows.net`,)
* Le point de terminaison du service Azure AD est`https://login.microsoftonline.com/TENANT/`

Où `TENANT` est le nom ou l’ID de locataire Azure ad. Par exemple, les services créés sous le locataire Microsoft peuvent utiliser `https://login.microsoftonline.com/microsoft.com/`. Sinon, pour l’authentification utilisateur uniquement, la demande peut être effectuée à `https://login.microsoftonline.com/common/`.

> [!NOTE]
> Le point de terminaison de service Azure AD change lorsqu’il s’exécute dans des clouds nationaux.
> Pour modifier le point de terminaison, définissez une `AadAuthorityUri` variable d’environnement sur l’URI requis.

Pour plus d’informations, consultez la [vue d’ensemble](../../management/access-control/index.md) de l’authentification et le [Guide pour Azure ad l’authentification](../../management/access-control/how-to-authenticate-with-aad.md).
