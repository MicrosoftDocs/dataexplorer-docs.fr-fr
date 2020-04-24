---
title: Microsoft Flow Azure Kusto Connector (Avant-première) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Microsoft Flow Azure Kusto Connector (Preview) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: c4e732afb9e6165f803b9dc7e801b4e2be4c2588
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504095"
---
# <a name="microsoft-flow-azure-kusto-connector-preview"></a>Microsoft Flow Azure Kusto Connector (Avant-première)

Le connecteur Microsoft Flow Azure Kusto permet aux utilisateurs d’exécuter automatiquement des requêtes et des commandes Kusto dans le cadre d’une tâche planifiée ou déclenchée, à l’aide de [Microsoft Flow](https://flow.microsoft.com/).

Les scénarios d’utilisation incluent notamment :

* Envoi de rapports quotidiens contenant des tables et des graphiques
* Configuration des notifications en fonction des résultats de la requête
* Planification de commandes de contrôle sur des clusters
* Exportation et importation de données entre Azure Data Explorer et d’autres bases de données 

## <a name="limitations"></a>Limites

1. Les résultats retournés au client sont limités à 500 000 dossiers. La mémoire globale de ces enregistrements ne peut excéder 64 Mo et 7 minutes de temps d’exécution.
2. Actuellement, le connecteur ne prend pas en charge les opérateurs [de fourchette](../query/forkoperator.md) et [de facette.](../query/facetoperator.md)
3. Flow fonctionne mieux sur Internet Explorer et Chrome.

##  <a name="login"></a>Connexion 

1. Connectez-vous à [Microsoft Flow](https://flow.microsoft.com/).

1. Lorsque vous vous connectez au connecteur Azure Kusto pour la première fois, vous serez invité à vous connecter.

1. Cliquez sur le bouton **Signez** et entrez vos informations d’identification pour commencer à utiliser Azure Kusto Flow.

![texte de remplacement](./Images/KustoTools-Flow/flow-signin.png "flow-signin")

## <a name="authentication"></a>Authentification

L’authentification du connecteur Azure Kusto Flow peut être effectuée à l’aide d’informations d’identification de l’utilisateur ou d’une application AAD.



### <a name="aad-application-authentication"></a>Authentification de l’application AAD

Vous pouvez vous authentifier à Azure Kusto Flow avec une application AAD en utilisant les étapes suivantes :

> Remarque : Assurez-vous que votre application est une [application AAD](../management/access-control/how-to-provision-aad-app.md) et est autorisée à exécuter des requêtes sur votre cluster.

1. Cliquez sur les trois points en haut à droite du connecteur Azure Kusto: ![texte alt](./Images/KustoTools-Flow/flow-addconnection.png "flow-addconnection")

1. Sélectionnez « Ajouter une nouvelle connexion », puis cliquez sur « Connectez-vous avec Le principal de service ».
![texte de remplacement](./Images/KustoTools-Flow/flow-signin.png "flow-signin")

1. Remplissez l’ID d’application, la clé d’application et l’id du locataire.

Par exemple, Microsoft locataire ID est: 72f988bf-86f1-41af-91ab-2d7cd011db47.
La valeur Nom de connexion est une chaîne de votre choix destinée à reconnaître la nouvelle connexion ajoutée.
![texte de remplacement](./Images/KustoTools-Flow/flow-appauth.png "flux-appauth")

Une fois l’authentification terminée, vous pourrez voir que votre flux utilise la nouvelle connexion ajoutée.
![texte de remplacement](./Images/KustoTools-Flow/flow-appauthcomplete.png "flow-appauthcomplete")

A partir de maintenant, ce flux s’exécutera à l’aide des informations d’identification de l’application.

## <a name="find-the-azure-kusto-connector"></a>Trouver le connecteur Azure Kusto

Pour utiliser le connecteur Azure Kusto, vous devez d’abord ajouter un déclencheur. Un déclencheur peut être défini en fonction d’une période de temps récurrente ou d’une réponse à une action de flux précédente.

1. [Créez un nouveau flux.](https://flow.microsoft.com/manage/flows/new)
2. Ajoutez 'Schedule - Recurrence' comme première étape.
3. Tapez 'Azure Kusto' dans la boîte de recherche de la deuxième étape.

Maintenant, vous devriez être en mesure de voir 'Azure Kusto' comme on le voit dans l’image ci-dessous.

![texte de remplacement](./Images/KustoTools-Flow/flow-actions.png "flux-actions")

## <a name="azure-kusto-flow-actions"></a>Azure Kusto Flow Actions

Lors de la recherche du connecteur Azure Kusto dans Flow, vous verrez 3 actions possibles que vous pouvez ajouter à votre flux.

La section suivante décrit les capacités et les paramètres de chaque action Azure Kusto Flow.

![texte de remplacement](./Images/KustoTools-Flow/flow-actions.png "flux-actions")

### <a name="azure-kusto---run-query-and-visualize-results"></a>Azure Kusto - Exécuter la requête et visualiser les résultats

> Remarque : Si votre requête commence par un point (ce qui signifie qu’il s’agit d’une commande de [contrôle)](../management/index.md)veuillez utiliser [la commande de contrôle de course et visualiser les résultats](./flow.md#azure-kusto---run-control-command-and-visualize-results)

Pour visualiser le résultat de la requête Kusto comme une table ou comme un graphique, vous pouvez utiliser l’action 'Azure Kusto - Run requête et visualiser les résultats'. Les résultats de cette action peuvent être envoyés plus tard par e-mail. Vous pouvez utiliser ce flux, par exemple, au cas où vous souhaitez obtenir des rapports quotidiens ICM. 

![texte de remplacement](./Images/KustoTools-Flow/flow-runquery.png "flux-runquery")

Dans cet exemple, les résultats de la requête sont retournés sous forme de table HTML.

### <a name="azure-kusto---run-control-command-and-visualize-results"></a>Azure Kusto - Commande de contrôle d’exécution et visualisez les résultats

Semblable à l’action 'Azure Kusto - Run requête et visualisez les résultats', vous pouvez également exécuter une [commande de contrôle](../management/index.md) à l’aide de l’action 'Azure Kusto - Run control control command and visualize results'.
Les résultats de cette action peuvent être envoyés plus tard par e-mail comme un tableau ou un graphique.

![texte de remplacement](./Images/KustoTools-Flow/flow-runcontrolcommand.png "flow-runcontrolcommand")

Dans cet exemple, les résultats de la commande de contrôle sont rendus comme un graphique à secteurs.

### <a name="azure-kusto---run-query-and-list-results"></a>Azure Kusto - Exécuter la requête et la liste des résultats

> Remarque : Au cas où votre requête commencerait par un point (ce qui signifie qu’il s’agit d’une commande de [contrôle)](../management/index.md)s’il vous plaît utiliser [la commande de contrôle de course et visualiser les résultats](./flow.md#azure-kusto---run-control-command-and-visualize-results)

Cette action envoie une requête au cluster Kusto. Les actions qui sont ajoutées ensuite itérer sur chaque ligne des résultats de la requête.

L’exemple suivant déclenche une requête chaque minute et envoie un e-mail basé sur les résultats de la requête. La requête vérifie le nombre de lignes dans la base de données, puis envoie un e-mail seulement si le nombre de lignes est supérieur à 0. 

![texte de remplacement](./Images/KustoTools-Flow/flow-runquerylistresults.png "flow-runquerylistresults")

Notez qu’au cas où la colonne comporte plusieurs lignes, le connecteur suivant s’exécuterait pour chaque ligne de la colonne.

## <a name="email-kusto-query-results"></a>Envoyer un courriel aux résultats de la requête Kusto

Pour envoyer des rapports par courriel, faites les étapes suivantes : 

![texte de remplacement](./Images/KustoTools-Flow/flow-sendemail.png "flow-sendemail")

1. Cliquez sur 'Nouvelle étape', puis 'Ajouter une action'.
2. Dans la boîte de recherche, entrez 'Office 365 Outlook - Envoyer un e-mail'.
3. Définissez le 'To' à votre adresse e-mail, le 'Sujet' à un texte, et ajoutez 'Body' du contenu dynamique au champ 'Body'.
4. Cliquez sur 'Options avancées' ajouter 'Nom d’attachement' au champ 'Attachements Name', 'Attachment Content' au champ 'Fixations Content' et assurez-vous que 'Is HTML' est réglé sur 'Oui'.
5. À la barre supérieure, définissez le « nom Flow » pour ce flux.
6. Cliquez sur 'Créer le flux' et vous avez terminé!

## <a name="how-to-make-sure-flow-succeeded"></a>Comment s’assurer que le flux réussit?

Rendez-vous sur [Microsoft Flow Home Page](https://flow.microsoft.com/), cliquez sur Mes [flux,](https://flow.microsoft.com/manage/flows) puis cliquez sur le bouton i.

![texte de remplacement](./Images/KustoTools-Flow/flow-myflows.png "flux-myflows")

Tous les débits sont répertoriés avec l’état, l’heure de début et la durée.

![texte de remplacement](./Images/KustoTools-Flow/flow-runs.png "flux-runs")

Lors de l’ouverture de la dernière course du flux, si toutes les étapes du flux sont marquées avec un V vert, puis le flux s’est terminé avec succès.
Sinon, élargissez l’étape qui a été marquée par un point d’exclamation rouge pour afficher les détails de l’erreur.

![texte de remplacement](./Images/KustoTools-Flow/flow-failed.png "flux-échoué")

Certaines erreurs peuvent être facilement résolues par vous-même, par exemple des erreurs de syntaxe de requête :

![texte de remplacement](./Images/KustoTools-Flow/flow-syntaxerror.png "flux-syntaxerreur")



## <a name="having-a-timeout-exception"></a>Vous avez une exception de délai d’attente?

Votre flux peut échouer et retourner l’exception "RequestTimeout" si elle s’exécute plus de 7 minutes.

Notez que 7 minutes est le délai maximum requêtes peuvent courir avant qu’il y ait une exception de délai d’attente.

[Cliquez ici](#limitations) pour voir les limites de Flux Microsoft.

La même requête peut fonctionner avec succès dans Kusto Explorer où le temps n’est pas limité et peut être changé.

L’exception "RequestTimeout" est affichée dans l’image ci-dessous:

![texte de remplacement](./Images/KustoTools-Flow/flow-requesttimeout.png "débit-demandetimeout")

Pour résoudre le problème, vous pouvez suivre ces étapes :
1. En savoir plus sur [les meilleures pratiques de Requête](../query/best-practices.md).
2. Essayez de rendre votre requête plus efficace afin de le faire fonctionner plus rapidement, ou de le séparer en morceaux, chaque morceau peut fonctionner sur une partie différente de la requête.

## <a name="usage-examples"></a>Exemples d’utilisation

Cette section contient plusieurs exemples courants d’utilisation du connecteur Azure Kusto Flow.

### <a name="example-1---azure-kusto-flow-and-sql"></a>Exemple 1 - Azure Kusto Flow et SQL

Vous pouvez utiliser azure Kusto flux pour interroger les données, puis les accumuler dans un DB SQL. 
> Remarque : L’insert SQL se fait séparément par rangée, s’il vous plaît utiliser cela uniquement pour de faibles quantités de données de sortie. 

![texte de remplacement](./Images/KustoTools-Flow/flow-sqlexample.png "flow-sqlexample")

### <a name="example-2---push-data-to-power-bi-dataset"></a>Exemple 2 - Pousser les données à Power BI dataset

Le connecteur Azure Kusto Flow peut être utilisé avec le connecteur Power BI pour pousser les données des requêtes Kusto vers les jeux de données en streaming Power BI.

Pour commencer, créez une nouvelle action «Kusto - Run requête et liste des résultats».

![texte de remplacement](./Images/KustoTools-Flow/flow-listresults.png "flow-listresults")

Cliquez sur 'Nouvelle étape', sélectionnez 'Ajouter une action' et recherchez 'Power BI'. Cliquez sur 'Power BI - Ajouter des lignes à un jeu de données'. 

![texte de remplacement](./Images/KustoTools-Flow/flow-powerbiconnector.png "flow-powerbiconnector")

Remplissez l’espace de travail, le jeu de données et la table à laquelle les données seront poussées.
Ajoutez une charge utile contenant votre schéma de jeu de données et les résultats de requête Kusto pertinents de la fenêtre de contenu dynamique. 

![texte de remplacement](./Images/KustoTools-Flow/flow-powerbifields.png "flow-powerbifields")

Notez que Flow appliquera automatiquement l’action Power BI pour chaque rangée du tableau de résultat de la requête Kusto. 

![texte de remplacement](./Images/KustoTools-Flow/flow-powerbiforeach.png "flow-powerbiforeach")

### <a name="example-3---conditional-queries"></a>Exemple 3 - Requêtes conditionnelles

Les résultats des requêtes Kusto peuvent être utilisés comme entrée ou conditions pour les prochaines actions Flow.

Dans l’exemple suivant, nous interrogeons Kusto pour des incidents survenus dans la dernière journée. Pour chaque incident résolu, nous publions un message mou à ce sujet et créons une notification push.
Pour chaque incident qui est toujours actif, nous interrogeons Kusto pour plus d’informations sur des incidents similaires, envoyez cette information comme un courriel et ouvrons une tâche liée à TFS.

Suivez ces instructions pour créer un flux similaire.

Créez une nouvelle action «Kusto - Run requête et résultats de liste».
Cliquez sur 'Nouvelle étape' et sélectionnez 'Ajouter une condition' 

![texte de remplacement](./Images/KustoTools-Flow/flow-listresults.png "flow-listresults")

Choisissez parmi la fenêtre de contenu dynamique le paramètre que vous souhaitez utiliser comme condition pour les prochaines actions.
Sélectionnez le type de Relation et la Valeur pour définir une condition spécifique sur le paramètre donné.

![texte de remplacement](./Images/KustoTools-Flow/flow-condition.png "état de flux")

Notez que Flow appliquera cette condition sur chaque rangée du tableau de résultat de requête.

Ajoutez des actions à effectuer lorsque la condition a la valeur true ou false.

![texte de remplacement](./Images/KustoTools-Flow/flow-conditionactions.png "conditions de flux")

Vous pouvez utiliser les valeurs de résultat de la requête Kusto comme entrée pour les prochaines actions en les sélectionnant à partir de la fenêtre de contenu dynamique.
Ci-dessous, nous avons ajouté l’action 'Slack - Post Message' et 'Visual Studio - Create a new work item' action contenant des données de la requête Kusto.

![texte de remplacement](./Images/KustoTools-Flow/flow-slack.png "flux-mou")

![texte de remplacement](./Images/KustoTools-Flow/flow-visualstudio.png "flow-visualstudio")

Dans cet exemple, si un incident est toujours actif, nous interrogeons à nouveau Kusto pour obtenir des informations sur la façon dont les incidents de la même source ont été résolus dans le passé.

![texte de remplacement](./Images/KustoTools-Flow/flow-conditionquery.png "flow-conditionquery")

Nous visualisons cette information comme un graphique à secteurs et les écrivons à notre équipe.

![texte de remplacement](./Images/KustoTools-Flow/flow-conditionemail.png "flux-conditionemail")

### <a name="example-4---email-multiple-azure-kusto-flow-charts"></a>Exemple 4 - Envoyer un e-mail à plusieurs graphiques Azure Kusto Flow

Créez un nouveau flux avec un déclencheur « Récurrence » et définissez l’intervalle du flux et de la fréquence. 

Ajoutez une nouvelle étape, avec une ou plusieurs actions 'Kusto - Run requête et visualisez les résultats'. 

![texte de remplacement](./Images/KustoTools-Flow/flow-severalqueries.png "flux-severalqueries")

Pour chaque 'Kusto - Run requête et visualisez le résultat' définir les champs suivants:

Nom de cluster, Nom de base de données, requête et type graphique (Tableau html/ graphique à secteurs/ graphique à barres/ graphique à barres/ Saisie de la valeur personnalisée).

![texte de remplacement](./Images/KustoTools-Flow/flow-visualizeresultsmultipleattachments.png "flow-visualizeresultsmultipleattachments")

Après avoir terminé avec des actions 'Kusto - Run requête et visualisez le résultat', ajoutez l’action "Envoyer un e-mail". 

Assurez-vous d’insérer dans le champ "Corps" le corps requis, afin d’attacher le résultat visualisé de la requête au corps de l’e-mail.
En outre, afin d’ajouter une pièce jointe à l’e-mail, ajoutez «Nom d’attachement» et «Contenu d’attachement».

Assurez-vous de sélectionner "Oui" sous "est HTML" champ.

![texte de remplacement](./Images/KustoTools-Flow/flow-emailmultipleattachments.png "flow-emailmultipleattachments")

Résultats :

![texte de remplacement](./Images/KustoTools-Flow/flow-resultsmultipleattachments.png "flow-resultsmultipleattachments")

![texte de remplacement](./Images/KustoTools-Flow/flow-resultsmultipleattachments2.png "flow-resultsmultipleattachments2")

### <a name="example-5---send-a-different-email-to-different-contacts"></a>Exemple 5 - Envoyer un e-mail différent à différents contacts

Vous pouvez tirer parti d’Azure Kusto Flow pour envoyer différents e-mails personnalisés à différents contacts. Les adresses e-mail ainsi que le contenu de l’e-mail sont le résultat d’une requête Kusto.

Voir l'exemple ci-dessous :

![texte de remplacement](./Images/KustoTools-Flow/flow-dynamicemailkusto.png "flow-dynamicemailkusto")

![texte de remplacement](./Images/KustoTools-Flow/flow-dynamicemail.png "flow-dynamicemail")

### <a name="example-6---create-custom-html-table"></a>Exemple 6 - Créer une table HTML personnalisée

Vous pouvez tirer parti d’Azure Kusto Flow pour créer et utiliser des éléments HTML personnalisés tels qu’une table HTML personnalisée.

L’exemple suivant montre comment créer une table HTML personnalisée. La table HTML aura ses lignes colorées par le niveau de la rondins (les mêmes que dans l’explorateur Kusto).

Pour créer un flux similaire, suivez ces instructions :

Créez une nouvelle action «Kusto - Run requête et résultats de liste».

![texte de remplacement](./Images/KustoTools-Flow/flow-listresultforhtmltable.png "flow-listresultforhtmltable")

Bouclez les résultats de la requête et créez le corps de table HTML. Tout d’abord, créez une variable qui tiendra la chaîne HTML - cliquez sur 'New step', sélectionnez 'Ajouter une action' et recherchez 'Variables'. Cliquez sur 'Variables - Initialize variable'. Initialiser une variable de chaîne comme suit :

![texte de remplacement](./Images/KustoTools-Flow/flow-initializevariable.png "flux-initializevariable")

Bouclez-vous sur les résultats - cliquez sur 'Nouvelle étape' et choisissez 'Ajouter une action'. Recherche de 'Variables'. Sélectionnez 'Variables - Annexe à la variable de chaîne'. Choisissez le nom variable que vous avez pararés auparavant et créez vos lignes de table HTML à l’aide des résultats de la requête. Lors du choix des résultats de la requête, 'Apply to each' est ajouté automaitcally.

Dans l’exemple ci-dessous, l’expression suivante si l’expression est utilisée pour définir le style de chaque rangée:

si (égales (éléments (Apply_to_each')?[' Niveau'], 'Avertissement'), 'Jaune', si (éléments égaux (éléments ('Apply_to_each')?[' Niveau], 'Erreur'), 'rouge', 'blanc'))

![texte de remplacement](./Images/KustoTools-Flow/flow-createhtmltableloopcontent.png "flow-createhtmltableloopcontent")

Enfin, créez le contenu HTML complet. Ajoutez une nouvelle action à l’extérieur 'Apply to each'. Dans l’exemple suivant, l’action utilisée est «Envoyer un e-mail». Définissez votre table HTML à l’aide de la variable des étapes précédentes. Si vous envoyez un e-mail, cliquez sur 'Afficher les options avancées' et choisissez 'Oui' sous 'Is HTML'

![texte de remplacement](./Images/KustoTools-Flow/flow-customhtmltablemail.png "flow-customhtmltablemail")

et voici le résultat:

![texte de remplacement](./Images/KustoTools-Flow/flow-customhtmltableresult.png "flow-customhtmltableresult")






