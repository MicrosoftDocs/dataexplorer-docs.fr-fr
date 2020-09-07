---
title: API REST Azure Data Explorer - Azure Data Explorer
description: Cet article décrit l’API REST Azure Data Explorer dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: e7598ccc2c04ab54a27830ba16e2b307e84bba2c
ms.sourcegitcommit: 9e0289945270db517e173aa10024e0027b173b52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89428275"
---
# <a name="azure-data-explorer-rest-api"></a>API REST Azure Data Explorer

Cet article explique comment interagir avec Azure Data Explorer via une connexion HTTPS.

## <a name="supported-actions"></a>Actions prises en charge

La liste des actions qui sont prises en charge par un point de terminaison diffère selon qu’il s’agit d’un point de terminaison de type « moteur » ou de type « gestion des données ».

|Action         |Verbe HTTP   |Modèle d’URI           |Moteur|Gestion des données|Authentification |
|---------------|------------|-----------------------|------|---------------|---------------|
|Requête          |GET ou POST |/v1/rest/query         |Oui   |Non             |Oui            |
|Requête          |GET ou POST |/v2/rest/query         |Oui   |Non             |Oui            |
|Gestion     |POST        |/v1/rest/mgmt          |Oui   |Oui            |Oui            |
|UI             |GET         |/                      |Oui   |Non             |Non             |
|UI             |GET         |/{dbname}              |Oui   |Non             |Non             |

Où *Action* représente un groupe d’activités connexes

* L’action Interrogation envoie une requête au service et récupère les résultats de la requête.
* L’action Gestion envoie une commande de contrôle au service et récupère les résultats de celle-ci.
* L’action Interface utilisateur peut être utilisée pour démarrer un client de bureau ou un client web. L’action est effectuée via une réponse de redirection HTTP pour interagir avec le service.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la requête HTTP et la réponse aux actions d’interrogation et de gestion, consultez :
 * [Requête HTTP d’interrogation ou de gestion](./request.md)
 * [Réponse HTTP d’interrogation ou de gestion](./response.md)
 * [Réponse HTTP d’interrogation v2](./response2.md)

Pour plus d’informations sur l’action Interface utilisateur, consultez :
 * [Lien ciblé vers l’interface utilisateur](./deeplink.md)
