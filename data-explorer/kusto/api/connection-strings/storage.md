---
title: Chaînes de connexion de stockage - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les chaînes de connexion de stockage dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 909198e42786666d3a26874e18b5cfd57b27ab1f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502956"
---
# <a name="storage-connection-strings"></a>Chaînes de connexion de stockage

Quelques commandes Kusto demandent à Kusto d’interagir avec les services de stockage externes. Par exemple, on peut dire à Kusto d’exporter des données vers un Blob de stockage Azure, auquel cas les paramètres spécifiques (tels que le nom de compte de stockage ou le conteneur blob) doivent être fournis.

Kusto prend en charge les fournisseurs de stockage suivants :


* Fournisseur de stockage Azure Storage Blob
* Fournisseur de stockage De stockage Azure Data Lake

Chaque type de fournisseur de stockage définit un format de chaîne de connexion utilisé pour décrire les ressources de stockage et la façon d’y accéder.
Kusto utilise un format URI pour décrire ces ressources de stockage et les propriétés nécessaires pour y accéder (comme les informations d’identification de sécurité).


|Fournisseur                   |Schéma    |Modèle d’URI                          |
|---------------------------|----------|--------------------------------------|
|Azure Storage Blob         |`https://`|`https://`*Conteneur*`.blob.core.windows.net/`de`/`*compte*[*BlobName*][`?`*SasKey* \| `;` *AccountKey*]|
|Azure Data Lake Store Gén. 2|`abfss://`|`abfss://`*Compte De filesystem*`@`*Account*`.dfs.core.windows.net/`*PathToDirectoryOrFile*[`;`*CallerCredentials*]|
|Azure Data Lake Store Gén. 1|`adl://`  |`adl://`*Compte*.azuredatalakestore.net/*PathToDirectoryOrFile*[`;`*CallerCredentials*]|

## <a name="azure-storage-blob"></a>Azure Storage Blob

Ce fournisseur est le plus couramment utilisé et est pris en charge dans tous les scénarios.
Le fournisseur doit recevoir les informations d’identification lors de l’accès à la ressource. Il existe deux mécanismes soutenus pour fournir des titres de compétences :

* Fournir une clé d’accès partagé (SAS), en utilisant la`?sig=...`requête standard de l’Azure Storage Blob ( ). Utilisez cette méthode lorsque Kusto a besoin d’accéder à la ressource pour un temps limité.
* Fournir la clé`;ljkAkl...==`du compte de stockage ( ). Utilisez cette méthode lorsque Kusto a besoin d’accéder à la ressource sur une base continue.

Exemples (notez que cela montre des littérals de cordes obscurcis, afin de ne pas exposer la clé du compte ou SAS) :

`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv;<storage_account_key_text, ends with '=='>"`
`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv?sv=...&sp=rwd"` 

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="azure-data-lake-store-gen-2"></a>Azure Data Lake Store Gén. 2

Ce fournisseur prend en charge l’accès aux données dans Azure Data Lake Store Gen 2.

Le format de l’URI est :

`abfss://`*Stockage du système de* `@` *fichiersAccountName* `.dfs.core.windows.net/` *Path* `;` *CallerCredentials*

Où :

* _Filesystem_ est le nom de l’objet de système de fichiers ADLS (à peu près équivalent à Blob Container)
* _StorageAccountName_ est le nom du compte de stockage
* _Le chemin_ est le chemin vers le répertoire`/`ou le fichier étant accessible Le slash ( ) caractère est utilisé comme un délimitant.
* _CallerCredentials_ indique les informations d’identification utilisées pour accéder au service, comme décrit ci-dessous.

Lors de l’accès à Azure Data Lake Store Gen 2, l’appelant doit fournir des informations d’identification valides pour accéder au service. Les méthodes suivantes de fournir des informations d’identification sont prises en charge :

* `;sharedkey=` *Appendez AccountKey* à l’URI, _accountKey_ étant la clé du compte de stockage
* `;impersonate` Annexez à l’URI. Kusto utilisera l’identité principale du demandeur et l’imitera pour accéder à la ressource. Le directeur doit avoir les affectations de rôleS RBAC appropriées pour être en mesure d’effectuer les opérations de lecture/ d’écriture, tel que documenté [ici](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). (Par exemple, le rôle minimal `Storage Blob Data Reader` pour les opérations de lecture est le rôle).
* Annexe *AadToken* à l’URI, avec _AadToken_ étant un jet d’accès AAD codé de base-64 (assurez-vous que le jeton est pour la ressource `https://storage.azure.com/`). `;token=`
* `;prompt` Annexez à l’URI. Kusto demande les informations d’identification des utilisateurs lorsqu’il a besoin d’accéder à la ressource. (L’utilisateur est désactivé pour les déploiements de cloud et n’est activé que dans des environnements de test.)
* Fournir une clé d’accès partagé (SAS), en utilisant la requête standard`?sig=...`de l’Azure Data Lake Storage Gen 2 ( ). Utilisez cette méthode lorsque Kusto a besoin d’accéder à la ressource pour un temps limité.



### <a name="azure-data-lake-store-gen-1"></a>Azure Data Lake Store Gén. 1

Ce fournisseur prend en charge l’accès aux fichiers et annuaires dans Azure Data Lake Store.
Il doit être fourni avec des informations d’identification (Kusto n’utilise pas son propre principal AAD pour accéder à Azure Data Lake.) Les méthodes suivantes de fournir des informations d’identification sont prises en charge :

* `;impersonate` Annexez à l’URI. Kusto utilisera l’identité principale du demandeur et l’imitera pour accéder à la ressource
* Annexe `;token=`_AadToken _to l’URI, _avec AadToken_ étant un jet d’accès AAD codé de base-64 (assurez-vous que le jeton est pour la ressource `https://management.azure.com/`).
* `;prompt` Annexez à l’URI. Kusto demandera les informations d’identification des utilisateurs lorsqu’il aura besoin d’accéder à la ressource. (L’utilisateur est désactivé pour les déploiements de cloud et n’est activé que dans des environnements de test.)



