---
title: Utiliser Azure Notebooks pour analyser les données dans Azure Data Explorer
description: Cette rubrique vous montre comment créer une requête à l’aide d’un notebook Azure
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 0f99e11be99f22feec73b72397b27522b90dbf49
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492617"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>Utiliser Azure Notebooks pour analyser les données dans Azure Data Explorer

[Azure Notebooks](https://notebooks.azure.com/) est un service cloud Azure qui simplifie la création et le partage de [notebooks Jupyter](https://jupyter.org/). Azure Notebooks permet de combiner facilement la documentation, le code et les résultats de l’exécution du code.

> [!Note]
> * La procédure suivante utilise le client Python dans l’environnement Azure Notebooks pour interroger Azure Data Explorer. Toutefois, vous pouvez également utiliser [KQLmagic](https://docs.microsoft.com/azure/data-explorer/kqlmagic) pour interroger Azure Data Explorer.
> * Certains utilisateurs ont signalé des problèmes d’authentification lorsqu’ils utilisent Edge. Tant que ces problèmes ne sont pas résolus, utilisez un autre navigateur.

## <a name="create-a-project"></a>Création d’un projet

1. Ouvrez [Azure Notebooks](https://notebooks.azure.com/) dans votre navigateur et connectez-vous.

1. Sélectionnez l’onglet **Mes projets** dans l’en-tête. 

    [![](media/azurenotebooks/an-myprojects.png "My projects")](media/azurenotebooks/an-myprojects.png#lightbox)

1. Sélectionnez **+ Nouveaux projets**.
    
1. Dans la boîte de dialogue **Créer un nouveau projet** :
    1. Entrez le **nom du projet**.
    1. Désactivez la case à cocher **Public**.
        >[!Important]
        > Si vous ne désactivez pas la case à cocher Public, votre projet sera exposé sur l’Internet ouvert.
    1. Sélectionnez **Create** (Créer).
    
    ![Création d'un projet](media/azurenotebooks/an-create-new-project-blank.png)

    L’ID de projet est créé automatiquement.

## <a name="create-a-notebook"></a>Créer un notebook

1. Dans votre nouveau projet, créez un notebook. Le notebook doit utiliser un [langage pris en charge](https://github.com/Azure/azure-kusto-python#minimum-requirements).
Pour créer un notebook, sélectionnez **+ Nouveau**, puis **Notebook**.

    ![Créer un nouveau notebook](media/azurenotebooks/an-create-new-notebook-menu.png) 

1. Dans la boîte de dialogue **Créer un nouveau notebook**, entrez le nom du notebook.

1. Sélectionnez **Python 3.6**, puis **Nouveau**.
    
    ![Créer un nouveau notebook](media/azurenotebooks/an-create-new-notebook.png) 
    
1. Dans votre projet, sélectionnez votre nouveau notebook.

    ![Sélectionner un nouveau notebook](media/azurenotebooks/an-select-notebook.png)

    Attendez que votre noyau Python s’initialise. Lorsque l’icône de cercle rempli devient une icône de cercle vide, vous pouvez continuer.

    ![Initialisations du noyau](media/azurenotebooks/an-python-init-icon.png)

1. Pour importer le module système, entrez les commandes suivantes et sélectionnez **Exécuter** :
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > Pour exécuter une cellule, vous pouvez également appuyer sur Maj + Entrée.

1.  Pour importer les classes du Kit de développement logiciel (SDK), entrez les commandes suivantes et sélectionnez **Exécuter** :
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  Pour créer la chaîne de connexion et démarrer le client Kusto, entrez les commandes suivantes et sélectionnez **Exécuter** :  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. À l’invite, ouvrez un nouvel onglet de navigateur pour [https://aka.ms/devicelogin](https://aka.ms/devicelogin). 
   
1. Entrez le code d’autorisation et connectez-vous à votre compte AAD (@microsoft.com). Le client Kusto `kc` peut désormais s’authentifier auprès d’Azure Data Explorer à l’aide de votre identité.

1. Revenez à votre notebook pour voir le résultat de l’authentification. 

[![](media/azurenotebooks/an-python-commands.png "Python commands")](media/azurenotebooks/an-python-commands.png#lightbox)

## <a name="execute-a-kusto-query"></a>Exécuter une requête Kusto

1. Entrez votre requête Kusto et sélectionnez **Exécuter**. Par exemple :

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

[![](media/azurenotebooks/an-commands.png "Python commands")](media/azurenotebooks/an-commands.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

* [Référentiel GitHub Kusto-Python](https://github.com/Azure/azure-kusto-python)