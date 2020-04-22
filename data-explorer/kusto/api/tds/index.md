---
title: MS-TDS (prise en charge de T-SQL) - Azure Data Explorer | Microsoft Docs
description: Cet article décrit MS-TDS (prise en charge de T-SQL) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/06/2019
ms.openlocfilehash: 8aaea26b6c4e8a7f76c4129faeb681791f25ae7e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490546"
---
# <a name="ms-tds-t-sql-support"></a>MS-TDS (prise en charge de T-SQL)

Kusto prend en charge un sous-ensemble du protocole de communication Microsoft SQL Server (MS-TDS), avec un sous-ensemble du langage de requête T-SQL, afin que les outils existants qui savent comment interroger SQL Server puissent fonctionner avec Kusto. Parmi les clients pris en charge, citons notamment Microsoft Excel, Microsoft Power BI.

Notez que pour qu’un outil client interroge Kusto par le biais de MS-TDS, le client doit utiliser l’authentification intégrée Azure Active Directory.

Pour plus d’informations sur le langage de requête T-SQL tel qu’il est implémenté par Kusto, consultez [T-SQL](./t-sql.md). 

Consultez [Clients MS-TDS et Kusto](./clients.md) pour obtenir des exemples d’utilisation de Kusto à partir de clients connus qui utilisent MS-TDS/T-SQL.

Consultez [Kusto en tant que serveur lié à un serveur SQL](./linkedserver.md) pour plus d’informations sur la configuration d’un cluster Kusto en tant que serveur lié à un serveur SQL local.

Consultez [MS-TDS avec Azure Active Directory](./aad.md) pour plus d’informations sur l’utilisation d’AAD par le biais de TDS pour la connexion à Kusto.

Consultez [KQL sur TDS](./tdskql.md) pour plus d’informations sur l’exécution de requêtes KQL natives par le biais d’un point de terminaison TDS. 

Enfin, consultez [ceci](./sqlknownissues.md) pour comprendre les principales différences entre l’implémentation de T-SQL par SQL Server et Kusto.