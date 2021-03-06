---
title: Connecteur Azure Data Explorer vers Power Automate (préversion)
description: Découvrez l’utilisation du connecteur Azure Data Explorer vers Power Automate pour créer des flux de tâches planifiées ou déclenchées automatiquement.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: 9f537011cb8030b7d82189df169b0447de488430
ms.sourcegitcommit: 2605250764561a295291b2862cd145572e0036c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2020
ms.locfileid: "97708277"
---
# <a name="azure-data-explorer-connector-to-power-automate-preview"></a>Connecteur Azure Data Explorer vers Power Automate (préversion)

Le connecteur Azure Data Explorer Power Automate (anciennement Microsoft Flow) permet à Azure Data Explorer d’utiliser les fonctionnalités de flux de [Microsoft Power Automate](https://flow.microsoft.com/). Vous pouvez exécuter automatiquement des requêtes et des commandes Kusto dans le cadre d’une tâche planifiée ou déclenchée.

Vous pouvez :

* Envoyer des rapports quotidiens contenant des tableaux et des graphiques.
* Définir des notifications en fonction des résultats des requêtes.
* Planifier des commandes de contrôle sur des clusters.
* Exporter et importer des données entre Azure Data Explorer et d’autres bases de données. 

Pour plus d’informations, consultez [Exemples d’utilisation du connecteur Azure Data Explorer Power Automate](flow-usage.md).

##  <a name="sign-in"></a>Se connecter 

1. Quand vous vous connectez pour la première fois, vous êtes invité à entrer vos informations de connexion.

1. Sélectionnez **Se connecter**, puis entrez vos informations d’identification.

![Capture d’écran de l’invite de connexion à Azure Data Explorer](./media/flow/flow-signin.png)

## <a name="authentication"></a>Authentification

Vous pouvez vous authentifier avec des informations d’identification d’utilisateur ou avec une application Azure Active Directory (Azure AD).

> [!Note]
> Vérifiez que votre application est une [application Azure AD](./provision-azure-ad-app.md) et qu’elle est autorisée à exécuter des requêtes sur votre cluster.

1. Dans **Exécuter une commande de contrôle et visualiser les résultats**, sélectionnez les points de suspension situés en haut à droite du connecteur de flux.

   ![Capture d’écran de l’option « Exécuter une commande de contrôle et visualiser les résultats »](./media/flow/flow-addconnection.png)

1. Sélectionnez **Ajouter une nouvelle connexion** > **Se connecter avec le principal de service**.

   ![Capture d’écran de l’invite de connexion à Azure Data Explorer, avec l’option Se connecter avec le principal du service](./media/flow/flow-signin.png)

1. Entrez les informations requises :
   - Nom de la connexion : Nom descriptif et explicite pour la nouvelle connexion.
   - ID client : Votre ID d’application.
   - Clé secrète client : Clé de votre application.
   - Locataire : ID de l’annuaire Azure AD dans lequel vous avez créé l’application.

   ![Capture d’écran de la boîte de dialogue d’authentification d’application Azure Data Explorer](./media/flow/flow-appauth.png)

Une fois l’authentification terminée, vous verrez que votre flux utilise la connexion qui vient d’être ajoutée.

![Capture d’écran d’une authentification d’application terminée](./media/flow/flow-appauthcomplete.png)

À partir de maintenant, ce flux s’exécutera en utilisant ces informations d’identification d’application.

## <a name="find-the-azure-kusto-connector"></a>Rechercher le connecteur Azure Kusto

Pour utiliser le connecteur Power Automate, vous devez d’abord ajouter un déclencheur. Vous pouvez définir un déclencheur en fonction d’une période récurrente ou en réponse à une action de flux antérieure.

1. [Créez un flux](https://flow.microsoft.com/manage/flows/new) ou, à partir de la page d’accueil Microsoft Power Automate, sélectionnez **Mes flux** >  **+ Nouveau**.

    ![Capture d’écran de la page d’accueil Microsoft Power Automate, avec « Mes flux » et « Nouveau » mis en évidence](./media/flow/flow-newflow.png)

1. Sélectionnez **Planifié - à partir de zéro**.

    ![Capture d’écran de la boîte de dialogue Nouveau, avec « Planifié » mis en évidence](./media/flow/flow-scheduled-from-blank.png)

1. Dans **Créer un flux planifié**, entrez les informations nécessaires.
    
    ![Capture d’écran de la page Créer un flux planifié, avec les options « Nom du flux » mises en évidence](./media/flow/flow-build-scheduled-flow.png)

1. Sélectionnez **Créer** >  **+ Nouvelle étape**.
1. Dans la zone de recherche, entrez *Kusto*, puis sélectionnez **Azure Data Explorer**.

    ![Capture d’écran des options « Choisir une action », avec la zone de recherche et Azure Data Explorer mis en évidence](./media/flow/flow-actions.png)

## <a name="flow-actions"></a>Actions de flux

Lorsque vous ouvrez le connecteur Azure Data Explorer, vous pouvez ajouter à votre flux trois actions possibles. Cette section décrit les fonctionnalités et les paramètres de chaque action.

![Capture d’écran des actions du connecteur Azure Data Explorer](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>Exécuter une commande de contrôle et visualiser les résultats

Utilisez cette action pour exécuter une [commande de contrôle](kusto/management/index.md).

1. Spécifiez l’URL du cluster. Par exemple : `https://clusterName.eastus.kusto.windows.net`.
1. Entrez le nom de la base de données.
1. Spécifiez la commande de contrôle :
   * Sélectionnez du contenu dynamique des applications et des connecteurs utilisés dans le flux.
   * Ajoutez une expression pour accéder aux valeurs, les convertir et les comparer.
1. Pour envoyer les résultats de cette action par e-mail sous la forme d’un tableau ou d’un graphique, spécifiez le type de graphique. Il peut s’agir des ordinateurs suivants :
   * Un tableau HTML.
   * Un graphique à secteurs.
   * Un graphique de temps.
   * Un graphique à barres.

![Capture d’écran de l’option « Exécuter une commande de contrôle et visualiser les résultats » dans le volet de récurrence](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> Dans le champ **Nom du cluster**, entrez l’URL du cluster.

### <a name="run-query-and-list-results"></a>Exécuter la requête et répertorier les résultats

> [!Note]
> Si votre requête commence par un point (ce qui signifie qu’il s’agit d’une [commande de contrôle](kusto/management/index.md)), utilisez [Exécuter une commande de contrôle et visualiser les résultats](#run-control-command-and-visualize-results).

Cette action envoie une requête au cluster Kusto. Les actions ajoutées par la suite itèrent sur chaque ligne des résultats de la requête.

L’exemple suivant déclenche une requête chaque minute et envoie un e-mail en fonction des résultats de la requête. La requête vérifie le nombre de lignes dans la base de données, puis envoie un e-mail uniquement si le nombre de lignes est supérieur à 0. 

![Capture d’écran de l’option « Exécuter la requête et répertorier les résultats »](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> Si la colonne comporte plusieurs lignes, le connecteur s’exécute pour chaque ligne de la colonne.

### <a name="run-query-and-visualize-results"></a>Exécuter une requête et visualiser les résultats
        
> [!Note]
> Si votre requête commence par un point (ce qui signifie qu’il s’agit d’une [commande de contrôle](kusto/management/index.md)), utilisez [Exécuter une commande de contrôle et visualiser les résultats](#run-control-command-and-visualize-results).
        
Utilisez cette action pour visualiser le résultat d’une requête Kusto sous forme de tableau ou de graphique. Par exemple, utilisez ce flux pour recevoir des rapports quotidiens par e-mail. 
    
Dans cet exemple, les résultats de la requête sont retournés sous la forme d’un tableau HTML.
            
![Capture d’écran de l’option « Exécuter une requête et visualiser les résultats »](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> Dans le champ **Nom du cluster**, entrez l’URL du cluster.

## <a name="email-kusto-query-results"></a>Envoyer par e-mail les résultats de la requête Kusto

Vous pouvez inclure une étape dans n’importe quel flux pour envoyer quelle adresse e-mail. 

1. Sélectionnez **+ Nouvelle étape** pour ajouter une nouvelle étape à votre flux.
1. Dans la zone de recherche, entrez *Office 365*, puis sélectionnez **Office 365 Outlook**.
1. Sélectionnez **Envoyer un e-mail (V2)** .
1. Entrez l’adresse e-mail à laquelle vous voulez envoyer le rapport par e-mail.
1. Entrez l’objet de l’e-mail.
1. Sélectionnez **Mode Code**.
1. Placez le curseur dans le champ **Corps**, puis sélectionnez **Ajouter du contenu dynamique**.
1. Sélectionnez **BodyHtml**.
    ![Capture d’écran de la boîte de dialogue « Envoyer un e-mail », avec le champ « Corps » et « BodyHtml » mis en évidence](./media/flow/flow-send-email.png)
1. Sélectionnez **Afficher les options avancées**.
1. Sous **Nom des pièces jointes -1**, sélectionnez **Nom de la pièce jointe**.
1. Sous **Contenu des pièces jointes**, sélectionnez **Contenu de la pièce jointe**.
1. Le cas échéant, ajoutez d’autres pièces jointes. 
1. Le cas échéant, définissez le niveau d’importance.
1. Sélectionnez **Enregistrer**.

![Capture d’écran de la boîte de dialogue « Envoyer un e-mail », avec « Nom des pièces jointes », « Contenu des pièces jointes » et « Enregistrer » mis en évidence](./media/flow/flow-add-attachments.png)

## <a name="check-if-your-flow-succeeded"></a>Vérifier si votre flux a réussi

Pour vérifier si votre flux a réussi, consultez l’historique des exécutions du flux :
1. Accédez à la [page d’accueil Microsoft Power Automate](https://flow.microsoft.com/).
1. Dans le menu principal, sélectionnez [Mes flux](https://flow.microsoft.com/manage/flows).
   
   ![Capture d’écran du menu principal de Microsoft Power Automate, avec « Mes flux » mis en évidence](./media/flow/flow-myflows.png)

1. Sur la ligne du flux que vous voulez examiner, sélectionnez l’icône « Plus de commandes », puis **Historique des exécutions**.

    ![Capture d’écran de l’onglet « Mes flux », avec « Historique des exécutions » mis en évidence](./media/flow//flow-runhistory.png)

    Toutes les exécutions de flux sont listées, avec des informations concernant l’heure de début, la durée et l’état.
    ![Capture d’écran de la page de résultats Historique des exécutions](./media/flow/flow-runhistoryresults.png)

    Pour obtenir des informations détaillées sur le flux, sur **[Mes flux](https://flow.microsoft.com/manage/flows)** , sélectionnez le flux que vous voulez examiner.

    ![Capture d’écran de la page des résultats complets Historique des exécutions](./media/flow/flow-fulldetails.png) 

Pour déterminer la raison de l’échec d’une exécution, sélectionnez l’heure de début de l’exécution. Le flux s’affiche, et l’étape du flux qui a échoué est indiquée par un point d’exclamation rouge. Développez l’étape ayant échoué pour afficher les détails. Le volet **Détails** situé à droite contient des informations sur l’échec afin que vous puissiez résoudre le problème.

![Capture d’écran de la page d’erreur de flux](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>Exceptions relatives au délai d’expiration

Votre flux peut échouer et retourner une exception « RequestTimeout » s’il s’exécute pendant plus de 90 secondes.
    
![Capture d’écran de l’erreur d’exception relative au délai d’expiration de la requête du flux](./media/flow/flow-requesttimeout.png)

Pour résoudre un problème de délai d’expiration, améliorez l’efficacité de votre requête afin qu’elle s’exécute plus rapidement, ou séparez-la en blocs. Chaque bloc peut s’exécuter sur une partie différente de la requête. Pour plus d’informations, consultez [Bonnes pratiques relatives aux requêtes](kusto/query/best-practices.md).

La même requête peut s’exécuter correctement dans Azure Data Explorer, où le délai n’est pas limité et peut être changé.

## <a name="limitations"></a>Limites

* Les résultats renvoyés au client sont limités à 500 000 enregistrements. La mémoire totale de ces enregistrements ne peut pas dépasser 64 Mo et un temps d’exécution de 90 secondes.
* Le connecteur ne prend pas en charge les opérateurs [fork](kusto/query/forkoperator.md) et [facet](kusto/query/facetoperator.md).
* Le fonctionnement du flux est optimal sur Microsoft Edge et Google Chrome.

## <a name="next-steps"></a>Étapes suivantes

Découvrez le [connecteur Logic Apps Azure Kusto](kusto/tools/logicapps.md), qui est une autre façon d’exécuter automatiquement des requêtes et des commandes Kusto dans le cadre d’une tâche planifiée ou déclenchée.
