---
title: Plug-in Python-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit le plug-in Python dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 3d88b04220851b8218d0d23fed93ba3627720afd
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737825"
---
# <a name="python-plugin"></a>Plug-in Python

::: zone pivot="azuredataexplorer"

Le plug-in Python exécute une fonction définie par l’utilisateur (UDF) à l’aide d’un script Python. Le script Python obtient les données tabulaires en tant qu’entrée et est censé produire une sortie tabulaire.
Le runtime du plug-in est hébergé dans les [bacs à sable (sandbox](../concepts/sandboxes.md)), en cours d’exécution sur les nœuds du cluster.

## <a name="syntax"></a>Syntaxe

*T* `|` `single``,` *script* *output_schema* `,` *external_artifacts*[`hint.distribution` (`per_node`)] `python(`output_schema script [`,` *script_parameters*] [external_artifacts] |  `evaluate` `=``)`

## <a name="arguments"></a>Arguments

* *output_schema*: `type` littéral qui définit le schéma de sortie des données tabulaires, retourné par le code Python.
    * Le format est : `typeof(` *ColumnName* `:` *ColumnType* [,...] `)`, par exemple : `typeof(col1:string, col2:long)`.
    * Pour étendre le schéma d’entrée, utilisez la syntaxe suivante :`typeof(*, col1:string, col2:long)`
* *script*: `string` littéral qui est le script Python valide à exécuter.
* *script_parameters*: un littéral `dynamic` facultatif, qui est un conteneur de propriétés de paires nom/valeur à passer au script Python en tant que dictionnaire `kargs` réservé (consultez [variables python réservées](#reserved-python-variables)).
* *hint. distribution*: indication facultative pour que l’exécution du plug-in soit distribuée sur plusieurs nœuds de cluster.
  * La valeur par défaut est `single`.
  * `single`: Une seule instance du script est exécutée sur l’ensemble des données de la requête.
  * `per_node`: Si la requête avant le bloc Python est distribuée, une instance du script s’exécutera sur chaque nœud sur les données qu’elle contient.
* *external_artifacts*: un littéral `dynamic` facultatif qui est un jeu de propriétés nom & paires d’URL pour les artefacts accessibles depuis le stockage cloud et pouvant être mis à la disposition du script à utiliser au moment de l’exécution.
  * Les URL référencées dans ce conteneur de propriétés sont requises pour :
  * Inclus dans la [stratégie de légende](../management/calloutpolicy.md)du cluster.
    2. Se trouve dans un emplacement disponible publiquement ou fournissez les informations d’identification nécessaires, comme expliqué dans [chaînes de connexion de stockage](../api/connection-strings/storage.md).
  * Les artefacts sont rendus disponibles pour le script à utiliser à partir d’un répertoire `.\Temp`temporaire local,, et les noms fournis dans le conteneur de propriétés sont utilisés comme noms de fichiers locaux (Voir l' [exemple](#examples) ci-dessous).
  * Pour plus d’informations, consultez [l’annexe](#appendix-installing-packages-for-the-python-plugin) ci-dessous.

## <a name="reserved-python-variables"></a>Variables python réservées

Les variables suivantes sont réservées pour l’interaction entre le langage de requête Kusto et le code Python :

* `df`: Données tabulaires d’entrée (les valeurs `T` ci-dessus), `pandas` en tant que tableau.
* `kargs`: La valeur de l’argument *script_parameters* , sous la forme d’un dictionnaire Python.
* `result`: `pandas` Tableau créé par le script Python dont la valeur devient les données tabulaires qui sont envoyées à l’opérateur de requête Kusto qui suit le plug-in.

## <a name="onboarding"></a>Mise en route

* Le plug-in est désactivé par défaut.
* Les conditions préalables à l’activation du plug-in sont répertoriées [ici](../concepts/sandboxes.md#prerequisites).
* Activez ou désactivez le plug-in dans le [portail Azure sous l’onglet **configuration** de votre cluster](https://docs.microsoft.com/azure/data-explorer/language-extensions).

## <a name="notes-and-limitations"></a>Remarques et limitations

* L’image Python sandbox est basée sur la distribution *Anaconda 5.2.0* avec le moteur *python 3,6* .
  La liste de ses packages est disponible [ici](http://docs.anaconda.com/anaconda/packages/old-pkg-lists/5.2.0/py3.6_win-64/) (un petit pourcentage de packages peut être incompatible avec les limitations imposées par le bac à sable (sandbox) dans lequel le plug-in est exécuté).
* L’image Python contient également des packages ml communs `tensorflow`: `keras`, `torch`, `hdbscan`, `xgboost` et d’autres packages utiles.
* Le plug- *numpy* in importe numpy `np`(comme) & *pandas* (AS `pd`) par défaut.  Vous pouvez importer d’autres modules en fonction des besoins.
* **Ingestion [des stratégies de requête et de](../management/data-ingestion/ingest-from-query.md) [mise à jour](../management/updatepolicy.md)**
  * Il est possible d’utiliser le plug-in dans les requêtes suivantes :
      1. Défini dans le cadre d’une stratégie de mise à jour, dont la table source est ingérée pour l’utilisation de la *non-diffusion en continu* .
      2. Exécuter dans le cadre d’une commande qui est ingérée à partir d' `.set-or-append`une requête (par exemple,).
  * Dans les deux cas, il est recommandé de vérifier que le volume et la fréquence de l’ingestion, ainsi que la complexité et l’utilisation des ressources de la logique Python sont alignés avec les [limitations du bac à sable (sandbox](../concepts/sandboxes.md#limitations)) et les ressources disponibles du cluster.
    Dans le cas contraire, vous risquez de provoquer des [Erreurs de limitation](../concepts/sandboxes.md#errors).
  * Il n’est *pas* possible d’utiliser le plug-in dans une requête qui est définie dans le cadre d’une stratégie de mise à jour, dont la table source est ingérée à l’aide de l’ingestion de [diffusion en continu](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming).

## <a name="examples"></a>Exemples

```kusto
range x from 1 to 360 step 1
| evaluate python(
//
typeof(*, fx:double),               //  Output schema: append a new fx column to original table 
//
'result = df\n'                     //  The Python decorated script
'n = df.shape[0]\n'
'g = kargs["gain"]\n'
'f = kargs["cycles"]\n'
'result["fx"] = g * np.sin(df["x"]/n*2*np.pi*f)\n'
//
, pack('gain', 100, 'cycles', 4)    //  dictionary of parameters
)
| render linechart 
```

:::image type="content" source="images/plugin/sine-demo.png" alt-text="démonstration sinus" border="false":::

```kusto
print "This is an example for using 'external_artifacts'"
| evaluate python(
    typeof(File:string, Size:string),
    "import os\n"
    "result = pd.DataFrame(columns=['File','Size'])\n"
    "sizes = []\n"
    "path = '.\\\\Temp'\n"
    "files = os.listdir(path)\n"
    "result['File']=files\n"
    "for file in files:\n"
    "    sizes.append(os.path.getsize(path + '\\\\' + file))\n"
    "result['Size'] = sizes\n"
    "\n",
    external_artifacts = 
        dynamic({"this_is_my_first_file":"https://kustoscriptsamples.blob.core.windows.net/samples/R/sample_script.r",
                 "this_is_a_script":"https://kustoscriptsamples.blob.core.windows.net/samples/python/sample_script.py"})
)
```

| Fichier                  | Taille |
|-----------------------|------|
| this_is_a_script      | 120  |
| this_is_my_first_file | 105  |

## <a name="performance-tips"></a>Conseils sur les performances

* Réduisez le jeu de données d’entrée du plug-in sur la quantité minimale requise (colonnes/lignes).
    * Utilisez des filtres sur le jeu de données source, si possible, avec le langage de requête de Kusto.
    * Pour effectuer un calcul sur un sous-ensemble des colonnes sources, projetez uniquement cette colonne avant d’appeler le plug-in.
* Utilisez `hint.distribution = per_node` chaque fois que la logique de votre script est distribuable.
    * Vous pouvez également utiliser l' [opérateur de partition](partitionoperator.md) pour partitionner le jeu de données d’entrée.
* Utilisez le langage de requête de Kusto, dans la mesure du possible, pour implémenter la logique de votre script Python.

    Exemple :

    ```kusto    
    .show operations
    | where StartedOn > ago(7d) // Filtering out irrelevant records before invoking the plugin
    | project d_seconds = Duration / 1s // Projecting only a subset of the necessary columns
    | evaluate hint.distribution = per_node python( // Using per_node distribution, as the script's logic allows it
        typeof(*, _2d:double),
        'result = df\n'
        'result["_2d"] = 2 * df["d_seconds"]\n' // Negative example: this logic should have been written using Kusto's query language
      )
    | summarize avg = avg(_2d)
    ```

## <a name="usage-tips"></a>Conseils d’utilisation

* Pour générer des chaînes multilignes contenant le script Python `Kusto.Explorer`dans, copiez votre script Python à partir de votre éditeur Python favori (*Jupyter*, *Visual Studio code*, *PyCharm*, etc.), puis :
    * Appuyez sur *F2* pour ouvrir la fenêtre **modifier dans python** . Collez le script dans cette fenêtre. Sélectionnez **OK**. Le script sera décoré avec des guillemets et de nouvelles lignes (il est donc valide dans Kusto) et collé automatiquement dans l’onglet requête.
    * Collez le code python directement sous l’onglet requête, sélectionnez ces lignes et appuyez sur *CTRL + k*, *CTRL + S* touche d’accès rapide pour les décorer comme indiqué ci-dessus (pour les inverser, appuyez sur *CTRL + k*, *Ctrl + M* touche d’accès rapide). [Voici](../tools/kusto-explorer-shortcuts.md#query-editor) la liste complète des raccourcis de l’éditeur de requête.
* Pour éviter les conflits entre les délimiteurs de chaîne Kusto et les littéraux de chaîne Python, nous vous`'`recommandons d’utiliser des guillemets simples () pour les littéraux de chaîne`"`Kusto dans des requêtes Kusto et des guillemets doubles () pour les littéraux de chaîne python dans les scripts Python.
* Utilisez l' [opérateur ExternalData](externaldata-operator.md) pour obtenir le contenu d’un script que vous avez stocké dans un emplacement externe, tel que le stockage d’objets BLOB Azure.
  
    **Exemple**

    ```kusto
    let script = 
        externaldata(script:string)
        [h'https://kustoscriptsamples.blob.core.windows.net/samples/python/sample_script.py']
        with(format = raw);
    range x from 1 to 360 step 1
    | evaluate python(
        typeof(*, fx:double),
        toscalar(script), 
        pack('gain', 100, 'cycles', 4))
    | render linechart 
    ```

## <a name="appendix-installing-packages-for-the-python-plugin"></a>Annexe : installation de packages pour le plug-in Python

Vous devrez peut-être installer automatiquement le ou les packages pour l’une des raisons suivantes :

* Le package est privé et est votre propre.
* Le package est public, mais n’est pas inclus dans l’image de base du plug-in.

Vous pouvez installer des packages en procédant comme suit :

1. Prérequis à usage unique :
  
  a. Créez un conteneur d’objets BLOB pour héberger le ou les packages, de préférence dans la même région que votre cluster.
    * Par exemple : `https://artifcatswestus.blob.core.windows.net/python` (en supposant que votre cluster se trouve dans l’ouest des États-Unis)
  
  b. Modifiez la stratégie de [légende](../management/calloutpolicy.md) du cluster pour permettre l’accès à cet emplacement.
    * Cela nécessite des autorisations [AllDatabasesAdmin](../management/access-control/role-based-authorization.md) .
    * Par exemple, pour activer l’accès à un objet BLOB `https://artifcatswestus.blob.core.windows.net/python`situé dans, la commande à exécuter est la suivante :

      ```kusto
      .alter-merge cluster policy callout @'[ { "CalloutType": "sandbox_artifacts", "CalloutUriRegex": "artifcatswestus\\.blob\\.core\\.windows\\.net/python/","CanCall": true } ]'
      ```

2. Pour les packages publics (dans [PyPi](https://pypi.org/) ou d’autres canaux) a. Téléchargez le package et ses dépendances.
  b. Si nécessaire, compilez-`*.whl`les dans les fichiers Wheel () :
    * Dans une fenêtre cmd (dans votre environnement python local), exécutez :
      ```python
      pip wheel [-w download-dir] package-name.
      ```

3. Créez un fichier zip contenant le package requis et ses dépendances :

    * Pour les packages publics : compressez les fichiers qui ont été téléchargés à l’étape précédente.
    * Remarques :
        * Veillez à compresser les `.whl` fichiers eux-mêmes et *non* leur dossier parent.
        * Vous pouvez ignorer `.whl` les fichiers pour les packages qui existent déjà avec la même version dans l’image de bac à sable (sandbox) de base.
    * Pour les packages privés : compresser le dossier du package et ceux de ses dépendances

4. Chargez le fichier zippé dans un objet blob à l’emplacement artefacts (à partir de l’étape 1).

5. Appel du `python` plug-in :
    * Spécifiez `external_artifacts` le paramètre avec un conteneur de propriétés de nom et une référence au fichier zip (l’URL de l’objet BLOB).
    * Dans votre code python inline : importez `Zipackage` à partir de `sandbox_utils` et appelez sa `install()` méthode avec le nom du fichier. zip.

### <a name="example"></a>Exemple

Installation du package [factice](https://pypi.org/project/Faker/) qui génère des données factices :

```kusto
range Id from 1 to 3 step 1 
| extend Name=''
| evaluate python(typeof(*),
    'from sandbox_utils import Zipackage\n'
    'Zipackage.install("Faker.zip")\n'
    'from faker import Faker\n'
    'fake = Faker()\n'
    'result = df\n'
    'for i in range(df.shape[0]):\n'
    '    result.loc[i, "Name"] = fake.name()\n',
    external_artifacts=pack('faker.zip', 'https://artifacts.blob.core.windows.net/kusto/Faker.zip?...'))
```

| Id | Nom         |
|----|--------------|
|   1| Gary Tapia   |
|   2| Emma Evans   |
|   3| Ashley Bowen |

---

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
