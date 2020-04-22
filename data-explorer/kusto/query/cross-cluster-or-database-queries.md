---
title: Questions cross-database et clusters croisés - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les requêtes cross-database et cross-cluster dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8bb0cf2674bae801fb98d14d93518f5617af6807
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766067"
---
# <a name="cross-database-and-cross-cluster-queries"></a>Requêtes entre plusieurs bases de données et clusters

::: zone pivot="azuredataexplorer"

Chaque requête Kusto fonctionne dans le cadre du cluster actuel et de la base de données par défaut.
* Dans [Kusto Explorer,](../tools/kusto-explorer.md) la base de données par défaut est celle sélectionnée dans le [panneau Connexions](../tools/kusto-explorer.md#connections-panel) et le cluster actuel est la connexion contenant cette base de données
* Lors de l’utilisation [de kusto Client Library,](../api/netfx/about-kusto-data.md) le cluster actuel et la base de données par défaut sont spécifiés respectivement par les `Data Source` chaînes de connexion Kusto et `Initial Catalog` les propriétés des chaînes de connexion [Kusto.](../api/connection-strings/kusto.md)

## <a name="queries"></a>Requêtes
Pour accéder aux tables à partir de n’importe quelle base de données autre que la valeur par défaut, la syntaxe *nom qualifié* doit être utilisée : Pour accéder à la base de données dans le cluster actuel :
```kusto
database("<database name>").<table name>
```
Base de données en cluster distant :
```kusto
cluster("<cluster name>").database("<database name>").<table name>
```

*nom* de base de données est sensible aux cas

*le nom du cluster* est insensible aux cas et peut être l’une des formes suivantes :
* URL bien formée: `http://contoso.kusto.windows.net:1234/`exemple , seuls les systèmes HTTP et HTTPS sont pris en charge.
* Nom de domaine entièrement qualifié (FQDN): par exemple `contoso.kusto.windows.net` - qui sera équivalent à`https://`**`contoso.kusto.windows.net`**`:443/`
* Nom court (nom d’hôte [et région] `contoso` sans la `https://` **`contoso`** `.kusto.windows.net:443/`partie `contoso.westus` domaine): par exemple - qui est interprété comme , ou - qui est interprété comme`https://`**`contoso.westus`**`.kusto.windows.net:443/`

> [!NOTE]
> L’accès à la base de données croisée est soumis aux contrôles d’autorisation habituels.
> Pour excuter une requête, vous devez avoir lu l’autorisation de la base de données par défaut et de toutes les autres bases de données référencées dans la requête (dans les clusters actuels et distants).

*Le nom qualifié* peut être utilisé dans n’importe quel contexte dans lequel un nom de table peut être utilisé.
Tous les éléments suivants sont valides :

```kusto
database("OtherDb").Table | where ...

union Table1, cluster("OtherCluster").database("OtherDb").Table2 | project ...

database("OtherDb1").Table1 | join cluster("OtherCluster").database("OtherDb2").Table2 on Key | join Table3 on Key | extend ...
```

Lorsque *le nom qualifié* apparaît comme un opérand de l’opérateur [syndical,](./unionoperator.md)wildcards peuvent être utilisés pour spécifier plusieurs tables et bases de données multiples. Les wildcards ne sont pas autorisés dans les noms de cluster :

```kusto
union withsource=TableName *, database("OtherDb*").*Table, cluster("OtherCluster").database("*").*
```

> [!NOTE]
>* Le nom de la base de données par défaut est également un match potentiel, de sorte que la base de données ("&#42;").
>* Comment les changements de schéma affectent-ils les requêtes à grappes croisées, voient [les requêtes à grappes croisées et les changements de schémas](../concepts/crossclusterandschemachanges.md)

## <a name="access-restriction"></a>Restriction d’accès 
Les noms ou modèles qualifiés peuvent également être inclus dans [l’énoncé d’accès restrict (les](./restrictstatement.md) wildcards dans les noms de cluster ne sont pas autorisés)
```kusto
restrict access to (my*, database("MyOther*").*, cluster("OtherCluster").database("my2*").*);
```

Ce qui précède limitera l’accès à la requête aux entites suivants :

* N’importe quel nom d’entité commençant par *mon...* dans la base de données par défaut. 
* N’importe quel tableau dans toutes les bases de données nommées *MyOther...* du cluster actuel.
* N’importe quel tableau dans toutes les bases de données nommées *my2...* dans le cluster *OtherCluster.kusto.windows.net*.

## <a name="functions-and-views"></a>Fonctions et vues

Les fonctions et les vues (persistantes et créées en ligne) peuvent se référer des tables au-delà des limites de base de données et de cluster. Ce qui suit est valide :

```kusto
let MyView = Table1 join database("OtherDb").Table2 on Key | join cluster("OtherCluster").database("SomeDb").Table3 on Key;
MyView | where ...
```

Des fonctions et des vues persistantes peuvent être consultées à partir d’une autre base de données dans le même cluster :

Fonction tabulaire (vue) dans `OtherDb`:

```kusto
.create function MyView(v:string) { Table1 | where Column1 has v ...  }  
```

Fonction Scalar `OtherDb`dans :
```kusto
.create function MyCalc(a:double, b:double, c:double) { (a + b) / c }  
```

Dans la base de données par défaut :

```kusto
database("OtherDb").MyView("exception") | extend CalCol=database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

## <a name="limitations-of-cross-cluster-function-calls"></a>Limitations des appels de fonctions multi-cluster

Les fonctions ou vues tabulaires peuvent être référencées à travers les clusters. La limite suivante s’applique :

1. La fonction à distance doit retourner schéma tabulaire. Les fonctions Scalar ne peuvent être consultées que dans le même cluster.
2. La fonction distante ne peut accepter que des paramètres scalaires. Les fonctions qui obtiennent un ou plusieurs arguments de table ne peuvent être consultées que dans le même cluster.
3. Le schéma de la fonction distante doit être connu et invariant de ses paramètres (voir [aussi requêtes à clusters croisés et changements de schémas).](../concepts/crossclusterandschemachanges.md)

L’appel cross-cluster suivant est **valide**:

```kusto
cluster("OtherCluster").database("SomeDb").MyView("exception") | count
```

La requête suivante appelle la `MyCalc`fonction scalaire à distance .
Il s’agit d’une violation de la règle #1, il n’est donc **pas valable**:

```kusto
MyTable | extend CalCol=cluster("OtherCluster").database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

La requête suivante appelle `MyCalc` la fonction distante et fournit un paramètre tabulaire.
Il s’agit d’une violation de la règle #2, il n’est donc **pas valable**:

```kusto
cluster("OtherCluster").database("OtherDb").MyCalc(datatable(x:string, y:string)["x","y"] ) 
```

La requête suivante appelle `SomeTable` fonction distante qui a `tablename`la sortie de schéma variable basée sur le paramètre .
Il s’agit d’une violation de la règle #3, il n’est donc **pas valable**:

Fonction tabulaire `OtherDb`dans :
```kusto
.create function SomeTable(tablename:string) { table(tablename)  }  
```

Dans la base de données par défaut :
```kusto
cluster("OtherCluster").database("OtherDb").SomeTable("MyTable")
```

La requête suivante appelle `GetDataPivot` fonction à distance qui a la sortie de schéma variable basée sur les données[(pivot)) plugin](pivotplugin.md) a une sortie dynamique).
Il s’agit d’une violation de la règle #3, il n’est donc **pas valable**:

Fonction tabulaire `OtherDb`dans :
```kusto
.create function GetDataPivot() { T | evaluate pivot(PivotColumn) }  
```

Fonction tabulaire dans la base de données par défaut :
```kusto
cluster("OtherCluster").database("OtherDb").GetDataPivot()
```

## <a name="displaying-data"></a>Affichage de données

Les déclarations selon lesquelles les données de retour au client sont implicitement limitées `take` par le nombre d’enregistrements retournés, même s’il n’y a pas d’utilisation spécifique de l’opérateur. Pour relever cette limite, utilisez l’option de requête client `notruncation` .

Pour afficher les données sous forme graphique, utilisez [l’opérateur de rendu](renderoperator.md).

::: zone-end

::: zone pivot="azuremonitor"

Ce n’est pas pris en charge dans Azure Monitor

::: zone-end
