---
title: Formats de données pris en charge par Azure Data Explorer pour l’ingestion.
description: Découvrez les différents formats de données et de compression pris en charge par Azure Data Explorer pour l’ingestion.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 4f7e61755b12c84fc49373a12edc0b507aee9bf4
ms.sourcegitcommit: 2764e739b4ad51398f4f0d3a9742d7168c4f5fd7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712049"
---
# <a name="data-formats-supported-by-azure-data-explorer-for-ingestion"></a>Formats de données pris en charge par Azure Data Explorer pour l’ingestion

L’ingestion des données est le processus par lequel les données sont ajoutées à une table et deviennent disponibles pour une requête dans Azure Data Explorer. Pour toutes les méthodes d’ingestion autres que l’ingestion à partir d’une requête, les données doivent être dans l’un des formats pris en charge. Le tableau suivant liste et décrit les formats pris en charge par Azure Data Explorer pour l’ingestion de données.

|Format   |Extension   |Description|
|---------|------------|-----------|
|ApacheAvro|`.avro`    |Format [Avro](https://avro.apache.org/docs/current/) avec prise en charge des [types logiques](https://avro.apache.org/docs/current/spec.html#Logical+Types) et du codec de compression `snappy`.|
|Avro     |`.avro`     |Implémentation existante du [fichier conteneur Avro](https://avro.apache.org/docs/current/). Les codes suivants sont pris en charge : `null`, `deflate` (pour `snappy` - utiliser le format de fichier `apacheavro`).|
|CSV      |`.csv`      |Fichier texte avec des valeurs séparées par des virgules (`,`). Consultez [RFC 4180 : _Common Format and MIME Type for Comma-Separated Values (CSV) Files_](https://www.ietf.org/rfc/rfc4180.txt).|
|JSON     |`.json`     |Fichier texte avec des objets JSON délimités par `\n` ou `\r\n`. Consultez [JSON Lines (JSONL)](http://jsonlines.org/).|
|MultiJSON|`.multijson`|Fichier texte avec un tableau JSON de conteneurs des propriétés (représentant chacun un enregistrement) ou n’importe quel nombre de conteneurs des propriétés délimités par des espaces, `\n` ou `\r\n`. Chaque conteneur de propriétés peut être réparti sur plusieurs lignes. Ce format est préférable à `JSON`, sauf si les données ne sont pas des conteneurs de propriétés.|
|ORC      |`.orc`      |[Fichier orc](https://en.wikipedia.org/wiki/Apache_ORC).|
|Parquet  |`.parquet`  |[Fichier Parquet](https://en.wikipedia.org/wiki/Apache_Parquet).|
|PSV      |`.psv`      |Fichier texte avec des valeurs séparées par des barres verticales (<code>&#124;</code>).|
|RAW      |`.raw`      |Fichier texte dont le contenu entier est une valeur de chaîne unique.|
|SCsv     |`.scsv`     |Fichier texte avec des valeurs séparées par des points-virgules (`;`).|
|SOHsv    |`.sohsv`    |Fichier texte avec des valeurs séparées par SOH. (SOH est le point de code ASCII 1 ; ce format est utilisé par Hive sur HDInsight.)|
|TSV      |`.tsv`      |Fichier texte avec des valeurs séparées par des tabulations (`\t`).|
|TSVE     |`.tsv`      |Fichier texte avec des valeurs séparées par des tabulations (`\t`). Une barre oblique inverse (`\`) est utilisée pour l’échappement.|
|TXT      |`.txt`      |Fichier texte dont les lignes sont délimitées par `\n`. Les lignes vides sont ignorées.|
|W3CLOGFILE |`.log`    |Format du [fichier journal web](https://www.w3.org/TR/WD-logfile.html) standardisé par le W3C.|

## <a name="supported-data-compression-formats"></a>Formats de compression de données pris en charge

Les objets blob et les fichiers peuvent être compressés par l’un des algorithmes de compression suivants :

|Compression|Extension|
|-----------|---------|
|GZip       |.gz      |
|Zip        |.zip     |

Indiquez la compression en ajoutant l’extension au nom de l’objet blob ou du fichier.

Par exemple :
* `MyData.csv.zip` indique un objet blob ou un fichier au format CSV, compressé avec ZIP (archive ou fichier unique)
* `MyData.json.gz` indique un objet blob ou un fichier au format JSON, compressé avec GZip.

Les noms d’objets blob ou de fichiers qui n’incluent pas les extensions de format mais simplement la compression (par exemple, `MyData.zip`) sont également pris en charge. Dans ce cas, le format de fichier doit être spécifié en tant que propriété d’ingestion, car il ne peut pas être déduit.

> [!NOTE]
> Certains formats de compression assurent le suivi de l’extension de fichier d’origine dans le cadre du flux compressé. Cette extension est généralement ignorée lors du processus de vérification du format de fichier. S’il n’est pas possible de déterminer le format de fichier à partir du nom de l’objet blob ou du fichier (compressé), vous devez le spécifier par le biais de la propriété d’ingestion `format`.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez-en plus sur l’[ingestion de données](ingest-data-overview.md).
* Découvrez-en plus sur les [propriétés d’ingestion de données Azure Data Explorer](ingestion-properties.md).
