---
title: Analyser les données de série chronologique avec Azure Data Explorer
description: Découvrez comment analyser des données de série chronologique dans le cloud avec Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/07/2019
ms.localizationpriority: high
ms.openlocfilehash: d7046b8767413889bc77240032a2292deb7c89a3
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512993"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Analyse des séries chronologiques dans Azure Data Explorer

Azure Data Explorer effectue une collecte continue de données de télémétrie à partir de services cloud ou d’appareils IoT. Ces données peuvent être analysées afin d’extraire diverses informations, par exemple, en lien avec la surveillance de l’intégrité de services, de processus de production physiques et de tendances d’utilisation. L’analyse vise à détecter, à partir de séries chronologiques de mesures sélectionnées, des écarts par rapport à un modèle de référence classique.
Azure Data Explorer prend en charge en mode natif la création, la manipulation et l’analyse de multiples séries chronologiques. Cette rubrique explique comment utiliser Azure Data Explorer pour créer et analyser des **milliers de séries chronologiques en quelques secondes**, ce qui permet de mettre en place des solutions de surveillance en quasi temps réel et autres workflows.

## <a name="time-series-creation"></a>Création de série chronologique

Dans le cadre de cette section, nous allons créer un vaste ensemble de séries chronologiques régulières, de façon simple et intuitive, à l’aide de l’opérateur `make-series`, et renseigner les valeurs manquantes en fonction des besoins.
La première étape de l’analyse d’une série chronologique consiste à partitionner et à transformer la table de données de télémétrie d’origine en un ensemble de séries chronologiques. La table contient généralement une colonne de type timestamp, des dimensions contextuelles et des métriques facultatives. Les dimensions sont utilisées pour partitionner les données. L’objectif est de créer des milliers de séries chronologiques par partition à intervalles réguliers.

La table d’entrée *demo_make_series1* contient 600 000 enregistrements de trafic de service web arbitraire. Utilisez la commande ci-dessous pour échantillonner 10 enregistrements :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03MTo0vTi3KTC02VKhRKAFyFQwNADOyzKUbAAAA) **\]**

```kusto
demo_make_series1 | take 10 
```

La table ainsi obtenue contient une colonne de type timestamp, trois colonnes de dimensions contextuelles et aucune métrique :

| TimeStamp | BrowserVer | OsVer | Pays/région |
| --- | --- | --- | --- |
| 2016-08-25 09:12:35.4020000 | Chrome 51.0 | Windows 7 | United Kingdom |
| 2016-08-25 09:12:41.1120000 | Chrome 52.0 | Windows 10 |   |
| 2016-08-25 09:12:46.2300000 | Chrome 52.0 | Windows 7 | United Kingdom |
| 2016-08-25 09:12:46.5100000 | Chrome 52.0 | Windows 10 | United Kingdom |
| 2016-08-25 09:12:46.5570000 | Chrome 52.0 | Windows 10 | République de Lituanie |
| 2016-08-25 09:12:47.0470000 | Chrome 52.0 | Windows 8.1 | Inde |
| 2016-08-25 09:12:51.3600000 | Chrome 52.0 | Windows 10 | United Kingdom |
| 2016-08-25 09:12:51.6930000 | Chrome 52.0 | Windows 7 | Pays-bas |
| 2016-08-25 09:12:56.4240000 | Chrome 52.0 | Windows 10 | United Kingdom |
| 2016-08-25 09:13:08.7230000 | Chrome 52.0 | Windows 10 | Inde |

À défaut de métriques, nous pouvons uniquement créer un ensemble de séries temporelles représentant le comptage du trafic proprement dit, partitionné par système d’exploitation à l’aide de la requête suivante :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPwQrCMBAE0Hu/Yo4NVLBn6Td4ULyWtV1tMJtIsoEq/XhbC4J48jgw+5h1rBDrW0UDDakjR7HsWUIrdOM2cbScakxIWYSiffJSL49W+KAkd2N2hVsMGv8yaPw2furFhCVu1gifpelC9loa9Hyh7LTZInh8FFiPSP7K5fufap1UoR4Mzg/s04njjEb2PUfofNYNFPUFtJiguAEBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Utilisez l’opérateur [`make-series`](kusto/query/make-seriesoperator.md) pour créer un ensemble de trois séries chronologiques dans lequel :
    - `num=count()` : est la série chronologique du trafic
    - `range(min_t, max_t, 1h)` : la série chronologique est créée en compartiments de 1 heure dans l’intervalle de temps (timestamps les plus anciens et les plus récents des enregistrements de la table)
    - `default=0` : spécifie la méthode de remplissage des compartiments manquants pour créer une série chronologique régulière. Vous pouvez également utiliser [`series_fill_const()`](kusto/query/series-fill-constfunction.md), [`series_fill_forward()`](kusto/query/series-fill-forwardfunction.md), [`series_fill_backward()`](kusto/query/series-fill-backwardfunction.md) et [`series_fill_linear()`](kusto/query/series-fill-linearfunction.md) pour des modifications
    - `byOsVer` : partitionne par système d’exploitation
- La structure de données d’une série chronologique réelle est un tableau numérique de la valeur agrégée par compartiment de temps. Nous utilisons `render timechart` pour la visualisation.

Dans le tableau ci-dessus, nous avons trois partitions. Nous pouvons créer une série chronologique distincte (Windows 10 en rouge, Windows 7 en bleu et Windows 8.1 en vert) pour chaque version du système d’exploitation, comme dans le graphique ci-dessous :

![Partition de série chronologique](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Fonctions d’analyse de séries chronologiques

Dans le cadre de cette section, nous allons exécuter des fonctions de traitement de série classiques.
Une fois qu’un ensemble de séries chronologiques est créé, Azure Data Explorer prend en charge une liste croissante de fonctions de traitement et d’analyse de celles-ci, qui figure dans la [documentation sur les séries chronologiques](kusto/query/machine-learning-and-tsa.md). Nous allons décrire quelques fonctions représentatives de traitement et d’analyse des séries chronologiques.

### <a name="filtering"></a>Filtrage

Le filtrage est une pratique courante dans le traitement de signal, utile pour les tâches de traitement de séries chronologiques (par exemple, lissage d’un signal bruyant, détection de modifications).
- Il existe deux fonctions de filtrage génériques :
    - [`series_fir()`](kusto/query/series-firfunction.md) : appliquant un filtre FIR. Utilisée pour effectuer un calcul simple de moyenne mobile et de différenciation des séries chronologiques pour la détection des modifications.
    - [`series_iir()`](kusto/query/series-iirfunction.md) : appliquant un filtre IIR. Utilisée pour effectuer un lissage exponentiel et calculer une somme cumulée.
- Étendre (`Extend`) l’ensemble de séries chronologiques en ajoutant une nouvelle série de moyennes mobiles de 5 compartiments (nommée *ma_num*) à la requête :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPQavCMBCE7/6KOSYQ4fXgSfobPDx517C2q4bXpLLZQBV/vKkFQTx5WRh25tvZgRUxJK9ooWPuaCAxPcfRR/pnn1kC5wZ35BIjSbjxbDf7EPlXKV6s3a6GmUHTVwya3hkf9tUds1wvEqnEthtLUmPR85HKoO0PxoQXBSFBKJ3YPP9xSyWH5mxxuGKX/1gqlCfl1Neln5EL3R+DmCodhC9MahqHjXVQKbxMW5NScyzQerA7k+gDa1tswzsBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Filtrage de séries chronologiques](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Analyse de régression

Azure Data Explorer prend en charge l’analyse de régression linéaire segmentée pour estimer la tendance des séries chronologiques.
- Utilisez [series_fit_line()](kusto/query/series-fit-linefunction.md) pour ajuster la meilleure droite à une série chronologique afin d’obtenir une détection de tendance générale.
- Utilisez [series_fit_2lines()](kusto/query/series-fit-2linesfunction.md) pour détecter des changements de tendances par rapport à une ligne de base, qui sont utiles dans les scénarios de surveillance.

Exemple de fonctions `series_fit_line()` et `series_fit_2lines()` dans une requête de série chronologique :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuKqUUitKEnNS1GACMSnZZbEG+Vk5qUWa1Rq6iCLggSBYkAdRUD1qUUKIIHkjMSiEoXyzJIMjYrk/JzS3DzbCk0AUIIJ02EAAAA=) **\]**

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Régression de série chronologique](media/time-series-analysis/time-series-regression.png)

- Bleu : série chronologique d’origine
- Vert : droite ajustée
- Rouge : deux droites ajustées

> [!NOTE]
> La fonction a détecté avec précision le point de saut (changement de niveau).

### <a name="seasonality-detection"></a>Détection de saisonnalité

De nombreuses métriques suivent un modèle saisonnier (périodique). Le trafic des utilisateurs des services cloud contient généralement des modèles quotidiens et hebdomadaires qui atteignent le niveau le plus élevé vers le milieu de journée, et le niveau le plus bas pendant la nuit et le week-end. Des capteurs IoT prennent des mesures à intervalles réguliers. Des mesures physiques, par exemple de température, de pression ou d’humidité, peuvent également suivre un modèle saisonnier.

L’exemple suivant applique une détection de saisonnalité au trafic d’un mois d’un service web (compartiments de 2 heures) :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuaqUShKzUtJLVIoycxNTc5ILCoBAHrjE80fAAAA) **\]**

```kusto
demo_series3
| render timechart 
```

![Saisonnalité de série chronologique](media/time-series-analysis/time-series-seasonality.png)

- Utilisez [series_periods_detect()](kusto/query/series-periods-detectfunction.md) pour détecter automatiquement des périodes dans la série chronologique. 
- Utilisez [series_periods_validate()](kusto/query/series-periods-validatefunction.md) si vous savez qu’une métrique doit comporter des périodes distinctes spécifiques et souhaitez vérifier qu’elles existent.

> [!NOTE]
> L’inexistence de périodes distinctes constitue une anomalie.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12OwQ6CMBBE737FHKmpVtAr39IguwkYyzZ0IZj48TZSLx533szOEAfxieeR0/XwRpzlwb2iilkSShapl5mTQYvd5QvxxJqd1bQEi8vZor6RawaLxsA5FewcOjBKBOP0PXUMXL7lyrCeeIvdRPjrzIw35Qyoe6W2GY4qJMv9yb91xtX0AS7N323BAAAA) **\]**

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

| periods | scores | jours |
| --- | --- | --- |
| 84 | 0,820622786055595 | 7 |
| 12 | 0,764601405803502 | 1 |

La fonction détecte les saisonnalités quotidienne et hebdomadaire. La saisonnalité quotidienne présente un score inférieur à celui de la saisonnalité hebdomadaire, car les jours du week-end diffèrent des jours de la semaine.

### <a name="element-wise-functions"></a>Fonctions par élément

Des opérations arithmétiques et logiques peuvent être appliquées à une série chronologique. À l’aide de [series_subtract()](kusto/query/series-subtractfunction.md), vous pouvez calculer une série chronologique résiduelle, qui est la différence entre une métrique brute d’origine et une métrique lissée, puis rechercher des anomalies dans le signal résiduel :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WQQU/DMAyF7/sVT5waqWjrgRPqb+AAgmPltR6LSNLJcdhA+/G4izRAnLhEerbfl2cHVkSfBkUPnfNIgaSZOM5DpDceMovn3OGMXGIk8Z+8jDdPPvKjUjw4d78KC4NO/2LQ6Tfjz/jqjEXeVolUYj/OJWnjMPGOStB+gznhSoFPEEqv3Fz2aWukFt3eYfuBh/zMYlA+KafJmsOCrPRh56Ux2UL4wKRN1+LOtVApXF/37RTOfioUfvpz2arQqBVS2Q7rtc6wa4wlkPLVCLXIqE7DHvcsXOOh73Hz4tM0HzO6zQ1gDOx8UOvZrtayst0Y7z4babkkYQxMyQbGPYnCiGIxTS/fXGpfwk+n7uQBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| extend residual_num=series_subtract(num, ma_num) //to calculate residual time series
| where OsVer == "Windows 10"   // filter on Win 10 to visualize a cleaner chart 
| render timechart
```

![Opérations de série chronologique](media/time-series-analysis/time-series-operations.png)

- Bleu : série chronologique d’origine
- Rouge : série chronologique lissée
- Vert : série chronologique résiduelle

## <a name="time-series-workflow-at-scale"></a>Workflow de série chronologique à grande échelle

L’exemple ci-dessous montre comment ces fonctions peuvent être appliquées à grande échelle à des milliers de séries chronologiques en quelques secondes à des fins de détection d’anomalies. Pour voir quelques exemples d’enregistrements de télémétrie d’une métrique de nombre de lectures d’un service de base de données sur quatre jours, exécutez la requête suivante :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKEnMTlUwAQArfAiiGgAAAA==) **\]**

```kusto
demo_many_series1
| take 4 
```

| timestamp | Loc | anonOp | DB | DataRead |
| --- | --- | --- | --- | --- |
| 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 262 | 0 |
| 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 241 | 0 |
| 2016-09-11 21:00:00.0000000 | Loc 9 | -865998331941149874 | 262 | 279862 |
| 2016-09-11 21:00:00.0000000 | Loc 9 | 371921734563783410 | 255 | 0 |

Et statistiques simples :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVcgrzbVNzi/NK9HQ1FHIzcyLL7EFkhohnr6uwSGOvgEg0cQKkGhiBZIoAEq2dK9VAAAA) **\]**

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

| num | min\_t | max\_t |
| --- | --- | --- |
| 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

La création d’une série chronologique en compartiments de 1 heure de la métrique de lecture (total de quatre jours * 24 heures = 96 points), entraîne une fluctuation du modèle normal :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPMQvCMBSE9/6KGxOoYGfpIOjgUBDtXh7twwabFF6ittIfb2rBQSfHg+8+7joOsMZVATlC72vqSFTDtq8subHyLIZ9hgn+Zi2JefKMq/JQ7M/ltjhqvQGSbrbQ8JeFhm/LTyGZInbl1RIhTI3P6X5ROwp0ikmjd/hYYByE3IXV+1G6TEqRtTqahF3DgmAs1y1JwMOEVo0Rzdf6BbBH5FAHAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Série chronologique à grande échelle](media/time-series-analysis/time-series-at-scale.png)

Le comportement décrit ci-dessus est trompeur, car la série chronologique normale unique est agrégée à partir de milliers d’instances différentes qui peuvent suivre des modèles anormaux. C’est pourquoi, nous créons une série chronologique par instance. Une instance est définie par Loc (emplacement), anonOp (opération) et DB (machine spécifique).

Combien de séries chronologiques pouvons-nous créer ?

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVUiqVPDJT9ZR8C/QUXBxAkol55fmlQAAWEsFxjQAAAA=) **\]**

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

| Count |
| --- |
| 18339 |

Nous allons à présent créer un ensemble de 18339 séries chronologiques de la métrique du nombre de lectures. Nous ajoutons la clause `by` à l’instruction make-series, appliquons une régression linéaire, et sélectionnons les deux premières séries chronologiques en termes de tendance décroissante la plus significative :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPsU7DQBBE+3zFdLmTTGHSgFAKUCiQiIKIe2u5rJ0T9l3YWwcH5eO5JBIFVJSzmnmz07Gi96FWzKExOepIzIb7WPcUDnVi8ZxKHJGGvifxX3yym+pp+biu7pcv1t4Bk+5EofFfFBp/U/4EJsdse+eri4QwbdKc9q1ZkNJrVhYx4IcCHyAUWjbnRcXlpQLl1uLtgOfoCqx2BRYPGcyjctjASPoYSLhA6uKObR5waasbr3XnA5tzrc0RjTtcn0hnKyg55KtkDAvU9+y2JIpPr1ujXjueT9cse+8YlVDTeIfVoNQymiiZ5ENSCi4vM3FQxAblzWx2a6f2G2UcBRyWAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Deux premières séries chronologiques](media/time-series-analysis/time-series-top-2.png)

Afficher les instances :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPvW4CMRCEe55iSlsyBWkjChApIoESAb21udsQg38O26AD8fDx3SEUJVXKWc18s2M5wxmvM6bIIVVkKYqaXdCO/EUnjobTBDekk3MUzZU7u9i+rl4229nqXcpnYGQ7CrX/olD7m/InMLoV24HHg0RkqtOUzjuxoEzroiSCx4MC4xHJ71j0i9TwksLkS+LjgmWoFN4ahcW8gLnN7GuImI4niqyQbGhYlgFDm/40WVvjWfS1skRyaPDUkXorKFXl2MSw5yr/pN9Z31SyxuhbAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc
| project Loc, Op, DB, slope 
```

| Loc | Op | DB | slope |
| --- | --- | --- | --- |
| Loc 15 | 37 | 1151 | -102743.910227889 |
| Loc 13 | 37 | 1249 | -86303.2334644601 |

En moins de deux minutes, Azure Data Explorer a analysé près de 20 000 séries chronologiques et détecté deux séries chronologiques anormales dans lesquelles le nombre de lectures chutait soudainement.

Ces fonctionnalités avancées combinées avec la rapidité des performances d’Azure Data Explorer produisent une solution unique et puissante pour l’analyse de séries chronologiques.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [Détection d’anomalies et la prévision pour la série chronologique](anomaly-detection.md) dans Azure Data Explorer.
* Découvrez les [fonctionnalités d’apprentissage automatique](machine-learning-clustering.md) dans Azure Data Explorer.