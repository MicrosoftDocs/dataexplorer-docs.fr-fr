---
title: Gestion de la stratégie d’ingestion de la diffusion en continu Kusto-Azure Explorateur de données
description: Cet article décrit la gestion de la stratégie d’ingestion de streaming dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 55ed390a1c98a307d2bb38476458f29fc9c92997
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84258009"
---
# <a name="streaming-ingestion-policy-management"></a>Gestion de la stratégie d’ingestion de streaming

La stratégie d’ingestion de streaming peut être définie sur une table pour permettre l’ingestion de la diffusion en continu dans cette table. La stratégie peut également être définie au niveau de la base de données pour appliquer le même paramètre aux tables actuelles et futures.

Pour plus d’informations, consultez ingestion de la [diffusion en continu](../../ingest-data-streaming.md). Pour en savoir plus sur la stratégie d’ingestion de diffusion en continu, consultez Stratégie d’ingestion de [streaming](streamingingestionpolicy.md).

## <a name="display-the-policy"></a>Afficher la stratégie

La `.show policy streamingingestion` commande affiche la stratégie d’ingestion de diffusion en continu de la base de données ou de la table.
 
**Syntaxe**

`.show``{database|table}` &lt; nom &gt; de l' `policy` entité`streamingingestion`

**Retourne**

Cette commande retourne une table avec les colonnes suivantes :

|Colonne    |Type    |Description
|---|---|---
|PolicyName|`string`|Nom de la stratégie-StreamingIngestionPolicy
|EntityName|`string`|Nom de la base de données ou de la table
|Policy    |`string`|[objet de stratégie d’ingestion de streaming](#streaming-ingestion-policy-object)

**Exemples**

```kusto
.show database DB1 policy streamingingestion

.show table T1 policy streamingingestion
```

|PolicyName|EntityName|Policy|ChildEntities|EntityType|
|---|---|---|---|---|
|StreamingIngestionPolicy|DB1|{"IsEnabled" : true, "HintAllocatedRate" : null}

## <a name="change-the-policy"></a>Modifier la stratégie

La `.alter[-merge] policy streamingingestion` famille de commandes fournit des moyens de modifier la stratégie d’ingestion de diffusion en continu de la base de données ou de la table.

**Syntaxe**

* `.alter``{database|table}` &lt; &gt; nom `policy` de `streamingingestion` l’entité`[enable|disable]`

* `.alter`objet de stratégie d’ingestion de `{database|table}` &lt; diffusion en &gt; `policy` `streamingingestion` &lt; [continu](#streaming-ingestion-policy-object) de nom d’entité&gt;

* `.alter-merge`objet de stratégie d’ingestion de `{database|table}` &lt; diffusion en &gt; `policy` `streamingingestion` &lt; [continu](#streaming-ingestion-policy-object) de nom d’entité&gt;

> [!Note]
>
> * Permet de modifier l’état activé/désactivé de l’ingestion de diffusion en continu sans modifier les autres propriétés de la stratégie ou définir les valeurs par défaut des propriétés si la stratégie n’a pas été définie précédemment sur l’entité.
>
> * Permet de remplacer l’intégralité de la stratégie d’ingestion de diffusion en continu sur l’entité. l' [objet de stratégie](#streaming-ingestion-policy-object) d’ingestion de streaming doit inclure toutes les propriétés obligatoires.
>
> * Permet de remplacer uniquement les propriétés spécifiées de la stratégie d’ingestion de streaming sur l’entité. L' [objet de stratégie](#streaming-ingestion-policy-object) d’ingestion de streaming peut inclure une partie ou aucune des propriétés obligatoires.

**Retourne**

La commande modifie la table ou l’objet de stratégie de base de données `streamingingestion` , puis retourne la sortie [ `.show policy` `streamingingestion` ](#display-the-policy) de la commande correspondante.

**Exemples**

```kusto
.alter database DB1 policy streamingingestion enable

.alter table T1 policy streamingingestion disable

.alter database DB1 policy streamingingestion '{"IsEnabled": true, "HintAllocatedRate": 2.1}'

.alter table T1 streamingingestion '{"IsEnabled": true}'

.alter-merge database DB1 policy streamingingestion '{"IsEnabled": false}'

.alter-merge table T1 policy streamingingestion '{"HintAllocatedRate": 1.5}'
```

## <a name="delete-the-policy"></a>Supprimer la stratégie

La `.delete policy streamingingestion` commande supprime la stratégie streamingingestion de la base de données ou de la table.

**Syntaxe**

`.delete``{database|table}` &lt; nom &gt; de l' `policy` entité`streamingingestion`

**Retourne**

La commande supprime l’objet de stratégie de la table ou de la base de données streamingingestion, puis retourne la sortie de la commande correspondante [. afficher la stratégie streamingingestion](#display-the-policy) .

**Exemples**

```kusto
.delete database DB1 policy streamingingestion

.delete table T1 policy streamingingestion
```

### <a name="streaming-ingestion-policy-object"></a>Objet de stratégie d’ingestion de streaming

Dans l’entrée et la sortie des commandes de gestion, l’objet de stratégie d’ingestion de streaming est une chaîne au format JSON qui comprend les propriétés suivantes.
|Propriété  |Type    |Description                                                       |Obligatoire ou facultatif |
|----------|--------|------------------------------------------------------------------|-------|
|IsEnabled |`bool`  |L’ingestion de diffusion en continu est-elle activée pour l’entité| Obligatoire|
|HintAllocatedRate|`double`|Fréquence estimée des entrées de données en Go/heure| Facultatif|
