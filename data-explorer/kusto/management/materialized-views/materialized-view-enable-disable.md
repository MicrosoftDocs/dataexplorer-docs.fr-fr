---
title: Activer et désactiver les commandes de vue matérialisée-Azure Explorateur de données
description: Cet article explique comment activer ou désactiver les commandes de vue matérialisée dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 1be8f66300b2d605c4004661c68e64f741437dc1
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057132"
---
# <a name="disable--enable-materialized-view"></a>. Disable |. activer la vue matérialisée

Une vue matérialisée peut être désactivée de l’une des manières suivantes :

* **Désactivation automatique par le système :**  La vue matérialisée est automatiquement désactivée si la matérialisation échoue avec une erreur permanente. Ce processus peut se produire dans les cas suivants : 
    * Modifications de schéma qui ne sont pas cohérentes avec la définition de la vue.  
    * Les modifications apportées à la table source qui entraînent la non-sémantique sémantique de la requête de vue matérialisée. <br>
    Pour plus d’informations, consultez [créer des vues matérialisées](materialized-view-create.md).
* **Désactivez explicitement la vue matérialisée :**  Si la vue matérialisée a un impact négatif sur l’intégrité du cluster (par exemple, si vous consommez trop d’UC), désactivez la vue à l’aide de la [commande](#syntax) ci-dessous.

> [!NOTE]
> * Quand une vue matérialisée est désactivée, la matérialisation est suspendue et ne consomme pas les ressources du cluster. L’interrogation de la vue matérialisée est possible même si elle est désactivée, mais les performances peuvent être médiocres. Les performances d’une vue matérialisée désactivée dépendent du nombre d’enregistrements qui ont été ingérés dans la table source depuis qu’elle a été désactivée. 
> * Vous pouvez activer une vue matérialisée qui a été précédemment désactivée. Lorsqu’elle est réactivée, la vue matérialisée continue de matérialiser à partir du point où elle s’était arrêtée, et aucun enregistrement ne sera ignoré. Si la vue a été désactivée pendant longtemps, le temps de rattrapage peut prendre beaucoup de temps.

La désactivation d’une vue est recommandée uniquement si vous soupçonnez que la vue a un impact sur l’intégrité de votre cluster.

## <a name="syntax"></a>Syntaxe

`.enable` | `disable``materialized-view` *MaterializedViewName*

## <a name="properties"></a>Propriétés

|Propriété|Type|Description
|----------------|-------|---|
|MaterializedViewName|Chaîne|Nom de la vue matérialisée.|

## <a name="example"></a>Exemple

```kusto
.enable materialized-view ViewName

.disable materialized-view ViewName
```