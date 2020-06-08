---
title: Cohérence des requêtes-Azure Explorateur de données
description: Cet article décrit la cohérence des requêtes dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: 8b4d1df4dc9a035764f9d50a4f9c4dcf452da67e
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512263"
---
# <a name="query-consistency"></a>Cohérence des requêtes

Kusto prend en charge deux modèles de cohérence des requêtes : **fort** et **faible**.

Les requêtes fortement cohérentes (par défaut) disposent d’une garantie de « lecture-mes-changes ». Si vous envoyez une commande de contrôle et recevez un accusé de réception indiquant que la commande a été exécutée avec succès, vous êtes assuré que toute requête immédiatement après observe les résultats de la commande.

Les requêtes faiblement cohérentes qui doivent être activées explicitement par le client ne disposent pas de cette garantie. Les clients effectuant des requêtes peuvent observer une certaine latence (généralement de 1-2 minutes) entre les modifications et les requêtes reflétant ces modifications.

L’avantage des requêtes faiblement cohérentes est qu’elle réduit la charge sur le nœud de cluster qui gère les modifications de la base de données. En général, nous vous recommandons d’essayer d’abord le modèle fortement cohérent. Basculez vers en utilisant des requêtes peu cohérentes uniquement si nécessaire.

Pour basculer vers des requêtes faiblement cohérentes, définissez la `queryconsistency` propriété lors d’un [appel d’API REST](../api/rest/request.md). Les utilisateurs du client .NET peuvent également le définir dans la [chaîne de connexion Kusto](../api/connection-strings/kusto.md) ou en tant qu’indicateur dans les [Propriétés de demande du client](../api/netfx/request-properties.md).
