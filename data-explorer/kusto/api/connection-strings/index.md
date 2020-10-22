---
title: Chaînes de connexion - Azure Data Explorer | Microsoft Docs
description: Cet article décrit les chaînes de connexion Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 02a459af689cf9f18fe129ee73bff7034a92c80a
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342482"
---
# <a name="connection-strings"></a>Chaînes de connexion

Les chaînes de connexion sont couramment utilisées dans les commandes de contrôle Kusto, dans l’API Kusto et parfois, dans les requêtes.
Les chaînes de connexion permettent de décrire et d’interagir avec les ressources extérieures à Kusto, telles que les objets blob du service Stockage Blob Azure et les bases de données Azure SQL Database.

Il existe plusieurs formats de chaîne de connexion :

* Les [chaînes de connexion Kusto](./kusto.md) expliquent comment communiquer avec un point de terminaison du service Kusto.
  Les chaînes de connexion Kusto sont modélisées d’après le [modèle de chaîne de connexion ADO.NET](/dotnet/framework/data/adonet/connection-string-syntax).
* Les [chaînes de connexion de stockage](./storage.md) expliquent comment faire pointer Kusto vers un service de stockage externe, comme Stockage Blob Azure ou Azure Data Lake Storage.
* Chaînes de connexion SQL : utilisées par le [plug-in sql_request](../../query/sqlrequestplugin.md) Kusto pour envoyer des requêtes au service Azure DB, et par la commande [Exporter dans SQL](../../management/data-export/export-data-to-sql.md).  
  Ces chaînes de connexion adhèrent à la spécification des [chaînes de connexion SqlClient](/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings).

> [!NOTE]
> Certaines chaînes de connexion peuvent référencer des principaux de sécurité. Consultez [Principaux et fournisseurs d’identité](../../management/access-control/principals-and-identity-providers.md) pour savoir comment spécifier des principaux de sécurité dans des chaînes de connexion.