---
title: . afficher le schéma des bases de données-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit le schéma. afficher les bases de données dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b77fe78ec43bce775774ae95c1ea713d03873cf4
ms.sourcegitcommit: c351c2c8ab6e184827c4702eb0ec8bf783c7bbd3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874795"
---
# <a name="show-database-schema-commands"></a>. afficher les commandes de schéma de base de données

Les commandes suivantes affichent le schéma de base de données sous la forme d’un tableau, d’un objet JSON ou d’un script CSL.

## <a name="show-databases-schema"></a>. afficher le schéma des bases de données

**Syntaxe**

`.show``database` *DatabaseName* `schema` [ `details` ] [ `if_later_than` *« version »*] 

`.show``databases` `(` *DatabaseName1* `,` .. `)` . `schema``details` 
 
`.show``databases` `(` *DatabaseName1* if_later_than *"version"* `,` ... `)` `schema``details`

**Retourne**

Retourne une liste plate de la structure des bases de données sélectionnées avec toutes leurs tables et colonnes dans une table ou un objet JSON unique.
Lorsqu’elle est utilisée avec une version, la base de données est retournée uniquement s’il s’agit d’une version ultérieure à la version fournie.

> [!NOTE]
> La version doit être fournie uniquement au format « vMM.mm ». MM représente la version principale et mm la version mineure.

**Exemple** 
 
La base de données « TestDB » a une table appelée « Events ».

```kusto
.show database TestDB schema 
```

**Sortie**

|nom_base_de_données|TableName|ColumnName|ColumnType|IsDefaultTable|IsDefaultColumn|PrettyName|Version
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v. 1.1       
|TestDB|Événements|||True|False||       
|TestDB|Événements| Nom|System.String|True|False||     
|TestDB|Événements| StartTime|  System.DateTime|True|False||    
|TestDB|Événements| EndTime|    System.DateTime|True|False||        
|TestDB|Événements| City|   System.String|True| False||     
|TestDB|Événements| SessionId|  System.Int32|True|  True|| 

**Exemple** 

Dans l’exemple suivant, la base de données est retournée uniquement s’il s’agit d’une version ultérieure à la version fournie.
 
```kusto
.show database TestDB schema if_later_than "v1.0" 
```

**Sortie**

|nom_base_de_données|TableName|ColumnName|ColumnType|IsDefaultTable|IsDefaultColumn|PrettyName|Version
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v. 1.1       
|TestDB|Événements|||True|False||       
|TestDB|Événements| Nom|System.String|True|False||     
|TestDB|Événements| StartTime|  System.DateTime|True|False||    
|TestDB|Événements| EndTime|    System.DateTime|True|False||        
|TestDB|Événements| City|   System.String|True| False||     
|TestDB|Événements| SessionId|  System.Int32|True|  True||  

Étant donné qu’une version antérieure à la version actuelle de la base de données a été fournie, le schéma « TestDB » a été retourné. Le fait de fournir une version égale ou supérieure aurait généré un résultat vide.

## <a name="show-database-schema-as-json"></a>. afficher le schéma de base de données au format JSON

**Syntaxe**

`.show``database` *DatabaseName* `schema` [ `if_later_than` *"version"*] `as``json`
 
`.show``databases` `(` *DatabaseName1* `,` .. `)` . `schema` `as``json`
 
`.show``databases` `(` *DatabaseName1* if_later_than *"version"* `,` ... `)` `schema` `as``json`

**Retourne**

Retourne une liste plate de la structure des bases de données sélectionnées avec toutes leurs tables et colonnes sous la forme d’un objet JSON.
Lorsqu’elle est utilisée avec une version, la base de données est retournée uniquement s’il s’agit d’une version ultérieure à la version fournie.

**Exemple** 
 
```kusto
.show database TestDB schema as json
```

**Sortie**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

## <a name="show-database-schema-as-csl-script"></a>. afficher le schéma de base de données en tant que script CSL

Génère un script CSL avec toutes les commandes requises pour créer une copie du schéma de base de données (ou actuel) donné.

**Syntaxe**

`.show``database` *DatabaseName* `schema` Databasename `as` `csl` `script` [ `with(` *Options* `)` ]

**Arguments**

Les *options* suivantes sont toutes facultatives :

* `IncludeEncodingPolicies`: ( `true`  |  `false` )-Si `true` , les stratégies d’encodage au niveau de la base de données/table/colonne seront incluses. La valeur par défaut est `false`. 
* `IncludeSecuritySettings`: ( `true`  |  `false` )-A la valeur par défaut `false` . Si `true` la condition est, les options suivantes sont incluses :
  * Entités autorisées au niveau de la base de données/table.
  * Stratégies de sécurité au niveau des lignes au niveau de la table.
  * Stratégies d’accès en affichage restreintes au niveau de la table.
* `IncludeIngestionMappings`: ( `true`  |  `false` )-Si `true` , les mappages d’ingestion au niveau de la table seront inclus. La valeur par défaut est `false`. 

**Retourne**

Le script, retourné sous forme de chaîne, contient les éléments suivants :

* Commandes pour créer la base de données et définir son nom convivial, le cas échéant.
  * La commande générée crée une base de données volatile et est commentée quand elle est ajoutée au script.
* Commandes pour créer toutes les tables dans la base de données.
* Commandes pour définir toutes les stratégies de base de données/tables/colonnes pour qu’elles correspondent aux stratégies d’origine.
* Commandes permettant de créer ou de modifier toutes les fonctions définies par l’utilisateur dans la base de données.

**Exemple** 
 
```kusto
.show database TestDB schema as csl script

.show database TestDB schema as csl script with(IncludeSecuritySettings = true)
```
