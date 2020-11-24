---
title: Rédiger des requêtes pour l’Explorateur de données Azure
description: Dans cette procédure, vous allez apprendre à effectuer des requêtes de base et plus avancées pour l’Explorateur de données Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: tutorial
ms.date: 04/07/2019
ms.localizationpriority: high
ms.openlocfilehash: 22a22f1050a366a79017904550854365dc2ef43c
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513282"
---
# <a name="write-queries-for-azure-data-explorer"></a>Rédiger des requêtes pour l’Explorateur de données Azure

Dans cet article, vous allez apprendre à utiliser le langage de requête dans l’Explorateur de données Azure pour effectuer des requêtes de base avec les opérateurs les plus courants. Certaines des fonctionnalités les plus avancées du langage vous sont également présentées.

## <a name="prerequisites"></a>Prérequis

Vous pouvez exécuter les requêtes dans cet article de deux manières :

- Sur le *cluster help* de l’Explorateur de données Azure que nous avons configuré pour vous assister dans votre apprentissage.
    [Connectez-vous au cluster](https://dataexplorer.azure.com/clusters/help/databases/samples) avec un compte e-mail professionnel qui est membre d’Azure Active Directory.

- Sur votre propre cluster qui inclut les exemples de données StormEvents. Pour plus d’informations, consultez [Démarrage rapide : Créer un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md) et [Ingérer des exemples de données dans Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

## <a name="overview-of-the-query-language"></a>Vue d’ensemble du langage de requête

Une requête dans Azure Data Explorer est une demande en lecture seule visant à traiter des données et à retourner des résultats. La demande est formulée en texte brut, à l’aide d’un modèle de flux de données conçu pour faciliter la lecture, la création et l’automatisation de la syntaxe. La requête utilise des entités de schéma qui sont organisées dans une hiérarchie similaire à SQL : bases de données, tables et colonnes.

La requête se compose d’une séquence d’instructions de requête, délimitées par un point-virgule (`;`), une instruction au moins étant une instruction d’expression tabulaire, qui est une instruction qui génère des données organisées dans un maillage sous forme de tableau de colonnes et de lignes. Les instructions d’expression tabulaire de la requête produisent les résultats de la requête.

La syntaxe de l’instruction d’expression tabulaire comporte un flux de données tabulaires d’un opérateur de requête tabulaire vers un autre, qui commence par la source de données (par exemple, une table dans une base de données ou un opérateur qui génère des données), puis transite par un ensemble d’opérateurs de transformation des données qui sont liés à l’aide de la barre verticale (`|`).

Par exemple, la requête suivante a une seule instruction, qui est une instruction d’expression tabulaire. L’instruction commence par une référence à une table appelée `StormEvents` (la base de données qui héberge cette table est ici implicite et fait partie des informations de connexion). Les données (lignes) pour cette table sont ensuite filtrées par la valeur de la colonne `StartTime`, puis par la valeur de la colonne `State`. La requête retourne ensuite le nombre de lignes restantes.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA) **\]**

```Kusto
StormEvents
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count
```

Dans ce cas, le résultat est le suivant :

|Count|
|-----|
|   23|
| |

Pour plus d’informations, consultez les [informations de référence sur le langage de requête](./kusto/query/index.md).

## <a name="most-common-operators"></a>Opérateurs les plus courants

Les opérateurs abordés dans cette section représentent les éléments fondamentaux pour comprendre les requêtes dans l’Explorateur de données Azure. La plupart des requêtes que vous rédigez contiennent plusieurs de ces opérateurs.

Pour exécuter des requêtes sur le cluster help : sélectionnez **Cliquer pour exécuter la requête** au-dessus de chaque requête.

Pour exécuter des requêtes sur votre propre cluster :

1. Copiez chaque requête dans l’application de requête web, puis sélectionnez la requête ou placez votre curseur dans la requête.

1. En haut de l’application, sélectionnez **Exécuter**.

### <a name="count"></a>count

[**count**](kusto/query/countoperator.md) : retourne le nombre de lignes dans la table.

La requête suivante retourne le nombre de lignes dans la table StormEvents.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA) **\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>take

[**take**](kusto/query/takeoperator.md) : retourne au maximum le nombre spécifié de lignes de données.

La requête suivante retourne cinq lignes de la table StormEvents. Le mot clé *limit* est un alias pour *take*.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d) **\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> Il n’est pas possible de savoir quels enregistrements vont être retournés, sauf si les données sources sont triées.

### <a name="project"></a>project

[**project**](kusto/query/projectoperator.md) : sélectionne un sous-ensemble de colonnes.

La requête suivante retourne un ensemble spécifique de colonnes.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA) **\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>where

[**where**](kusto/query/whereoperator.md) : Filtre une table d’après le sous-ensemble de lignes correspondant à un prédicat.

La requête suivante filtre les données par `EventType` et `State`.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>sort

[**sort**](kusto/query/sortoperator.md) : Trie les lignes de la table d’entrée dans l’ordre d’après une ou plusieurs colonnes.

La requête suivante trie les données dans l’ordre décroissant par `DamageProperty`.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> L’ordre des opérations est important. Essayez de placer `take 5` avant `sort by`. Obtenez-vous des résultats différents ?

### <a name="top"></a>top

[**top**](kusto/query/topoperator.md) : Retourne les *N* premiers enregistrements triés d’après les colonnes spécifiées.

La requête suivante retourne les mêmes résultats que ci-dessus avec un opérateur en moins.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>extend

[**extend**](kusto/query/extendoperator.md) : calcule des colonnes dérivées.

La requête suivante crée une colonne en calculant une valeur dans chaque ligne.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

Les expressions peuvent inclure tous les opérateurs habituels (+, -, *, /, %) et il existe une gamme de fonctions utiles que vous pouvez appeler.

### <a name="summarize"></a>summarize

[**summarize**](kusto/query/summarizeoperator.md) : agrège des groupes de lignes.

La requête suivante retourne le nombre d’événements par `State`.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA) **\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

L’opérateur **summarize** regroupe des lignes qui ont les mêmes valeurs dans la clause **by**, puis utilise la fonction d’agrégation (telle que **count**) pour combiner chaque groupe en une seule ligne. Par conséquent, dans ce cas, il existe une ligne pour chaque état et une colonne pour le nombre de lignes dans cet état.

Il existe une gamme de fonctions d’agrégation et vous pouvez utiliser plusieurs d’entre elles dans un opérateur **summarize** afin de produire plusieurs colonnes calculées. Par exemple, vous pouvez obtenir le nombre de tempêtes dans chaque état et le nombre unique de tempêtes par état, puis utiliser **top** pour obtenir les états les plus touchés par les tempêtes.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d) **\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

Le résultat d’une opération **summarize** contient :

- Chaque colonne nommée dans **by**

- Une colonne pour chaque expression calculée

- Une ligne pour chaque combinaison de valeurs by

### <a name="render"></a>render

[**render**](kusto/query/renderoperator.md) : affiche les résultats sous la forme d’une sortie graphique.

La requête suivante affiche un histogramme.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d) **\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

La requête suivante affiche un graphique de temps simple.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA) **\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

La requête suivante compte les événements en prenant un jour comme modulo de temps et en fractionnant le résultat par heure, et affiche un graphique de temps.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d) **\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

La requête suivante compare plusieurs séries quotidiennes sur un graphique de temps.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA) **\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> L’opérateur **render** est une fonctionnalité côté client plutôt qu’une partie du moteur. Il est intégré à la langue pour une plus grande simplicité d’utilisation. L’application web prend en charge les options suivantes : graphique à barres, histogramme, graphique en secteurs, graphique de temps et graphique en courbes. 

## <a name="scalar-operators"></a>Opérateurs scalaires

Cette section aborde certains des opérateurs scalaires les plus importants.

### <a name="bin"></a>bin()

[**bin()**](kusto/query/binfunction.md) : Arrondit les valeurs à l’entier inférieur multiple d’une taille bin donnée.

La requête suivante calcule le nombre avec une taille de compartiment d’une journée.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>case()

[**case()**](kusto/query/casefunction.md) : évalue une liste de prédicats et retourne la première expression de résultat dont le prédicat est satisfait ou la dernière expression **else**. Vous pouvez utiliser cet opérateur pour catégoriser ou regrouper les données :

La requête suivante retourne une nouvelle colonne `deaths_bucket` et regroupe les décès selon le nombre.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d) **\]**

```Kusto
StormEvents
| summarize deaths = sum(DeathsDirect) by State
| extend deaths_bucket = case (
    deaths > 50, "large",
    deaths > 10, "medium",
    deaths > 0, "small",
    "N/A")
| sort by State asc
```

### <a name="extract"></a>extract()

[**extract()**](kusto/query/extractfunction.md) : obtient une correspondance pour une expression régulière à partir d’une chaîne de texte.

La requête suivante extrait des valeurs d’attribut spécifiques à partir d’une trace.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d) **\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

Cette requête utilise une instruction **let**, qui lie un nom (dans ce cas `MyData`) à une expression. Pour le reste de la portée dans laquelle l’instruction **let** apparaît (portée globale ou portée du corps de la fonction), le nom peut être utilisé pour faire référence à sa valeur liée.

### <a name="parse_json"></a>parse_json()

[**parse_json()**](kusto/query/parsejsonfunction.md) : interprète une chaîne en tant que valeur JSON et retourne la valeur comme dynamique. Son efficacité est supérieure à celle de la fonction **extractjson()** si vous devez extraire plusieurs éléments d’un objet JSON composite.

La requête suivante extrait les éléments JSON à partir d’un tableau.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d) **\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

La requête suivante extrait les éléments JSON.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA) **\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

La requête suivante extrait les éléments JSON avec un type de données dynamique.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA) **\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>ago()

[**ago()**](kusto/query/agofunction.md) : Soustrait l’intervalle de temps donné de l’heure UTC actuelle.

La requête suivante retourne des données pour les 12 dernières heures.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA) **\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mv-expand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>startofweek()

[**startofweek()**](kusto/query/startofweekfunction.md) : retourne le début de la semaine contenant la date, avec un décalage, s’il est fourni.

La requête suivante retourne le début de la semaine avec des décalages différents.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d) **\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

Cette requête utilise l’opérateur **range**, qui génère une table de valeurs à une seule colonne. Voir aussi : [**startofday()**](kusto/query/startofdayfunction.md), [**startofweek()**](kusto/query/startofweekfunction.md), [**startofyear()**](kusto/query/startofyearfunction.md)), [**startofmonth()**](kusto/query/startofmonthfunction.md), [**endofday()**](kusto/query/endofdayfunction.md), [**endofweek()**](kusto/query/endofweekfunction.md), [**endofmonth()**](kusto/query/endofmonthfunction.md) et [**endofyear()**](kusto/query/endofyearfunction.md).

### <a name="between"></a>between()

[**between()**](kusto/query/betweenoperator.md) : établit une correspondance avec l’entrée à l’intérieur de la plage inclusive.

La requête suivante filtre les données selon une plage de dates donnée.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

La requête suivante filtre les données selon une plage de dates donnée, avec une légère variation de trois jours (`3d`) à partir de la date de début.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>Opérateurs tabulaires

Kusto comporte de nombreux opérateurs tabulaires, certains d’entre eux sont couverts dans d’autres sections de cet article. Ici, nous allons nous concentrer sur **parse**. 

### <a name="parse"></a>parse

[**parse**](kusto/query/parseoperator.md) : évalue une expression de chaîne et analyse sa valeur dans une ou plusieurs colonnes calculées. Il existe trois sortes d’analyses : simple (par défaut), par expression régulière et souple.

La requête suivante analyse une trace et extrait les valeurs appropriées à l’aide de l’analyse simple par défaut. L’expression (appelée StringConstant) est une valeur de chaîne régulière et la correspondance est stricte : les colonnes étendues doivent correspondre aux types requis.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d) **\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse EventTrace with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

La requête suivante analyse une trace et extrait les valeurs appropriées à l’aide de `kind = regex`. StringConstant peut être une expression régulière.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA) **\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind = regex EventTrace with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previousLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previousLockTime
```

La requête suivante analyse une trace et extrait les valeurs appropriées à l’aide de `kind = relaxed`. StringConstant est une valeur de chaîne régulière et la correspondance est souple : les colonnes étendues peuvent correspondre partiellement aux types requis.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d) **\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind=relaxed "Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=NULL, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=NULL, previousLockTime=02/17/2016 08:39:01)" with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

## <a name="time-series-analysis"></a>Analyse de série chronologique

### <a name="make-series"></a>make-series

[**make-series**](kusto/query/make-seriesoperator.md) : agrège des groupes de lignes comme [summarize](kusto/query/summarizeoperator.md), mais génère un vecteur de série (chronologique) pour chaque combinaison de valeurs by.

La requête suivante retourne un ensemble de séries chronologiques pour le nombre de tempêtes par jour. La requête couvre une période de trois mois pour chaque état, en remplissant les compartiments manquants par la constante 0 :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d) **\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

Une fois que vous avez créé un ensemble de séries (chronologiques), vous pouvez appliquer des fonctions de séries pour détecter les formes anormales, les modèles saisonniers, et bien plus encore.

La requête suivante extrait les trois premiers états qui ont connu le plus de tempêtes au cours d’une journée spécifique :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d) **\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

Pour plus d’informations, consultez la liste complète des [fonctions de séries](kusto/query/scalarfunctions.md#series-processing-functions).

## <a name="advanced-aggregations"></a>Agrégations avancées

Nous avons abordé les agrégations de base, comme **count** et **summarize**, plus haut dans cet article. Cette section présente des options plus avancées.

### <a name="top-nested"></a>top-nested

[**top-nested**](kusto/query/topnestedoperator.md) : produit les premiers résultats hiérarchiques, où chaque niveau est une vue détaillée des valeurs du niveau précédent.

Cet opérateur s’avère utile pour les scénarios de visualisation de tableau de bord ou lorsqu’il est nécessaire de répondre à une question comme la suivante : « Rechercher les N premières valeurs de K1 (à l’aide d’une agrégation) ; pour chacune d’elles, rechercher les m premières valeurs de K2 (à l’aide d’une autre agrégation) ; etc. »

La requête suivante retourne une table hiérarchique avec `State` au niveau supérieur, suivi de `Sources`.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>plug-in pivot()

[**pivot() plugin**](kusto/query/pivotplugin.md) : fait pivoter une table en transformant les valeurs uniques d’une colonne dans la table d’entrée en plusieurs colonnes dans la table de sortie. L’opérateur effectue des agrégations là où elles sont nécessaires sur les valeurs de colonne restantes dans la sortie finale.

La requête suivante applique un filtre et change les lignes en colonnes.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d) **\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>dcount()

[**dcount()**](kusto/query/dcount-aggfunction.md) : retourne une estimation du nombre de valeurs distinctes d’une expression dans le groupe. Utilisez [**count()**](kusto/query/countoperator.md) pour compter toutes les valeurs.

La requête suivante compte les valeurs distinctes de `Source` par `State`.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d) **\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>dcountif()

[**dcountif()**](kusto/query/dcountif-aggfunction.md) : retourne une estimation du nombre de valeurs distinctes de l’expression pour les lignes pour lesquelles le prédicat prend la valeur True.

La requête suivante compte les valeurs distinctes de `Source` où `DamageProperty < 5000`.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d) **\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcount_hll"></a>dcount_hll()

[**dcount_hll()**](kusto/query/dcount-hllfunction.md) : Calcule la valeur **dcount** à partir des résultats HyperLogLog (générés par [**hll**](kusto/query/hll-aggfunction.md) ou [**hll_merge**](kusto/query/hll-merge-aggfunction.md)).

La requête suivante utilise l’algorithme HLL pour générer le nombre.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA) **\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="arg_max"></a>arg_max()

[**arg_max()**](kusto/query/arg-max-aggfunction.md) : recherche dans le groupe une ligne qui optimise une expression et retourne la valeur d’une autre expression (ou * pour retourner la ligne entière).

La requête suivante retourne l’heure du dernier rapport d’inondation dans chaque état.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA) **\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeset()

[**makeset()**](kusto/query/makeset-aggfunction.md) : Retourne un tableau (JSON) dynamique de l’ensemble de valeurs distinctes prises par une expression dans le groupe.

La requête suivante retourne toutes les heures auxquelles une inondation a été signalée par chaque état et crée un tableau à partir de l’ensemble de valeurs distinctes.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA) **\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mv-expand"></a>mv-expand

[**mv-expand**](kusto/query/mvexpandoperator.md) : Développe des collections multivaleurs à partir d’une colonne de type dynamique afin que chaque valeur de la collection obtienne une ligne distincte. Toutes les autres colonnes d’une ligne développée sont dupliquées. Il s’agit de l’opposé de makelist.

La requête suivante génère des exemples de données en créant un ensemble, puis en l’utilisant afin d’illustrer les fonctionnalités **mv-expand**.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA) **\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mv-expand FloodReports
```

### <a name="percentiles"></a>percentiles()

[**percentiles()**](kusto/query/percentiles-aggfunction.md) : Retourne une estimation pour le [**centile de rang le plus proche**](kusto/query/percentiles-aggfunction.md) spécifié de la population définie par une expression. La précision dépend de la densité de population dans la région du centile. Peut être utilisé uniquement dans le contexte d’agrégation à l’intérieur de [**summarize**](kusto/query/summarizeoperator.md).

La requête suivante calcule les centiles de durée de la tempête.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA) **\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

La requête suivante calcule les centiles de durée de la tempête par état et normalise les données en compartiments de cinq minutes (`5m`).

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d) **\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>Plusieurs jeux de données

Cette section traite des éléments qui vous permettent de créer des requêtes plus complexes, de joindre des données entre les tables et d’interroger plusieurs bases de données et clusters.

### <a name="let"></a>let

[**let**](kusto/query/letstatement.md) : améliore la modularité et la réutilisation. L’instruction **let** vous permet de diviser une expression potentiellement complexe en plusieurs parties, chacune liée à un nom, et d’assembler ces parties. Une instruction **let** peut également être utilisée pour créer des fonctions définies par l’utilisateur et des vues (expressions sur les tables dont les résultats se présentent comme une nouvelle table). Les expressions liées par une instruction **let** peuvent être de type scalaire, de type tabulaire, ou une fonction définie par l’utilisateur (expressions lambda).

L’exemple suivant crée une variable de type tabulaire et l’utilise dans une expression suivante.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d) **\]**

```Kusto
let LightningStorms =
StormEvents
| where EventType == "Lightning";
let AvalancheStorms =
StormEvents
| where EventType == "Avalanche";
LightningStorms
| join (AvalancheStorms) on State
| distinct State
```

### <a name="join"></a>join

[**join**](kusto/query/joinoperator.md) : fusionne les lignes de deux tables pour former une nouvelle table en mettant en correspondance les valeurs des colonnes spécifiées de chaque table. Kusto prend en charge une gamme complète de types de jointures : **fullouter**, **inner**, **innerunique**, **leftanti**, **leftantisemi**, **leftouter**, **leftsemi**, **rightanti**, **rightantisemi**, **rightouter**, **rightsemi**.

L’exemple suivant joint deux tables avec une jointure interne.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==) **\]**

```Kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X 
| join kind=inner Y on Key
```

> [!TIP]
> Utilisez les opérateurs **where** et **project** pour réduire le nombre de lignes et de colonnes dans les tables d’entrée avant la jointure. Si une table est toujours plus petite que l’autre, utilisez-la pour le côté gauche de la jointure. Les colonnes de la correspondance de jointure doivent avoir le même nom. Utilisez l’opérateur **project** si nécessaire pour renommer une colonne dans l’une des tables.

### <a name="serialize"></a>serialize

[**serialize**](kusto/query/serializeoperator.md) : sérialise l’ensemble de lignes pour que vous puissiez utiliser les fonctions qui requièrent des données sérialisées, comme **row_number()** .

La requête suivante réussit, car les données sont sérialisées.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

L’ensemble de lignes est également considéré comme sérialisé s’il s’agit d’un résultat des opérateurs **sort**, **top** ou **range**, suivis éventuellement des opérateurs **project**, **project-away**, **extend**, **where**, **parse**, **mv-expand** ou **take**.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA) **\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>Requêtes entre plusieurs bases de données et clusters

[Requêtes entre plusieurs bases de données et clusters](kusto/query/cross-cluster-or-database-queries.md) : Vous pouvez interroger une base de données sur le même cluster en y faisant référence en tant que `database("MyDatabase").MyTable`. Vous pouvez interroger une base de données sur un cluster distant en y faisant référence en tant que `cluster("MyCluster").database("MyDatabase").MyTable`.

La requête suivante est appelée à partir d’un cluster et interroge les données du cluster `MyCluster`. Pour exécuter cette requête, utilisez vos propres noms de cluster et de base de données.

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>Analyse des utilisateurs

Cette section inclut des éléments et des requêtes qui illustrent combien il est facile d’analyser les comportements des utilisateurs dans Kusto.

### <a name="activity_counts_metrics-plugin"></a>plug-in activity_counts_metrics

[**plug-in activity_counts_metrics**](kusto/query/activity-counts-metrics-plugin.md) : calcule des métriques d’activité utiles (valeurs de nombre total, valeurs de nombre distinct, nombre distinct de nouvelles valeurs et nombre distinct agrégé). Les métriques sont calculées pour chaque fenêtre de temps, puis comparées et agrégées dans et avec toutes les fenêtres de temps précédentes.

La requête suivante analyse l’adoption par les utilisateurs en calculant le nombre d’activités quotidiennes.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d) **\]**

```Kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01),
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
T
| evaluate activity_counts_metrics(UserId, Timestamp, start, end,
window)
```

### <a name="activity_engagement-plugin"></a>plug-in activity_engagement

[**plug-in activity_engagement**](kusto/query/activity-engagement-plugin.md) : calcule le taux d’engagement d’activité selon la colonne d’ID sur une fenêtre de chronologie glissante. Le **plug-in activity_engagement** peut être utilisé pour calculer les utilisateurs actifs quotidiens, hebdomadaires et mensuels.

La requête suivante retourne dans une fenêtre dynamique de sept jours le taux du nombre total d’utilisateurs distincts qui se servent d’une application quotidiennement par rapport au nombre total d’utilisateurs distincts qui s’en servent une fois par semaine.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d) **\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100
| render timechart
```

> [!TIP]
> Lors du calcul des utilisateurs actifs quotidiens et mensuels, modifiez les données de fin et la période de la fenêtre dynamique (OuterActivityWindow).

### <a name="activity_metrics-plugin"></a>plug-in activity_metrics

[**plug-in activity_metrics**](kusto/query/activity-metrics-plugin.md) : calcule des métriques d’activité utiles (valeurs de nombre distinct, nombre distinct de nouvelles valeurs, taux de conservation et taux de variation) en fonction de la fenêtre de la période actuelle comparée à la fenêtre de la période précédente.

La requête suivante calcule les taux de variation et de conservation pour un jeu de données spécifique.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d) **\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
| where _day > datetime(2017-01-02)
| project _day, id
// Calculate weekly retention rate
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate*100, churn_rate*100
| render timechart
```

### <a name="new_activity_metrics-plugin"></a>plug-in new_activity_metrics

[**plug-in new_activity_metrics**](kusto/query/new-activity-metrics-plugin.md) : calcule des métriques d’activité utiles (valeurs de nombre distinct, nombre distinct de nouvelles valeurs, taux de conservation et taux de variation) pour la cohorte de nouveaux utilisateurs. Le concept de ce plug-in est similaire au [**plug-in activity_metrics**](kusto/query/activity-metrics-plugin.md), mais se concentre sur les nouveaux utilisateurs.

La requête suivante calcule des taux de variation et de conservation avec une fenêtre sur plusieurs semaines pour la cohorte de nouveaux utilisateurs (utilisateurs qui sont arrivés la première semaine).

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d) **\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

### <a name="session_count-plugin"></a>plug-in session_count

[**plug-in session_count**](kusto/query/session-count-plugin.md) : calcule le nombre de sessions selon la colonne d’ID sur une chronologie.

La requête suivante retourne le nombre de sessions. Une session est considérée comme active si un ID d’utilisateur s’affiche au moins une fois dans une période de 100 plages horaires, alors que la fenêtre de rétrospective de session comporte 41 plages horaires.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d) **\]**

```Kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart
```

### <a name="funnel_sequence-plugin"></a>plug-in funnel_sequence

[**plug-in funnel_sequence**](kusto/query/funnel-sequence-plugin.md) : calcule le nombre distinct d’utilisateurs qui ont pris une séquence d’états ; affiche la distribution des états précédents et suivants qui ont abouti à la séquence ou ont été suivis par celle-ci.

La requête suivante montre l’événement qui se produit avant et après toutes les tornades en 2007.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA) **\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnel_sequence_completion-plugin"></a>plug-in funnel_sequence_completion

[**plug-in funnel_sequence_completion**](kusto/query/funnel-sequence-completion-plugin.md) : calcule la synthèse des étapes de séquence terminées dans différentes périodes de temps.

La requête suivante vérifie la synthèse d’achèvement de la séquence `Hail -> Tornado -> Thunderstorm -> Wind` dans des périodes « globales » d’une heure, de quatre heures et d’un jour (`[1h, 4h, 1d]`).

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA) **\]**

```Kusto
let _start = datetime(2007-01-01);
let _end = datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm', 'Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods)
```

## <a name="functions"></a>Fonctions

Cette section couvre les [**fonctions**](kusto/query/functions/index.md) : requêtes réutilisables qui sont stockées sur le serveur. Les fonctions peuvent être appelées par des requêtes et d’autres fonctions (les fonctions récursives ne sont pas prises en charge).

> [!NOTE]
> Vous ne pouvez pas créer de fonctions sur le cluster help, qui est en lecture seule. Utilisez votre propre cluster de test pour cette partie.

L’exemple suivant crée une fonction qui prend un nom d’état (`MyState`) en tant qu’argument.

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

L’exemple suivant appelle une fonction qui obtient des données pour l’état du Texas.

```Kusto
MyFunction ("Texas")
| summarize count()
```

L’exemple suivant supprime la fonction qui a été créée dans la première étape.

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>Étapes suivantes

[Informations de référence sur le langage de requête Kusto](./kusto/query/index.md)