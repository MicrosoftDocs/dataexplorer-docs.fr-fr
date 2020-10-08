---
title: Mappages de données-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les mappages de données dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/19/2020
ms.openlocfilehash: 9695bd1a1330b4dc7cd44131d566c538c0264de4
ms.sourcegitcommit: eff06eb34f78630fd78470d918ebc04ff5dc863e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91847194"
---
# <a name="data-mappings"></a>Mappages des données

Les mappages de données sont utilisés lors de l’ingestion pour mapper les données entrantes aux colonnes dans les tables Kusto.

Kusto prend en charge différents types de mappages `row-oriented` (CSV, JSON et Avro) et `column-oriented` (parquet).

Chaque élément de la liste de mappage est construit à partir de trois propriétés :

|Propriété|Description|
|----|--|
|`column`|Nom de la colonne cible dans la table Kusto|
|`datatype`| Facultatif Type de données avec lequel créer la colonne mappée si elle n’existe pas déjà dans la table Kusto|
|`Properties`|Facultatif Conteneur de propriétés contenant des propriétés spécifiques pour chaque mappage, comme décrit dans chaque section ci-dessous.


Tous les mappages peuvent être [créés au préalable](create-ingestion-mapping-command.md) et peuvent être référencés à partir de la commande de réception à l’aide de `ingestionMappingReference` paramètres.

## <a name="csv-mapping"></a>Mappage CSV

Lorsque le fichier source est un fichier CSV (ou tout format séparé par des délimiteur) et que son schéma ne correspond pas au schéma de la table Kusto actuel, un mappage CSV est mappé du schéma de fichier au schéma de la table Kusto. Si la table n’existe pas dans Kusto, elle sera créée en fonction de ce mappage. Si certains champs du mappage sont absents de la table, ils sont ajoutés. 

Le mappage de volumes partagés de cluster peut être appliqué à tous les formats séparés par des délimiteurs : CSV, TSV, PSV, SCSV et SOHsv.

Chaque élément de la liste décrit un mappage pour une colonne spécifique et peut contenir les propriétés suivantes :

|Propriété|Description|
|----|--|
|`ordinal`|Numéro d’ordre des colonnes au format CSV|
|`constantValue`|Facultatif Valeur de constante à utiliser pour une colonne au lieu d’une valeur dans le CSV|

> [!NOTE]
> `Ordinal` and `ConstantValue` s'excluent mutuellement.

### <a name="example-of-the-csv-mapping"></a>Exemple de mappage de volumes partagés de cluster

``` json
[
  { "column" : "rownumber", "Properties":{"Ordinal":"0"}},
  { "column" : "rowguid",   "Properties":{"Ordinal":"1"}},
  { "column" : "xdouble",   "Properties":{"Ordinal":"2"}},
  { "column" : "xbool",     "Properties":{"Ordinal":"3"}},
  { "column" : "xint32",    "Properties":{"Ordinal":"4"}},
  { "column" : "xint64",    "Properties":{"Ordinal":"5"}},
  { "column" : "xdate",     "Properties":{"Ordinal":"6"}},
  { "column" : "xtext",     "Properties":{"Ordinal":"7"}},
  { "column" : "const_val", "Properties":{"ConstValue":"Sample: constant value"}}
]
```

> [!NOTE]
> Lorsque le mappage ci-dessus est fourni dans le cadre de la `.ingest` commande de contrôle, il est sérialisé en tant que chaîne JSON.

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMapping = 
        "["
            "{\"column\":\"rownumber\",\"Properties\":{\"Ordinal\":0}},"
            "{\"column\":\"rowguid\",  \"Properties\":{\"Ordinal\":1}}"
        "]" 
    )
```

> [!NOTE]
> Lorsque le mappage ci-dessus est [créé au préalable](create-ingestion-mapping-command.md) , il peut être référencé dans la `.ingest` commande de contrôle :

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMappingReference = "Mapping1"
    )
```

> [!NOTE]
> Le format de mappage suivant, sans le `Properties` conteneur de propriétés, est déconseillé.

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMapping = 
        "["
            "{\"column\":\"rownumber\",\"Ordinal\": 0},"
            "{\"column\":\"rowguid\",  \"Ordinal\": 1}"
        "]" 
    )
```

## <a name="json-mapping"></a>Mappage JSON

Lorsque le fichier source est au format JSON, le contenu du fichier est mappé à la table Kusto. La table doit exister dans la base de données Kusto, sauf si un type de données valide est spécifié pour toutes les colonnes mappées. Les colonnes mappées dans le mappage JSON doivent exister dans la table Kusto, sauf si un type de données est spécifié pour toutes les colonnes non existantes.

Chaque élément de la liste décrit un mappage pour une colonne spécifique et peut contenir les propriétés suivantes : 

|Propriété|Description|
|----|--|
|`path`|Si commence par `$` : chemin d’accès JSON au champ qui devient le contenu de la colonne dans le document JSON (le chemin d’accès JSON qui indique le document entier est `$` ). Si la valeur ne commence pas par `$` : une valeur constante est utilisée. Les chemins d’accès JSON qui incluent des espaces blancs doivent être placés dans une séquence d’échappement en tant que [ \' nom de propriété \' ].|
|`transform`|Facultatif Transformation à appliquer sur le contenu avec des [transformations de mappage](#mapping-transformations).|

### <a name="example-of-json-mapping"></a>Exemple de mappage JSON

```json
[
  { "column" : "rownumber",   "Properties":{"Path":"$.rownumber"}}, 
  { "column" : "rowguid",     "Properties":{"Path":"$.rowguid"}}, 
  { "column" : "xdouble",     "Properties":{"Path":"$.xdouble"}}, 
  { "column" : "xbool",       "Properties":{"Path":"$.xbool"}}, 
  { "column" : "xint32",      "Properties":{"Path":"$.xint32"}}, 
  { "column" : "xint64",      "Properties":{"Path":"$.xint64"}}, 
  { "column" : "xdate",       "Properties":{"Path":"$.xdate"}}, 
  { "column" : "xtext",       "Properties":{"Path":"$.xtext"}}, 
  { "column" : "location",    "Properties":{"transform":"SourceLocation"}}, 
  { "column" : "lineNumber",  "Properties":{"transform":"SourceLineNumber"}}, 
  { "column" : "full_record", "Properties":{"Path":"$"}}
]
```

> [!NOTE]
> Lorsque le mappage ci-dessus est fourni dans le cadre de la `.ingest` commande de contrôle, il est sérialisé en tant que chaîne JSON.

```kusto
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "json", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"Properties\":{\"Path\":\"$.rownumber\"}},"
        "{\"column\":\"rowguid\",  \"Properties\":{\"Path\":\"$.rowguid\"}}",
        "{\"column\":\"custom_column\",  \"Properties\":{\"Path\":\"$.[\'property name with space\']\"}}"
      "]"
  )
```

> [!NOTE]
> Lorsque le mappage ci-dessus est [créé au préalable](create-ingestion-mapping-command.md) , il peut être référencé dans la `.ingest` commande de contrôle :

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="json", 
        ingestionMappingReference = "Mapping1"
    )
```

> [!NOTE]
> Le format de mappage suivant, sans le `Properties` conteneur de propriétés, est déconseillé.

```kusto
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "json", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"path\":\"$.rownumber\"},"
        "{\"column\":\"rowguid\",  \"path\":\"$.rowguid\"}"
      "]"
  )
```
    
## <a name="avro-mapping"></a>Mappage Avro

Lorsque le fichier source est au format Avro, le contenu du fichier Avro est mappé à la table Kusto. La table doit exister dans la base de données Kusto, sauf si un type de données valide est spécifié pour toutes les colonnes mappées. Les colonnes mappées dans le mappage Avro doivent exister dans la table Kusto, sauf si un type de données est spécifié pour toutes les colonnes non existantes.

Chaque élément de la liste décrit un mappage pour une colonne spécifique et peut contenir les propriétés suivantes : 

|Propriété|Description|
|----|--|
|`Field`|Nom du champ dans l’enregistrement Avro.|
|`Path`|Alternative à l’utilisation de `field` qui permet de détenir la partie interne d’un champ d’enregistrement Avro, si nécessaire. La valeur désigne un chemin d’accès JSON à partir de la racine de l’enregistrement. Pour plus d’informations, consultez les remarques ci-dessous. Les chemins d’accès JSON qui incluent des espaces blancs doivent être placés dans une séquence d’échappement en tant que [ \' nom de propriété \' ].|
|`transform`|Facultatif Transformation à appliquer au contenu à l’aide de [transformations prises en charge](#mapping-transformations).|

**Remarques**
>[!NOTE]
> * `field` et `path` ne peuvent pas être utilisés ensemble, un seul est autorisé. 
> * `path` Impossible de pointer vers la racine `$` uniquement, elle doit avoir au moins un niveau de chemin d’accès.

Les deux options ci-dessous sont égales :

``` json
[
  {"column": "rownumber", "Properties":{"Path":"$.RowNumber"}}
]
```

``` json
[
  {"column": "rownumber", "Properties":{"Field":"RowNumber"}}
]
```

### <a name="example-of-the-avro-mapping"></a>Exemple de mappage AVRO

``` json
[
  {"column": "rownumber", "Properties":{"Field":"rownumber"}},
  {"column": "rowguid",   "Properties":{"Field":"rowguid"}},
  {"column": "xdouble",   "Properties":{"Field":"xdouble"}},
  {"column": "xboolean",  "Properties":{"Field":"xboolean"}},
  {"column": "xint32",    "Properties":{"Field":"xint32"}},
  {"column": "xint64",    "Properties":{"Field":"xint64"}},
  {"column": "xdate",     "Properties":{"Field":"xdate"}},
  {"column": "xtext",     "Properties":{"Field":"xtext"}}
]
``` 

> [!NOTE]
> Lorsque le mappage ci-dessus est fourni dans le cadre de la `.ingest` commande de contrôle, il est sérialisé en tant que chaîne JSON.

```kusto
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "avro", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"Properties\":{\"Path\":\"$.rownumber\"}},"
        "{\"column\":\"rowguid\",  \"Properties\":{\"Path\":\"$.rowguid\"}}"
      "]"
  )
```

> [!NOTE]
> Lorsque le mappage ci-dessus est [créé au préalable](create-ingestion-mapping-command.md) , il peut être référencé dans la `.ingest` commande de contrôle :

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="avro", 
        ingestionMappingReference = "Mapping1"
    )
```

> [!NOTE]
> Le format de mappage suivant, sans le `Properties` conteneur de propriétés, est déconseillé.

```kusto
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "avro", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"field\":\"rownumber\"},"
        "{\"column\":\"rowguid\",  \"field\":\"rowguid\"}"
      "]"
  )
```

## <a name="parquet-mapping"></a>Mappage parquet

Lorsque le fichier source est au format parquet, le contenu du fichier est mappé à la table Kusto. La table doit exister dans la base de données Kusto, sauf si un type de données valide est spécifié pour toutes les colonnes mappées. Les colonnes mappées dans le mappage parquet doivent exister dans la table Kusto, sauf si un type de données est spécifié pour toutes les colonnes non existantes.

Chaque élément de la liste décrit un mappage pour une colonne spécifique et peut contenir les propriétés suivantes :

|Propriété|Description|
|----|--|
|`path`|Si commence par `$` : chemin d’accès JSON au champ qui deviendra le contenu de la colonne dans le document parquet (le chemin d’accès JSON qui indique le document entier est `$` ). Si la valeur ne commence pas par `$` : une valeur constante est utilisée. Les chemins d’accès JSON qui incluent des espaces blancs doivent être placés dans une séquence d’échappement en tant que [ \' nom de propriété \' ]. |
|`transform`|Facultatif [mappages de transformations](#mapping-transformations) qui doivent être appliqués au contenu.


### <a name="example-of-the-parquet-mapping"></a>Exemple de mappage parquet

```json
[
  { "column" : "rownumber",   "Properties":{"Path":"$.rownumber"}}, 
  { "column" : "xdouble",     "Properties":{"Path":"$.xdouble"}}, 
  { "column" : "xbool",       "Properties":{"Path":"$.xbool"}}, 
  { "column" : "xint64",      "Properties":{"Path":"$.xint64"}}, 
  { "column" : "xdate",       "Properties":{"Path":"$.xdate"}}, 
  { "column" : "xtext",       "Properties":{"Path":"$.xtext"}}, 
  { "column" : "location",    "Properties":{"transform":"SourceLocation"}}, 
  { "column" : "lineNumber",  "Properties":{"transform":"SourceLineNumber"}}, 
  { "column" : "full_record", "Properties":{"Path":"$"}}
]
```      

> [!NOTE]
> Lorsque le mappage ci-dessus est fourni dans le cadre de la `.ingest` commande de contrôle, il est sérialisé en tant que chaîne JSON.

```kusto
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "parquet", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"Properties\":{\"Path\":\"$.rownumber\"}},"
        "{\"column\":\"rowguid\",  \"Properties\":{\"Path\":\"$.rowguid\"}}",
        "{\"column\":\"custom_column\",  \"Properties\":{\"Path\":\"$.[\'property name with space\']\"}}"
      "]"
  )
```

> [!NOTE]
> Lorsque le mappage ci-dessus est [créé au préalable](create-ingestion-mapping-command.md) , il peut être référencé dans la `.ingest` commande de contrôle :

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="parquet", 
        ingestionMappingReference = "Mapping1"
    )
```

## <a name="orc-mapping"></a>Mappage ORC

Lorsque le fichier source est au format ORC, le contenu du fichier est mappé à la table Kusto. La table doit exister dans la base de données Kusto, sauf si un type de données valide est spécifié pour toutes les colonnes mappées. Les colonnes mappées dans le mappage ORC doivent exister dans la table Kusto, sauf si un type de données est spécifié pour toutes les colonnes non existantes.

Chaque élément de la liste décrit un mappage pour une colonne spécifique et peut contenir les propriétés suivantes :

|Propriété|Description|
|----|--|
|`path`|Si commence par `$` : chemin d’accès JSON au champ qui deviendra le contenu de la colonne dans le document orc (le chemin d’accès JSON qui indique le document entier est `$` ). Si la valeur ne commence pas par `$` : une valeur constante est utilisée. Les chemins d’accès JSON qui incluent des espaces blancs doivent être placés dans une séquence d’échappement en tant que [ \' nom de propriété \' ].|
|`transform`|Facultatif [mappages de transformations](#mapping-transformations) qui doivent être appliqués au contenu.

### <a name="example-of-orc-mapping"></a>Exemple de mappage ORC

```json
[
  { "column" : "rownumber",   "Properties":{"Path":"$.rownumber"}}, 
  { "column" : "xdouble",     "Properties":{"Path":"$.xdouble"}}, 
  { "column" : "xbool",       "Properties":{"Path":"$.xbool"}}, 
  { "column" : "xint64",      "Properties":{"Path":"$.xint64"}}, 
  { "column" : "xdate",       "Properties":{"Path":"$.xdate"}}, 
  { "column" : "xtext",       "Properties":{"Path":"$.xtext"}}, 
  { "column" : "location",    "Properties":{"transform":"SourceLocation"}}, 
  { "column" : "lineNumber",  "Properties":{"transform":"SourceLineNumber"}}, 
  { "column" : "full_record", "Properties":{"Path":"$"}}
]
```      

> [!NOTE]
> Lorsque le mappage ci-dessus est fourni dans le cadre de la `.ingest` commande de contrôle, il est sérialisé en tant que chaîne JSON.

```kusto
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "orc", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"Properties\":{\"Path\":\"$.rownumber\"}},"
        "{\"column\":\"rowguid\",  \"Properties\":{\"Path\":\"$.rowguid\"}}",
        "{\"column\":\"custom_column\",  \"Properties\":{\"Path\":\"$.[\'property name with space\']\"}}"
      "]"
  )
```

> [!NOTE]
> Lorsque le mappage ci-dessus est [créé au préalable](create-ingestion-mapping-command.md) , il peut être référencé dans la `.ingest` commande de contrôle :

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="orc", 
        ingestionMappingReference = "Mapping1"
    )
```

## <a name="mapping-transformations"></a>Mappages de transformations

Certains mappages de format de données (parquet, JSON et Avro) prennent en charge les transformations d’ingestion simples et utiles. Lorsque le scénario nécessite un traitement plus complexe à l’heure de réception, utilisez la [stratégie de mise à jour](update-policy.md), qui permet de définir un traitement léger à l’aide de l’expression KQL.

|Transformation dépendante du chemin d’accès|Description|Conditions|
|--|--|--|
|`PropertyBagArrayToDictionary`|Transforme un tableau JSON de propriétés (par exemple, {Events : [{"N1" : "V1"}, {"N2" : "v2"}]}) en dictionary et le sérialise vers un document JSON valide (par exemple, {"N1" : "V1", "N2" : "v2"}).|Peut être appliqué uniquement quand `path` est utilisé|
|`SourceLocation`|Nom de l’artefact de stockage qui a fourni les données, type chaîne (par exemple, le champ « BaseUri » de l’objet BLOB).|
|`SourceLineNumber`|Décalage par rapport à cet artefact de stockage, type long (commençant par « 1 » et incrémentant par nouvel enregistrement).|
|`DateTimeFromUnixSeconds`|Convertit le nombre représentant l’heure UNIX (en secondes depuis 1970-01-01) en chaîne DateTime UTC|
|`DateTimeFromUnixMilliseconds`|Convertit le nombre représentant l’heure UNIX (millisecondes depuis 1970-01-01) en chaîne DateTime UTC|
|`DateTimeFromUnixMicroseconds`|Convertit le nombre représentant l’heure UNIX (en microsecondes depuis 1970-01-01) en une chaîne DateTime UTC|
|`DateTimeFromUnixNanoseconds`|Convertit un nombre représentant l’heure UNIX (nanosecondes depuis 1970-01-01) en une chaîne DateTime UTC|
