---
title: Meilleures pratiques pour la gestion de schéma-Azure Explorateur de données
description: Cet article décrit les meilleures pratiques pour la gestion des schémas dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: ba0d931fcca257cb3b5658354dfb8bf70dc6848f
ms.sourcegitcommit: c6cb2b1071048daa872e2fe5a1ac7024762c180e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96774620"
---
# <a name="best-practices-for-schema-management"></a>Meilleures pratiques pour la gestion des schémas

Voici plusieurs bonnes pratiques à suivre. Ils vous aideront à améliorer le fonctionnement de vos commandes de gestion et à avoir un impact plus clair sur les ressources du service.

|Action  |Utilisation  |N’utilisez pas | Notes |
|---------|---------|---------|----
| **Créer plusieurs tables**    |  Utiliser une seule [`.create tables`](create-tables-command.md) commande       | N’émettez pas de nombreuses `.create table` commandes        | |
| **Renommer plusieurs tables**    | Effectuer un appel unique à [`.rename tables`](rename-table-command.md)        |  N’émettez pas d’appel séparé pour chaque paire de tables   |    |
|**Afficher les commandes**   |   Utiliser la commande dont l’étendue est la plus faible `.show` |   Ne pas appliquer de filtres après un canal ( `|` )   </ul></li>  | Limitez l’utilisation autant que possible. Dans la mesure du possible, mettez en cache les informations qu’elles renvoient. |
| Afficher les étendues  | Utilisez `.show table T extents`.   |N’utilisez pas `.show cluster extents | where TableName == 'T'`  |
|  Affiche le schéma de la base de données. |Utilisez `.show database DB schema`.  |  N’utilisez pas `.show schema | where DatabaseName == 'DB'` |
| **Afficher le schéma sur un cluster qui est un schéma volumineux** <br> |Faites [`.show databases schema`](../management/show-schema-database.md) |N’utilisez pas `.show schema`| Par exemple, utilisez sur un cluster avec plus de 100 bases de données.
| **Vérifier l’existence d’une table ou obtenir le schéma de la table**|Utilisez `.show table T schema as json`.|N’utilisez pas `.show table T` |Utilisez cette commande uniquement pour obtenir des statistiques réelles sur une seule table.|
| **Définir le schéma d’une table qui comprendra des `datetime` valeurs**  |Définissez les colonnes appropriées sur le `datetime` type | Ne convertit pas `string` les colonnes numériques en `datetime` au moment de la requête pour le filtrage, si cette opération peut être effectuée avant ou pendant l’heure d’ingestion|
| **Ajouter une balise d’étendue aux métadonnées** |Utilisation avec modération |Évitez `drop-by:` les balises, qui limitent la capacité du système à effectuer des processus de nettoyage orientés performances en arrière-plan.|  <br> Consultez les remarques sur les [performances](../management/extents-overview.md#extent-tagging). |
