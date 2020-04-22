---
title: API REST Azure Data Explorer - Azure Data Explorer | Microsoft Docs
description: Cet article décrit l’API REST Azure Data Explorer dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: 000561f96bd4174b94cca8e9db4c932a3cf3f0c6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490682"
---
# <a name="azure-data-explorer-rest-api"></a>API REST Azure Data Explorer

Cet article explique comment interagir avec Kusto via une connexion HTTPS.

## <a name="what-actions-are-supported"></a>Quelles sont les actions prises en charge ?

La liste des actions qui sont prises en charge par un point de terminaison diffère selon qu’il s’agit d’un point de terminaison de type « moteur » ou de type « gestion des données ».

|Action         |Verbe HTTP  |Modèle d’URI             |Moteur|Gestion des données|Authentification ?|
|---------------|-----------|-------------------------|------|---------------|---------------|
|Requête          |GET ou POST|`/v1/rest/query`         |Oui   |Non             |Oui            |
|Requête          |GET ou POST|`/v2/rest/query`         |Oui   |Non             |Oui            |
|Gestion     |POST       |`/v1/rest/mgmt`          |Oui   |Oui            |Oui            |
|UI             |GET        |`/`                      |Oui   |Non             |Non             |
|UI             |GET        |`/{dbname}`              |Oui   |Non             |Non             |

Où **Action** représente un groupe d’activités connexes :

* L’action **Interrogation** envoie une requête au service et récupère les résultats de la requête.
* L’action **Gestion** envoie une commande de contrôle au service et récupère les résultats de celle-ci.
* L’action **Interface utilisateur** peut être utilisée dans le but de démarrer un client pour ordinateur de bureau ou un client web (via une réponse de redirection HTTP) afin d’interagir avec le service.

Pour plus d’informations sur la requête et la réponse HTTP des actions d’interrogation et de gestion, consultez [Requête HTTP d’interrogation ou de gestion](./request.md), [Réponse HTTP d’interrogation ou de gestion](./response.md) et [Réponse HTTP d’interrogation v2](./response2.md). Pour plus d’informations sur l’action d’interface utilisateur, consultez [Interface utilisateur (lien ciblé)](./deeplink.md).