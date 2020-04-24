---
title: .afficher les schémas de bases de données - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .show bases de données schémas dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 212aaf428e03190226a17509c97e9acd1394d2b1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519803"
---
# <a name="show-databases-schema"></a>.afficher les bases de données schéma

Retourne une liste plate de la structure des bases de données sélectionnées avec toutes leurs tables et colonnes dans une seule table ou un objet JSON.
Lorsqu’elle est utilisée avec une version, la base de données n’est retournée que si c’est une version ultérieure que la version fournie.

> [!NOTE]
> La version ne doit être fournie qu’en format "vMM.mm". MM représente la version principale et mm représentent la version mineure.

`.show``database` *DatabaseName* `schema` `details`[`if_later_than` ] [ *"Version"*] 

`.show``database` *DatabaseName* `schema` `if_later_than` [ *"Version"*] `as``json`
 
`.show``databases` `,` DatabaseName1 ... *DatabaseName1* `(` `)` `schema` [`details` | `as` `json`]
 
`.show``databases` `,` *"Version"* *DatabaseName1* DatabaseName1 if_later_than "Version" ... `(` `)` `schema` [`details` | `as` `json`]

**Exemple** 
 
La base de données 'TestDB' a 1 table appelée 'Events'.

```
.show database TestDB schema 
```

**Exemple de sortie**

|nom_base_de_données|TableName|ColumnName|ColumnType|IsDefaultTable|IsDefaultColumn|PrettyName (En)|Version
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v.1.1       
|TestDB|Événements|||True|False||       
|TestDB|Événements| Nom|System.String|True|False||     
|TestDB|Événements| StartTime|  System.DateTime|True|False||    
|TestDB|Événements| EndTime|    System.DateTime|True|False||        
|TestDB|Événements| City|   System.String|True| False||     
|TestDB|Événements| SessionId|  System.Int32|True|  True|| 

**Exemple** 
 
```
.show database TestDB schema if_later_than "v1.0" 
```
**Exemple de sortie**

|nom_base_de_données|TableName|ColumnName|ColumnType|IsDefaultTable|IsDefaultColumn|PrettyName (En)|Version
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v.1.1       
|TestDB|Événements|||True|False||       
|TestDB|Événements| Nom|System.String|True|False||     
|TestDB|Événements| StartTime|  System.DateTime|True|False||    
|TestDB|Événements| EndTime|    System.DateTime|True|False||        
|TestDB|Événements| City|   System.String|True| False||     
|TestDB|Événements| SessionId|  System.Int32|True|  True||  

Étant donné qu’une version inférieure à la version actuelle de la base de données a été fournie, le schéma « TestDB » a été retourné. Fournir une version égale ou supérieure aurait généré un résultat vide.

**Exemple** 
 
```
.show database TestDB schema as json
```

**Exemple de sortie**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

