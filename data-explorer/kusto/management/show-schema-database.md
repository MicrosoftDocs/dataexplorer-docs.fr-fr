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
ms.openlocfilehash: 90a48ec3a830e754bf823712ca7016d162474a39
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616981"
---
# <a name="show-databases-schema"></a>. afficher le schéma des bases de données

Retourne une liste plate de la structure des bases de données sélectionnées avec toutes leurs tables et colonnes dans une table ou un objet JSON unique.
Lorsqu’elle est utilisée avec une version, la base de données est retournée uniquement s’il s’agit d’une version ultérieure à la version fournie.

> [!NOTE]
> La version doit être fournie uniquement au format « vMM.mm ». MM représente la version principale et mm la version mineure.

`.show``database` *DatabaseName* DatabaseName `schema` [`details`] [`if_later_than` *« version »*] 

`.show``database` *DatabaseName* DatabaseName `schema` [`if_later_than` *"version"*] `as``json`
 
`.show``databases` `,` *DatabaseName1* DatabaseName1 `(` ... `)` `schema` [`details` | `as` `json`]
 
`.show``databases` *"Version"* `,` *DatabaseName1* DatabaseName1 if_later_than "version"... `(` `)` `schema` [`details` | `as` `json`]

**Exemple** 
 
La base de données « TestDB » a 1 table appelée « Events ».

```kusto
.show database TestDB schema 
```

**Exemple de sortie**

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
 
```kusto
.show database TestDB schema if_later_than "v1.0" 
```
**Exemple de sortie**

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

**Exemple** 
 
```kusto
.show database TestDB schema as json
```

**Exemple de sortie**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

