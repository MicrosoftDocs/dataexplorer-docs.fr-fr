---
title: alter-fusion table colonne-docstrings - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les documents de colonnes de table alter-fusion dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 75d298f35a215af5da443f673278e4a252c24cc9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522387"
---
# <a name="alter-merge-table-column-docstrings"></a>alter-fusion de table colonne-docstrings

Définit `docstring` la propriété d’une ou plusieurs colonnes de la table spécifiée. Les colonnes non définies explicitement **conservent** leur valeur existante pour cette propriété, si elles en ont une.

Pour modifier la colonne de table- docstring, voir [ci-dessous](#alter-table-column-docstrings).

**Syntaxe**

`.alter-merge``table` *TableName* `column-docstring` `(` *Col1* `:` *Docstring1* [`,` *Col2* `:` *Docstring2*]...`)`

**Exemple** 

```
.alter-merge table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```

## <a name="alter-table-column-docstrings"></a>modifier les colonnes de table-docstrings

Définit `docstring` la propriété d’une ou plusieurs colonnes de la table spécifiée. Colonnes non explicitement définies auront cette propriété **enlevée**.

**Syntaxe**

`.alter``table` *TableName* `column-docstring` `(` *Col1* `:` *Docstring1* [`,` *Col2* `:` *Docstring2*]...`)`

**Exemple** 

```
.alter table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```