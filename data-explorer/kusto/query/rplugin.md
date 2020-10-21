---
title: Plug-in R (version préliminaire)-Azure Explorateur de données
description: Cet article décrit le plug-in R (version préliminaire) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: ae8a82253dfd17b7d3667cd4c72648df0c42658a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242783"
---
# <a name="r-plugin-preview"></a>Plug-in R (version préliminaire)

::: zone pivot="azuredataexplorer"

Le plug-in R exécute une fonction définie par l’utilisateur (UDF) à l’aide d’un script R. Le script obtient les données tabulaires en tant qu’entrée, et produit une sortie tabulaire.
Le runtime du plug-in est hébergé dans un [bac à sable (sandbox)](../concepts/sandboxes.md) sur les nœuds du cluster. Le bac à sable (sandbox) fournit un environnement isolé et sécurisé.

## <a name="syntax"></a>Syntaxe

*T* `|` `evaluate` [ `hint.distribution` `=` ( `single`  |  `per_node` )] `r(` *output_schema* `,` *script* [ `,` *script_parameters*]`)`

## <a name="arguments"></a>Arguments

* *output_schema*: `type` littéral qui définit le schéma de sortie des données tabulaires, retourné par le code R.
    * Le format est : `typeof(` *ColumnName* `:` *ColumnType*[,...] `)` , par exemple : `typeof(col1:string, col2:long)` .
    * Pour étendre le schéma d’entrée, utilisez la syntaxe suivante : `typeof(*, col1:string, col2:long)` .
* *script*: `string` littéral qui est le script R valide à exécuter.
* *script_parameters*: un `dynamic` littéral facultatif qui est un conteneur de propriétés de paires nom/valeur à passer au script R comme `kargs` dictionnaire réservé. Pour plus d’informations, consultez [variables R réservées](#reserved-r-variables).
* *hint. distribution*: indication facultative pour que l’exécution du plug-in soit distribuée sur plusieurs nœuds de cluster.
   Par défaut : `single`.
    * `single`: Une seule instance du script est exécutée sur l’ensemble des données de la requête.
    * `per_node`: Si la requête avant le bloc R est distribuée, une instance du script s’exécutera sur chaque nœud sur les données qu’elle contient.

## <a name="reserved-r-variables"></a>Variables R réservées

Les variables suivantes sont réservées pour l’interaction entre le langage de requête Kusto et le code R :

* `df`: Données tabulaires d’entrée (les valeurs `T` ci-dessus), en tant que R tableau.
* `kargs`: Valeur de l’argument *script_parameters* , sous la forme d’un dictionnaire R.
* `result`: R tableau créé par le script R. La valeur devient les données tabulaires qui sont envoyées à n’importe quel opérateur de requête Kusto qui suit le plug-in.

## <a name="enable-the-plugin"></a>Activer le plug-in

* Le plug-in est désactivé par défaut.
* Activez ou désactivez le plug-in dans le Portail Azure sous l’onglet **configuration** de votre cluster. Pour plus d’informations, consultez [gérer les extensions de langage dans votre cluster Azure Explorateur de données (version préliminaire)](../../language-extensions.md)

## <a name="notes-and-limitations"></a>Remarques et limitations

* L’image R sandbox est basée sur *r 3.4.4 pour Windows*et comprend des packages de l' [offre groupée r Essentials de Anaconda](https://docs.anaconda.com/anaconda/packages/r-language-pkg-docs/).
* Le bac à sable (sandbox) R limite l’accès au réseau. Le code R ne peut pas installer de manière dynamique des packages supplémentaires qui ne sont pas inclus dans l’image. Si vous avez besoin de packages spécifiques, ouvrez une **nouvelle demande de support** dans le portail Azure.

## <a name="examples"></a>Exemples

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

:::image type="content" source="images/plugin/sine-demo.png" alt-text="Démonstration sinus" border="false":::

## <a name="performance-tips"></a>Conseils sur les performances

* Réduisez le jeu de données d’entrée du plug-in sur la quantité minimale requise (colonnes/lignes).
    * Utilisez des filtres sur le jeu de données source à l’aide du langage de requête Kusto, lorsque cela est possible.
    * Pour effectuer un calcul sur un sous-ensemble des colonnes sources, projetez uniquement ces colonnes avant d’appeler le plug-in.
* Utilisez `hint.distribution = per_node` chaque fois que la logique de votre script est distribuable.
    * Vous pouvez également utiliser l' [opérateur de partition](partitionoperator.md) pour partitionner le jeu de données d’entrée.
* Dans la mesure du possible, utilisez le langage de requête Kusto pour implémenter la logique de votre script R.

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

## <a name="usage-tips"></a>Conseils d’utilisation

* Pour éviter les conflits entre les délimiteurs de chaîne Kusto et les délimiteurs de chaîne R :  
    * Utilisez des guillemets simples ( `'` ) pour les littéraux de chaîne Kusto dans les requêtes Kusto.
    * Utilisez des guillemets doubles ( `"` ) pour les littéraux de chaîne r dans les scripts r.
* Utilisez l' [opérateur de données externes](externaldata-operator.md) pour obtenir le contenu d’un script que vous avez stocké dans un emplacement externe, tel qu’un stockage d’objets BLOB Azure ou un référentiel GitHub public.
  
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

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end

