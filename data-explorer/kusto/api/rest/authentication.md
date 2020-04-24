---
title: Authentification sur HTTPS - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’authentification sur HTTPS dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 4b6fbf5bb34dc3ff52938c7042778a7e49fd5faf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503041"
---
# <a name="authentication-over-https"></a>Authentification sur HTTPS

Lors de l’utilisation de `Authorization` HTTPS, le service prend en charge l’en-tête HTTP standard pour effectuer l’authentification.

Les méthodes d’authentification HTTP prises en charge sont les suivante :

* **Azure Active Directory** `bearer` , à travers la méthode.

Lors de l’authentification `Authorization` à l’aide d’Azure Active Directory, l’en-tête a le format:

```txt
Authorization: bearer TOKEN
```

Où `TOKEN` est le jeton d’accès que l’appelant acquiert en communiquant avec le service d’annuaire actif Azure, avec les propriétés suivantes :

* La ressource est le service URI `https://help.kusto.windows.net`(p. ex., ).
* Le point de terminaison `https://login.microsoftonline.com/TENANT/`du service Azure Active Directory est .

Où `TENANT` se trouve le locataire ou le nom du locataire d’Azure Active Directory. Par exemple, les services créés `https://login.microsoftonline.com/microsoft.com/`sous le locataire de Microsoft peuvent utiliser . Alternativement, pour l’authentification de `https://login.microsoftonline.com/common/` l’utilisateur seulement, la demande peut être faite à la place.

> [!NOTE]
> Le point de terminaison du service Azure Active Directory change lors de l’exécution dans les nuages nationaux.
> Pour modifier le point de terminaison `AadAuthorityUri` qui sera utilisé, définissez une variable d’environnement à l’URI requise.

Pour plus d’information, consultez [l’avant-première de l’authentification](../../management/access-control/index.md) et [guide de l’authentification Azure Active Directory](../../management/access-control/how-to-authenticate-with-aad.md).