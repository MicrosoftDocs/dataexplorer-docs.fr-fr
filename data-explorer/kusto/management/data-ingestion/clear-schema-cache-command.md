---
title: Effacement du schéma mis en cache pour l’ingestion de diffusion en continu-Azure Explorateur de données
description: Cet article décrit la commande de gestion pour l’effacement du schéma de base de données mis en cache dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/20/2020
ms.openlocfilehash: 23d86e528dfe687b79ce225b16712184a9bfcde4
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84784495"
---
# <a name="clear-schema-cache-for-streaming-ingestion"></a>Effacer le cache de schéma pour l’ingestion de diffusion en continu

Les nœuds de cluster cachent le schéma des bases de données qui reçoivent des données par le biais de l’ingestion de diffusion en continu. Ce processus optimise les performances et l’utilisation des ressources de cluster, mais peut entraîner des retards de propagation lorsque le schéma est modifié.
Effacez le cache pour garantir que les demandes d’ingestion de streaming suivantes incorporent les modifications de schéma de base de données ou de table.
Pour plus d’informations, consultez ingestion de la [diffusion en continu et modifications du schéma](streaming-ingestion-schema-changes.md).

**Effacer le cache de schéma**

La `.clear cache streamingingestion schema` commande vide le schéma mis en cache à partir de tous les nœuds de cluster.

**Syntaxe**

`.clear``table` &lt; &gt; nom `cache` de `streamingingestion` la table`schema`

`.clear` `database` `cache` `streamingingestion` `schema`

**Retourne**

Cette commande retourne une table avec les colonnes suivantes :

|Colonne    |Type    |Description
|---|---|---
|NodeId|`string`|Identificateur du nœud de cluster
|Statut|`string`|Réussite/échec

**Exemple**

```kusto
.clear database cache streamingingestion schema

.show table T1 cache streamingingestion schema
```

|NodeId|Statut|
|---|---|
|Nœud1|Succès
|Nœud2|Échec

> [!NOTE]
> Si la commande échoue ou si l’une des lignes de la table retournée contient *Status = failed* , la commande peut être retentée en toute sécurité.
