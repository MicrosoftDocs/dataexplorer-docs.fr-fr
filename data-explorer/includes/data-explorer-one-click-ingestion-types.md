---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 30/03/2020
ms.author: orspodek
ms.openlocfilehash: 56095df921864896dacdb100302d686d66f40572
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82109632"
---
## <a name="select-an-ingestion-type"></a>Sélectionner un type d’ingestion

Pour **Type d’ingestion**, sélectionnez l’une des options suivantes :
   * **à partir du stockage** : dans le champ **Lien vers le stockage**, ajoutez l’URL du compte de stockage. Utilisez [l’URL SAS d’objet blob](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) pour les comptes de stockage privés.
   
      ![Ingestion en un clic à partir du stockage](media/data-explorer-one-click-ingestion-types/from-storage-blob.png)

    * **à partir d’un fichier** : sélectionnez **Parcourir** pour localiser le fichier ou faites glisser ce dernier dans le champ.
  
      ![Ingestion en un clic à partir d’un fichier](media/data-explorer-one-click-ingestion-types/from-file.png)

    * **à partir d’un conteneur** : dans le champ **Lien vers le stockage**, ajoutez l’[URL SAS](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) du conteneur, puis entrez éventuellement la taille de l’échantillon.

      ![Ingestion en un clic à partir d’un conteneur](media/data-explorer-one-click-ingestion-types/from-container.png)

  Un échantillon des données s’affiche. Si vous le souhaitez, vous pouvez les filtrer pour afficher uniquement les fichiers qui commencent ou qui se terminent par des caractères spécifiques. Quand vous ajustez les filtres, l’aperçu est mis à jour automatiquement.
  
  Par exemple, vous pouvez filtrer tous les fichiers qui commencent par le mot *data* (données) et qui se terminent par l’extension  *.csv.gz*.

  ![Filtre de l’ingestion en un clic](media/data-explorer-one-click-ingestion-types/from-container-with-filter.png)
