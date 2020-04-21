---
title: Embed Web UI dans un iframe - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’interface utilisateur Embed Web dans un iframe dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 9c5469a577c3e09cd433ec250f9215e5f450d5f5
ms.sourcegitcommit: 653bfb3edf32553c52ef36b339c8b80713a601b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81524461"
---
# <a name="embed-web-ui-in-an-iframe"></a>Embed Web UI dans un iframe

L’interface utilisateur Web Azure Data Explorer peut être intégrée dans un iframe et hébergée sur des sites Web tiers.

![texte de remplacement](../images/web-ux.jpg "Interface utilisateur Web Azure Data Explorer")

L’intégration de l’Azure Data Explorer Web UX dans votre site Web permet à vos utilisateurs de faire ce qui suit :

- Modifier les requêtes (inclut toutes les fonctionnalités linguistiques telles que la colorisation et l’intellisense)
- Explorez visuellement les schémas de table
- Authentifier à AAD
- Exécuter des requêtes
- Afficher les résultats d’exécution des requêtes
- Créer plusieurs onglets
- Enregistrer les requêtes localement
- Partager les requêtes par e-mail

Toutes les fonctionnalités sont testées pour l’accessibilité et supportent le thème sombre et léger.

## <a name="use-monaco-kusto-or-embed-the-web-ui"></a>Utiliser Monaco-Kusto ou intégrer l’interface utilisateur Web?

Monaco-Kusto vous offre une expérience d’édition telle que l’achèvement, la colorisation, le refactoring, le changement de nom et le go-to-definition, il vous oblige à construire une solution pour l’authentification, l’exécution de requête, l’affichage des résultats, et l’exploration de schéma autour d’elle. D’autre part, vous obtenez la flexibilité complète pour façonner l’expérience utilisateur qui correspond à vos besoins.

L’intégration de l’interface utilisateur Azure Data Explorer Web vous offre des fonctionnalités étendues avec peu d’effort, mais contient une flexibilité limitée en ce qui concerne l’expérience utilisateur. Il existe un ensemble fixe de paramètres de requête qui permettent un contrôle limité sur l’apparence et le comportement du système.

## <a name="how-to-embed-the-web-ui-in-an-iframe"></a>Comment intégrer l’interface utilisateur Web dans un iframe

### <a name="host-the-website-in-iframe"></a>Hébergez le site Web à iframe

Ajoutez le code suivant à votre site Web :

```html
<iframe
  src="https://dataexplorer.azure.com/clusters/<cluster>?ibizaPortal=true"
></iframe>
```

Le `ibizaPortal` paramètre de requête indique à l’interface utilisateur Web Azure Data Explorer _de ne pas_ rediriger pour obtenir un jeton d’authentification. Cela est nécessaire puisque le site d’hébergement est responsable de fournir un jeton d’authentification à l’iframe intégré.

Remplacez-le `<cluster>` par le nom d’hôte du cluster que `example: help.kusto.windows.net`vous souhaitez charger dans la vitre de connexion (pour ). Par défaut, le mode iframe-intégré ne fournit pas un moyen d’ajouter des clusters de l’interface utilisateur, puisque l’hypothèse est que le site Web d’hébergement est au courant du cluster requis.

### <a name="handle-authentication"></a>Gérer l’authentification

1. Lorsqu’il est configuré`ibizaPortal=true`en mode iframe (), l’interface utilisateur Web Azure Data Explorer ne tentera pas de rediriger pour l’authentification. L’interface utilisateur Web utilisera le mécanisme d’affichage de messages que les navigateurs utilisent pour demander et recevoir un jeton. Pendant le chargement de la page, le message suivant sera affiché sur la fenêtre parente :

   ```javascript
   window.parent.postMessage(
     {
       signature: "queryExplorer",
       type: "getToken"
     },
     "*"
   );
   window.addEventListener(
     "message",
     event => this.handleIncomingMessage(event),
     false
   );
   ```

1. Ensuite, il écoutera un message avec la structure suivante :

   ```json
   {
     "type": "postToken",
     "message": "${the actual authentication token}"
   }
   ```

1. Le jeton fourni devrait être un [jeton JWT](https://tools.ietf.org/html/rfc7519) obtenu à partir du [[point de terminaison d’authentification AAD]](../../management/access-control/how-to-authenticate-with-aad.md#web-client-javascript-authentication-and-authorization).

> [!IMPORTANT]
> Il est de la responsabilité de la fenêtre d’hébergement de rafraîchir le jeton avant l’expiration et d’utiliser le même mécanisme pour fournir le jeton mis à jour à l’application. Sinon, une fois le jeton expiré, les appels de service échoueront toujours.

### <a name="feature-flags"></a>Drapeaux caractéristiques

L’application d’hébergement peut vouloir contorsioner certains aspects de l’expérience utilisateur. Par exemple - masquer la vitre de connexion, ou désactiver la connexion à d’autres clusters.
Pour ce scénario, l’explorateur web prend en charge les drapeaux caractéristiques.

Un indicateur de fonctionnalité peut être utilisé dans l’URL comme paramètre de requête. Par exemple, si l’application d’hébergement souhaite désactiver l’ajout d’un autre cluster, ils doivent utiliserhttps://dataexplorer.azure.com/?ShowConnectionButtons=false

| Paramètre de                 | Description                                                                                                                                                                                                                                                                                       | Valeur par défaut |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- |
| ShowShareMenu           | Afficher l’élément du menu de partage                                                                                                                                                                                                                                                                          | true          |
| ShowConnectionButtons   | Afficher le bouton de connexion ajouter pour ajouter un nouveau cluster                                                                                                                                                                                                                                               | true          |
| ShowOpenNewWindowButton | Afficher "ouvert sur le bouton d’interface utilisateur web. cela ouvrira une nouvelle fenêtre de https://dataexplorer.azure.com navigateur qui pointera vers le bon cluster et base de données dans la portée                                                                                                                                   | false         |
| ShowFileMenu            | Afficher le menu du fichier (télécharger le contenu de l’onglet ETC)                                                                                                                                                                                                                                                     | true          |
| ShowTos ShowToS                 | Afficher le lien vers les conditions de service pour l’explorateur de données azur du dialogue paramètres                                                                                                                                                                                                                | true          |
| ShowPersona             | afficher le nom d’utilisateur en haut à droite et à partir du menu paramètres                                                                                                                                                                                                                                        | true          |
| IFrameAuth (en)              | Si c’est vrai, l’explorateur web s’attendra à ce que l’iframe gère l’authentification et fournisse un jeton par message. Ce sera toujours vrai pour les scénarios iframe                                                                                                                                              | false         |
| PersistAftereachRun     | Habituellement, l’explorateur web persistera dans l’événement onunload. lors de l’hébergement dans les iframes, il ne tire pas dans certains cas. ainsi, ce drapeau déclenchera persiting état local après chaque exécution de requête. Ainsi, toute perte de données qui se produit n’affectera que le texte qui n’avait jamais été exécuté limitant ainsi l’impact. | false         |
| SpectaclesSmoothIngestion     | Si c’est vrai, montrez l’expérience d’ingestion en 1 clic lorsque vous cliquez correctement sur une base de données                                                                                                                                                                                                                  | true          |
| RafraîchiConnection (en)       | Si c’est vrai, rafraîchit toujours le schéma lors du chargement de la page et ne dépend jamais du stockage local                                                                                                                                                                                                      | false         |
| ShowPageHeader          | Si c’est vrai, affiche l’en-tête de la page (qui inclut le titre Azure Data Explorer, les paramètres et autres)                                                                                                                                                                                                 | true          |
| HideConnectionPane      | Si c’est vrai, n’affiche pas le volet de connexion gauche                                                                                                                                                                                                                                                | false         |
| SkipMonacoFocusOnInit   | Fixe la question de mise au point lors de l’hébergement sur iframe                                                                                                                                                                                                                                                          | false         |

### <a name="feature-flag-presets"></a>Presets de drapeau de fonction

presets définira un tas de drapeaux de fonction à la fois.
Aujourd’hui, nous avons un seul préréglage.

`IbizaPortal`- Modifications des drapeaux suivants par défaut :

```json
ShowOpenNewWindowButton: true,
PersistAfterEachRun: true,
IFrameAuth: true,
ShowPageHeader: false,                                 |
```

> [!WARNING]
> Si vous utilisez un préréglage, vous ne serez pas en mesure d’ajouter des indicateurs de fonctionnalité supplémentaires sur le dessus de celui-ci. Si vous avez besoin de cette flexibilité, vous devez utiliser des indicateurs de fonctionnalités individuels.
