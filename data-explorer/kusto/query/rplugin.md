---
title: R plugin (Avant-première) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit R plugin (Preview) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d815a75b241f7779a5f4ee9cae626c38ed54f9f4
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765996"
---
# <a name="r-plugin-preview"></a>R plugin (Avant-première)

::: zone pivot="azuredataexplorer"

Le plugin R exécute une fonction définie par l’utilisateur (UDF) à l’aide d’un script R. Le script R obtient des données tabulaires comme son entrée, et devrait produire la sortie tabulaire.
Le temps d’exécution du plugin est hébergé dans un bac à [sable,](../concepts/sandboxes.md)un environnement isolé et sécurisé fonctionnant sur les nœuds du cluster.

### <a name="syntax"></a>Syntaxe

*T* `|` `=` `single` `r(` *output_schema* `,` *script* `,` [`hint.distribution` ( | )] output_schema script [ script_parameters ] *script_parameters* `evaluate` `per_node``)`


### <a name="arguments"></a>Arguments

* *output_schema*: Un `type` littéral qui définit le schéma de sortie des données tabulaires, retourné par le code R.
    * Le format `typeof(`est: *ColumnName* `:` *ColumnType* [, ...] `)`, par `typeof(col1:string, col2:long)`exemple: .
    * Pour étendre le schéma d’entrée, `typeof(*, col1:string, col2:long)`utilisez la syntaxe suivante : .
* *script*: `string` Un texte littéral qui est le script R valide à exécuter.
* *script_parameters*: Un littéral optionnel `dynamic` qui est un sac de propriété de paires `kargs` de nom/valeur à transmettre au script R comme dictionnaire réservé (voir [variables reservantes R).](#reserved-r-variables)
* *hint.distribution*: Un indice optionnel pour l’exécution du plugin à distribuer sur plusieurs nœuds cluster.
   Par défaut : `single`.
    * `single`: Une seule instance du script s’exécutera sur l’ensemble des données de requête.
    * `per_node`: Si la requête avant la distribution du bloc R est distribuée, une instance du script s’exécutera sur chaque nœud sur les données qu’il contient.


### <a name="reserved-r-variables"></a>Variables reservantes reS

Les variables suivantes sont réservées à l’interaction entre kusto Query Language et le code R :

* `df`: Les données tabulaires `T` d’entrée (les valeurs ci-dessus), comme un R DataFrame.
* `kargs`: La valeur de *l’argument script_parameters,* en tant que dictionnaire R.
* `result`: Un DataFrame R créé par le script R, dont la valeur devient les données tabulaires qui est envoyée à n’importe quel opérateur de requête Kusto qui suit le plugin.

### <a name="onboarding"></a>Mise en route


* Le plugin est désactivé par défaut.
    * *Vous souhaitez activer le plugin sur votre cluster ?*
        
        * Dans le portail Azure, au sein de votre cluster Azure Data Explorer, sélectionnez **Nouvelle demande de support** dans le menu de gauche.
        * Désactiver le plugin nécessite l’ouverture d’un billet de soutien ainsi.

### <a name="notes-and-limitations"></a>Notes et limitations

* L’image de bac à sable R est basée sur *R 3.4.4 pour Windows*, et comprend des paquets de [Anaconda R Essentials bundle](https://docs.anaconda.com/anaconda/packages/r-language-pkg-docs/).
* Le bac à sable R limite l’accès au réseau, donc le code R ne peut pas installer dynamiquement des paquets supplémentaires qui ne sont pas inclus dans l’image. Ouvrez une **nouvelle demande de support** dans le portail Azure si vous avez besoin de forfaits spécifiques.


### <a name="examples"></a>Exemples

```kusto
range x from 1 to 360 step 1
| evaluate r(
//
typeof(*, fx:double),               //  Output schema: append a new fx column to original table 
//
'result <- df\n'                    //  The R decorated script
'n <- nrow(df)\n'
'g <- kargs$gain\n'
'f <- kargs$cycles\n'
'result$fx <- g * sin(df$x / n * 2 * pi * f)'
//
, pack('gain', 100, 'cycles', 4)    //  dictionary of parameters
)
| render linechart 
```

:::image type="content" source="images/samples/sine-demo.png" alt-text="Démo de Sine":::

### <a name="performance-tips"></a>Conseils sur les performances

* Réduisez l’ensemble de données d’entrée du plugin à la quantité minimale requise (colonnes/lignes).
    * Utilisez des filtres sur l’ensemble de données source, si possible, en utilisant la langue de requête Kusto.
    * Pour effectuer un calcul sur un sous-ensemble des colonnes sources, ne projetez que ces colonnes avant d’invoquer le plugin.
* Utilisez `hint.distribution = per_node` chaque fois que la logique de votre script est distribuable.
    * Vous pouvez également utiliser [l’opérateur](partitionoperator.md) de partition pour le partage de l’ensemble de données d’entrée.
* Dans la mesure du possible, utilisez le langage Kusto Query pour implémenter la logique de votre script R.

    Par exemple :

    ```kusto    
    .show operations
    | where StartedOn > ago(1d) // Filtering out irrelevant records before invoking the plugin
    | project d_seconds = Duration / 1s // Projecting only a subset of the necessary columns
    | evaluate hint.distribution = per_node r( // Using per_node distribution, as the script's logic allows it
        typeof(*, d2:double),
        'result <- df\n'
        'result$d2 <- df$d_seconds\n' // Negative example: this logic should have been written using Kusto's query language
      )
    | summarize avg = avg(d2)
    ```

### <a name="usage-tips"></a>Conseils d’utilisation

* Pour éviter les conflits entre les délimitations de cordes Kusto`'`et ceux de R, nous vous recommandons d’utiliser des`"`caractères de citation unique () pour les littérals de chaîne Kusto dans les requêtes Kusto, et les caractères de double citation () pour les littérals de cordes R dans les scripts R.
* Utilisez [l’opérateur de données externes](externaldata-operator.md) pour obtenir le contenu d’un script que vous avez stocké dans un emplacement externe, comme le stockage de blob Azure, un référentiel GitHub public, etc.
  
  Par exemple :

    ```kusto    
    let script = 
        externaldata(script:string)
        [h'https://kustoscriptsamples.blob.core.windows.net/samples/R/sample_script.r']
        with(format = raw);
    range x from 1 to 360 step 1
    | evaluate r(
        typeof(*, fx:double),
        toscalar(script), 
        pack('gain', 100, 'cycles', 4))
    | render linechart 
    ```

---

::: zone-end

::: zone pivot="azuremonitor"

Ce n’est pas pris en charge dans Azure Monitor

::: zone-end

