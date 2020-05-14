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
ms.openlocfilehash: 1c3ce0c0d383d07375333b08de336503d1578b1a
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83381993"
---
# <a name="streaming-ingestion-policy-management"></a>Gestion de la stratégie d’ingestion de streaming

La stratégie d’ingestion de streaming peut être attachée à une base de données ou à une table pour permettre l’ingestion de la diffusion en continu vers ces emplacements. La stratégie définit également les magasins de lignes utilisés pour l’ingestion de diffusion en continu.

Pour plus d’informations sur l’ingestion de la diffusion en continu, voir ingestion de [diffusion en continu (](../../ingest-data-streaming.md)préversion). Pour en savoir plus sur la stratégie d’ingestion de diffusion en continu, consultez Stratégie d’ingestion de [streaming](streamingingestionpolicy.md).

## <a name="show-policy-streamingingestion"></a>. afficher la stratégie streamingingestion

La `.show policy streamingingestion` commande affiche la stratégie d’ingestion de diffusion en continu de la base de données ou de la table.

**Syntaxe**

`.show``database` `policy` `streamingingestion` 
 MyDatabase `.show` `table`MyTable `policy``streamingingestion`

**Retourne**

Cette commande retourne une table avec les colonnes suivantes :

|Colonne    |Type    |Description
|---|---|---
|PolicyName|`string`|Nom de la stratégie-StreamingIngestionPolicy
|EntityName|`string`|Nom de la base de données ou de la table
|Stratégie    |`string`|Objet JSON qui définit la stratégie d’ingestion de diffusion en continu, mise en forme en tant qu' [objet de stratégie](#streaming-ingestion-policy-object) d’ingestion de streaming

**Exemple**

```kusto
.show database DB1 policy streamingingestion 
.show table T1 policy streamingingestion 
```

|PolicyName|EntityName|Stratégie|ChildEntities|EntityType|
|---|---|---|---|---|
|StreamingIngestionPolicy|DB1|{"NumberOfRowStores" : 4}

### <a name="streaming-ingestion-policy-object"></a>Objet de stratégie d’ingestion de streaming

|Propriété  |Type    |Description                                                       |
|----------|--------|------------------------------------------------------------------|
|NumberOfRowStores |`int`  |Nombre de magasins de lignes attribués à l’entité|
|SealIntervalLimit|`TimeSpan?`|Limite facultative pour les intervalles entre les opérations de scellement sur la table. La plage valide est comprise entre 1 et 24 heures. Par défaut : 24 heures.|
|SealThresholdBytes|`int?`|Limite facultative pour la quantité de données à prendre pour une seule opération d’étanchéité sur la table. La plage valide pour la valeur est comprise entre 10 et 200 Mo. Valeur par défaut : 200 Mo.|

## <a name="alter-policy-streamingingestion"></a>. Alter Policy streamingingestion

La `.alter policy streamingingestion` commande définit la stratégie streamingingestion de la base de données ou de la table.

**Syntaxe**

* `.alter``database` `policy` MyDatabase `streamingingestion` *StreamingIngestionPolicyObject*

* `.alter``database` `policy` MyDatabase `streamingingestion``enable`

* `.alter``database` `policy` MyDatabase `streamingingestion``disable`

* `.alter``table` `policy` MyTable `streamingingestion` *StreamingIngestionPolicyObject*

* `.alter``table` `policy` MyTable `streamingingestion``enable`

* `.alter``table` `policy` MyTable `streamingingestion``disable`

**Remarques**

1. *StreamingIngestionPolicyObject* est un objet JSON pour lequel l’objet de stratégie d’ingestion de streaming est défini.

2. `enable`-définir la stratégie d’ingestion de streaming sur 4 rowstores si la stratégie n’est pas définie ou si elle est déjà définie avec 0 rowstores, sinon la commande ne fait rien.

3. `disable`-définir la stratégie d’ingestion de streaming avec 0 rowstores si la stratégie est déjà définie avec un rowstores positif, sinon la commande ne fait rien.

**Exemple**

```kusto
.alter database MyDatabase policy streamingingestion '{  "NumberOfRowStores": 4}'

.alter table MyTable policy streamingingestion '{  "NumberOfRowStores": 4}'
```

## <a name="delete-policy-streamingingestion"></a>. supprimer la stratégie streamingingestion

La `.delete policy streamingingestion` commande supprime la stratégie streamingingestion de la base de données ou de la table.

**Syntaxe** 

`.delete``database`MyDatabase `policy``streamingingestion`

`.delete``table`MyTable `policy``streamingingestion`

**Retourne**

La commande supprime l’objet de stratégie de la table ou de la base de données streamingingestion, puis retourne la sortie de la commande correspondante [. afficher la stratégie streamingingestion](#show-policy-streamingingestion) .

**Exemple**

```kusto
.delete database MyDatabase policy streamingingestion 
```