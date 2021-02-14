---
title: Vue d’ensemble - Azure Data Explorer
description: Cet article présente une vue d’ensemble d’Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/07/2019
ms.localizationpriority: high
adobe-target: true
ms.openlocfilehash: d0f71416410812b8160354781111f4a6f46350fd
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359690"
---
# <a name="overview"></a>Vue d’ensemble

Une requête Kusto est une demande en lecture seule de traitement de données et de retour de résultats.
La demande est formulée en texte brut, à l’aide d’un modèle de flux de données conçu pour faciliter la lecture, la création et l’automatisation de la syntaxe. La requête utilise des entités de schéma organisées dans une hiérarchie similaire à celle de SQL : bases de données, tables et colonnes.

La requête se compose d’une séquence d’instructions de requête, délimitées par un point-virgule (`;`), une instruction au moins étant une [instruction d’expression tabulaire](tabularexpressionstatements.md), qui est une instruction qui génère des données organisées dans un maillage sous forme de tableau de colonnes et de lignes. Les instructions d’expression tabulaire de la requête produisent les résultats de la requête.

La syntaxe de l’instruction d’expression tabulaire comporte un flux de données tabulaires d’un opérateur de requête tabulaire vers un autre, qui commence par la source de données (par exemple, une table dans une base de données ou un opérateur qui génère des données), puis transite par un ensemble d’opérateurs de transformation des données qui sont liés à l’aide de la barre verticale (`|`).

Par exemple, la requête Kusto suivante a une seule instruction, qui est une instruction d’expression tabulaire. L’instruction commence par une référence à une table appelée `StormEvents` (la base de données qui héberge cette table est ici implicite et fait partie des informations de connexion). Les données (lignes) pour cette table sont ensuite filtrées par la valeur de la colonne `StartTime`, puis par la valeur de la colonne `State`. La requête retourne ensuite le nombre de lignes restantes.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count 
```

Pour exécuter cette requête, [cliquez ici](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAwsuyS/KdS1LzSspVuDlqlEoz0gtSlUILkksKgnJzE1VsLNVSEksSS0BsjWMDAzMdQ0NdQ0MNRUS81KQVNmgKzICKUIxryRVwdZWQcnNxz/I08VRSQFsW3J+aV6JAgAwMx4+hAAAAA==).
Dans cet exemple, le résultat est le suivant :

|Count|
|-----|
|   23|
