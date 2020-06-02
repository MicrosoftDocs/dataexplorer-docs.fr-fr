---
title: Commande inline de réception (push)-Azure Explorateur de données
description: Cet article décrit la commande Inline. deréception (push).
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 35098d2605a637832fd513da62a956382183a47c
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294540"
---
# <a name="ingest-inline-command-push"></a>. réception de la commande inline (push)

Cette commande ingère les données dans une table en « poussant » les données incorporées inline dans le texte de la commande.

> [!NOTE]
> Cette commande est utilisée pour les tests manuels ad hoc.
> Pour une utilisation en production, nous vous recommandons d’utiliser d’autres méthodes d’ingestion plus adaptées à la distribution en bloc d’énormes quantités de données, telles que l’ingestion [du stockage](./ingest-from-storage.md).

**Syntaxe**

`.ingest``inline` `into` `table` *TableName* [ `with` `(` *IngestionPropertyName* `=` *IngestionPropertyValue* [ `,` ...] `)` ] `<|` *Données*

**Arguments**

* *TableName* est le nom de la table dans laquelle les données sont ingérées.
  Le nom est toujours lié à la base de données en contexte.
  Le schéma de table est le schéma qui sera utilisé pour les données si aucun objet de mappage de schéma n’est fourni.

* *Data* est le contenu de données à ingérer. Sauf modification contraire des propriétés d’ingestion, ce contenu est analysé en tant que CSV.
 
 > [!NOTE]
 > Contrairement à la plupart des commandes et des requêtes de contrôle, le texte de la partie *données* de la commande ne doit pas nécessairement respecter les conventions syntaxiques de la langue. Par exemple, les caractères d’espace blanc sont importants ou la `//` combinaison n’est pas traitée comme un commentaire.

* *IngestionPropertyName*, *IngestionPropertyValue*: nombre quelconque de [Propriétés](../../../ingestion-properties.md) d’ingestion qui affectent le processus d’ingestion.

**Résultats**

Le résultat est une table avec autant d’enregistrements que le nombre de données générées partitions (« extents »).
Si aucun partitions de données n’est généré, un seul enregistrement est retourné avec un ID d’extension vide (de valeur zéro).

|Nom       |Type      |Description                                                               |
|-----------|----------|--------------------------------------------------------------------------|
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
You can generate inline ingests commands using the Kusto.Data client library. 
Compression lets you embed new lines in quoted fields.

    Kusto.Data.Common.CslCommandGenerator.GenerateTableIngestPushCommand(tableName, compressed: true, csvData: csvStream);

-->
