---
title: Incorporer l’interface utilisateur Web dans un **IFRAME** -Azure Explorateur de données
description: Cet article décrit l’interface utilisateur Web embed dans un **IFRAME** dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: c6b9d1d5cb971b5c9c51cf9f9918562f12323a03
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799660"
---
# <a name="embed-web-ui-in-an-iframe"></a>Incorporer l’interface utilisateur Web dans un IFRAME

L’interface utilisateur Web d’Azure Explorateur de données peut être incorporée dans un IFRAME et hébergée sur des sites Web tiers.

![texte de remplacement](../images/web-ux.jpg "Interface utilisateur Web d’Azure Explorateur de données")

L’intégration de l’expérience utilisateur Azure Explorateur de données Web à votre site Web permet à vos utilisateurs :

- Modifier les requêtes (comprend toutes les fonctionnalités de langage telles que la colorisation et IntelliSense)
- Explorer visuellement les schémas de table
- S’authentifier auprès de Azure AD
- Exécuter des requêtes
- Afficher les résultats de l’exécution de la requête
- Créer plusieurs onglets
- Enregistrer les requêtes localement
- Partager des requêtes par e-mail

Toutes les fonctionnalités sont testées pour l’accessibilité et prennent en charge les thèmes sombres et clairs à l’écran.

## <a name="use-monaco-kusto-or-embed-the-web-ui"></a>Utiliser Monaco-Kusto ou incorporer l’interface utilisateur Web ?

Monaco-Kusto améliore votre expérience de modification avec la saisie semi-automatique, la coloration, la refactorisation, le changement de nom et la définition. Elle vous permet de créer une solution pour l’authentification, l’exécution des requêtes, l’affichage des résultats et l’exploration des schémas. Monaco-Kusto vous offre également la flexibilité totale de l’expérience utilisateur qui répond à vos besoins.

L’incorporation de l’interface utilisateur Web d’Azure Explorateur de données vous offre des fonctionnalités très importantes. Toutefois, l’incorporation entraîne également une flexibilité limitée dans l’expérience utilisateur. Un ensemble fixe de paramètres de requête vous donne un contrôle limité sur l’apparence et le comportement du système.

## <a name="how-to-embed-the-web-ui-in-an-iframe"></a>Comment incorporer l’interface utilisateur Web dans un IFRAME

### <a name="host-the-website-in-an-iframe"></a>Héberger le site Web dans un IFRAME

Ajoutez le code suivant à votre site Web :

```html
<iframe
  src="https://dataexplorer.azure.com/clusters/<cluster>?ibizaPortal=true"
></iframe>
```

Le `ibizaPortal` paramètre de requête indique à l’interface utilisateur Web d’Azure Explorateur de données de *ne pas* effectuer de redirection pour obtenir un jeton d’authentification. Cette action est nécessaire, car le site Web d’hébergement est chargé de fournir un jeton d’authentification à l’iframe incorporé.

Remplacez `<cluster>` par le nom d’hôte du cluster que vous souhaitez charger dans le volet de connexion (par `help.kusto.windows.net`exemple :). Par défaut, le mode intégré iframe n’offre aucun moyen d’ajouter des clusters à partir de l’interface utilisateur, car le site Web d’hébergement est conscient du cluster requis.

### <a name="handle-authentication"></a>Gérer l’authentification

1. Quand la valeur est « iframe mode »`ibizaPortal=true`(), l’interface utilisateur Web d’Azure Explorateur de données ne tente pas de rediriger l’authentification. L’interface utilisateur Web utilise le mécanisme de publication de messages utilisé par les navigateurs pour demander et recevoir un jeton. Lors du chargement de la page, le message suivant est publié dans la fenêtre parente :

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

1. Ensuite, il écoute un message avec la structure suivante :

   ```json
   {
     "type": "postToken",
     "message": "${the actual authentication token}"
   }
   ```

1. Le jeton fourni doit être un [jeton JWT](https://tools.ietf.org/html/rfc7519) obtenu à partir du [[point de terminaison d’authentification AAD]](../../management/access-control/how-to-authenticate-with-aad.md#web-client-javascript-authentication-and-authorization).

> [!IMPORTANT]
> La fenêtre d’hébergement doit actualiser le jeton avant l’expiration et utiliser le même mécanisme pour fournir le jeton mis à jour à l’application. Dans le cas contraire, une fois le jeton expiré, les appels de service échouent.

### <a name="feature-flags"></a>Indicateurs de fonctionnalités

L’application d’hébergement peut souhaiter contrôler certains aspects de l’expérience utilisateur, tels que le masquage du volet connexion, ou la désactivation de la connexion à d’autres clusters.
Dans ce scénario, l’explorateur Web prend en charge les indicateurs de fonctionnalité.

Un indicateur de fonctionnalité peut être utilisé dans l’URL en tant que paramètre de requête. Par exemple, si l’application d’hébergement souhaite désactiver l’ajout d’autres clusters, ils doivent utiliserhttps://dataexplorer.azure.com/?ShowConnectionButtons=false

| Paramètre                 | Description                    | Valeur par défaut |
| ----------------------- | ------------------------------ | ------------- |
| ShowShareMenu           | Afficher l’élément de menu partager       | true          |
| ShowConnectionButtons   | Afficher le bouton **Ajouter une connexion** qui ajoute un nouveau cluster                                                                                                              | true          |
| ShowOpenNewWindowButton | Affichez le bouton **ouvrir dans le Web** qui ouvre une nouvelle fenêtre de navigateur. La fenêtre pointe vers https://dataexplorer.azure.com avec le cluster et la base de données appropriés dans l’étendue                                                                                                                        | false         |
| ShowFileMenu            | Afficher le menu fichier (**Télécharger**, **onglet**, **contenu**, etc.)                                                                                                      | true          |
| ShowToS                 | Afficher le **lien vers les conditions de service pour Azure Explorateur de données** à partir de la boîte de dialogue Paramètres                                                                                  | true          |
| ShowPersona             | Affichez le nom d’utilisateur à partir du menu paramètres, dans l’angle supérieur droit                                                                                                      | true          |
| IFrameAuth              | Si la valeur est true, l’explorateur Web s’attend à ce que l’iframe gère l’authentification et fournit un jeton via un message. Ce processus sera toujours vrai pour les scénarios iframe      | false         |
| PersistAfterEachRun     | En règle générale, l’explorateur Web persiste dans l’événement de déchargement (Notez qu’en cas d’hébergement dans IFRAMES, il ne se déclenche pas toujours). Cet indicateur déclenchera alors l' **État local persistant** après chaque exécution de la requête. Toute perte de données qui se produit n’affecte que le texte qui n’a jamais été exécuté et limite son impact. | false         |
| ShowSmoothIngestion     | Si la valeur est true, affiche l’expérience d’ingestion en 1 clic lorsque vous cliquez avec le bouton droit sur une base de données                                                                                        | true          |
| RefreshConnection       | Si la valeur est true, actualise toujours le schéma lors du chargement de la page et ne dépend jamais du stockage local                                                                          | false         |
| ShowPageHeader          | Si la valeur est true, affiche l’en-tête de page (qui comprend le titre et les paramètres Azure Explorateur de données)                                                                              | true          |
| HideConnectionPane      | Si la valeur est true, le volet de connexion gauche ne s’affiche pas                                                                                                                      | false         |
| SkipMonacoFocusOnInit   | Résout le problème de focus lors de l’hébergement sur iframe                                                                                                                            | false         |

### <a name="feature-flag-presets"></a>Présélections d’indicateurs de fonctionnalités

les présélections définissent une série d’indicateurs de fonctionnalités à la fois.
Actuellement, il n’existe qu’une seule présélection.

`IbizaPortal`: Modifie les valeurs par défaut des indicateurs suivants.

```json
ShowOpenNewWindowButton: true,
PersistAfterEachRun: true,
IFrameAuth: true,
ShowPageHeader: false,                                 |
```

> [!WARNING]
> Si vous utilisez une présélection, vous ne pourrez pas ajouter d’indicateurs de fonctionnalités supplémentaires par-dessus. Si vous avez besoin de cette flexibilité, vous devez utiliser des indicateurs de fonctionnalités individuels.
