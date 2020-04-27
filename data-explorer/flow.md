---
title: Connecteur Microsoft Azure Data Explorer Flow (préversion)
description: En savoir plus sur l’utilisation du connecteur Microsoft Flow pour créer des flux de tâches planifiées ou déclenchées automatiquement.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 95b5e60c0993d3e364dc54ca7c06e06eaae876cb
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108284"
---
# <a name="microsoft-flow-connector-preview"></a>Connecteur Microsoft Flow (préversion)

Le connecteur Azure Data Explorer Flow permet à Azure Data Explorer d’utiliser les [capacités Flow de Microsoft Power Automate](https://flow.microsoft.com/) pour exécuter automatiquement des requêtes et commandes Kusto dans le cadre d’une tâche planifiée ou déclenchée.

Les scénarios d’utilisation incluent notamment :

* Envoi de rapports quotidiens contenant des tables et des graphiques
* Configuration des notifications en fonction des résultats de la requête
* Planification de commandes de contrôle sur des clusters
* Exportation et importation de données entre Azure Data Explorer et d’autres bases de données 

Pour plus d’informations, consultez [Exemples d’utilisation du connecteur Microsoft Flow](flow-usage.md).

##  <a name="log-in"></a>Se connecter 

1. Connectez-vous à [Microsoft Power Automate](https://flow.microsoft.com/).

1. Lorsque vous l’utilisez pour la première fois, vous êtes invité à vous connecter.

1. Sélectionnez **Se connecter** et entrez vos informations d’identification.

![Boîte de dialogue de connexion](./media/flow/flow-signin.png)

## <a name="authentication"></a>Authentification

Vous pouvez vous authentifier à l’aide des informations d’identification de l’utilisateur ou d’une application AAD.

> [!Note]
> Assurez-vous que votre application est une [application AAD](kusto/management/access-control/how-to-provision-aad-app.md) et qu’elle est autorisée à exécuter des requêtes sur votre cluster.

1. Sélectionnez les trois points en haut à droite du connecteur Microsoft Flow : ![Add a connection](./media/flow/flow-addconnection.png)

1. Sélectionnez **Ajouter une connexion**, puis sélectionnez **Se connecter avec le principal de service**.
![Sign in dialog](./media/flow/flow-signin.png)

1. Entrez les informations requises :
    * Nom de la connexion : Nom descriptif et explicite pour la nouvelle connexion
    * ID client : Votre ID d’application
    * Clé secrète client : Clé de votre application.
    * Locataire : ID du répertoire AAD dans lequel vous avez créé l’application. Par exemple, l’ID de locataire Microsoft est le suivant : 72f988bf-86f1-41af-91ab-2d7cd011db47

![Authentification de l’application](./media/flow/flow-appauth.png)

Une fois l’authentification terminée, vous verrez que votre flux utilise la connexion qui vient d’être ajoutée.

![Authentification de l’application terminée](./media/flow/flow-appauthcomplete.png)

Désormais, ce flux s’exécutera à l’aide des informations d’identification de l’application.

## <a name="find-the-azure-kusto-connector"></a>Rechercher le connecteur Azure Kusto

Pour utiliser le connecteur Microsoft Flow, vous devez d’abord ajouter un déclencheur. Un déclencheur peut être défini en fonction d’une période récurrente ou en réponse à une action de flux antérieure.

1. [Créez un flux](https://flow.microsoft.com/manage/flows/new) ou, à partir de la page d’accueil, sélectionnez l’action **Mes flux**, puis sélectionnez **+ Nouveau**.

    ![Créer un flux](./media/flow/flow-newflow.png)

1. Ajouter Planifié – à partir de zéro

    ![Nouveau flux planifié](./media/flow/flow-scheduled-from-blank.png)

1. Entrez les informations requises dans la page Créer un flux planifié.
    ![Build a scheduled flow](./media/flow/flow-build-scheduled-flow.png)
1. Sélectionnez **Create** (Créer).
1. Sélectionnez **+ Nouvelle étape**.
1. Dans la zone de recherche, entrez « Kusto ».

    ![Sélectionner les actions de flux](./media/flow/flow-actions.png)

1. Sélectionnez **Azure Data Explorer**.

## <a name="flow-actions"></a>Actions de flux

Lorsque vous ouvrez le connecteur Azure Data Explorer, vous pouvez ajouter à votre flux trois actions possibles.

Cette section décrit les capacités et les paramètres de chaque action Microsoft Flow.

![Actions de flux Azure Data Explorer](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>Exécuter une commande de contrôle et visualiser les résultats

Utilisez l’action « Exécuter la commande de contrôle et visualiser les résultats » pour exécuter une [commande de contrôle](kusto/management/index.md).

1. Spécifiez l’URL du cluster. Par exemple : `https://clusterName.eastus.kusto.windows.net`
1. Entrez le nom de la base de données.
1. Spécifiez la commande de contrôle :
    * Sélectionner du contenu dynamique à partir des applications et des connecteurs utilisés dans le flux
    * Ajouter une expression pour accéder aux valeurs, les convertir et les comparer
1. Pour envoyer les résultats de cette action par e-mail sous la forme d’un tableau ou d’un graphique, spécifiez le type de graphique, qui peut être :
    * Un tableau HTML
    * Un graphique à secteurs
    * Un graphique chronologique
    * Un diagramme en bâtons

![Exécuter la commande de contrôle Flow](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> Dans le champ *Nom du cluster*, entrez l’URL du cluster.

### <a name="run-query-and-list-results"></a>Exécuter la requête et répertorier les résultats

> [!Note]
> Si votre requête commence par un point (ce qui signifie qu’il s’agit d’une [commande de contrôle](kusto/management/index.md)), utilisez [Exécuter une commande de contrôle et visualiser les résultats](#run-control-command-and-visualize-results).

Cette action envoie une requête au cluster Kusto. Les actions ajoutées par la suite itèrent sur chaque ligne des résultats de la requête.

L’exemple suivant déclenche une requête chaque minute et envoie un e-mail en fonction des résultats de la requête. La requête vérifie le nombre de lignes dans la base de données, puis envoie un e-mail uniquement si le nombre de lignes est supérieur à 0. 

![Exécuter une requête et répertorier les résultats](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> Si la colonne comporte plusieurs lignes, le connecteur s’exécute pour chaque ligne de la colonne.

### <a name="run-query-and-visualize-results"></a>Exécuter une requête et visualiser les résultats
        
> [!Note]
> Si votre requête commence par un point (ce qui signifie qu’il s’agit d’une [commande de contrôle](kusto/management/index.md)), utilisez [Exécuter une commande de contrôle et visualiser les résultats](#run-control-command-and-visualize-results).
        
Utilisez l’action « Exécuter une requête et visualiser les résultats » pour visualiser le résultat de la requête Kusto sous forme de tableau ou de graphique. Par exemple, utilisez ce flux pour recevoir des rapport  ICM quotidiens par e-mail. 
    
Dans cet exemple, les résultats de la requête sont retournés sous la forme d’un tableau HTML.
            
![Exécuter une requête et visualiser les résultats](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> Dans le champ *Nom du cluster*, entrez l’URL du cluster.

## <a name="email-kusto-query-results"></a>Envoyer par e-mail les résultats de la requête Kusto

Vous pouvez inclure une étape à n’importe quel flux pour envoyer des rapports par e-mail à une adresse e-mail. 

1. Sélectionnez **+ Nouvelle étape** pour ajouter une nouvelle étape à votre flux.
1. Dans le champ de recherche, entrez Office 365 et sélectionnez **Office 365 Outlook**.
1. Sélectionnez **Envoyer un e-mail (V2)** .
1. Entrez l’adresse e-mail à laquelle vous souhaitez envoyer le rapport électronique.
1. Entrez l’objet de l’e-mail.
1. Sélectionnez **Mode Code**.
1. Placez le curseur dans le champ *Corps*, puis sélectionnez **Ajouter du contenu dynamique**.
1. Sélectionnez **BodyHtml**.
    ![Envoi d’e-mail](./media/flow/flow-send-email.png)
1. Sélectionnez **Afficher les options avancées**.
1. Dans le champ *Nom de pièces jointes -1*, sélectionnez **Nom de la pièce jointe**.
1. Dans le champ *Contenu des pièces jointes*, sélectionnez **Contenu de la pièce jointe**.
1. Le cas échéant, ajoutez d’autres pièces jointes. 
1. Le cas échéant, définissez le niveau d’importance.
1. Sélectionnez **Enregistrer**.

![Envoi de courrier électronique](./media/flow/flow-add-attachments.png)

## <a name="check-if-your-flow-succeeded"></a>Vérifier si votre flux a réussi

Pour vérifier si votre flux a réussi, consultez l’historique des exécutions du flux :
1. Accédez à la [page d’accueil de Microsoft Flow](https://flow.microsoft.com/).
1. Dans le menu principal, sélectionnez [Mes flux](https://flow.microsoft.com/manage/flows).
    ![My Flows page](./media/flow/flow-myflows.png)
1. Sur la ligne du flux que vous souhaitez examiner, sélectionnez l’icône « Plus de commandes », puis **Historique des exécutions**.

    ![Historique des exécutions, menu](./media/flow//flow-runhistory.png)

    Toutes les exécutions de flux sont répertoriées avec l’heure de début, la durée et l’état.
    ![Run history results page](./media/flow/flow-runhistoryresults.png)

    Pour obtenir des informations détaillées sur le flux, sélectionnez le flux que vous souhaitez examiner sur la page [Mes flux](https://flow.microsoft.com/manage/flows).

    ![Historique des exécutions, page des résultats complets](./media/flow/flow-fulldetails.png) 

Pour déterminer la raison de l’échec d’une exécution, sélectionnez l’heure de début de l’exécution. Le flux s’affiche et l’étape du flux qui a échoué est indiquée par un point d’exclamation rouge. Développez l’étape ayant échoué pour afficher les détails. Le volet de droite contient des informations sur l’échec afin que vous puissiez détecter le problème.

![Erreur de flux](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>Exceptions relatives au délai d’expiration

Votre flux peut échouer et retourner une exception « RequestTimeout » s’il s’exécute pendant plus de sept minutes.

En savoir plus sur les [limitations de Microsoft Flow](#limitations).
    
La même requête peut s’exécuter correctement dans Azure Data Explorer où le délai n’est pas limité et peut être modifié.
            
L’exception « RequestTimeout » est illustrée dans l’image ci-dessous :
    
![Erreur d’exception relative au délai d’expiration de la requête du flux](./media/flow/flow-requesttimeout.png)
    
Pour résoudre un problème de délai d’expiration, essayez de rendre votre requête plus efficace, afin qu’elle s’exécute plus rapidement, ou de la séparer en blocs. Chaque bloc peut s’exécuter sur une partie différente de la requête.

Pour plus d’informations, consultez [Meilleures pratiques relatives aux requêtes](kusto/query/best-practices.md).

## <a name="limitations"></a>Limites

* Les résultats renvoyés au client sont limités à 500 000 enregistrements. La mémoire totale de ces enregistrements ne peut pas dépasser 64 Mo et un temps d’exécution de 7 minutes.
* Le connecteur ne prend pas en charge les opérateurs de [fourche](kusto/query/forkoperator.md) et de [facette](kusto/query/facetoperator.md).
* Le fonctionnement de Flow est optimal sur Microsoft Edge et Chrome.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur le [connecteur Microsoft Azure Explorer Logic App](kusto/tools/logicapps.md), qui est une autre façon d’exécuter automatiquement des requêtes et des commandes Kusto dans le cadre d’une tâche planifiée ou déclenchée.
