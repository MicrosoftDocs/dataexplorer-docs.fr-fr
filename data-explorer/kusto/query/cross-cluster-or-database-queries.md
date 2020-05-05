---
title: Requêtes entre les bases de données et les clusters-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les requêtes de bases de données croisées et entre les clusters dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 834fd81e1832b8ab624da8d99cb5cc32407db84f
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741752"
---
# <a name="cross-database-and-cross-cluster-queries"></a>Requêtes entre plusieurs bases de données et clusters

::: zone pivot="azuredataexplorer"

Chaque requête Kusto opère dans le contexte du cluster actuel et de la base de données par défaut.
* Dans [Kusto Explorer](../tools/kusto-explorer.md) , la base de données par défaut est celle qui est sélectionnée dans le [volet connexions](../tools/kusto-explorer.md#connections-panel) et le cluster actuel est la connexion contenant cette base de données.
* Lors de l’utilisation de la [bibliothèque cliente Kusto](../api/netfx/about-kusto-data.md) , le cluster actif et la `Data Source` base `Initial Catalog` de données par défaut sont spécifiés respectivement par les propriétés et des [chaînes de connexion Kusto](../api/connection-strings/kusto.md) .

## <a name="queries"></a>Requêtes
Pour accéder aux tables à partir de toute base de données autre que la base de données par défaut, vous devez utiliser la syntaxe de *nom qualifié* : pour accéder à la base de données dans le cluster actuel :
```kusto
database("<database name>").<table name>
```
Base de données dans un cluster distant :
```kusto
cluster("<cluster name>").database("<database name>").<table name>
```

le *nom de la base de données* respecte la casse

le *nom du cluster* ne respecte pas la casse et peut prendre l’une des formes suivantes :
* URL correcte : par exemple `http://contoso.kusto.windows.net:1234/`, seuls les schémas HTTP et HTTPS sont pris en charge.
* Nom de domaine complet (FQDN) : par exemple `contoso.kusto.windows.net` , qui sera équivalent à`https://`**`contoso.kusto.windows.net`**`:443/`
* Nom abrégé (nom d’hôte [et région] sans la partie domaine) : par `contoso` exemple, qui est interprété `https://` **`contoso`** `.kusto.windows.net:443/`comme, `contoso.westus` ou-qui est interprété comme`https://`**`contoso.westus`**`.kusto.windows.net:443/`

> [!NOTE]
> L’accès entre bases de données est soumis aux vérifications d’autorisations habituelles.
> Pour obtenir une requête, vous devez disposer d’une autorisation de lecture sur la base de données par défaut et sur chaque autre base de données référencée dans la requête (dans les clusters actuels et distants).

Le *nom qualifié* peut être utilisé dans n’importe quel contexte dans lequel un nom de table peut être utilisé.
Tous les éléments suivants sont valides :

```kusto
database("OtherDb").Table | where ...

union Table1, cluster("OtherCluster").database("OtherDb").Table2 | project ...

database("OtherDb1").Table1 | join cluster("OtherCluster").database("OtherDb2").Table2 on Key | join Table3 on Key | extend ...
```

Quand le *nom qualifié* apparaît comme opérande de l' [opérateur Union](./unionoperator.md), les caractères génériques peuvent être utilisés pour spécifier plusieurs tables et plusieurs bases de données. Les caractères génériques ne sont pas autorisés dans les noms de cluster :

```kusto
union withsource=TableName *, database("OtherDb*").*Table, cluster("OtherCluster").database("*").*
```

> [!NOTE]
>* Le nom de la base de données par défaut est également une correspondance potentielle, donc la base de données (« &#42; »). * spécifie toutes les tables de toutes les bases de données, y compris la valeur par défaut.
>* Comment les modifications de schéma affectent les requêtes entre clusters, consultez [requêtes entre clusters et modifications de schéma](../concepts/crossclusterandschemachanges.md)

## <a name="access-restriction"></a>Restriction d’accès 
Les noms qualifiés ou les modèles peuvent également être inclus dans l’instruction [restrict access](./restrictstatement.md) (les caractères génériques dans les noms de cluster ne sont pas autorisés)
```kusto
restrict access to (my*, database("MyOther*").*, cluster("OtherCluster").database("my2*").*);
```

La commande ci-dessus limite l’accès aux requêtes aux entités suivantes :

* Nom d’entité commençant par *My...* dans la base de données par défaut. 
* Toutes les tables de toutes les bases de données nommées *MyOther...* du cluster actuel.
* Toutes les tables de toutes les bases de données nommées *MY2...* dans le *OtherCluster.Kusto.Windows.net*du cluster.

## <a name="functions-and-views"></a>Fonctions et vues

Les fonctions et les vues (permanentes et créées Inline) peuvent référence des tables entre les limites de la base de données et du cluster. Les éléments suivants sont valides :

```kusto
let MyView = Table1 join database("OtherDb").Table2 on Key | join cluster("OtherCluster").database("SomeDb").Table3 on Key;
MyView | where ...
```

Il est possible d’accéder à des fonctions et des vues persistantes à partir d’une autre base de données dans le même cluster :

Fonction tabulaire (vue) dans `OtherDb`:

```kusto
.create function MyView(v:string) { Table1 | where Column1 has v ...  }  
```

Fonction scalaire dans `OtherDb`:
```kusto
.create function MyCalc(a:double, b:double, c:double) { (a + b) / c }  
```

Dans la base de données par défaut :

```kusto
database("OtherDb").MyView("exception") | extend CalCol=database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

## <a name="limitations-of-cross-cluster-function-calls"></a>Limitations des appels de fonction entre clusters

Les vues ou les fonctions tabulaires peuvent être référencées sur plusieurs clusters. La limitation suivante s’applique :

1. La fonction distante doit retourner un schéma tabulaire. Les fonctions scalaires sont accessibles uniquement dans le même cluster.
2. La fonction distante ne peut accepter que des paramètres scalaires. Les fonctions qui obtiennent un ou plusieurs arguments de table sont accessibles uniquement dans le même cluster.
3. Le schéma de la fonction distante doit être connu et invariant de ses paramètres (voir aussi [requêtes entre clusters et modifications de schéma](../concepts/crossclusterandschemachanges.md)).

L’appel entre clusters suivant est **valide**:

```kusto
cluster("OtherCluster").database("SomeDb").MyView("exception") | count
```

La requête suivante appelle la fonction `MyCalc`scalaire distante.
Cela ne respecte pas la règle #1, donc il n’est **pas valide**:

```kusto
MyTable | extend CalCol=cluster("OtherCluster").database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

La requête suivante appelle la fonction `MyCalc` distante et fournit un paramètre tabulaire.
Cela ne respecte pas la règle #2, donc il n’est **pas valide**:

```kusto
cluster("OtherCluster").database("OtherDb").MyCalc(datatable(x:string, y:string)["x","y"] ) 
```

La requête suivante appelle la fonction `SomeTable` distante qui a une sortie de schéma `tablename`variable basée sur le paramètre.
Cela ne respecte pas la règle #3, donc il n’est **pas valide**:

Fonction tabulaire dans `OtherDb`:
```kusto
.create function SomeTable(tablename:string) { table(tablename)  }  
```

Dans la base de données par défaut :
```kusto
cluster("OtherCluster").database("OtherDb").SomeTable("MyTable")
```

La requête suivante appelle la fonction `GetDataPivot` distante qui a une sortie de schéma variable basée sur le plug-in de données (le[plug-in pivot ()](pivotplugin.md) a une sortie dynamique).
Cela ne respecte pas la règle #3, donc il n’est **pas valide**:

Fonction tabulaire dans `OtherDb`:
```kusto
.create function GetDataPivot() { T | evaluate pivot(PivotColumn) }  
```

Fonction tabulaire dans la base de données par défaut :
```kusto
cluster("OtherCluster").database("OtherDb").GetDataPivot()
```

## <a name="displaying-data"></a>Affichage de données

Les instructions qui retournent des données au client sont implicitement limitées par le nombre d’enregistrements retournés, même s’il n' `take` y a pas d’utilisation spécifique de l’opérateur. Pour relever cette limite, utilisez l’option de requête client `notruncation` .

Pour afficher les données sous forme graphique, utilisez l' [opérateur Render](renderoperator.md).

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
