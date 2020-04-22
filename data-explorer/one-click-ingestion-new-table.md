---
title: Utiliser l’ingestion en un clic pour ingérer des données dans une nouvelle table Azure Data Explorer
description: Ingestion (chargement) simple de données dans une nouvelle table Azure Data Explorer, au moyen de l’ingestion en un clic.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 07303130bbe33472f85e3d620ecd2eace555dbe2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81491304"
---
# <a name="use-one-click-ingestion-to-ingest-data-to-a-new-table-in-azure-data-explorer"></a>Utiliser l’ingestion en un clic pour ingérer des données dans une nouvelle table d’Azure Data Explorer

L’ingestion en un clic vous permet d’ingérer rapidement dans une table des données au format JSON, CSV et dans d’autres formats. À l’aide de l’interface utilisateur web d’Azure Data Explorer, vous pouvez ingérer des données à partir du stockage, d’un fichier local ou d’un conteneur. 

Ce document décrit l’utilisation de l’Assistant intuitif Ingestion en un clic pour ingérer des données CSV depuis un conteneur dans une nouvelle table. Vous pouvez ensuite modifier la table et exécuter des requêtes avec l’interface utilisateur web d’Azure Data Explorer. Vous pouvez également définir l’ingestion continue pour ingérer automatiquement des données dans la table lorsque les données sources sont mises à jour.

L’ingestion en un clic est utile quand vous procédez à l’ingestion de données pour la première fois ou quand le schéma de vos données ne vous est pas familier. 

Pour obtenir une vue d’ensemble de l’ingestion en un clic, et la liste des prérequis, consultez [Ingestion en un clic](ingest-data-one-click.md).
Pour plus d’informations sur l’ingestion de données dans une table existante d’Azure Data Explorer, consultez [Ingestion en un clic dans une table existante](one-click-ingestion-existing-table.md).

## <a name="ingest-new-data"></a>Ingérer de nouvelles données

1. Dans le menu de gauche de l’interface utilisateur web, cliquez avec le bouton droit sur une *base de données*, puis sélectionnez **Ingérer de nouvelles données (préversion)** .

    ![Sélectionner l'ingestion en un clic dans l'interface utilisateur web](media/one-click-ingestion-new-table/one-click-ingestion-in-web-ui.png)   
 
1. Dans la fenêtre **Ingérer de nouvelles données (préversion)** , l’onglet **Source** est sélectionné. 

1. Sélectionnez **Créer une table** et entrez un nom pour la nouvelle table. Vous pouvez utiliser des caractères alphanumériques, des traits d’union et des traits de soulignement. Les caractères spéciaux ne sont pas pris en charge.

![Créer une table](media/one-click-ingestion-new-table/create-new-table.png) 

[!INCLUDE [data-explorer-one-click-ingestion-types](includes/data-explorer-one-click-ingestion-types.md)]

Sélectionnez **Modifier le schéma** pour afficher et modifier la configuration de colonne de votre table. Le système sélectionne un des objets blob au hasard, et le schéma est généré en fonction de cet objet blob. En examinant le nom de la source, le service identifie automatiquement s’il est compressé ou non.

## <a name="edit-the-schema"></a>Modifier le schéma

1. Sous l’onglet **Schéma** :

    1. Sélectionnez **Format de données**.

        [!INCLUDE [data-explorer-one-click-ingestion-edit-schema](includes/data-explorer-one-click-ingestion-edit-schema.md)]

    1. Si vous sélectionnez **JSON**, vous devez également sélectionner **Niveaux JSON**, entre 1 et 10. Les niveaux affectent la représentation des données de colonne de la table. 

    ![Sélectionner un des niveaux JSON](media/one-click-ingestion-new-table/json-levels.png)

    * Si vous sélectionnez un autre format que JSON, vous pouvez cocher la case **Inclure les noms des colonnes** pour ignorer la ligne d’en-tête du fichier.

        ![Sélectionner Inclure les noms des colonnes](media/one-click-ingestion-new-table/non-json-format.png)
        
1. Dans le champ **Nom du mappage**, entrez un nom de mappage. Vous pouvez utiliser des caractères alphanumériques et des traits de soulignement. Les espaces, les caractères spéciaux et les traits d’Union ne sont pas pris en charge.
    
    ![Nom du mappage de table](media/one-click-ingestion-new-table/table-mapping.png)

## <a name="copy-and-paste-queries"></a>Copier et coller des requêtes

1. Au-dessus du volet **Éditeur**, sélectionnez le bouton **v** pour ouvrir l’éditeur. Dans l’éditeur, vous pouvez afficher et copier les requêtes automatiques générées à partir de vos entrées. 
1. Dans le tableau : 
    * Double-cliquez sur le nom de la nouvelle colonne à modifier.
    * Sélectionnez de nouveaux en-têtes de colonnes et effectuez une des actions suivantes :
    
|Action         |Description                                  |
|-----------------|-------------------------------------------|
|Modifier le type de données |Changement du type de données en remplaçant celui que le service a sélectionné automatiquement par l’un des autres [types de données pris en charge](#edit-the-schema)|
|Renommer une colonne    |Changement de nom de la colonne |
|Nouvelle colonne       |Ajout d'une nouvelle colonne|
|Supprimer la colonne    |Suppression de la colonne sélectionnée|
|Tri croissant   |Tri de la table en suivant la colonne sélectionnée, dans l’ordre croissant (colonnes existantes seulement)|
|Tri décroissant  |Tri de la table en suivant la colonne sélectionnée, dans l’ordre décroissant (colonnes existantes seulement) |

> [!Note]
> Pour les formats tabulaires, chaque colonne peut être ingérée dans une colonne dans Azure Data Explorer.
> Les attributs JSON peuvent être ingérés dans plusieurs colonnes. Vous pouvez créer des colonnes de différents niveaux JSON.

[![](media/one-click-ingestion-new-table/edit-view.png "One-click ingestion edit view")](media/one-click-ingestion-new-table/edit-view.png#lightbox) 

## <a name="start-ingestion"></a>Démarrer l’ingestion

Sélectionnez **Démarrer l’ingestion** pour créer une table et un mappage et pour commencer l’ingestion de données.
![Démarrer l’ingestion](media/one-click-ingestion-new-table/start-ingestion.png)

## <a name="data-ingestion-completed"></a>Ingestion de données terminée

Dans la fenêtre **Ingestion de données terminée**, les trois étapes sont signalées par des coches vertes si l'ingestion des données s'est terminée avec succès.
 
![Ingestion de données en un clic terminée](media/one-click-ingestion-new-table/one-click-data-ingestion-complete.png)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

**Ingestion continue**

L’ingestion continue vous permet de créer une grille d’événement qui écoute le conteneur source. Tout nouvel objet blob qui répond aux critères des paramètres prédéfinis (préfixe, suffixe, etc.) est automatiquement ingéré dans la table de destination.

> [!Note]
> L’ingestion continue n’est pertinente que lors d’une ingestion à partir d’un conteneur.

1. Sélectionnez **Ingestion continue** pour ouvrir le portail Azure. La page de connexion des données s’affiche avec le connecteur de données de la grille d’événement ouvert, et les paramètres source et cible déjà entrés (conteneur source, tables et mappages).

1. Sélectionnez **Créer** pour créer une connexion de données qui écoute toutes les modifications dans ce conteneur. 

## <a name="next-steps"></a>Étapes suivantes

* [Interroger des données dans l’interface utilisateur web Azure Data Explorer](/azure/data-explorer/web-query-data)
* [Écrire des requêtes pour Azure Data Explorer à l’aide du langage de requête Kusto](/azure/data-explorer/write-queries)
