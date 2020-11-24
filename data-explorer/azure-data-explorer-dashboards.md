---
title: Visualiser des données avec le tableau de bord Azure Data Explorer
description: Découvrir comment visualiser des données avec le tableau de bord Azure Data Explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 05/26/2020
ms.localizationpriority: high
ms.openlocfilehash: 23bd17b54a1910633baabf2b78a1015f31eead5a
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512772"
---
# <a name="visualize-data-with-azure-data-explorer-dashboardspreview"></a>Visualiser des données avec des tableaux de bord Azure Data Explorer (préversion)

L’Explorateur de données Azure est un service d’exploration de données rapide et hautement évolutive pour les données des journaux et les données de télémétrie. Azure Data Explorer fournit une application web qui vous permet d’exécuter des requêtes et de générer des tableaux de bord. Les tableaux de bord sont disponibles dans l’application web autonome, l’[interface utilisateur web](web-query-data.md). Azure Data Explorer est également intégré à d’autres services de tableau de bord comme [Power BI](power-bi-connector.md) et [Grafana](grafana.md).

Les tableaux de bord Azure Data Explorer offrent trois avantages principaux :

* Exportation des requêtes en mode natif de l’interface utilisateur web vers des tableaux de bord Azure Data Explorer 
* Exploration des données dans l’interface utilisateur web
* Optimisation des performances de rendu des tableaux de bord

L’image suivante représente un tableau de bord Azure Data Explorer.

:::image type="content" source="media/adx-dashboards/dash.png" alt-text="Tableau de bord final":::

> [!IMPORTANT]
> Vos données sont sécurisées. Les tableaux de bord et les métadonnées relatives aux tableaux de bord concernant les utilisateurs sont chiffrés au repos.

## <a name="create-a-dashboard"></a>Création d’un tableau de bord

1. Dans la barre de navigation, sélectionnez **Tableaux de bord**, puis **Nouveau tableau de bord**.

    :::image type="content" source="media/adx-dashboards/new-dashboard.png" alt-text="Nouveau tableau de bord":::

1. Entrez un nom de tableau de bord, puis sélectionnez **Créer**.

    :::image type="content" source="media/adx-dashboards/new-dashboard-popup.png" alt-text="Création d’un tableau de bord":::

## <a name="add-data-source"></a>Ajouter une source de données

Ajoutez les sources de données nécessaires pour générer les tableaux de bord.

1. Sélectionnez l’élément de menu **Sources de données** dans la barre supérieure. Sélectionnez le bouton **+ Nouvelle source de données** dans le volet **Sources de données**.

    :::image type="content" source="media/adx-dashboards/data-source.png" alt-text="Source de données":::

1. Dans le volet **Créer une nouvelle source de données** :
    1. Entrez l’**URI du cluster** ou un nom partiel (région incluse), puis sélectionnez **Se connecter**. 
    1. Sélectionnez la **Base de données** dans la liste déroulante.
    1. Utilisez la valeur par défaut ou modifiez le **Nom de la source de données** si nécessaire. 
    1. Sélectionnez **Appliquer**.

    :::image type="content" source="media/adx-dashboards/data-source-pane.png" alt-text="Volet de source de données":::

## <a name="use-parameters"></a>Utiliser des paramètres

Les paramètres permettent d’utiliser des filtres de tableau de bord. Les paramètres améliorent considérablement les performances de rendu des tableaux de bord et vous permettent d’utiliser les valeurs de filtre le plus tôt possible dans la requête. Pour plus d’informations sur l’utilisation des paramètres, consultez [Utiliser des paramètres dans des tableaux de bord Azure Data Explorer](dashboard-parameters.md).

1. Sélectionnez **Paramètres** dans la barre supérieure. Sélectionnez le bouton **Nouveau paramètre** dans le volet **Paramètres**.

    :::image type="content" source="media/adx-dashboards/parameters.png" alt-text="Sélectionner un nouveau paramètre":::

1. Entrez des valeurs pour tous les champs obligatoires, puis sélectionnez **Terminé**.

    :::image type="content" source="media/adx-dashboards/parameter-pane.png" alt-text="Volet Paramètre":::

|Champ  |Description |
|---------|---------|
|**Nom complet du paramètre**    |   Nom du paramètre affiché sur le tableau de bord ou la carte de modification.      |
|**Type de paramètre**    |Celui-ci peut avoir l'une des valeurs suivantes :<ul><li>**Sélection unique** : une seule valeur peut être sélectionnée dans le filtre comme entrée du paramètre.</li><li>**Sélection multiple** : une ou plusieurs valeurs peuvent être sélectionnées dans le filtre comme entrées du paramètre.</li><li>**Intervalle de temps** : permet de créer des paramètres supplémentaires pour filtrer les requêtes et les tableaux de bord en fonction de l’heure. Chaque tableau de bord a un sélecteur de plage horaire par défaut.</li></ul>    |
|**Nom de la variable**     |   Nom du paramètre à utiliser dans la requête.      |
|**Type de données**    |    Type de données des valeurs de paramètre.     |
|**Épingler comme filtre de tableau de bord**   |   Épingle le filtre basé sur le paramètre au tableau de bord ou le désépingle.       |
|**Source**     |    Source des valeurs de paramètre : <ul><li>**Valeurs fixes** : Valeurs de filtre statiques introduites manuellement. </li><li>**Requête** : Valeurs introduites dynamiquement à l’aide d’une requête KQL.  </li></ul>    |
|**Ajouter une valeur « Tout sélectionner »**    |   S’applique uniquement aux types de paramètres Sélection unique et Sélection multiple. Permet de récupérer les données pour toutes les valeurs du paramètre.      |

## <a name="add-query"></a>Ajouter une requête

**Ajouter une requête** utilise des extraits de code en KQL pour récupérer des données et afficher des visuels. Chaque requête peut prendre en charge un visuel.

1. Sélectionnez **Ajouter une requête** dans le canevas du tableau de bord ou la barre de menus supérieure.

    :::image type="content" source="media/adx-dashboards/empty-dashboard-new-query.png" alt-text="Nouvelle requête":::

1. Dans le volet **Requête** : 
    1. Sélectionnez la source de données partagée dans la liste déroulante.
    1. Tapez la requête, puis sélectionnez **Exécuter**. 
    1. Sélectionnez **+ Ajouter un visuel**.

    :::image type="content" source="media/adx-dashboards/initial-query.png" alt-text="Exécuter la requête":::

1. Dans le volet **Mise en forme du visuel**, sélectionnez **Type de graphique** pour choisir le type de visuel. 
1. Nommez le visuel et sélectionnez **Appliquer les modifications** pour épingler le visuel au tableau de bord.

    :::image type="content" source="media/adx-dashboards/add-visual.png" alt-text="Ajouter un visuel à la requête":::

1. Vous pouvez redimensionner le visuel et sélectionner **Enregistrer les modifications** pour enregistrer le tableau de bord.

    :::image type="content" source="media/adx-dashboards/save-dashboard.png" alt-text="Enregistrer le tableau de bord":::

## <a name="share-dashboards"></a>Partager des tableaux de bord

Utilisez le menu Partager pour [accorder des autorisations](#grant-permissions) au tableau de bord, [changer le niveau d’autorisation d’un utilisateur](#change-a-user-permission-level) ou [partager le lien du tableau de bord](#share-the-dashboard-link).

> [!IMPORTANT]
> Pour accéder au tableau de bord, un afficheur de tableau de bord a besoin des éléments suivants :
> * Lien du tableau de bord pour l’accès
> * Autorisations du tableau de bord
> * Accès à la base de données sous-jacente dans le cluster Azure Data Explorer  

1. Sélectionnez l’élément de menu **Partager** dans la barre supérieure du tableau de bord.
1. Sélectionnez **Gérer les autorisations** dans la liste déroulante. 

    :::image type="content" source="media/adx-dashboards/share-dashboard.png" alt-text="Liste déroulante de partage de tableau de bord":::

### <a name="grant-permissions"></a>Accorder des autorisations

Pour accorder des autorisations à un utilisateur dans le volet **Autorisations du tableau de bord** :
1. Écrivez le nom ou l’adresse e-mail de l’utilisateur dans la zone **Ajouter de nouveaux membres**.
1. Sélectionnez le niveau d’**autorisation** **Affichage** ou **Modification**, puis cliquez sur **Ajouter**.

:::image type="content" source="media/adx-dashboards/dashboard-permissions.png" alt-text="Gérer les autorisations du tableau de bord":::

### <a name="change-a-user-permission-level"></a>Changer le niveau d’autorisation d’un utilisateur

Pour changer le niveau d’autorisation d’un utilisateur dans le volet **Autorisations du tableau de bord** :
1. Utilisez la zone de recherche ou faites défiler la liste des utilisateurs pour trouver l’utilisateur.
1. Changez le niveau d’**autorisation** en fonction des besoins.

### <a name="share-the-dashboard-link"></a>Partager le lien du tableau de bord

Pour partager le lien du tableau de bord :
* Sélectionnez la liste déroulante **Partager**, puis sélectionnez **Copier le lien**, ou
* Dans la fenêtre **Autorisations du tableau de bord**, sélectionnez **Copier le lien**. 

## <a name="enable-auto-refresh"></a>Activer l’actualisation automatique 

1. Sélectionnez **Modifier** dans le menu du tableau de bord pour passer en mode d’édition.
1. Sélectionnez **Actualisation automatique**. 
 
    :::image type="content" source="media/adx-dashboards/auto-refresh.png" alt-text="Sélectionner l’actualisation automatique":::

1. Activez ou désactivez l’option pour **activer** l’actualisation automatique. 
1. Sélectionnez des valeurs pour **Intervalle de temps minimal** et **Fréquence d’actualisation par défaut**. 

    :::image type="content" source="media/adx-dashboards/auto-refresh-toggle.png" alt-text="Activer l’actualisation automatique":::

1. Sélectionnez **Appliquer** et **enregistrez** le tableau de bord.

> [!NOTE]
> * Sélectionnez l’intervalle de temps minimal le plus petit pour réduire la charge inutile sur le cluster. 
> * Un afficheur de tableau de bord : 
>    * Peut modifier les intervalles de temps minimum pour un usage personnel uniquement. 
>    * Ne peut pas sélectionner une valeur inférieure à l’**intervalle de temps minimal** spécifié par l’éditeur.

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser des paramètres dans des tableaux de bord Azure Data Explorer](dashboard-parameters.md)
* [Personnaliser les visuels des tableaux de bord](dashboard-customize-visuals.md)
* [Interroger des données dans Azure Data Explorer](web-query-data.md)
