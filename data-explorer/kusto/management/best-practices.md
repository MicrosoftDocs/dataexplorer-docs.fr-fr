---
title: Meilleures pratiques pour la conception de schémas - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les meilleures pratiques pour la conception de schémas dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: f9e2d4a2ad50b140d041179736b48a41a424f5c6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522166"
---
# <a name="best-practices-for-schema-design"></a>Meilleures pratiques pour la conception de schémas

Il ya plusieurs "Dos and Don’ts" que vous pouvez suivre pour rendre vos commandes de gestion plus performants et avoir un effet plus léger sur le service.

## <a name="do"></a>À faire

1. Si vous avez besoin de [`.create tables`](create-tables-command.md) créer plusieurs tables, utilisez la commande, au lieu d’émettre de nombreuses `.create table` commandes.
2. Si vous avez besoin de renommer plusieurs tables, [`.rename tables`](rename-table-command.md)faites-le avec un seul appel à , au lieu d’émettre un appel séparé pour chaque paire de tables.
3. Utilisez la `.show` commande la plus basse, au lieu`|`d’appliquer des filtres après un tuyau ( ). Par exemple :
    - Utiliser `.show table T extents` à la place de `.show cluster extents | where TableName == 'T'`
    - Utilisez `.show database DB schema` à la place de `.show schema | where DatabaseName == 'DB'`.
4. Utilisez `.show table T` seulement si vous avez besoin d’obtenir des statistiques réelles sur une seule table. Si vous avez juste besoin de vérifier l’existence de la `.show table T schema as json`table, ou tout simplement obtenir le schéma de la table, utilisez .
5. Lors de la définition du schéma d’une table qui comprendra des valeurs `datetime` d’heure de date, assurez-vous que ces colonnes sont tapées avec le type.
    - Kusto est hautement optimisé pour `datetime` le filtrage sur les colonnes. Ne pas `string` convertir ou numérique (p. `long` `datetime` ex. ) colonnes à l’heure de la requête pour le filtrage, si cela peut être fait avant ou pendant le temps d’ingestion.

## <a name="dont"></a>À ne pas faire

1. Ne exécutez `.show` pas les commandes trop `.show schema`fréquemment `.show databases` `.show tables`(p. ex. , , ). Dans la mesure du possible - cachez les informations qu’ils retournent.
2. Ne pas `.show schema` exécuter la commande sur un cluster qui est un grand schéma (par exemple avec plus de 100 bases de données). Au lieu [`.show databases schema`](../management/show-schema-database.md)de cela, l’utilisation .
3. N’exécutez pas trop fréquemment les opérations [de commande-puis-requête.](index.md#combining-queries-and-control-commands)
    - *commande-alors-requête*: signifie tuyauter l’ensemble de résultat de la commande de contrôle et appliquer des filtres/agrégations sur elle.
        - Par exemple : `.show ... | where ... | summarize ...`
    - Lors de l’exécution de quelque chose comme: `.show cluster extents | count` (accent sur le ), Kusto prépare d’abord `| count`une table de données tenant tous les détails sur toutes les étendues dans le cluster, puis envoie cette table en mémoire seulement au moteur Kusto afin de faire le compte. Cela signifie que Kusto travaille en fait très dur dans un chemin non optimisé pour vous redonner une réponse si triviale.
4. Utilisation excessive des balises d’étendue dans le cadre de l’ingestion de données. Surtout lors `drop-by:` de l’utilisation de balises, qui limitent la capacité du système à effectuer des processus de toilettage axés sur les performances en arrière-plan.
    - Voir les notes de performance [ici](../management/extents-overview.md#extent-tagging).
    