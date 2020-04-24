---
title: La commande en ligne .ingest (push) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la commande en ligne .ingest (push) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 47da2df6ff974afb5e91224ade695dc0863b6817
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521367"
---
# <a name="the-ingest-inline-command-push"></a>La commande en ligne .ingest (push)

Cette commande ingèrent des données dans un tableau en « poussant » les données qui sont intégrées en ligne dans le texte de commande lui-même.

> [!NOTE]
> L’objectif principal de cette commande est à des fins de test ad hoc manuel.
> Pour l’utilisation de la production, il est recommandé d’utiliser d’autres méthodes d’ingestion plus appropriées pour la livraison en vrac d’énormes quantités de données, telles que [l’ingérer du stockage](./ingest-from-storage.md).

**Syntaxe**

`.ingest``inline` `with` `(` `=` `,` *TableName* *IngestionPropertyName* *IngestionPropertyValue* TableName [ IngestionPropertyName IngestionPropertyValue [ ...] `into` `table` `)`] `<|` *Données*



**Arguments**

* *TableName* est le nom de la table pour ingérer les données.
  Le nom de table est toujours relatif à la base de données dans son contexte, et son schéma est le schéma qui sera supposé pour les données si aucun objet de cartographie schématique n’est fourni.

* *Les données* sont le contenu des données à ingérer. Sauf modification contraire par les propriétés d’ingestion, ce contenu est analysé sous le titre CSV.
  Notez que contrairement à la plupart des commandes et requêtes de contrôle, le texte de la partie *Données* de la commande n’a pas à suivre les conventions syntaxiques de la langue (p. ex., les caractères de l’espace blanc sont importants, la `//` combinaison n’est pas traitée comme un commentaire, etc.)

* *IngestionPropertyName*, *IngestionPropertyValue*: Tout nombre de [propriétés d’ingestion](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) qui affectent le processus d’ingestion.

**Résultats**

Le résultat de la commande est un tableau avec autant d’enregistrements qu’il y a des éclats de données («étendues») générés par la commande.
Si aucun éclat de données n’a été généré, un seul enregistrement est retourné avec un ID vide (valeur nulle).

|Nom       |Type      |Description                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|ExtentId (extentId)   |`guid`    |L’identifiant unique pour le fragment de données qui a été généré par la commande.|

**Exemples**

La commande suivante ingère les`Purchases`données dans un `SKU` tableau `string`( `Quantity` ) avec `long`deux colonnes, (de type ) et (de type ).

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