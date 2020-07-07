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
ms.openlocfilehash: be9eea23373489faf44c70146109fe1488877269
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967551"
---
# <a name="restricted-view-access-policy"></a>Stratégie d’accès avec vue limitée

*RestrictedViewAccess* est une stratégie facultative qui peut être activée pour les tables d’une base de données.

Lorsque cette stratégie est activée pour une table, les données de la table peuvent uniquement être interrogées par des principaux qui ont un rôle [UnrestrictedViewer](../management/access-control/role-based-authorization.md) dans la base de données.
N’importe quel principal, qui n’est pas inscrit avec un rôle au niveau de la base de données [UnrestrictedViewer](../management/access-control/role-based-authorization.md) , ne peut pas interroger les données de la table. Même une table/base de données/administrateur de cluster non inscrite.

Le rôle [UnrestrictedViewer](../management/access-control/role-based-authorization.md) accorde l’autorisation View à *toutes les* tables de la base de données pour lesquelles la stratégie est activée.
Le principal actuel, administrateur de base de données/utilisateur/visionneuse, est déjà autorisé à interroger la base de données. L’ajout ou la suppression de principaux peut être effectué par un [DatabaseAdmin](../management/access-control/role-based-authorization.md).

> [!NOTE]
> La stratégie RestrictedViewAccess ne peut pas être configurée sur une table pour laquelle une [stratégie de sécurité au niveau des lignes](./rowlevelsecuritypolicy.md) est activée.

Pour plus d’informations, consultez les commandes de contrôle pour la gestion de la [stratégie RestrictedViewAccess](../management/restrictedviewaccess-policy.md).
