---
title: Cartographies de données - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les cartographies de données dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 0d94815eedfd551a09a979c57c68baf125abec40
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520772"
---
# <a name="data-mappings"></a>Cartographie des données

Les cartographies de données sont utilisées pendant l’ingestion pour cartographier les données entrantes vers les colonnes à l’intérieur des tables Kusto.

Kusto prend en charge différents `row-oriented` types de cartes, à la `column-oriented` fois (CSV, JSON et AVRO), et (Parquet).

Chaque élément de la liste de cartographie est construit à partir de trois propriétés :

|Propriété|Description|
|----|--|
|`column`|Nom de colonne cible dans la table de Kusto|
|`datatype`| (Facultatif) Datatype avec lequel créer la colonne cartographiée si elle n’existe pas déjà dans la table Kusto|
|`Properties`|(Facultatif) Sac de propriété contenant des propriétés spécifiques pour chaque cartographie telle que décrite dans chaque section ci-dessous.


Toutes les cartes peuvent être [pré-créées](create-ingestion-mapping-command.md) et peuvent être `ingestionMappingReference` référencées à partir de la commande ingéreuse à l’aide de paramètres.

## <a name="csv-mapping"></a>Cartographie CSV

Lorsque le fichier source est un CSV (ou n’importe quel format délimeter séparé) et son schéma ne correspond pas au schéma actuel de la table Kusto, un CSV cartographie les cartes du schéma de fichiers au schéma de table Kusto. Si le tableau n’existe pas à Kusto, il sera créé selon cette cartographie. Si certains champs de la cartographie sont manquants dans le tableau, ils seront ajoutés. 

La cartographie CSV peut être appliquée sur tous les formats séparés par les délimitants : CSV, TSV, PSV, SCSV et SOHsv.

Chaque élément de la liste décrit une cartographie pour une colonne spécifique, et peut contenir les propriétés suivantes:

|Propriété|Description|
|----|--|
|`ordinal`|Le numéro de commande de colonne dans CSV|
|`constantValue`|(Facultatif) La valeur constante à utiliser pour une colonne au lieu d’une certaine valeur à l’intérieur du CSV|

> [!NOTE]
> `Ordinal`et `ConstantValue` s’excluent mutuellement.

### <a name="example-of-the-csv-mapping"></a>Exemple de la cartographie CSV

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
> Lorsque la cartographie ci-dessus `.ingest` est fournie dans le cadre de la commande de contrôle, elle est sérialisée sous forme de chaîne JSON.

* Lorsque la cartographie [ci-dessus est pré-créée,](create-ingestion-mapping-command.md) elle peut être référencée dans la commande de `.ingest` contrôle :
```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMappingReference = "Mapping1"
    )
```

* Lorsque la cartographie ci-dessus `.ingest` est fournie dans le cadre de la commande de contrôle, elle est sérialisée sous forme de chaîne JSON :

```
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

**Note:** Le format de cartographie `Properties` suivant, sans le sac de propriété, est actuellement pris en charge, mais peut être déprécié à l’avenir.

```
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

## <a name="json-mapping"></a>Cartographie JSON

Lorsque le fichier source est en format JSON, le contenu du fichier est cartographié sur la table Kusto. Le tableau doit exister dans la base de données Kusto à moins qu’un type de données valide ne soit spécifié pour toutes les colonnes cartographiées. Les colonnes cartographiées dans la cartographie JSON doivent exister dans le tableau Kusto à moins qu’un datatype ne soit spécifié pour toutes les colonnes non existantes.

Chaque élément de la liste décrit une cartographie pour une colonne spécifique, et peut contenir les propriétés suivantes: 

|Propriété|Description|
|----|--|
|`path`|Si commence `$`par : JSON chemin vers le champ qui deviendra le contenu de la colonne dans `$`le document JSON (JSON chemin qui dénote l’ensemble du document est ). Si la valeur ne `$`commence pas : une valeur constante est utilisée.|
|`transform`|(Facultatif) Transformation qui devrait être appliquée sur le contenu avec [des transformations cartographiques](#mapping-transformations).|

### <a name="example-of-json-mapping"></a>Exemple de cartographie JSON

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
> Lorsque la cartographie ci-dessus `.ingest` est fournie dans le cadre de la commande de contrôle, elle est sérialisée sous forme de chaîne JSON.

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="json", 
        ingestionMappingReference = "Mapping1"
    )
```

**Note:** Le format de cartographie `Properties` suivant, sans le sac de propriété, est actuellement pris en charge, mais peut être déprécié à l’avenir.

```
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
    
## <a name="avro-mapping"></a>Cartographie Avro

Lorsque le fichier source est en format Avro, le contenu du fichier Avro est cartographié sur la table Kusto. Le tableau doit exister dans la base de données Kusto à moins qu’un type de données valide ne soit spécifié pour toutes les colonnes cartographiées. Les colonnes cartographiées dans la cartographie Avro doivent exister dans la table Kusto à moins qu’un datatype ne soit spécifié pour toutes les colonnes non existantes.

Chaque élément de la liste décrit une cartographie pour une colonne spécifique, et peut contenir les propriétés suivantes: 

|Propriété|Description|
|----|--|
|`Field`|Le nom du champ dans le dossier Avro.|
|`Path`|Alternative à `field` l’utilisation qui permet de prendre la partie intérieure d’un champ d’enregistrement Avro, si nécessaire. La valeur désigne un JSON-path de la racine de l’enregistrement. Voir les notes ci-dessous pour plus d’informations. |
|`transform`|(Facultatif) Transformation qui doit être appliquée sur le contenu avec [des transformations prises en charge](#mapping-transformations).|

**Remarques**
>[!NOTE]
> * `field`et `path` ne peut pas être utilisé ensemble, un seul est autorisé. 
> * `path`ne peut `$` pas pointer vers la racine seulement, il doit avoir au moins un niveau de chemin.

Les deux alternatives ci-dessous sont égales :

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

### <a name="example-of-the-avro-mapping"></a>Exemple de la cartographie AVRO

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
> Lorsque la cartographie ci-dessus `.ingest` est fournie dans le cadre de la commande de contrôle, elle est sérialisée sous forme de chaîne JSON.

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="avro", 
        ingestionMappingReference = "Mapping1"
    )
```

**Note:** Le format de cartographie `Properties` suivant, sans le sac de propriété, est actuellement pris en charge, mais peut être déprécié à l’avenir.

```
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

## <a name="parquet-mapping"></a>Cartographie de parquet

Lorsque le fichier source est en format Parquet, le contenu du fichier est cartographié sur la table Kusto. Le tableau doit exister dans la base de données Kusto à moins qu’un type de données valide ne soit spécifié pour toutes les colonnes cartographiées. Les colonnes cartographiées dans la cartographie parquet doivent exister dans le tableau Kusto à moins qu’un datatype ne soit spécifié pour toutes les colonnes non existantes.

Chaque élément de la liste décrit une cartographie pour une colonne spécifique, et peut contenir les propriétés suivantes:

|Propriété|Description|
|----|--|
|`path`|Si commence `$`par : JSON chemin vers le champ qui deviendra le contenu de la colonne dans `$`le document parquet (chemin JSON qui dénote l’ensemble du document est ). Si la valeur ne `$`commence pas : une valeur constante est utilisée.|
|`transform`|(Facultatif) [cartographier les transformations](#mapping-transformations) qui doivent être appliquées sur le contenu.


### <a name="example-of-the-parquet-mapping"></a>Exemple de la cartographie du Parquet

``` json
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
> Lorsque la cartographie ci-dessus `.ingest` est fournie dans le cadre de la commande de contrôle, elle est sérialisée sous forme de chaîne JSON.

* Lorsque la cartographie [ci-dessus est pré-créée,](create-ingestion-mapping-command.md) elle peut être référencée dans la commande de `.ingest` contrôle :

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="parquet", 
        ingestionMappingReference = "Mapping1"
    )
```

* Lorsque la cartographie ci-dessus `.ingest` est fournie dans le cadre de la commande de contrôle, elle est sérialisée sous forme de chaîne JSON :

```
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "parquet", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"Properties\":{\"Path\":\"$.rownumber\"}},"
        "{\"column\":\"rowguid\",  \"Properties\":{\"Path\":\"$.rowguid\"}}"
      "]"
  )
```

## <a name="orc-mapping"></a>Cartographie Orc

Lorsque le fichier source est en format Orc, le contenu du fichier est cartographié sur la table Kusto. Le tableau doit exister dans la base de données Kusto à moins qu’un type de données valide ne soit spécifié pour toutes les colonnes cartographiées. Les colonnes cartographiées dans la cartographie Orc doivent exister dans le tableau Kusto à moins qu’un datatype ne soit spécifié pour toutes les colonnes non existantes.

Chaque élément de la liste décrit une cartographie pour une colonne spécifique, et peut contenir les propriétés suivantes:

|Propriété|Description|
|----|--|
|`path`|Si commence `$`par : JSON chemin vers le champ qui deviendra le contenu de la colonne dans `$`le document Orc (JSON chemin qui dénote l’ensemble du document est ). Si la valeur ne `$`commence pas : une valeur constante est utilisée.|
|`transform`|(Facultatif) [cartographier les transformations](#mapping-transformations) qui doivent être appliquées sur le contenu.

### <a name="example-of-orc-mapping"></a>Exemple de cartographie Orc

``` json
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
> Lorsque la cartographie ci-dessus `.ingest` est fournie dans le cadre de la commande de contrôle, elle est sérialisée sous forme de chaîne JSON.

```
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "orc", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"Properties\":{\"Path\":\"$.rownumber\"}},"
        "{\"column\":\"rowguid\",  \"Properties\":{\"Path\":\"$.rowguid\"}}"
      "]"
  )
```

## <a name="mapping-transformations"></a>Cartographier les transformations

Certaines des cartes de format de données (Parquet, JSON et Avro) prennent en charge des transformations simples et utiles en cas d’ingère. Lorsque le scénario nécessite un traitement plus complexe au moment de l’ingérer, utilisez [la stratégie De mise à jour](update-policy.md), qui permet de définir le traitement léger à l’aide de l’expression KQL.

|Transformation dépendante des chemins|Description|Conditions|
|--|--|--|
|`PropertyBagArrayToDictionary`|Transforme la gamme de propriétés JSON (par exemple les événements : [n1":"v1», "n2":"v2") au dictionnaire et le sérialise en document JSON valide (par exemple, "n1":"v1","n2":"v2").|Ne peut être `path` appliqué que lorsqu’il est utilisé|
|`GetPathElement(index)`|Extrait un élément du chemin donné selon l’indice donné (par exemple, Chemin : $.a.b.c, GetPathElement(0) ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' '|Ne peut être `path` appliqué que lorsqu’il est utilisé|
|`SourceLocation`|Nom de l’artefact de stockage qui a fourni les données, chaîne de type (par exemple, le champ "BaseUri" du blob).|
|`SourceLineNumber`|Décalage par rapport à cet artefact de stockage, type long (en commençant par '1' et incrémentation par nouvel enregistrement).|
|`DateTimeFromUnixSeconds`|Convertit le numéro représentant unix-temps (secondes depuis 1970-01-01) à la chaîne de date UTC|
|`DateTimeFromUnixMilliseconds`|Convertit le nombre représentant unix-temps (millisecondes depuis 1970-01-01) à la chaîne de date UTC|
|`DateTimeFromUnixMicroseconds`|Convertit le nombre représentant unix-time (microsecondes depuis 1970-01-01) en chaîne de date UTC|
|`DateTimeFromUnixNanoseconds`|Convertit le nombre représentant unix-time (nanosecondes depuis 1970-01-01) en chaîne de date UTC|