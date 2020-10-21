---
title: Chaînes de connexion de stockage-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les chaînes de connexion de stockage dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 4b212435eb506ce71b52e19d3304461e9be35b5e
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342499"
---
# <a name="storage-connection-strings"></a>Chaînes de connexion de stockage

Quelques commandes Kusto indiquent à Kusto d’interagir avec les services de stockage externes. Par exemple, Kusto peut être invité à exporter des données vers un Azure Storage Blob, auquel cas les paramètres spécifiques (tels que le nom du compte de stockage ou le conteneur d’objets BLOB) doivent être fournis.

Kusto prend en charge les fournisseurs de stockage suivants :


* Fournisseur de stockage Azure Storage Blob
* Fournisseur de stockage Azure Data Lake Storage

Chaque type de fournisseur de stockage définit un format de chaîne de connexion utilisé pour décrire les ressources de stockage et comment y accéder.
Kusto utilise un format d’URI pour décrire ces ressources de stockage et les propriétés nécessaires pour y accéder (telles que les informations d’identification de sécurité).


|Fournisseur                   |Schéma    |Modèle d’URI                          |
|---------------------------|----------|--------------------------------------|
|Azure Storage Blob         |`https://`|`https://`*Compte* `.blob.core.windows.net/` *Conteneur*[ `/` *BlobName*] [ `?` *SasKey* \| `;` *AccountKey*]|
|Azure Data Lake Store Gén. 2|`abfss://`|`abfss://`*Système de fichiers* `@` *Compte* `.dfs.core.windows.net/` *PathToDirectoryOrFile*[ `;` *CallerCredentials*]|
|Azure Data Lake Store Gén. 1|`adl://`  |`adl://`*Account*. azuredatalakestore.NET/*PathToDirectoryOrFile*[ `;` *CallerCredentials*]|

## <a name="azure-storage-blob"></a>Azure Storage Blob

Ce fournisseur est le plus couramment utilisé et est pris en charge dans tous les scénarios.
Les informations d’identification doivent être fournies au fournisseur lors de l’accès à la ressource. Il existe deux mécanismes pris en charge pour fournir des informations d’identification :

* Fournissez une clé d’accès partagé (SAP) à l’aide de la requête standard du Azure Storage Blob ( `?sig=...` ). Utilisez cette méthode lorsque Kusto doit accéder à la ressource pendant une période limitée.
* Fournissez la clé de compte de stockage ( `;ljkAkl...==` ). Utilisez cette méthode lorsque Kusto doit accéder à la ressource de manière continue.

Exemples (Notez qu’il s’agit de littéraux de chaîne obscurcis, afin de ne pas exposer la clé de compte ou la SAP) :

`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv;<storage_account_key_text, ends with '=='>"`
`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv?sv=...&sp=rwd"` 

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="azure-data-lake-store-gen-2"></a>Azure Data Lake Store Gén. 2

Ce fournisseur prend en charge l’accès aux données dans Azure Data Lake Store Gen 2.

Le format de l’URI est le suivant :

`abfss://`*Système de fichiers* `@` *StorageAccountName* `.dfs.core.windows.net/` *Chemin d’accès* `;` *CallerCredentials*

Où :

* _FileSystem_ est le nom de l’objet de système de fichiers ADLS (à peu près équivalent au conteneur d’objets BLOB)
* _StorageAccountName_ est le nom du compte de stockage
* _Path_ est le chemin d’accès au répertoire ou au fichier auquel le caractère barre oblique ( `/` ) est utilisé comme délimiteur.
* _CallerCredentials_ indique les informations d’identification utilisées pour accéder au service, comme décrit ci-dessous.

Lors de l’accès à Azure Data Lake Store Gen 2, l’appelant doit fournir des informations d’identification valides pour l’accès au service. Les méthodes suivantes pour fournir des informations d’identification sont prises en charge :

* Ajoutez `;sharedkey=` *AccountKey* à l’URI, avec _AccountKey_ étant la clé de compte de stockage.
* Ajoutez `;impersonate` à l’URI. Kusto utilise l’identité du principal du demandeur et l’emprunte pour accéder à la ressource. Le principal doit disposer des attributions de rôle RBAC appropriées pour pouvoir effectuer les opérations de lecture/écriture, comme indiqué [ici](/azure/storage/blobs/data-lake-storage-access-control). (Par exemple, le rôle minimal pour les opérations de lecture est le `Storage Blob Data Reader` rôle).
* Ajoutez `;token=` *AadToken* à l’URI, avec _AadToken_ étant un jeton d’accès AAD encodé en base 64 (Assurez-vous que le jeton est destiné à la ressource `https://storage.azure.com/` ).
* Ajoutez `;prompt` à l’URI. Kusto demande les informations d’identification de l’utilisateur lorsqu’il doit accéder à la ressource. (L’invite de l’utilisateur est désactivée pour les déploiements Cloud et est uniquement activée dans les environnements de test.)
* Fournissez une clé d’accès partagé (SAP) à l’aide de la requête standard de Azure Data Lake Storage Gen 2 ( `?sig=...` ). Utilisez cette méthode lorsque Kusto doit accéder à la ressource pendant une période limitée.



### <a name="azure-data-lake-store-gen-1"></a>Azure Data Lake Store Gén. 1

Ce fournisseur prend en charge l’accès aux fichiers et aux répertoires dans Azure Data Lake Store.
Elle doit être fournie avec les informations d’identification (Kusto n’utilise pas son propre principal AAD pour accéder à Azure Data Lake.) Les méthodes suivantes pour fournir des informations d’identification sont prises en charge :

* Ajoutez `;impersonate` à l’URI. Kusto utilise l’identité du principal du demandeur et l’emprunte pour accéder à la ressource
* Ajoutez `;token=` *AadToken* à l’URI, avec *AadToken* étant un jeton d’accès AAD encodé en base 64 (Assurez-vous que le jeton est destiné à la ressource `https://management.azure.com/` ).
* Ajoutez `;prompt` à l’URI. Kusto demande des informations d’identification de l’utilisateur lorsqu’il doit accéder à la ressource. (L’invite de l’utilisateur est désactivée pour les déploiements Cloud et est uniquement activée dans les environnements de test.)