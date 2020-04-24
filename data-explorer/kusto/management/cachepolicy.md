---
title: Politique cache (cache chaude et froide) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique de Cache (cache chaude et froide) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 591763ac5d94a8361a4b78c1b199bb05299cc004
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522132"
---
# <a name="cache-policy-hot-and-cold-cache"></a>Politique de cache (cache chaude et froide)

Azure Data Explorer stocke ses données ingérées dans un stockage fiable (le plus souvent Azure Blob Storage), loin de ses nœuds de traitement (par exemple Azure Compute). Pour accélérer les requêtes sur ces données, Azure Data Explorer cache ces données (ou certaines parties de celle-ci) sur ses nœuds de traitement, SSD, ou même dans la RAM. Azure Data Explorer comprend un mécanisme de cache sophistiqué conçu pour décider intelligemment quels objets de données à cacher. Le cache permet à Azure Data Explorer de décrire les artefacts de données qu’il utilise (comme les index de colonne et les éclats de données de colonne) afin que des données plus « importantes » puissent être prioritaires.

Bien que les meilleures performances de requête soient obtenues lorsque toutes les données ingérées sont mises en cache, souvent certaines données ne justifient pas le coût de le garder « au chaud » dans le stockage SSD local.
Par exemple, de nombreuses équipes considèrent que les registres de journaux plus anciens rarement consultés sont de moindre importance.
Ils préféreraient avoir réduit les performances lors de la requête de ces données, plutôt que de payer pour les garder au chaud tout le temps.

Le cache Azure Data Explorer fournit une **stratégie de cache** granulaire que les clients peuvent utiliser pour différencier entre deux stratégies de cache de données : le cache de données **chaudes** et le cache de données **à froid**. Azure Data Explorer cache tente de conserver toutes les données qui tombent dans le cache de données chaudes dans SSD local (ou RAM), jusqu’à la taille définie du cache de données chaudes. L’espace SSD local restant sera utilisé pour contenir des données qui ne sont pas classées comme chaudes. Une implication utile de cette conception est que les requêtes qui chargent beaucoup de données froides à partir de stockage fiable n’expulsera pas les données du cache de données chaudes. Par conséquent, il n’y aura pas d’impact majeur sur les requêtes impliquant les données dans le cache de données chaudes.

Les principales implications de l’établissement de la politique de cache chaude sont les principales:
* **Coût** Le coût d’un stockage fiable peut être considérablement inférieur à celui du SSD local (par exemple, en Azure, il est actuellement environ 45 fois moins cher).
* **Performance** Les données peuvent être interrogées plus rapidement lorsqu’elles sont dans le SSD local. Cela est particulièrement vrai pour les requêtes à distance, c’est-à-dire les requêtes qui analysent de grandes quantités de données.  

[Les commandes de contrôle](cache-policy.md) permettent aux administrateurs de gérer la stratégie de cache.

## <a name="how-the-cache-policy-gets-applied"></a>Comment la politique de cache est appliquée

Lorsque les données sont ingérées dans Azure Data Explorer, le système garde une trace de la date/heure à laquelle l’ingestion a été effectuée et l’étendue a été créée. La date d’ingestion/valeur de l’heure de l’étendue (ou la valeur maximale, si une étendue a été construite à partir de plusieurs étendues préexistantes) est utilisée pour évaluer la stratégie de cache.

Par défaut, la `null`stratégie efficace est , ce qui signifie que toutes les données sont considérées comme **chaudes**.
Une politique`null` non au niveau de la table l’emporte sur une politique au niveau de la base de données.

> [!Note] 
> Vous pouvez spécifier une valeur pour la date/heure `creationTime`d’ingestion en utilisant la propriété d’ingestion . 

## <a name="scoping-queries-to-hot-cache"></a>Scoping requêtes à cache chaude

Kusto prend en charge les requêtes qui sont étendues vers le bas aux données de cache chaude seulement. Pour ce faire, plusieurs méthodes sont possibles :

- Ajouter une propriété `query_datascope` de demande de client `default` `all`appelée `hotcache`à la requête valeurs possibles: , , et .
- Utilisez `set` une déclaration dans le `set query_datascope='...'`texte de la requête: , Les valeurs possibles sont les mêmes que pour la demande de propriété du client.
- Ajouter `datascope=...` un texte immédiatement après une référence de table dans le corps de requête. Les valeurs possibles sont `all` et `hotcache`.

La `default` valeur indique l’utilisation des paramètres par défaut du cluster, qui déterminent que la requête doit couvrir toutes les données.



S’il y a un écart `set` entre les différentes méthodes : prime sur la propriété de demande du client, et la spécification d’une valeur pour une référence de table prime sur les deux.

Par exemple, dans la requête suivante, toutes les références de table n’utiliseront que des données hotcache, à l’exception de la deuxième référence à `T` laquelle sont étendues à toutes les données :

```kusto
set query_datascope="hotcache";
T | union U | join (T datascope=all | where Timestamp < ago(365d) on X
```

## <a name="cache-policy-vs-retention-policy"></a>Politique de cache vs politique de conservation

La politique de Cache est indépendante de la politique de [rétention](./retentionpolicy.md): 
- La politique de Cache définit comment prioriser les ressources afin que les requêtes sur des données importantes soient plus rapides et résistantes à l’impact des requêtes sur des données moins importantes. 
- La politique de conservation définit l’étendue des données requêtes `SoftDeletePeriod`dans un tableau/base de données (en particulier, ).

Il est recommandé de configurer cette stratégie pour atteindre l’équilibre optimal entre le coût et les performances en fonction du modèle de requête attendu.

Exemple :
* `SoftDeletePeriod`56d
* `hot cache policy`28d

Dans l’exemple, les 28 derniers jours de données seront sur le cluster SSD et les 28 jours **supplémentaires** seront stockés dans le stockage de blob Azure. Vous pouvez exécuter des requêtes sur les 56 jours complets de données. 

## <a name="cache-policy-does-not-make-kusto-a-cold-storage-technology"></a>La politique de cache ne fait pas de Kusto une technologie de stockage frigorifique

Azure Data Explorer est conçu pour effectuer des requêtes ad hoc, avec des ensembles de résultats intermédiaires qui adaptent la RAM totale du cluster. Pour les grands emplois, comme la carte-réduction, où vous souhaitez stocker des résultats intermédiaires dans un stockage persistant (comme un SSD) utiliser la fonction d’exportation continue. Cela vous permet de faire des requêtes par lots de longue durée à l’aide de services comme HDInsight ou Azure Databricks.