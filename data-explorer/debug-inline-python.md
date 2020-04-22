---
title: Déboguer le code Python inline dans le langage de requête Kusto avec VS Code - Azure Data Explorer
description: Découvrez comment déboguer le code Python inline dans le langage de requête Kusto (KQL) avec VS Code.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 808d51ff5bc070449c87d14822fe4f41d2038950
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493489"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>Déboguer le code Python inline dans le langage de requête Kusto avec VS Code

Azure Data Explorer prend en charge l’exécution de code Python incorporé dans le langage de requête Kusto à l’aide du [plug-in python()](kusto/query/pythonplugin.md). Le runtime du plug-in est hébergé dans un bac à sable, un environnement Python isolé et sécurisé. Le plug-in python() étend les fonctionnalités natives du langage de requête Kusto avec l’énorme archive des packages Python OSS. Cette extension vous permet d’exécuter des algorithmes avancés (machine learning, intelligence artificielle, statistiques et série chronologique) dans le cadre de la requête.

Les outils du langage de requête Kusto ne sont pas pratiques pour développer et déboguer des algorithmes Python. Par conséquent, développez l’algorithme dans votre environnement de développement intégré Python préféré, comme Jupyter, PyCharm, VS ou VS Code. Une fois l’algorithme terminé, copiez et collez dans KQL. Pour améliorer et rationaliser ce workflow, Azure Data Explorer prend en charge l’intégration entre Kusto Explorer ou les clients d’interface utilisateur web et VS Code pour la création et le débogage du code Python inline dans KQL. 

> [!NOTE]
> Ce workflow peut uniquement être utilisé pour déboguer des tables d’entrée relativement petites (jusqu’à quelques Mo). C’est pourquoi vous devrez peut-être limiter l’entrée pour le débogage.  Si vous avez besoin de traiter une table volumineuse, limitez-la au débogage à l’aide de `| take`, `| sample` ou `where rand() < 0.x`.

## <a name="prerequisites"></a>Prérequis

1. Installez la [distribution Anaconda](https://www.anaconda.com/distribution/#download-section) Python. Dans **Options avancées**, sélectionnez **Ajouter Anaconda à ma variable d’environnement PATH**.
2. Installer [Visual Studio Code](https://code.visualstudio.com/Download)
3. Installez l’[extension Python pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

## <a name="run-your-query-in-your-client-application"></a>Exécuter votre requête dans votre application cliente

1. Dans votre application cliente, préfixez une requête contenant du code Python inline avec `set query_python_debug;`
1. Exécute la requête.
    * Kusto Explorer : VS Code se lance automatiquement avec le script *debug_python.py*.
    * Interface utilisateur web Kusto : 
        1. Téléchargez et enregistrez *debug_python.py*, *df.txt* et *kargs.txt*. Dans la fenêtre, sélectionnez **Autoriser**. **Enregistrez** les fichiers dans le répertoire sélectionné. 

            ![L’interface utilisateur web télécharge les fichiers python inline](media/debug-inline-python/webui-inline-python.png)

        1. Cliquez avec le bouton droit sur *debug_python.py* et ouvrez avec VS Code. 
        Le script *debug_python.py* contient le code Python inline, issu de la requête KQL, préfixé par le code de modèle pour initialiser le dataframe d’entrée à partir de *df.txt* et le dictionnaire de paramètres à partir de *kargs.txt*.    
            
1. Dans VS Code, lancez le débogueur de VS Code : **Déboguer** > **Démarrer le débogage (F5)** , sélectionnez la configuration **Python**. Le débogueur démarre et accède automatiquement à un point d’arrêt pour déboguer le code inline.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>Comment le débogage de code Python inclus fonctionne-t-il dans VS Code ?

1. La requête est analysée et exécutée sur le serveur jusqu’à ce que la clause `| evaluate python()` nécessaire soit atteinte.
1. Le bac à sable Python est appelé, mais au lieu d’exécuter le code, il sérialise la table d’entrée, le dictionnaire de paramètres et le code, puis les renvoie au client.
1. Ces trois objets sont enregistrés dans trois fichiers : *df.txt*, *kargs.txt* et *debug_python.py* dans le répertoire sélectionné (interface utilisateur web) ou dans le répertoire %TEMP% du client (Kusto Explorer).
1. VS Code se lance, préchargé avec le fichier *debug_python.py* qui contient un code de préfixe pour initialiser df et kargs à partir de leurs fichiers respectifs, suivi du script Python incorporé dans la requête KQL.

## <a name="query-example"></a>Exemple de requête

1. Exécutez la requête KQL suivante dans votre application cliente :

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    Consultez le tableau de résultats :

    | x  | x4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. Exécutez la même requête KQL dans votre application cliente en utilisant `set query_python_debug;` :

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. VS Code se lance :

    ![Lancement de VS Code](media/debug-inline-python/launch-vs-code.png)

1. VS Code débogue et imprime le dataframe 'result' dans la console de débogage :

    ![Débogage de VS Code](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Il peut y avoir des différences entre l’image du bac à sable Python et votre installation locale. [Vérifiez l’image du bac à sable pour obtenir des packages spécifiques en interrogeant le plug-in](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl).
