---
title: Utiliser un bloc-notes Jupyter pour analyser des données dans Azure Data Explorer
description: Cet article montre comment analyser des données dans Azure Data Explorer à l’aide d’un bloc-notes Jupyter et de l’extension Kqlmagic.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 33fe6750c355c8dc79bbbd9223166786f844a502
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492829"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Utiliser un bloc-notes Jupyter et l’extension Kqlmagic pour analyser des données dans Azure Data Explorer

Jupyter Notebook est une application web open source qui vous permet de créer et de partager des documents contenant du code en temps réel, des équations, des visualisations et du texte narratif. L’utilisation inclut le nettoyage des données et la transformation, la simulation numérique, la modélisation statistique, la visualisation des données et le machine learning.
[Jupyter Notebook](https://jupyter.org/) offre des fonctions magiques qui étendent les fonctionnalités du noyau par la prise en charge de commandes supplémentaires. KQL magic est une commande qui étend les fonctionnalités du noyau Python dans Jupyter Notebook pour que vous puissiez exécuter des requêtes en langage Kusto en mode natif. Vous pouvez facilement combiner Python et le langage de requête Kusto pour interroger et visualiser des données à l’aide de la bibliothèque Plot.ly enrichie intégrée avec les commandes `render`. Les sources de données pour l’exécution de requêtes sont prises en charge. Ces sources de données incluent Azure Data Explorer, un service d’exploration de données rapide et hautement scalable pour les données de journal et de télémétrie, ainsi que pour les journaux Azure Monitor et Application Insights. KQL magic fonctionne également avec Azure Notebooks, Jupyter Lab et l’extension Jupyter de Visual Studio Code.

## <a name="prerequisites"></a>Prérequis

- Un compte e-mail professionnel qui est membre d’Azure Active Directory (AAD).
- Jupyter Notebook installé sur votre ordinateur local ou utilisation d’Azure Notebooks et clonage de l’exemple d’[Azure Notebook](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)

## <a name="install-kql-magic-library"></a>Installer la bibliothèque KQL magic

1. Installez KQL magic :

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Lorsque vous utilisez Azure Notebooks, cette étape n’est pas requise.

1. Chargez KQL magic :

    ```python
    %reload_ext Kqlmagic
    ```
    > [!NOTE]
    > Remplacez la version du noyau par Python 3.6 en cliquant sur Noyau > Change Kernel (Modifier le noyau) > Python 3.6
    
## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Se connecter au cluster d’aide d’Azure Data Explorer

Utilisez la commande suivante pour vous connecter aux *exemples* de base de données hébergés sur le cluster d’*aide*. Pour les utilisateurs non-Microsoft AAD, remplacez le nom du locataire `Microsoft.com` par votre locataire AAD.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Interroger et visualiser

Interrogez les données à l’aide de l’[opérateur de rendu](kusto/query/renderoperator.md) et visualisez les données à l’aide de la bibliothèque ploy.ly. Cette requête et cette visualisation fournit une expérience intégrée qui utilise KQL natif. Kqlmagic prend en charge la plupart des graphiques, à l’exception de `timepivot`, `pivotchart` et `ladderchart`. Le rendu est pris en charge avec tous les attributs, à l’exception de `kind`, `ysplit` et `accumulate`. 

### <a name="query-and-render-piechart"></a>Interroger et afficher un graphique en secteurs

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Interroger et afficher un graphique temporel

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Ces graphiques sont interactifs. Sélectionnez un intervalle de temps pour zoomer sur une durée spécifique.

### <a name="customize-the-chart-colors"></a>Personnaliser les couleurs du graphique

Si vous n’aimez pas la palette de couleurs par défaut, personnalisez les graphiques à l’aide des options de palette. Les palettes disponibles sont les suivantes : [Choisissez la palette de couleurs pour les résultats de votre graphique de requête KQL magic](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Pour obtenir la liste des palettes :

    ```python
    %kql --palettes -popup_window
    ```

1. Sélectionnez la palette de couleurs `cool` et affichez de nouveau la requête :

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Paramétrer une requête avec Python

KQL magic permet l’échange simple entre le langage de requête Kusto et Python. Pour en savoir plus : [Paramétrer votre requête KQL magic avec Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Utiliser une variable Python dans votre requête KQL

Vous pouvez utiliser la valeur d’une variable Python dans votre requête pour filtrer les données :

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>Convertir les résultats de la requête en tramedonnées Pandas

Vous pouvez accéder aux résultats d’une requête KQL dans les tramedonnées Pandas. Pour accéder aux résultats de la dernière requête exécutée par la variable `_kql_raw_result_` et convertir facilement les résultats en tramedonnées Pandas, procédez comme suit :

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Exemple

Dans de nombreux scénarios d’analyse, vous souhaitez créer des notebooks réutilisables qui contiennent de nombreuses requêtes et alimentent les résultats d’une requête dans les requêtes suivantes. L’exemple ci-dessous utilise la variable Python `statefilter` pour filtrer les données.

1. Exécutez une requête pour afficher les 10 principaux états avec maximum `DamageProperty` :

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Exécutez une requête pour extraire l’état principal et le définir dans une variable Python :

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Exécuter une requête en utilisant l’instruction `let` et la variable Python :

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Exécuter la commande d’aide :

    ```python
    %kql --help "help"
    ```

> [!TIP]
> Pour recevoir des informations sur toutes les configurations disponibles, utilisez `%config Kqlmagic`. Pour résoudre les problèmes et capturer les erreurs Kusto, comme les problèmes de connexion et les requêtes incorrectes, utilisez `%config Kqlmagic.short_errors=False`

## <a name="next-steps"></a>Étapes suivantes

Exécutez la commande d’aide pour explorer les exemples de notebooks suivants qui contiennent toutes les fonctionnalités prises en charge :
- [Prise en main de KQL magic pour Azure Data Explorer](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Prise en main de KQL magic pour Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Bien démarrer avec KQL magic pour les journaux Azure Monitor](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Paramétrer votre requête KQL magic avec Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Choisissez la palette de couleurs pour les résultats de votre graphique de requête KQL magic](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
