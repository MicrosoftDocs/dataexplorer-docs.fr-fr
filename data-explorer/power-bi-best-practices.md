---
title: Bonnes pratiques relatives à l’utilisation de Power BI pour interroger et visualiser des données Azure Data Explorer
description: Dans cet article, vous allez apprendre les bonnes pratiques concernant l’utilisation de Power BI pour interroger et visualiser des données Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/26/2019
ms.openlocfilehash: f277ff9caaaf29b39b7e1fac4175ce2fa862c269
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872979"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Bonnes pratiques relatives à l’utilisation de Power BI pour interroger et visualiser des données Azure Data Explorer

L’Explorateur de données Azure est un service d’exploration de données rapide et hautement évolutive pour les données des journaux et les données de télémétrie. [Power BI](https://docs.microsoft.com/power-bi/) est une solution d’analytique métier qui vous permet de visualiser vos données et de partager les résultats dans votre organisation. Azure Data Explorer fournit trois options de connexion aux données de Power BI. Utilisez le [connecteur intégré](power-bi-connector.md), [importez une requête à partir d’Azure Data Explorer dans Power BI](power-bi-imported-query.md) ou utilisez une [requête SQL](power-bi-sql-query.md). Cet article fournit des conseils concernant l’interrogation et la visualisation de vos données Azure Data Explorer avec Power BI. 

## <a name="best-practices-for-using-power-bi"></a>Bonnes pratiques relatives à l’utilisation de Power BI 

Quand vous travaillez avec plusieurs téraoctets de données brutes actualisées, suivez ces instructions pour maintenir les tableaux de bord et rapports Power BI élégants et à jour :

* **Voyagez léger** : n’apportez dans Power BI que les données dont vous avez besoin pour vos rapports. Pour une analyse interactive approfondie, utilisez l’[interface utilisateur Azure Data Explorer](web-query-data.md) qui est optimisée pour l’exploration ad hoc avec le langage de requête Kusto.

* **Modèle composite** : utilisez un [modèle composite](https://docs.microsoft.com/power-bi/desktop-composite-models) pour combiner des données agrégées pour les tableaux de bord de premier niveau avec des données brutes opérationnelles filtrées. Vous pouvez définir clairement quand utiliser des données brutes et quand utiliser une vue agrégée. 

* **Mode Import et mode DirectQuery** : utiliser le mode **Import** pour l’interaction de petits jeux de données. Utilisez le mode **DirectQuery** pour les jeux de données volumineux et fréquemment mis à jour. Par exemple, créez des tables de dimension à l’aide du mode **Import** car elles sont peu volumineuses et ne changent pas souvent. Définissez l’intervalle d’actualisation en fonction de la fréquence attendue des mises à jour des données. Créez des tables de faits à l’aide du mode **DirectQuery** car ces tables sont volumineuses et contiennent des données brutes. Utilisez ces tables pour présenter des données filtrées à l’aide de l’[extraction](https://docs.microsoft.com/power-bi/desktop-drillthrough) Power BI.

* **Parallélisme** – Azure Data Explorer est une plateforme de données linéairement scalable. Par conséquent, vous pouvez améliorer les performances du rendu des tableaux de bord en augmentant le parallélisme du flux de bout en bout comme suit :

   * Augmentez le nombre de [connexions simultanées en mode DirectQuery dans Power BI](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery).

   * Utilisez une [cohérence faible pour améliorer le parallélisme](kusto/concepts/queryconsistency.md). Cela peut avoir un impact sur l’actualisation des données.

* **Segments effectifs** – Utilisez des [segments synchronisés](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) pour empêcher les rapports de charger des données avant que vous soyez prêt. Après avoir structuré le jeu de données, placé tous les visuels et marqué tous les segments, vous pouvez sélectionner le segment synchronisé pour charger uniquement les données nécessaires.

* **Utilisez des filtres** : utilisez autant de filtres Power BI que possible pour concentrer la recherche d’Azure Data Explorer sur les partitions de données appropriées.

* **Visuels efficaces** : sélectionnez les visuels les plus performants pour vos données.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Conseils d’interrogation de données à l’aide du connecteur Azure Data Explorer pour Power BI

La section suivante contient des conseils et des astuces concernant l’utilisation du langage de requête Kusto avec Power BI. Utilisez le [connecteur Azure Data Explorer pour Power BI](power-bi-connector.md) pour visualiser des données.

### <a name="complex-queries-in-power-bi"></a>Requêtes complexes dans Power BI

Les requêtes complexes sont exprimées plus facilement dans Kusto que dans Power Query. Elles doivent être implémentées sous forme de [fonctions Kusto](kusto/query/functions/index.md) et appelées dans Power BI. Cette méthode est nécessaire lors de l’utilisation de **DirectQuery** avec les instructions `let` dans votre requête Kusto. Étant donné que Power BI joint deux requêtes et que les instructions `let` ne peuvent pas être utilisées avec l’opérateur `join`, des erreurs de syntaxe peuvent se produire. Par conséquent, enregistrez chaque partie de la jointure en tant que fonction Kusto et autorisez Power BI à joindre ces deux fonctions.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>Comment simuler un opérateur date-heure relatif

Power BI ne contient pas d’opérateur date-heure *relatif*, comme `ago()`.
Pour simuler `ago()`, utilisez une combinaison des fonctions Power BI `DateTime.FixedLocalNow()` et `#duration`.

Au lieu de cette requête qui utilise l’opérateur `ago()` :

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

Utilisez la requête équivalente suivante :

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>Atteindre les limites de requête Kusto 

Les requêtes Kusto retournent, par défaut, jusqu’à 500 000 lignes ou 64 Mo, comme décrit dans les [limites de requête](kusto/concepts/querylimits.md). Vous pouvez remplacer ces valeurs par défaut à l’aide des **Options avancées** affichées dans la fenêtre de connexion **Azure Data Explorer (Kusto)**  :

![options avancées](media/power-bi-best-practices/advanced-options.png)

Ces options émettent des [instructions SET](kusto/query/setstatement.md) avec votre requête pour changer les limites de requête par défaut :

  * **Limiter le nombre d’enregistrements dans les résultats de requête** génère une valeur `set truncationmaxrecords`
  * **Limiter la taille des données en octets dans les résultats de requête** génère une valeur `set truncationmaxsize`
  * **Désactiver la troncation du jeu de résultats** génère une valeur `set notruncation`

### <a name="using-query-parameters"></a>Utilisation de paramètres de requête

Vous pouvez utiliser des [paramètres de requête](kusto/query/queryparametersstatement.md) pour modifier votre requête de manière dynamique. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Utilisation d’un paramètre de requête dans les informations de connexion

Utilisez un paramètre de requête pour filtrer les informations contenues dans la requête et optimiser les performances de celle-ci.
 
Dans la fenêtre **Modifier les requêtes**, **Accueil** > **Éditeur avancé**

1. Recherchez la section suivante de la requête :

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   Par exemple :

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Remplacez la partie concernée de la requête par votre paramètre. Fractionnez la requête en plusieurs parties et concaténez-les à nouveau à l’aide d’un signe & et de ce paramètre.

   Par exemple, dans la requête ci-dessus, nous allons prendre la partie `State == 'ALABAMA'` et la fractionner en `State == '` et `'`, puis nous allons placer le paramètre `State` entre elles :
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Si votre requête contient des guillemets, encodez-les correctement. Par exemple, la requête suivante : 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   s’affiche dans l’**Éditeur avancé** comme suit avec deux guillemets :

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   Elle doit être remplacée par la requête suivante avec trois guillemets :

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Utiliser un paramètre de requête dans les étapes de requête

Vous pouvez utiliser un paramètre de requête dans n’importe quelle étape de requête qui le prend en charge. Par exemple, filtrez les résultats en fonction de la valeur d’un paramètre.

![filtrer les résultats à l’aide d’un paramètre](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>N’utilisez pas le planificateur d’actualisation des données Power BI pour émettre des commandes de contrôle vers Kusto

Power BI comprend un planificateur d’actualisation des données qui peut émettre régulièrement des requêtes sur une source de données. Ce mécanisme ne doit pas être utilisé pour planifier des commandes de contrôle sur Kusto, car Power BI suppose que toutes les requêtes sont en lecture seule.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI peut envoyer uniquement des requêtes courtes (&lt; 2 000 caractères) à Kusto

Si l’exécution d’une requête dans Power BI génère l’erreur suivante : _« DataSource.Error : Web.Contents n’a pas réussi à obtenir le contenu de... »_ , la requête comporte probablement plus de 2 000 caractères. Power BI utilise **PowerQuery** pour interroger Kusto en émettant une requête HTTP GET qui encode la requête en tant que partie de l’URI récupéré. Par conséquent, les requêtes Kusto émises par Power BI sont limitées à la longueur maximale d’un URI de requête (2 000 caractères, moins un petit décalage). Pour contourner ce problème, vous pouvez définir une [fonction stockée](kusto/query/schema-entities/stored-functions.md) dans Kusto et faire utiliser cette fonction par Power BI dans la requête.

## <a name="next-steps"></a>Étapes suivantes

[Visualiser des données à l'aide du connecteur Azure Data Explorer pour Power BI](power-bi-connector.md)




