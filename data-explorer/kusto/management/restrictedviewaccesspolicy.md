---
title: Politique RestrictedViewAccess - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique de RestrictedViewAccess dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 6f994f5b80632650ab6dbe5dcf28cd82407d688f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520415"
---
# <a name="restrictedviewaccess-policy"></a>Politique RestrictedViewAccess

*RestrictedViewAccess* est une stratégie facultative qui peut être activée sur les tables d’une base de données.

Lorsque cette stratégie est activée sur une table, les données dans le tableau ne peuvent être interrogées *qu’aux* principes ajoutés au rôle [De Vision sans restriction](../management/access-control/role-based-authorization.md) dans la base de données.

Lorsqu’une stratégie est activée sur une table, tout mandant (même une table/base de données/administrateur de cluster) qui n’est pas inclus dans le rôle de base de données [De Visions sans restriction,](../management/access-control/role-based-authorization.md) ne sera pas en mesure d’interroger les données dans le tableau.

Les subventions de rôle [De Visionn sans restriction](../management/access-control/role-based-authorization.md) voient l’autorisation à toutes *les* tables de la base de données qui ont la stratégie activée, en supposant que le principal actuel est déjà autorisé à interroger la base de données (une base de données admin /utilisateur / spectateur). L’ajout ou la suppression des directeurs d’école à ou à partir du rôle peut être fait par un [DatabaseAdmin](../management/access-control/role-based-authorization.md).

> [!NOTE]
> La stratégie RestrictedViewAccess ne peut pas être configurée sur une table sur laquelle une [stratégie de sécurité au niveau de ligne](./rowlevelsecuritypolicy.md) est activée.

Pour en savoir plus sur les commandes de contrôle pour la gestion de la politique RestrictedViewAccess, [voir ici](../management/restrictedviewaccess-policy.md).