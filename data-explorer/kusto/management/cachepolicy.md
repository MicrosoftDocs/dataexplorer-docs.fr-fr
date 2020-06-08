---
title: Stratégie de cache (Hot et Cold cache)-Azure Explorateur de données
description: Cet article décrit la stratégie de cache (Hot et Cold cache) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 130526b41030ac3936236f8fd8bba81f20b4bb0e
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512518"
---
# <a name="cache-policy-hot-and-cold-cache"></a>Stratégie de cache (Hot et Cold cache) 

Azure Explorateur de données stocke ses données ingérées dans un stockage fiable (le plus souvent, le stockage d’objets BLOB Azure), en dehors de son traitement réel (par exemple, les nœuds Azure Compute). Pour accélérer les requêtes sur ces données, Azure Explorateur de données les met en cache, ou en partie, sur ses nœuds de traitement, SSD ou même en RAM. Azure Explorateur de données comprend un mécanisme de cache sophistiqué conçu pour déterminer intelligemment les objets de données à mettre en cache. Le cache permet à Azure Explorateur de données de décrire les artefacts de données qu’il utilise, afin que les données plus importantes puissent être prioritaires. Par exemple, les index de colonne et les données de colonne partitions,

Les meilleures performances de requête sont obtenues lorsque toutes les données ingérées sont mises en cache. Parfois, certaines données ne justifient pas le coût de la conservation « chaude » dans le stockage SSD local.
Par exemple, de nombreuses équipes considèrent que les enregistrements de journal les plus rarement consultés sont d’une importance moindre.
Ils préfèrent avoir des performances réduites lors de l’interrogation de ces données, plutôt que de payer pour rester chaud tout le temps.

Azure Explorateur de données cache fournit une **stratégie de cache** granulaire que les clients peuvent utiliser pour différencier : le cache de **données à chaud** et le cache de **données à froid**. Le cache Explorateur de données Azure tente de conserver toutes les données qui se trouvent dans la catégorie cache de données à chaud, en disque SSD local (ou RAM), jusqu’à la taille définie du cache de données à chaud. L’espace de disque SSD local restant sera utilisé pour stocker les données qui ne sont pas classées comme étant chaudes. Une implication utile de cette conception est que les requêtes qui chargent beaucoup de données froides à partir d’un stockage fiable ne suppriment pas les données du cache de données à chaud. En conséquence, il n’y aura pas d’impact majeur sur les requêtes impliquant les données dans le cache de données à chaud.

Les principales implications de la définition de la stratégie de mise en cache à chaud sont les suivantes :
* **Coût**: le coût du stockage fiable peut être considérablement inférieur à celui du disque SSD local. Elle est actuellement d’environ 45 fois moins cher dans Azure.
* **Performances**: les données sont interrogées plus rapidement lorsqu’elles se trouvent dans le disque SSD local, en particulier pour les requêtes de plage qui analysent de grandes quantités de données.  

Utilisez la [commande de stratégie de cache](cache-policy.md) pour gérer la stratégie de cache.

> [!TIP]
>Azure Explorateur de données est conçu pour les requêtes ad hoc avec des jeux de résultats intermédiaires qui ajustent la RAM totale du cluster.
>Pour les travaux de grande taille, tels que Map-Reduce, où vous souhaitez stocker les résultats intermédiaires dans un stockage persistant, tel qu’un SSD, utilisez la fonctionnalité d’exportation continue. Cette fonctionnalité vous permet d’exécuter des requêtes par lots de longue durée à l’aide de services tels que HDInsight ou Azure Databricks.
 
## <a name="how-cache-policy-is-applied"></a>Application de la stratégie de cache

Lorsque les données sont ingérées dans Azure Explorateur de données, le système effectue le suivi de la date et de l’heure de l’ingestion et de l’étendue qui a été créée. La valeur de date et d’heure d’ingestion de l’étendue (ou valeur maximale, si une extension a été créée à partir de plusieurs extensions préexistantes), est utilisée pour évaluer la stratégie de cache.

> [!Note]
> Vous pouvez spécifier une valeur pour la date et l’heure d’ingestion à l’aide de la propriété ingestion `creationTime` .

Par défaut, la stratégie effective est `null` , ce qui signifie que toutes les données sont considérées comme étant **chaudes**.
Une stratégie non `null` au niveau de la table remplace une stratégie au niveau de la base de données.

## <a name="scoping-queries-to-hot-cache"></a>Étendue des requêtes au cache à chaud

Kusto prend en charge les requêtes dont la portée est limitée aux données de cache à chaud uniquement.
Il existe plusieurs possibilités de requête.

- Ajoutez une propriété de demande cliente appelée `query_datascope` à la requête.
   Valeurs possibles : `default` , `all` et `hotcache` .
- Utilisez une `set` instruction dans le texte de la requête : `set query_datascope='...'` .
   Les valeurs possibles sont les mêmes que pour la propriété demande du client.
- Ajoutez un `datascope=...` texte immédiatement après une référence de table dans le corps de la requête. 
   Les valeurs possibles sont `all` et `hotcache`.

La `default` valeur indique l’utilisation des paramètres par défaut du cluster, qui déterminent que la requête doit couvrir toutes les données.

S’il existe une différence entre les différentes méthodes, `set` est prioritaire sur la propriété de demande du client. La spécification d’une valeur pour une référence de table est prioritaire sur les deux.

Par exemple, dans la requête suivante, toutes les références de table utilisent uniquement les données de cache à chaud, à l’exception de la deuxième référence à « T », dont la portée est limitée à toutes les données :

```kusto
set query_datascope="hotcache";
T | union U | join (T datascope=all | where Timestamp < ago(365d) on X
```

## <a name="cache-policy-vs-retention-policy"></a>Stratégie de cache et stratégie de rétention

La stratégie de cache est indépendante de la [stratégie de rétention](./retentionpolicy.md): 
- La stratégie de cache définit comment hiérarchiser les ressources. Les requêtes sur des données importantes seront plus rapides et résistantes à l’impact des requêtes sur des données moins importantes.
- La stratégie de rétention définit l’étendue des données interrogeables dans une table ou une base de données (en particulier, `SoftDeletePeriod` ).

Configurez cette stratégie pour obtenir un équilibre optimal entre les coûts et les performances, en fonction du modèle de requête attendu.

Exemple :
* `SoftDeletePeriod`= 56D
* `hot cache policy`= 28D

Dans l’exemple, les 28 derniers jours de données seront sur le disque SSD de cluster et les 28 jours de données supplémentaires seront stockés dans le stockage d’objets BLOB Azure.
Vous pouvez exécuter des requêtes sur l’intégralité de 56 jours de données.
 
