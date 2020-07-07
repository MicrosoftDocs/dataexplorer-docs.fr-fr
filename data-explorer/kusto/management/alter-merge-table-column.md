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
ms.openlocfilehash: 9f71a46e6c747b7f9d9f6a3ba2d2f8a308635128
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967211"
---
# <a name="alter-merge-table-column-docstrings"></a>.alter-merge table column-docstrings

Définit la `docstring` propriété d’une ou plusieurs colonnes de la table spécifiée. Les colonnes qui ne sont pas définies explicitement **conservent** leur valeur existante pour cette propriété, si elles en ont une.

Pour ALTER TABLE Column-DocString, voir [ci-dessous](#alter-table-column-docstrings).

**Syntaxe**

`.alter-merge``table` *TableName* `column-docstring` TableName `(` *Col1* `:` *Docstring1* [ `,` *col2* `:` *Docstring2*]...`)`

**Exemple** 

```kusto
.alter-merge table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```

## <a name="alter-table-column-docstrings"></a>ALTER TABLE Column-docstrings

Définit la `docstring` propriété d’une ou plusieurs colonnes de la table spécifiée. Cette propriété est **supprimée**des colonnes qui ne sont pas explicitement définies.

**Syntaxe**

`.alter``table` *TableName* `column-docstring` TableName `(` *Col1* `:` *Docstring1* [ `,` *col2* `:` *Docstring2*]...`)`

**Exemple** 

```kusto
.alter table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```
