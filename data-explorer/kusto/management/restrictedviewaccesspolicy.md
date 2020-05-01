---
title: Requêtes de contrôles de stratégie Kusto RestrictedViewAccess-Azure Explorateur de données
description: Cet article décrit la stratégie RestrictedViewAccess dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: e44aa2b14aa8babdab95a6ad8c6f7ef5ed026ff9
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617424"
---
# <a name="restrictedviewaccess-policy"></a>Stratégie RestrictedViewAccess

*RestrictedViewAccess* est une stratégie facultative qui peut être activée sur les tables d’une base de données.

Lorsque cette stratégie est activée sur une table, les données de la table ne peuvent être interrogées *que* sur les principaux ajoutés au rôle [UnrestrictedViewer](../management/access-control/role-based-authorization.md) dans la base de données.

Lorsqu’une stratégie est activée sur une table, tout principal (même une table/base de données/administrateur de cluster) qui n’est pas inclus dans le rôle [UnrestrictedViewer](../management/access-control/role-based-authorization.md) au niveau de la base de données ne peut pas interroger les données de la table.

Le rôle [UnrestrictedViewer](../management/access-control/role-based-authorization.md) accorde l’autorisation View à *toutes les* tables de la base de données pour lesquelles la stratégie est activée, en supposant que le principal actuel est déjà autorisé à interroger la base de données (administrateur de base de données/utilisateur/visionneuse). L’ajout ou la suppression de principaux dans le rôle peut être effectué par un [DatabaseAdmin](../management/access-control/role-based-authorization.md).

> [!NOTE]
> La stratégie RestrictedViewAccess ne peut pas être configurée sur une table pour laquelle une [stratégie de sécurité au niveau des lignes](./rowlevelsecuritypolicy.md) est activée.

Pour plus d’informations sur les commandes de contrôle pour la gestion de la stratégie RestrictedViewAccess, [voir ici](../management/restrictedviewaccess-policy.md).