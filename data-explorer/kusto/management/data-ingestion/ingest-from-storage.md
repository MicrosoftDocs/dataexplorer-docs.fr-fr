---
title: L’ingest dans la commande (tirer les données du stockage) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit The .ingest in command (tirer les données du stockage) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 1f304f9dc064094c6d55cb32f3fb453f32114979
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521435"
---
# <a name="the-ingest-into-command-pull-data-from-storage"></a>L’ingère de .ingest dans la commande (tirer les données du stockage)

La `.ingest into` commande ingèrent des données dans une table en « tirant » les données d’un ou de plusieurs artefacts de stockage en nuage.
Par exemple, la commande peut récupérer 1000 blobs formatés CSV à Azure Blob Storage, les analyser et les ingérer ensemble dans une seule table cible.
Les données sont jointes à la table sans affecter les enregistrements existants, et sans modifier le schéma de la table.

**Syntaxe**

`.ingest`[`async` `into` `table` ] *TableName* *SourceDataLocator* `with` `(` [ *IngestionPropertyName* `=` *IngestionPropertyValue* [`,` ...] `)`]

**Arguments**

* `async`: Si spécifié, la commande reviendra immédiatement et continuera l’ingestion en arrière-plan. Les résultats de la `OperationId` commande incluront une valeur `.show operation` qui peut ensuite être utilisée avec la commande pour récupérer l’état et les résultats d’achèvement de l’ingestion.
  
* *TableName*: Le nom de la table pour ingérer les données.
  Le nom de table est toujours relatif à la base de données dans son contexte, et son schéma est le schéma qui sera supposé pour les données si aucun objet de cartographie schématique n’est fourni.

* *SourceDataLocator*: Un littéral de type, `string`ou une liste de comma-délimité de ces littérals entourés par `(` et `)` des caractères, indiquant les artefacts de stockage contenant les données à tirer. Voir [les chaînes de connexion de stockage](../../api/connection-strings/storage.md).

> [!NOTE]
> Il est fortement recommandé d’utiliser [des littérals de cordes obscurcis](../../query/scalar-data-types/string.md#obfuscated-string-literals) pour le *SourceDataPointer* qui comprend les informations d’identification réelles en elle.
> Le service sera sûr de frotter les informations d’identification dans ses traces internes, messages d’erreur, etc.

* *IngestionPropertyName*, *IngestionPropertyValue*: Tout nombre de [propriétés d’ingestion](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) qui affectent le processus d’ingestion.

**Résultats**

Le résultat de la commande est un tableau avec autant d’enregistrements qu’il y a des éclats de données («étendues») générés par la commande.
Si aucun éclat de données n’a été généré, un seul enregistrement est retourné avec un ID vide (valeur nulle).

|Nom       |Type      |Description                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|ExtentId (extentId)   |`guid`    |L’identifiant unique pour le fragment de données qui a été généré par la commande.|
|Article Chargé |`string`  |Un ou plusieurs artefacts de stockage qui sont liés à cet enregistrement.             |
|Duration   |`timespan`|Combien de temps il a fallu pour effectuer l’ingestion.                                     |
|HasErrors HasErrors  |`bool`    |Que ce dossier représente ou non un échec d’ingestion.                |
|OpérationId|`guid`    |Une pièce d’identité unique représentant l’opération. Peut être utilisé `.show operation` avec la commande.|

**Remarques**

Cette commande ne modifie pas le schéma de la table ingérée.
Si nécessaire, les données sont «forcées» dans ce schéma pendant l’ingestion, et non l’inverse (colonnes supplémentaires sont ignorées, et les colonnes manquantes sont traitées comme des valeurs nulles).

**Exemples**

L’exemple suivant demande au moteur de lire deux blobs d’Azure Blob Storage sous `T`forme de fichiers CSV, et d’ingérer leur contenu dans la table . Il `...` s’agit d’une signature d’accès partagé Azure Storage (SAS) qui donne un accès à chaque blob. Notez également l’utilisation de cordes obfusquées (devant `h` les valeurs de la chaîne) pour s’assurer que le SAS n’est jamais enregistré.

```kusto
.ingest into table T (
    h'https://contoso.blob.core.windows.net/container/file1.csv?...',
    h'https://contoso.blob.core.windows.net/container/file2.csv?...'
)
```

L’exemple suivant est pour l’ingestion des données de Azure Data Lake Storage Gen 2 (ADLSv2). Les informations d’identification utilisées ici (`...`) sont les informations d’identification de compte de stockage (clé partagée), et nous utilisons l’obscurcissement des chaînes que pour la partie secrète de la chaîne de connexion.

```kusto
.ingest into table T (
  'abfss://myfilesystem@contoso.dfs.core.windows.net/path/to/file1.csv;'
    h'...'
)
```

L’exemple suivant ingère un seul fichier de Azure Data Lake Storage (ADLS).
Il utilise les informations d’identification de l’utilisateur pour accéder à ADLS (il n’est donc pas nécessaire de traiter l’URI de stockage comme contenant un secret). Il montre également comment spécifier les propriétés d’ingestion.

```kusto
.ingest into table T ('adl://contoso.azuredatalakestore.net/Path/To/File/file1.ext;impersonate')
  with (format='csv')
```

