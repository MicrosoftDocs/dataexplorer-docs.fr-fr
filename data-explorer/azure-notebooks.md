---
title: Utiliser Azure Notebooks pour analyser les données dans Azure Data Explorer
description: Cette rubrique vous montre comment créer une requête à l’aide d’un notebook Azure
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/01/2020
ms.openlocfilehash: d41c9e2719ef5139d89986ff5333c03e6e34d26c
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872469"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>Utiliser Azure Notebooks pour analyser les données dans Azure Data Explorer

[Azure Notebooks](https://notebooks.azure.com/) est un service cloud Azure qui simplifie la création et le partage de [notebooks Jupyter](https://jupyter.org/). Azure Notebooks permet de combiner facilement la documentation, le code et les résultats de l’exécution du code.

> [!Note]
> * La procédure suivante utilise le client Python dans l’environnement Azure Notebooks pour interroger Azure Data Explorer. Toutefois, vous pouvez également utiliser [KQLmagic](kqlmagic.md) pour interroger Azure Data Explorer.
> * Certains utilisateurs ont signalé des problèmes d’authentification lorsqu’ils utilisent Edge. Tant que ces problèmes ne sont pas résolus, utilisez un autre navigateur.

## <a name="create-a-project"></a>Création d’un projet

1. Ouvrez [Azure Notebooks](https://notebooks.azure.com/) dans votre navigateur et connectez-vous.

1. Sélectionnez l’onglet **Mes projets** dans l’en-tête. 

    :::image type="content" source="media/azurenotebooks/an-myprojects.png" alt-text="Page Projets, Onglet Mes projets, Microsoft Azure Notebooks, Azure Data Explorer" lightbox="media/azurenotebooks/an-myprojects.png#lightbox":::

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

:::image type="content" source="media/azurenotebooks/an-python-commands.png" alt-text="Sortie du résultat de l’authentification, Fenêtre de notebook, Microsoft Azure Notebooks, Azure Data Explorer" lightbox="media/azurenotebooks/an-python-commands.png#lightbox":::

## <a name="execute-a-kusto-query"></a>Exécuter une requête Kusto

1. Entrez votre requête Kusto et sélectionnez **Exécuter**. Par exemple :

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

:::image type="content" source="media/azurenotebooks/an-commands.png" alt-text="Bouton Exécuter, Fenêtre de notebook, Microsoft Azure Notebooks, Azure Data Explorer" lightbox="media/azurenotebooks/an-commands.png#lightbox":::

## <a name="next-steps"></a>Étapes suivantes

* [Référentiel GitHub Kusto-Python](https://github.com/Azure/azure-kusto-python)
