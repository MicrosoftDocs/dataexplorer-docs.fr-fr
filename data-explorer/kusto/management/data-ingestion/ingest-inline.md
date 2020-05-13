---
title: Commande Inline. deréception (push)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la commande Inline. deréception (push) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 2b1766b295fd348639d8d91c8308a3ed0a35a3dc
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373415"
---
# <a name="the-ingest-inline-command-push"></a>Commande Inline. deréception (push)

Cette commande ingère les données dans une table en « poussant » les données incorporées inline dans le texte de la commande.

> [!NOTE]
> L’objectif principal de cette commande est d’effectuer des tests manuels ad hoc.
> Pour l’utilisation en production, il est recommandé d’utiliser d’autres méthodes d’ingestion plus appropriées pour la distribution en bloc d’énormes quantités de données, telles que l’ingestion [du stockage](./ingest-from-storage.md).

**Syntaxe**

`.ingest``inline` `into` `table` *TableName* [ `with` `(` *IngestionPropertyName* `=` *IngestionPropertyValue* [ `,` ...] `)` ] `<|` *Données*



**Arguments**

* *TableName* est le nom de la table vers laquelle les données sont ingérées.
  Le nom de la table est toujours relatif à la base de données en contexte, et son schéma est le schéma qui sera utilisé pour les données si aucun objet de mappage de schéma n’est fourni.

* *Data* est le contenu de données à ingérer. Sauf modification contraire des propriétés d’ingestion, ce contenu est analysé en tant que CSV.
  Notez que contrairement à la plupart des commandes et des requêtes de contrôle, le texte de la partie *données* de la commande ne doit pas respecter les conventions syntaxiques de la langue (par exemple, les espaces sont importants, la `//` combinaison n’est pas traitée comme un commentaire, etc.)

* *IngestionPropertyName*, *IngestionPropertyValue*: nombre quelconque de [Propriétés](../../../ingestion-properties.md) d’ingestion qui affectent le processus d’ingestion.

**Résultats**

Le résultat de la commande est une table avec autant d’enregistrements qu’il y a de données partitions (« extents ») générées par la commande.
Si aucun partitions de données n’a été généré, un seul enregistrement est retourné avec un ID d’extension vide (valeur zéro).

|Nom       |Type      |Description                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|ExtentId   |`guid`    |Identificateur unique pour le partition de données qui a été généré par la commande.|

**Exemples**

La commande suivante ingère les données dans une table ( `Purchases` ) avec deux colonnes, `SKU` (de type `string` ) et `Quantity` (de type `long` ).

```kusto
.ingest inline into table Purchases <|
Shoes,1000
Wide Shoes,50
"Coats, black",20
"Coats with ""quotes""",5
```



<!--
It is possible to generate inline ingests commands using the Kusto.Data client library. (Note that compression does allow one to embed newlines in quoted fields) 

    Kusto.Data.Common.CslCommandGenerator.GenerateTableIngestPushCommand(tableName, compressed: true, csvData: csvStream);

-->