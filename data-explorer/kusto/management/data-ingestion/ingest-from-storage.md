---
title: La commande. ingestion dans (extraire des données du stockage)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la commande. ingestion de (extraire des données du stockage) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 05d62aaa7b123f7f6d02b784402fd06335e155b2
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373421"
---
# <a name="the-ingest-into-command-pull-data-from-storage"></a>Commande. ingestion dans (extraire des données du stockage)

La `.ingest into` commande ingère les données dans une table en « extrayant » les données d’un ou de plusieurs artefacts de stockage cloud.
Par exemple, la commande peut récupérer des objets BLOB au format CSV 1000 à partir du stockage d’objets BLOB Azure, les analyser et les ingérer dans une table cible unique.
Les données sont ajoutées à la table sans affecter les enregistrements existants, et sans modifier le schéma de la table.

**Syntaxe**

`.ingest`[ `async` ] `into` `table` *TableName* *SourceDataLocator* [ `with` `(` *IngestionPropertyName* `=` *IngestionPropertyValue* [ `,` ...] `)` ]

**Arguments**

* `async`: S’il est spécifié, la commande est immédiatement retournée et continue l’ingestion en arrière-plan. Les résultats de la commande incluent une `OperationId` valeur qui peut ensuite être utilisée avec la `.show operation` commande pour récupérer l’état d’achèvement de l’ingestion et les résultats.
  
* *TableName*: nom de la table à laquelle les données doivent être ingérées.
  Le nom de la table est toujours relatif à la base de données en contexte, et son schéma est le schéma qui sera utilisé pour les données si aucun objet de mappage de schéma n’est fourni.

* *SourceDataLocator*: littéral de type `string` , ou liste délimitée par des virgules de ces littéraux entourés `(` de `)` caractères et, indiquant les artefacts de stockage contenant les données à extraire. Consultez [chaînes de connexion de stockage](../../api/connection-strings/storage.md).

> [!NOTE]
> Il est fortement recommandé d’utiliser des [littéraux de chaîne obscurcis](../../query/scalar-data-types/string.md#obfuscated-string-literals) pour le *SourceDataPointer* qui contient les informations d’identification réelles.
> Le service va veiller à nettoyer les informations d’identification dans ses traces internes, les messages d’erreur, etc.

* *IngestionPropertyName*, *IngestionPropertyValue*: nombre quelconque de [Propriétés](../../../ingestion-properties.md) d’ingestion qui affectent le processus d’ingestion.

**Résultats**

Le résultat de la commande est une table avec autant d’enregistrements qu’il y a de données partitions (« extents ») générées par la commande.
Si aucun partitions de données n’a été généré, un seul enregistrement est retourné avec un ID d’extension vide (valeur zéro).

|Nom       |Type      |Description                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|ExtentId   |`guid`    |Identificateur unique pour le partition de données qui a été généré par la commande.|
|ItemLoaded |`string`  |Un ou plusieurs artefacts de stockage associés à cet enregistrement.             |
|Duration   |`timespan`|Le temps nécessaire pour effectuer l’ingestion.                                     |
|HasErrors  |`bool`    |Indique si cet enregistrement représente un échec d’ingestion.                |
|OperationId|`guid`    |ID unique représentant l’opération. Peut être utilisé avec la `.show operation` commande.|

**Remarques**

Cette commande ne modifie pas le schéma de la table en cours de réception dans.
Si nécessaire, les données sont « forcées » dans ce schéma lors de l’ingestion, et non dans le sens inverse (les colonnes supplémentaires sont ignorées, et les colonnes manquantes sont traitées comme des valeurs null).

**Exemples**

L’exemple suivant indique au moteur de lire deux objets BLOB à partir du stockage d’objets BLOB Azure en tant que fichiers CSV et d’ingérer leur contenu dans une table `T` . `...`Représente une signature d’accès partagé (SAP) de stockage Azure qui donne un accès en lecture à chaque objet BLOB. Notez également l’utilisation de chaînes obscurcies ( `h` devant les valeurs de chaîne) pour garantir que la signature d’accès partagé n’est jamais enregistrée.

```kusto
.ingest into table T (
    h'https://contoso.blob.core.windows.net/container/file1.csv?...',
    h'https://contoso.blob.core.windows.net/container/file2.csv?...'
)
```

L’exemple suivant consiste à ingérer des données de Azure Data Lake Storage Gen 2 (ADLSv2). Les informations d’identification utilisées ici ( `...` ) sont les informations d’identification du compte de stockage (clé partagée) et nous utilisons l’obscurcissement de chaîne uniquement pour la partie secrète de la chaîne de connexion.

```kusto
.ingest into table T (
  'abfss://myfilesystem@contoso.dfs.core.windows.net/path/to/file1.csv;'
    h'...'
)
```

L’exemple suivant ingère un fichier unique à partir de Azure Data Lake Storage (ADLS).
Il utilise les informations d’identification de l’utilisateur pour accéder à ADLS (de sorte qu’il n’est pas nécessaire de traiter l’URI de stockage comme contenant un secret). Il montre également comment spécifier les propriétés d’ingestion.

```kusto
.ingest into table T ('adl://contoso.azuredatalakestore.net/Path/To/File/file1.ext;impersonate')
  with (format='csv')
```

