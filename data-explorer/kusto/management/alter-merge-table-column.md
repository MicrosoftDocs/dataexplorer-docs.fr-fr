---
title: ALTER-Merge table Column-docstrings-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit ALTER-Merge table Column-docstrings dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7dd36181be1140d3960369b1c8a5284ed55e48f5
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616488"
---
# <a name="alter-merge-table-column-docstrings"></a>ALTER-Merge, colonne de table-docstrings

Définit la `docstring` propriété d’une ou plusieurs colonnes de la table spécifiée. Les colonnes qui ne sont pas définies explicitement **conservent** leur valeur existante pour cette propriété, si elles en ont une.

Pour ALTER TABLE Column-DocString, voir [ci-dessous](#alter-table-column-docstrings).

**Syntaxe**

`.alter-merge``table` *TableName* `:` *Docstring1* *Col1* `:` *Docstring2*col1 Docstring1 [`,` col2 Docstring2]... *Col2* `column-docstring` `(``)`

**Exemple** 

```kusto
.alter-merge table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```

## <a name="alter-table-column-docstrings"></a>ALTER TABLE Column-docstrings

Définit la `docstring` propriété d’une ou plusieurs colonnes de la table spécifiée. Cette propriété est **supprimée**des colonnes qui ne sont pas explicitement définies.

**Syntaxe**

`.alter``table` *TableName* `:` *Docstring1* *Col1* `:` *Docstring2*col1 Docstring1 [`,` col2 Docstring2]... *Col2* `column-docstring` `(``)`

**Exemple** 

```kusto
.alter table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```
