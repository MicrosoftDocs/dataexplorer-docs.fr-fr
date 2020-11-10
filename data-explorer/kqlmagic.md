---
title: Utiliser un bloc-notes Jupyter pour analyser des données dans Azure Data Explorer
description: Cet article montre comment analyser des données dans Azure Data Explorer à l’aide d’un notebook Jupyter et de l’extension kqlmagic.
author: orspod
ms.author: orspodek
ms.reviewer: maraheja
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/20/2020
ms.openlocfilehash: 857c654c70a2170a42902514718a52fbf7b02944
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349458"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Utiliser un notebook Jupyter et l’extension kqlmagic pour analyser des données dans Azure Data Explorer

Jupyter Notebook est une application web open source qui vous permet de créer et de partager des documents contenant du code en temps réel, des équations, des visualisations et du texte narratif. L’utilisation inclut le nettoyage des données et la transformation, la simulation numérique, la modélisation statistique, la visualisation des données et le machine learning.
[Jupyter Notebook](https://jupyter.org/) offre des fonctions magiques qui étendent les fonctionnalités du noyau par la prise en charge de commandes supplémentaires. kqlmagic est une commande qui étend les fonctionnalités du noyau Python dans Jupyter Notebook pour que vous puissiez exécuter des requêtes en langage Kusto en mode natif. Vous pouvez facilement combiner Python et le langage de requête Kusto pour interroger et visualiser des données à l’aide de la bibliothèque Plot.ly enrichie intégrée avec les commandes `render`. Les sources de données pour l’exécution de requêtes sont prises en charge. Ces sources de données incluent Azure Data Explorer, un service d’exploration de données rapide et hautement scalable pour les données de journal et de télémétrie, ainsi que pour les journaux Azure Monitor et Application Insights. kqlmagic fonctionne également avec Azure Data Studio, Jupyter Lab et l’extension Visual Studio Code Jupyter.

## <a name="prerequisites"></a>Prérequis

- Un compte e-mail professionnel qui est membre d’Azure Active Directory (Azure AD).
- Jupyter Notebook installé sur votre ordinateur local ou utiliser [Azure Data Studio](/sql/azure-data-studio/notebooks/notebooks-kqlmagic?view=sql-server-ver15)

## <a name="install-kqlmagic-library"></a>Installer la bibliothèque kqlmagic

1. Installez kqlmagic :

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```

1. Chargez kqlmagic :

    ```python
    %reload_ext Kqlmagic
    ```
    > [!NOTE]
    > Remplacez la version du noyau par Python 3.6 en cliquant sur Noyau > Change Kernel (Modifier le noyau) > Python 3.6
    
## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Se connecter au cluster d’aide d’Azure Data Explorer

Utilisez la commande suivante pour vous connecter aux *exemples* de base de données hébergés sur le cluster d’ *aide*. Pour les utilisateurs Azure AD non-Microsoft, remplacez le nom de locataire `Microsoft.com` par votre locataire Azure AD.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

> [!Note]
> Si vous utilisez votre propre cluster ADX, vous devez préciser la région dans la chaîne de connexion :   
   ```%kql azuredataexplorer://tenant="youecompany.com";code;cluster='mycluster.westus';database='mykustodb'```

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

Si vous n’aimez pas la palette de couleurs par défaut, personnalisez les graphiques à l’aide des options de palette. Les palettes disponibles sont les suivantes : [Choisissez la palette de couleurs pour les résultats de votre graphique de requête kqlmagic](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

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

Kqlmagic permet l’échange simple entre le langage de requête Kusto et Python. Pour en savoir plus : [Paramétrer votre requête kqlmagic avec Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

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

### <a name="example"></a> Exemple

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
- [Get started with kqlmagic for Azure Data Explorer](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Get started with kqlmagic for Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Get started with kqlmagic for Azure Monitor logs](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametrize your kqlmagic query with Python](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Choose colors palette for your kqlmagic query chart result](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)