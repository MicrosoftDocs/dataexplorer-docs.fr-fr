---
title: Créer une table dans Azure Data Explorer
description: Découvrez comment créer facilement une table dans Azure Data Explorer avec l’expérience en un clic.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/06/2020
ms.openlocfilehash: 8732edf3b2cb9600fcce1ed03097d893c6615528
ms.sourcegitcommit: c2ab3176db4dd55ac9ca8eee52bbd24096d1277f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90740893"
---
# <a name="create-a-table-in-azure-data-explorer-preview"></a>Créer une table cible dans Azure Data Explorer (préversion)

La création d’une table est une étape importante du processus d’[ingestion des données](ingest-data-overview.md) et d’[interrogation](write-queries.md) dans Azure Data Explorer. Une fois que vous avez [créé un cluster et une base de données dans Azure Data Explorer](create-cluster-database-portal.md), vous pouvez créer une table. L’article suivant montre comment créer une table et une mise en correspondance du schéma rapidement et facilement à l’aide de l’interface utilisateur web d’Azure Data Explorer. 

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Créez [un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md).
* Connectez-vous à [l’interface utilisateur web Azure Data Explorer](https://dataexplorer.azure.com/) et [ajoutez une connexion à votre cluster](web-query-data.md#add-clusters).

## <a name="create-a-table"></a>Créer une table

1. Dans le menu de gauche de l’interface utilisateur web, cliquez avec le bouton droit sur **ExampleDB** qui correspond au nom de votre base de données, puis sélectionnez **Créer une table (préversion)** .

    :::image type="content" source="./media/one-click-table/create-table.png" alt-text="Créer une table dans l’interface utilisateur web d’Azure Data Explorer":::

La fenêtre **Créer une table** s’ouvre avec l’onglet **Source** sélectionné.
1. Le champ **Base de données** est renseigné automatiquement avec votre base de données. Vous pouvez sélectionner une autre base de données dans le menu déroulant.
1. Dans **Nom de la table**, entrez un nom pour votre table. 
    > [!TIP]
    >  Les noms de tables peuvent comporter jusqu’à 1024 caractères, y compris des caractères alphanumériques, des traits d’union et des traits de soulignement. Les caractères spéciaux ne sont pas pris en charge.

### <a name="select-source-type"></a>Sélectionner le type de source

1. Dans **Type de source**, sélectionnez la source de données que vous utiliserez pour créer votre mappage de table. Choisissez parmi les options suivantes : **À partir d’un blob**, **À partir d’un fichier** ou **À partir d’un container**.
   
    
    * Si vous utilisez un **objet blob** :
        * Entrez l’URL de stockage de votre objet blob et entrez éventuellement la taille de l’échantillon. 
        * Filtrez vos fichiers à l’aide des **filtres de fichiers**. 
        * Sélectionnez un fichier qui sera utilisé à l’étape suivante pour définir le schéma.

        :::image type="content" source="media/one-click-table/blob.png" alt-text="Créer une table à l’aide d’un objet blob pour créer une mise en correspondance du schéma":::
    
    * Si vous utilisez un **fichier local** :
        * Sélectionnez **Parcourir** pour localiser le fichier ou faites glisser ce dernier dans le champ.

        :::image type="content" source="./media/one-click-table/data-from-file.png" alt-text="Créer une table basée sur les données d’un fichier local":::

    * Si vous utilisez un **conteneur** :
        * Dans le champ **Lien vers le stockage**, ajoutez l’[URL SAS](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) du conteneur, puis entrez éventuellement la taille de l’échantillon. 

1. Sélectionnez **Modifier le schéma** pour accéder à l’onglet **Schéma**.

### <a name="edit-schema"></a>Modifier le schéma

Sous l’onglet **Schéma**, le [format de données](ingest-data-one-click.md#file-formats) et la compression sont identifiées automatiquement dans le volet gauche. Si l’identification est incorrecte, utilisez le menu déroulant **Format des données** pour sélectionner le format approprié.

   * Si vos données sont au format JSON, vous devez également sélectionner les niveaux JSON, de 1 à 10. Les niveaux déterminent la division des données dans les colonnes de la table.
   * Si vos données sont au format CSV, cochez la case **Inclure les noms des colonnes** pour ignorer la ligne d’en-tête du fichier.

        :::image type="content" source="./media/one-click-table/schema-tab.png" alt-text="Onglet Modifier le schéma pour la création d’une table dans l’expérience en un clic dans Azure Data Explorer":::
 
1. Dans **Mappage**, entrez un nom pour la mise en correspondance du schéma de cette table. 
    > [!TIP]
    >  Les noms des tables peuvent inclure des caractères alphanumériques et des traits de soulignement. Les espaces, les caractères spéciaux et les traits d’Union ne sont pas pris en charge.
1. Sélectionnez **Create** (Créer).

## <a name="create-table-completed-window"></a>Fenêtre Création de table terminée

Dans la fenêtre **Création de table terminée**, les deux étapes sont signalées par des coches vertes quand la création de table s’est terminée avec succès.

* Sélectionnez **Afficher la commande** pour ouvrir l’éditeur pour chaque étape. 
    * Dans l’éditeur, vous pouvez afficher et copier les commandes automatiques générées à partir de vos entrées.
    
    :::image type="content" source="./media/one-click-table/table-completed.png" alt-text="Création de table terminée pour la création d’une table dans l’expérience en un clic dans Azure Data Explorer":::
 
Dans les vignettes situées sous la progression **Créer une table**, explorez **Requêtes rapides** ou **Outils** :

* Les **requêtes rapides** incluent des liens vers l’interface utilisateur web avec des exemples de requêtes.

* **Outils** contient des liens permettant d’**Annuler** l’exécution des commandes .drop appropriées.

> [!NOTE]
> L’utilisation des commandes .drop peut entraîner une perte de données.<br>
> Les commandes drop dans ce workflow annulent uniquement les modifications apportées par le processus de création de table (nouvelle table et nouvelle mise en correspondance du schéma).

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de l’ingestion des données](ingest-data-overview.md)
* [Ingestion en un clic](ingest-data-one-click.md)
* [Écrire des requêtes pour l’Explorateur de données Azure](write-queries.md)  
