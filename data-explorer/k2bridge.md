---
title: Visualiser des données Azure Data Explorer avec Kibana
description: Dans cet article, vous allez apprendre à configurer Azure Data Explorer en tant que source de données pour Kibana.
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: bf479a7248033d2aa70a8e09b039814361c78031
ms.sourcegitcommit: bcd0c96b1581e43e33aa35f4d68af6dcb4979d39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88039231"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Visualiser des données à partir d’Azure Data Explorer dans Kibana avec le connecteur open source K2Bridge

K2Bridge (Kibana-Kusto Bridge) vous permet d’utiliser Azure Data Explorer comme source de données et de visualiser ces données dans Kibana. K2Bridge est une application conteneurisée [open source](https://github.com/microsoft/K2Bridge). Elle fait office de proxy entre une instance Kibana et un cluster Azure Data Explorer. Cet article explique comment utiliser K2Bridge pour créer cette connexion.

K2Bridge traduit les requêtes Kibana en langage de requête Kusto (KQL) et renvoie les résultats d’Azure Data Explorer à Kibana.

   ![Connexion Kibana avec Azure Data Explorer via K2Bridge](media/k2bridge/k2bridge-chart.png)

K2Bridge prend en charge l’onglet **Discover** (Découvrir) de Kibana, où vous pouvez :

* Effectuer une recherche dans les données et les explorer
* Filtrer les résultats
* Ajouter ou supprimer des champs dans la grille de résultats
* Afficher le contenu des enregistrements
* Enregistrer et partager les recherches

L’image suivante montre une instance Kibana liée à Azure Data Explorer par K2Bridge. L’expérience utilisateur dans Kibana est inchangée.

   [![Page Kibana liée à Azure Data Explorer](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir visualiser des données à partir d’Azure Data Explorer dans Kibana, préparez les éléments suivants :

* [Helm v3](https://github.com/helm/helm#install), qui est le gestionnaire de package de Kubernetes.

* Cluster Azure Kubernetes Service (AKS) ou tout autre cluster Kubernetes. Les versions 1.14 à 1.16 ont été testées et vérifiées. Si vous avez besoin d’un cluster AKS, consultez la documentation indiquant comment déployer un cluster AKS avec [Azure CLI](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) ou avec le [portail Azure](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal).

* Un [cluster Azure Data Explorer](create-cluster-database-portal.md), y compris l’URL du cluster et le nom de la base de données.

* Un principal de service Azure Active Directory (Azure AD) autorisé à afficher des données dans Azure Data Explorer, notamment l’ID client et le secret client.

    Nous recommandons un principal de service avec autorisation d’affichage et déconseillons l’utilisation d’autorisations de niveau supérieur. [Définissez les autorisations d’affichage du cluster pour le principal de service Azure AD](manage-database-permissions.md#manage-permissions-in-the-azure-portal).

    Pour plus d’informations sur le principal de service Azure AD, consultez [Créer un principal de service Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>Exécuter K2Bridge sur Azure Kubernetes Service (AKS)

Par défaut, le chart Helm de K2Bridge référence une image disponible publiquement située dans le registre de conteneurs de Microsoft. Ce registre ne nécessite aucune information d’identification.

1. Téléchargez les charts Helm requis.

1. Ajoutez la dépendance Elasticsearch à Helm. La dépendance est nécessaire, car K2Bridge utilise une petite instance Elasticsearch interne. L’instance traite les demandes relatives aux métadonnées, telles que les requêtes de modèles d’index et les requêtes enregistrées. Cette instance interne n’enregistre aucune donnée métier. Vous pouvez considérer l’instance comme un détail d’implémentation.

    1. Pour ajouter la dépendance Elasticsearch à Helm, exécutez les commandes suivantes :

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. Pour récupérer le graphique K2Bridge du répertoire charts du dépôt GitHub :

        1. Clonez le dépôt à partir de [GitHub](https://github.com/microsoft/K2Bridge).
        1. Accédez au répertoire du dépôt racine K2Bridges.
        1. Exécutez cette commande :

            ```bash
            helm dependency update charts/k2bridge
            ```

1. Déployez K2Bridge.

    1. Définissez les variables sur les valeurs correctes applicables à votre environnement.

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. Si vous le souhaitez, activez la télémétrie Azure Application Insights. Si vous utilisez Application Insights pour la première fois, [créez une ressource Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource). [Copiez la clé d’instrumentation](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) dans une variable.

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>Installez le graphique K2Bridge.

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        Dans [Configuration](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md), vous trouverez l’ensemble complet des options de configuration.

    1. <a name="install-kibana-service"></a> La sortie de la commande précédente suggère la prochaine commande Helm pour déployer Kibana. Si vous le souhaitez, exécutez la commande suivante :

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```

    1. Utilisez le réacheminement de port pour accéder à Kibana sur localhost.

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```

    1. Connectez-vous à Kibana en accédant à http://127.0.0.1:5601.

    1. Exposez Kibana aux utilisateurs. Pour ce faire, il existe plusieurs méthodes. La méthode que vous utilisez dépend en grande partie de votre cas d’usage.

        Par exemple, vous pouvez exposer le service en tant que service Load Balancer. Pour ce faire, ajoutez le paramètre **--set service.type=LoadBalancer** à la [commande Helm **install** Kibana antérieure](#install-kibana-service).

        Ensuite, exécutez cette commande :

        ```bash
        kubectl get service -w -n k2bridge
        ```

        La sortie doit ressembler à ceci :

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx    <pending>     5601:30128/TCP   4m24s
        ```

        Vous pouvez ensuite utiliser la valeur EXTERNAL-IP générée qui s’affiche. Utilisez-la pour accéder à Kibana en ouvrant un navigateur et en accédant à \<EXTERNAL-IP\> :5601.

1. Configurez des modèles d’index pour accéder à vos données.

    Dans une nouvelle instance de Kibana :

    1. Ouvrez Kibana.
    1. Accédez à **Management** (Gestion).
    1. Sélectionnez **Index Patterns** (Modèles d’index).
    1. Créez un modèle d’index. Le nom de l’index doit correspondre exactement au nom de la table ou au nom de la fonction sans astérisque (\*). Vous pouvez copier la ligne appropriée à partir de la liste.

> [!Note]
> Pour exécuter K2Bridge sur d’autres fournisseurs Kubernetes, définissez la valeur du paramètre **storageClassName** Elasticsearch dans values.yaml sur celle suggérée par le fournisseur.

## <a name="visualize-data"></a>Visualiser les données

Quand Azure Data Explorer est configuré en tant que source de données pour Kibana, vous pouvez utiliser Kibana pour explorer les données.

1. Dans Kibana, dans le menu le plus à gauche, sélectionnez l’onglet **Discover**.

1. Dans la zone de liste déroulante la plus à gauche, sélectionnez un modèle d’index. Le modèle définit la source de données que vous souhaitez explorer. En l’occurrence, le modèle d’index est une table Azure Data Explorer.

   ![Sélection d’un modèle d’index](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Si vos données comportent un champ de filtre d’heure, vous pouvez spécifier l’intervalle de temps. En haut à droite de la page **Discover**, sélectionnez un filtre de temps. Par défaut, la page affiche les données des 15 dernières minutes.

   ![Sélection d’un filtre de temps](media/k2bridge/k2bridge-time-filter.png)

1. La table des résultats affiche les 500 premiers enregistrements. Vous pouvez développer un document pour examiner ses données de champ au format JSON ou de table.

   ![Enregistrement développé](media/k2bridge/k2bridge-expand-record.png)

1. Par défaut, la table des résultats comprend la colonne **_source**. Elle comprend également la colonne **Time** si le champ d’heure existe. Vous pouvez ajouter des colonnes spécifiques à la table des résultats en sélectionnant **Add** (Ajouter) en regard du nom de champ dans le volet le plus à gauche.

   ![Colonnes spécifiques avec mise en évidence du bouton Add](media/k2bridge/k2bridge-specific-columns.png)

1. Dans la barre de requête, vous pouvez effectuer une recherche dans les données en :

    * Entrant un terme de recherche.
    * Utilisant la syntaxe de requête Lucene. Par exemple :
        * Recherchez « error » (erreur) pour trouver tous les enregistrements qui contiennent cette valeur.
        * Recherchez « status : 200 » (état : 200) pour obtenir tous les enregistrements dont la valeur d’état est 200.
    * Utilisant les opérateurs logiques **AND**, **OR** et **NOT**.
    * Utilisant les caractères génériques astérisque (\*) et point d’interrogation ( ?). Par exemple, la requête « destination_city: L* » récupère les enregistrements dont la valeur destination_city commence par « L » ou « l ». (K2Bridge ne respecte pas la casse.)

    ![Exécution d’une requête](media/k2bridge/k2bridge-run-query.png)

    > [!Tip]
    > Dans [Searching](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md) (Recherche), vous pouvez rechercher d’autres règles et logiques de recherche.

1. Pour filtrer les résultats de votre recherche, utilisez la liste des champs dans le volet le plus à droite de la page. La liste des champs indique :

    * Les cinq premières valeurs du champ
    * Le nombre d’enregistrements qui contiennent le champ
    * Le pourcentage d’enregistrements qui contiennent chaque valeur

    >[!Tip]
    > Utilisez la loupe pour rechercher tous les enregistrements qui ont une valeur spécifique.

    ![Liste de champs avec mise en évidence de la loupe](media/k2bridge/k2bridge-field-list.png)

    Vous pouvez également utiliser la loupe pour filtrer les résultats et afficher la vue format de la table de résultats pour chaque enregistrement.

     ![Liste table avec mise en évidence de la loupe](media/k2bridge/k2bridge-table-list.png)

1. Sélectionnez **Save** (Enregistrer) ou **Share** (Partager) pour votre recherche.

     ![Mise en évidence des boutons permettant d’enregistrer ou de partager la recherche](media/k2bridge/k2bridge-save-search.png)
