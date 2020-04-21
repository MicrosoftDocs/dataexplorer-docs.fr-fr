---
title: Cohérence de requête - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la cohérence de requête dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: b66540af2d2d4bef4571249474cd618e69eb2261
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523101"
---
# <a name="query-consistency"></a>Cohérence des requêtes

Kusto prend en charge deux modèles de cohérence de requête : **fort** et **faible.**

Les requêtes fortement cohérentes (par défaut) ont une garantie de « lecture-mon-changements ». Un client qui envoie une commande de contrôle et qui reçoit une reconnaissance positive que la commande a été complétée avec succès sera assuré que toute requête immédiatement après observera les résultats de la commande.

Les requêtes faiblement cohérentes (doivent être explicitement activées par le client) ne font pas la garantie. Les clients qui font des requêtes peuvent observer une certaine latence (habituellement 1-2 minutes) entre les changements et les requêtes reflétant ces changements.

L’avantage des requêtes faiblement cohérentes est qu’il réduit la charge sur le nœud de cluster qui gère les modifications de base de données. En général, il est recommandé que les clients essaient d’abord le modèle fortement cohérent et passent à l’utilisation de la cohérence faible si absolument nécessaire.

Le passage à des requêtes faiblement cohérentes se fait en définissant la propriété lors de la `queryconsistency` prise [d’un appel REST API](../api/rest/request.md). Les utilisateurs du client Kusto .NET peuvent également le définir dans la [chaîne de connexion Kusto](../api/connection-strings/kusto.md) ou comme un drapeau dans les [propriétés de demande](../api/netfx/request-properties.md)du client .