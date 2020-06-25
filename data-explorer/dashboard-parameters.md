---
title: Paramètres dans des tableaux de bord Azure Data Explorer
description: Utilisez des paramètres comme composant pour les filtres de tableau de bord.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: reference
ms.date: 06/09/2020
ms.openlocfilehash: 6a06e5ea90bb16466c3550c8de07f930477dad96
ms.sourcegitcommit: b0cbb89e88b64a36880e6d34b4d6779283174456
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84637238"
---
# <a name="use-parameters-in-azure-data-explorer-dashboards"></a>Utiliser des paramètres dans des tableaux de bord Azure Data Explorer

Les paramètres sont utilisés comme composants pour les filtres dans les tableaux de bord Azure Data Explorer. Ils sont gérés dans le cadre des tableaux de bord et peuvent être ajoutés à des requêtes pour filtrer les données présentées par le visuel sous-jacent. Une requête peut utiliser un ou plusieurs paramètres. Ce document décrit la création et l’utilisation de paramètres et de filtres liés dans des tableaux de bord Azure Data Explorer. 

> [!NOTE]
> La gestion des paramètres est disponible en mode d’édition pour les éditeurs de tableaux de bord.

## <a name="prerequisites"></a>Prérequis

[Visualiser des données avec des tableaux de bord Azure Data Explorer](azure-data-explorer-dashboards.md)

## <a name="view-parameters-list"></a>Consulter une liste de paramètres

Sélectionnez le bouton **Paramètres** situé en haut du tableau de bord pour voir la liste de tous les paramètres du tableau de bord.

:::image type="content" source="media/dashboard-parameters/dashboard-icons.png" alt-text="bouton Paramètres en haut du tableau de bord":::

## <a name="create-a-parameter"></a>Créer un paramètre

Pour créer un paramètre, sélectionnez le bouton **Nouveau paramètre** situé en haut du volet de droite.

:::image type="content" source="media/dashboard-parameters/new-parameter-button.png" alt-text="bouton Nouveaux paramètres":::

### <a name="properties"></a>Propriétés

1. Dans le volet **Ajouter un paramètre**, configurez les propriétés détaillées ci-dessous.

:::image type="content" source="media/dashboard-parameters/properties.png" alt-text="ajouter des propriétés de paramètre":::

|Champ  |Description |
|---------|---------|
|**Nom complet du paramètre**    |   Nom du paramètre affiché sur le tableau de bord ou la carte de modification.      |
|**Type de paramètre**    |Celui-ci peut avoir l'une des valeurs suivantes :<ul><li>**Sélection unique** : une seule valeur peut être sélectionnée dans le filtre comme entrée du paramètre.</li><li>**Sélection multiple** : une ou plusieurs valeurs peuvent être sélectionnées dans le filtre comme entrées du paramètre.</li><li>**Intervalle de temps** : permet de créer des paramètres supplémentaires pour filtrer les requêtes et les tableaux de bord en fonction du temps. Chaque tableau de bord a un sélecteur de plage d’heures par défaut.</li></ul>    |
|**Nom de la variable**     |   Nom du paramètre à utiliser dans la requête.      |
|**Type de données**    |    Type de données des valeurs de paramètre.     |
|**Épingler comme filtre de tableau de bord**   |   Épingle le filtre basé sur le paramètre au tableau de bord ou le désépingle.       |
|**Source**     |    Source des valeurs de paramètre : <ul><li>**Valeurs fixes** : Valeurs de filtre statiques introduites manuellement. </li><li>**Requête** : Valeurs introduites dynamiquement à l’aide d’une requête KQL.  </li></ul>    |
|**Ajouter une valeur « Tout sélectionner »**    |   S’applique uniquement aux types de paramètres Sélection unique et Sélection multiple. Permet de récupérer les données pour toutes les valeurs du paramètre. Cette valeur doit être intégrée à la requête pour fournir cette fonctionnalité. Pour obtenir plus d’exemples sur la génération de telles requêtes, consultez [Utiliser le paramètre basé sur une requête et à sélection multiple](#use-the-multiple-selection-query-based-parameter).     |

## <a name="manage-parameters-in-parameter-card"></a>Gérer des paramètres dans une carte de paramètres

Dans le menu représentant trois points de la carte de paramètres, sélectionnez **Modifier**, **Paramètre dupliqué**, **Supprimer** ou **Détacher du tableau de bord**/**Épingler au tableau de bord**.

Les indicateurs suivants sont affichés dans la carte de paramètres :
* Nom complet du paramètre 
* Noms des variables 
* Nombre de requêtes dans lesquelles le paramètre a été utilisé
* État Épingler/Désépingler dans le tableau de bord

:::image type="content" source="media/dashboard-parameters/modify-parameter.png" alt-text="Modifier les paramètres":::

## <a name="use-parameters-in-your-query"></a>Utiliser des paramètres dans votre requête

Un paramètre doit être utilisé dans la requête pour rendre le filtre applicable pour le visuel de cette requête. Une fois les paramètres définis, vous pouvez les voir dans la page **Requête** > barre supérieure de filtre et dans la requête IntelliSense.

:::image type="content" source="media/dashboard-parameters/query-intellisense.png" alt-text="Consulter les paramètres dans la barre supérieure et dans IntelliSense":::

> [!NOTE]
> Si le paramètre n’est pas utilisé dans la requête, le filtre reste désactivé. Une fois le paramètre ajouté à la requête, le filtre devient actif.

## <a name="use-different-parameter-types"></a>Utiliser différents types de paramètres

Plusieurs types de paramètres de tableau de bord sont pris en charge. Les exemples suivants décrivent comment utiliser des paramètres dans une requête pour différents types de paramètres. 

### <a name="use-the-default-time-range-parameter"></a>Utiliser le paramètre Intervalle de temps par défaut

Chaque tableau de bord dispose d’un paramètre *Intervalle de temps* par défaut. Il s’affiche dans le tableau de bord comme filtre uniquement quand il est utilisé dans une requête. Utilisez les mots clés de paramètre `_startTime` et `__endTime` pour utiliser le paramètre d’intervalle de temps par défaut dans une requête, comme indiqué dans l’exemple suivant :

```kusto
EventsAll
| where Repo.name has 'Microsoft'
| where CreatedAt between (_startTime.._endTime)
| summarize TotalEvents = count() by RepoName=tostring(Repo.name)
| top 5 by TotalEvents
```

Une fois enregistré, le filtre d’intervalle de temps s’affiche sur le tableau de bord. Il peut maintenant être utilisé pour filtrer les données sur la carte. Vous pouvez filtrer votre tableau de bord en le sélectionnant dans la liste déroulante : **Intervalle de temps** (x dernières minutes/dernières heures/derniers jours) ou un **Intervalle de temps personnalisé**.

:::image type="content" source="media/dashboard-parameters/time-range.png" alt-text="filtre utilisant un intervalle de temps personnalisé":::

### <a name="use-the-single-selection-fixed-value-parameter"></a>Utiliser le paramètre à valeur fixe et à sélection unique

Les paramètres à valeur fixe sont basés sur des valeurs prédéfinies spécifiées par l’utilisateur. L’exemple suivant vous montre comment créer un paramètre à valeur fixe et à sélection unique.

#### <a name="create-the-parameter"></a>Créer le paramètre

1. Sélectionnez **Paramètres** pour ouvrir le volet **Paramètres**, puis sélectionnez **Nouveau paramètre**.

1. Renseignez les détails comme suit :

    * **Nom complet du paramètre** : Company
    * **Type de paramètre** : Sélection unique
    * **Nom de la variable** : `_company`
    * **Type de données** : String
    * **Épingler comme filtre de tableau de bord** : activé
    * **Source** : Valeurs fixes
    
    Dans cet exemple, utilisez les valeurs suivantes :
    
    | Valeur      | Nom complet du paramètre |
    |------------|------------------------|
    | google/    | Google                 |
    | microsoft/ | Microsoft              |
    | facebook/  | Facebook               |
    | aws/       | AWS                    |
    | uber/      | Uber                   |
    
    * Ajouter une valeur **Tout sélectionner** : Désactivé
    * Valeur par défaut : Microsoft

1. Sélectionnez **Terminé** pour créer le paramètre.

Les paramètres sont affichés dans le volet latéral **Paramètres**, mais ils ne sont actuellement utilisés dans aucun visuel.

:::image type="content" source="media/dashboard-parameters/start-end-side-pane.png" alt-text="paramètres d’heure de début et d’heure de fin dans le volet latéral":::

#### <a name="use-the-parameter"></a>Utiliser le paramètre

1. Exécutez un exemple de requête à l’aide du nouveau paramètre *Company* en utilisant le nom de variable `_company` :

    ```kusto
    EventsAll
    | where CreatedAt > ago(7d)
    | where Type == "WatchEvent"
    | where Repo.name has _company
    | summarize WatchEvents=count() by RepoName = tolower(tostring(Repo.name))
    | top 5 by WatchEvents
    ```

    Le nouveau paramètre s’affiche dans la liste de paramètres en haut du tableau de bord.

1. Sélectionnez différentes valeurs pour mettre à jour les visuels.

    :::image type="content" source="media/dashboard-parameters/top-five-repos.png" alt-text="résultat des cinq principaux dépôts":::

### <a name="use-the-multiple-selection-fixed-value-parameters"></a>Utiliser les paramètres à valeur fixe et à sélection multiple

Les paramètres à valeur fixe sont basés sur des valeurs prédéfinies spécifiées par l’utilisateur. L’exemple suivant vous montre comment créer et utiliser un paramètre à valeur fixe et à sélection multiple.

#### <a name="create-the-parameters"></a>Créer les paramètres

1. Sélectionnez **Paramètres** pour ouvrir le volet **Paramètres**, puis sélectionnez **Nouveau paramètre**.

1. Renseignez les détails comme indiqué dans [Utiliser le paramètre à valeur fixe et à sélection unique](#use-the-single-selection-fixed-value-parameter) avec les changements suivants :

    * **Nom complet du paramètre** : Companies
    * **Type de paramètre** : Sélection multiple
    * **Nom de la variable** : `_companies`

1. Cliquez sur **Terminé** pour créer le paramètre.

Les nouveaux paramètres sont affichés dans le volet latéral **Paramètres**, mais ils ne sont actuellement utilisés dans aucun visuel.

:::image type="content" source="media/dashboard-parameters/companies-side-pane.png" alt-text="volet latéral Companies":::

#### <a name="use-the-parameters"></a>Utiliser les paramètres 

1. Exécutez un exemple de requête à l’aide du nouveau paramètre *companies* en utilisant la variable `_companies`.

    ```kusto
    EventsAll
    | where CreatedAt > ago(7d)
    | extend RepoName = tostring(Repo.name)
    | where Type == "WatchEvent"
    | where RepoName has_any (_companies)
    | summarize WatchEvents=count() by RepoName
    | top 5 by WatchEvents
    ```

    Le nouveau paramètre s’affiche dans la liste de paramètres en haut du tableau de bord.  

1. Sélectionnez une ou plusieurs valeurs différentes pour mettre à jour les visuels.

    :::image type="content" source="media/dashboard-parameters/select-companies.png" alt-text="sélectionner companies":::

### <a name="use-the-single-selection-query-based-parameter"></a>Utiliser le paramètre basé sur une requête et à sélection unique

Les valeurs des paramètres basés sur une requête sont récupérées lors du chargement du tableau de bord en exécutant la requête avec paramètres. L’exemple suivant vous montre comment créer et utiliser un paramètre basé sur une requête et à sélection unique.

#### <a name="create-the-parameter"></a>Créer le paramètre

1. Sélectionnez **Paramètres** pour ouvrir le volet **Paramètres**, puis sélectionnez **Nouveau paramètre**.

2. Renseignez les détails comme indiqué dans [Utiliser le paramètre à valeur fixe et à sélection unique](#use-the-single-selection-fixed-value-parameter) avec les changements suivants :

    * **Nom complet du paramètre** : Événement
    * **Nom de la variable** : `_event`
    * **Source** : Requête
    * **Source de données** : GitHub
    * Sélectionnez **Ajouter une requête**, puis entrez la requête suivante. Sélectionnez **Terminé**.

    ```kusto
    EventsAll
    | distinct Type
    | order by Type asc
    ```

    * **Valeur** : Type
    * **Nom d'affichage** : Type
    * **Valeur par défaut** : WatchEvent

1. Sélectionnez **Terminé** pour créer le paramètre.

#### <a name="use-a-parameter-in-the-query"></a>Utiliser un paramètre dans la requête

1. Voici un exemple de requête utilisant le nouveau paramètre Event avec la variable `_ event` :

    ``` kusto
    EventsAll
    | where Type has (_event)
    | summarize count(Id) by Type, bin(CreatedAt,7d)
    ```

    Le nouveau paramètre s’affiche dans la liste de paramètres en haut du tableau de bord. 

1. Sélectionnez différentes valeurs pour mettre à jour les visuels.

### <a name="use-the-multiple-selection-query-based-parameter"></a>Utiliser le paramètre basé sur une requête et à sélection multiple

Les valeurs des paramètres basés sur une requête sont dérivées au moment du chargement du tableau de bord en exécutant la requête spécifiée par l’utilisateur. L’exemple suivant montre comment créer un paramètre basé sur une requête et à sélection multiple :

#### <a name="create-a-parameter"></a>Créer un paramètre

1. Sélectionnez **Paramètres** pour ouvrir le volet **Paramètres**, puis sélectionnez **Nouveau paramètre**.

1. Renseignez les détails comme indiqué dans [Utiliser le paramètre à valeur fixe et à sélection unique](#use-the-single-selection-fixed-value-parameter) avec les changements suivants :

    * **Nom complet du paramètre** : Événements
    * **Type de paramètre** : Sélection multiple
    * **Nom de la variable** : `_events`

1. Cliquez sur **Terminé** pour créer le paramètre.

#### <a name="use-parameters-in-the-query"></a>Utiliser des paramètres dans la requête

1. L’exemple de requête suivant utilise le nouveau paramètre *Events* avec la variable `_events`.

    ``` kusto
    EventsAll
    | where Type in (_event) or isempty(_events)
    | summarize count(Id) by Type, bin(CreatedAt,7d)
    ```

    > [!NOTE]
    > Cet exemple utilise l’option **Tout sélectionner** en vérifiant la présence de valeurs vides avec la fonction `isempty()`.

    Le nouveau paramètre s’affiche dans la liste de paramètres en haut du tableau de bord. 

1. Sélectionnez une ou plusieurs valeurs différentes pour mettre à jour les visuels.
