---
title: Commandes de contrôle général de la table externe Kusto-Azure Explorateur de données
description: Cet article décrit les commandes générales de contrôle de table externe
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/26/2020
ms.openlocfilehash: a08f1f154c0efa17164d15a075456e2b6fab3212
ms.sourcegitcommit: a562ce255ac706ca1ca77d272a97b5975235729d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83867084"
---
# <a name="external-table-general-control-commands"></a>Commandes de contrôle générales de table externe

Pour obtenir une vue d’ensemble des tables externes, consultez [tables externes](../query/schema-entities/externaltables.md) . Les commandes suivantes s’appliquent à _toute_ table externe (de tout type).

## <a name="show-external-tables"></a>. afficher les tables externes

* Retourne toutes les tables externes de la base de données (ou d’une table externe spécifique).
* Nécessite l' [autorisation du moniteur de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe :** 

`.show` `external` `tables`

`.show` `external` `table` *TableName*

**Sortie**

| Paramètre de sortie | Type   | Description                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | string | Nom de la table externe                                             |
| TableType        | string | Type de table externe                                              |
| Dossier           | string | Dossier de la table                                                     |
| DocString        | string | Chaîne documentant la table                                       |
| Propriétés       | string | Propriétés sérialisées JSON de la table (spécifiques au type de table) |


**Exemples :**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | Dossier         | DocString | Propriétés |
|-----------|-----------|----------------|-----------|------------|
| T         | Objet blob      | ExternalTables | Documents      | {}         |


## <a name="show-external-table-schema"></a>. afficher le schéma de la table externe

* Retourne le schéma de la table externe, au format JSON ou CSL. 
* Nécessite l' [autorisation du moniteur de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe :** 

`.show``external` `table` *TableName* `schema` `as` ( `json`  |  `csl` )

`.show` `external` `table` *TableName* `cslschema`

**Sortie**

| Paramètre de sortie | Type   | Description                        |
|------------------|--------|------------------------------------|
| TableName        | string | Nom de la table externe            |
| schéma           | string | Schéma de table au format JSON |
| nom_base_de_données     | string | Nom de la base de données de la table             |
| Dossier           | string | Dossier de la table                    |
| DocString        | string | Chaîne documentant la table      |

**Exemples :**

```kusto
.show external table T schema as JSON
```

```kusto
.show external table T schema as CSL
.show external table T cslschema
```

**Output:**

*format*

| TableName | schéma    | nom_base_de_données | Dossier         | DocString |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | {« Name » : « ExternalBlob »,<br>« Dossier » : « ExternalTables »,<br>« DocString » : « docs »,<br>"OrderedColumns" : [{"Name" : "x", "type" : "System. Int64", "CslType" : "long", "DocString" : ""}, {"Name" : "s", "type" : "System. String", "CslType" : "String", "DocString" : ""}]} | DB           | ExternalTables | Documents      |


*CSL*

| TableName | schéma          | nom_base_de_données | Dossier         | DocString |
|-----------|-----------------|--------------|----------------|-----------|
| T         | x:long,s:string | DB           | ExternalTables | Documents      |

## <a name="drop-external-table"></a>. supprimer la table externe

* Supprime une table externe 
* La définition de la table externe ne peut pas être restaurée après cette opération
* Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe :**  

`.drop` `external` `table` *TableName*

**Sortie**

Retourne les propriétés de la table supprimée. Consultez [. afficher les tables externes](#show-external-tables).

**Exemples :**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | Dossier         | DocString | schéma       | Propriétés |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | Objet blob      | ExternalTables | Documents      | [{"Name" : "x", "CslType" : "long"},<br> {"Name" : "s", "CslType" : "String"}] | {}         |

## <a name="next-steps"></a>Étapes suivantes

* [Créer et modifier des tables externes dans le stockage Azure ou Azure Data Lake](external-tables-azurestorage-azuredatalake.md)
* [Créer et modifier des tables SQL externes](external-sql-tables.md)
