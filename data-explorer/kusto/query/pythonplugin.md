---
title: Python plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Python plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 5192474779fb712595ff1c25785892bb543fda84
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765715"
---
# <a name="python-plugin"></a>Plug-in Python

::: zone pivot="azuredataexplorer"

Le plugin Python exécute une fonction définie par l’utilisateur (UDF) à l’aide d’un script Python. Le script Python obtient des données tabulaires comme son entrée, et devrait produire une sortie tabulaire.
Le temps d’exécution du plugin est hébergé dans [des bacs](../concepts/sandboxes.md)à sable, fonctionnant sur les nœuds du cluster.

## <a name="syntax"></a>Syntaxe

*T* `|` `per_node` `python(` *output_schema* `,` *script* `,` *script_parameters*`,` *external_artifacts*[ `=` `single`( | )] output_schema script [ script_parameters ] [ external_artifacts ] `evaluate` `hint.distribution``)`

## <a name="arguments"></a>Arguments

* *output_schema*: Un `type` littéral qui définit le schéma de sortie des données tabulaires, retournés par le code Python.
    * Le format `typeof(`est: *ColumnName* `:` *ColumnType* [, ...] `)`, par `typeof(col1:string, col2:long)`exemple: .
    * Pour étendre le schéma d’entrée, utilisez la syntaxe suivante :`typeof(*, col1:string, col2:long)`
* *script*: `string` Un texte littéral qui est le script Python valide à exécuter.
* *script_parameters*: Un `dynamic` littéral optionnel, qui est un sac de propriété de paires de nom/valeur à transmettre au script Python comme dictionnaire réservé `kargs` (voir les variables Python [réservées).](#reserved-python-variables)
* *hint.distribution*: Un indice optionnel pour l’exécution du plugin à distribuer sur plusieurs nœuds cluster.
  * La valeur par défaut est `single`.
  * `single`: Une seule instance du script s’exécutera sur l’ensemble des données de requête.
  * `per_node`: Si la requête avant la distribution du bloc Python est distribuée, une instance du script s’exécutera sur chaque nœud sur les données qu’il contient.
* *external_artifacts*: Un littéral optionnel `dynamic` qui est un sac de nom de propriété & paires d’URL pour les artefacts qui sont accessibles à partir du stockage en nuage et peuvent être mis à la disposition du script à utiliser au moment de l’exécution.
  * Les URL mentionnées dans ce sac de propriété sont tenues de :
  * Soyez inclus dans la [politique d’appel](../management/calloutpolicy.md)du cluster .
    2. Etre dans un endroit accessible au public, ou fournir les informations d’identification nécessaires, comme expliqué dans [les chaînes de connexion de stockage](../api/connection-strings/storage.md).
  * Les artefacts sont mis à la disposition du script `.\Temp`à consommer à partir d’un répertoire temporaire local, et les noms fournis dans le sac de propriété sont utilisés comme noms de fichiers locaux (voir [l’exemple](#examples) ci-dessous).
  * Pour plus d’informations, voir [l’annexe](#appendix-installing-packages-for-the-python-plugin) ci-dessous.

## <a name="reserved-python-variables"></a>Variables Python réservées

Les variables suivantes sont réservées à l’interaction entre le langage de requête Kusto et le code Python :

* `df`: Les données tabulaires `T` d’entrée `pandas` (les valeurs ci-dessus), en tant que DataFrame.
* `kargs`: La valeur de *l’argument script_parameters,* en tant que dictionnaire Python.
* `result`: `pandas` Un DataFrame créé par le script Python dont la valeur devient les données tabulaires qui est envoyée à l’opérateur de requête Kusto qui suit le plugin.

## <a name="onboarding"></a>Mise en route

* Le plugin est désactivé par défaut.
* Les conditions préalables pour permettre le plugin sont énumérées [ici.](../concepts/sandboxes.md#prerequisites)
* Activez ou désactivez le plugin du [portail Azure dans l’onglet **Configuration** de votre cluster](https://docs.microsoft.com/azure/data-explorer/language-extensions).

## <a name="notes-and-limitations"></a>Notes et limitations

* L’image de bac à sable Python est basée sur la distribution *Anaconda 5.2.0* avec le moteur *Python 3.6.*
  La liste de ses paquets peut être trouvée [ici](http://docs.anaconda.com/anaconda/packages/old-pkg-lists/5.2.0/py3.6_win-64/) (un petit pourcentage de paquets peut être incompatible avec les limitations appliquées par le bac à sable dans lequel le plugin est exécuté).
* L’image Python contient également `tensorflow` `keras`des `torch` `hdbscan`paquets ML courants: , , , `xgboost` et d’autres paquets utiles.
* Le plugin importe *numpy* (comme `np`) & `pd` *pandas* (par défaut.  Vous pouvez importer d’autres modules au besoin.
* **[Ingestion des](../management/data-ingestion/ingest-from-query.md) stratégies de requête et [de mise à jour](../management/updatepolicy.md)**
  * Il est possible d’utiliser le plugin dans les requêtes qui sont:
      1. Défini comme faisant partie d’une stratégie de mise à jour, dont le tableau source est ingéré à l’aide d’ingestion *non-streaming.*
      2. Exécutez-vous dans le cadre d’une commande qui ingérer à partir d’une requête (par exemple `.set-or-append`).
  * Dans les deux cas ci-dessus, il est recommandé de vérifier que le volume et la fréquence de l’ingestion, ainsi que la complexité et l’utilisation des ressources de la logique Python sont alignés avec [les limites sandbox](../concepts/sandboxes.md#limitations), et les ressources disponibles de la grappe.
    Ne pas le faire peut entraîner [des erreurs de limitation](../concepts/sandboxes.md#errors).
  * Il *n’est pas* possible d’utiliser le plugin dans une requête qui est définie comme faisant partie d’une stratégie de mise à jour, dont le tableau source est ingéré à l’aide de [l’ingestion en continu](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming).

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
:::image type="content" source="images/samples/sine-demo.png" alt-text="démo sine":::

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

* Réduisez l’ensemble de données d’entrée du plugin à la quantité minimale requise (colonnes/lignes).
    * Utilisez des filtres sur l’ensemble de données source, si possible, avec le langage de requête de Kusto.
    * Pour effectuer un calcul sur un sous-ensemble des colonnes sources, ne projetez que ces colonnes avant d’invoquer le plugin.
* Utilisez `hint.distribution = per_node` chaque fois que la logique de votre script est distribuable.
    * Vous pouvez également utiliser [l’opérateur](partitionoperator.md) de partition pour le partage de l’ensemble de données d’entrée.
* Utilisez le langage de requête de Kusto, dans la mesure du possible, pour implémenter la logique de votre script Python.

    Exemple :

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

* Pour générer des cordes multi-lignes `Kusto.Explorer`contenant le script Python en , copiez votre script Python à partir de votre éditeur Python préféré (*Jupyter*, *Visual Studio Code*, *PyCharm*, etc.), puis soit:
    * Appuyez sur *F2* pour ouvrir la fenêtre **Edit in Python.** Collez le script dans cette fenêtre. Sélectionnez **OK**. Le script sera décoré de citations et de nouvelles lignes (il est donc valable dans Kusto) et automatiquement collé dans l’onglet requête.
    * Coller le code Python directement dans l’onglet requête, sélectionnez ces lignes et appuyez sur *Ctrl-K*, clé chaude *Ctrl-S* pour les décorer comme ci-dessus (pour inverser la presse *Ctrl-K*, clé chaude *Ctrl-M).* [Voici](../tools/kusto-explorer-shortcuts.md#query-editor) la liste complète des raccourcis De Requête Editor.
* Pour éviter les conflits entre les délimitations de cordes Kusto`'`et les littérals de cordes Python, nous vous`"`recommandons d’utiliser des caractères de citation unique () pour les littérals de cordes Kusto dans les requêtes Kusto, et les caractères de double citation () pour les littérals de cordes Python dans les scripts Python.
* Utilisez [l’opérateur de données externes](externaldata-operator.md) pour obtenir le contenu d’un script que vous avez stocké dans un emplacement externe, comme le stockage Azure Blob.
  
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

## <a name="appendix-installing-packages-for-the-python-plugin"></a>Annexe: Installation de paquets pour le plugin Python

Vous devrez peut-être vous installer lui-même le paquet(s) en raison de l’une des raisons suivantes :

* Le paquet est privé et est le vôtre.
* Le paquet est public mais n’est pas inclus dans l’image de base du plugin.

Vous pouvez installer des paquets en suivant ces étapes :

1. Préalable unique :
  
  a. Créez un contenant blob pour accueillir le paquet(s), de préférence dans la même région que votre cluster.
    * Par exemple https://artifcatswestus.blob.core.windows.net/python : (en supposant que votre cluster se trouve dans l’ouest des États-Unis)
  
  b. Modifier la [politique Callout](../management/calloutpolicy.md) du cluster pour permettre l’accès à cet emplacement.
    * Cela nécessite [des autorisations AllDatabasesAdmin.](../management/access-control/role-based-authorization.md)
    * Par exemple, pour permettre l’accès https://artifcatswestus.blob.core.windows.net/pythonà un blob situé dans , la commande de courir est:

      ```kusto
      .alter-merge cluster policy callout @'[ { "CalloutType": "sandbox_artifacts", "CalloutUriRegex": "artifcatswestus\\.blob\\.core\\.windows\\.net/python/","CanCall": true } ]'
      ```

2. Pour les forfaits publics (en [PyPi](https://pypi.org/) ou sur d’autres canaux) a. Téléchargez le forfait et ses dépendances.
  b. Si nécessaire, compilez`*.whl`aux fichiers de roue ( ) :
    * D’une fenêtre cmd (dans votre environnement Python local) courir:
      ```python
      pip wheel [-w download-dir] package-name.
      ```

3. Créez un fichier zip contenant le paquet requis et ses dépendances :

    * Pour les forfaits publics : zip les fichiers qui ont été téléchargés dans l’étape précédente.
    * Remarques :
        * Assurez-vous de `.whl` zip les fichiers eux-mêmes et *non pas* leur dossier parent.
        * Vous pouvez `.whl` sauter des fichiers pour les paquets qui existent déjà avec la même version dans l’image sandbox de base.
    * Pour les forfaits privés : zip le dossier de l’emballage et ceux de ses dépendances

4. Téléchargez le fichier zippé sur un blob dans l’emplacement des artefacts (de l’étape 1.).

5. Appeler `python` le plugin:
    * Spécifiez le `external_artifacts` paramètre avec un sac de nom et une référence au fichier zip (URL du blob).
    * Dans votre code python `Zipackage` en `sandbox_utils` ligne: `install()` importer et appeler sa méthode avec le nom du fichier zip.

### <a name="example"></a>Exemple

Installation du paquet [Faker](https://pypi.org/project/Faker/) qui génère de fausses données :

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

Ce n’est pas pris en charge dans Azure Monitor

::: zone-end
