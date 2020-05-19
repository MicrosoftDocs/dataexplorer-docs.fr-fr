---
title: Prise en charge de MS-TDS T-SQL - Azure Data Explorer
description: Cet article présente la prise en charge de MS-TDS T-SQL dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/06/2019
ms.openlocfilehash: a128db995c78c0583bc7c7712c06292a2f6598d1
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550535"
---
# <a name="ms-tds-t-sql-support"></a>Prise en charge de MS-TDS T-SQL

Azure Data Explorer (Kusto) prend en charge une partie du protocole de communication Microsoft SQL Server (MS-TDS) avec une partie du langage de requête T-SQL. Microsoft Excel et Microsoft Power BI font partie des nombreux outils pouvant fonctionner avec Azure Data Explorer (Kusto). Ces applications Microsoft savent également comment interroger SQL Server.

> [!NOTE]
> Utilisez l’authentification intégrée Azure Active Directory (Azure AD) comme outil client pour interroger Kusto via MS-TDS.

## <a name="next-steps"></a>Étapes suivantes

* [T-SQL](./t-sql.md) - Découvrez le langage de requête T-SQL tel qu’il est implémenté par Kusto. 

* [KQL sur TDS](./tdskql.md) - Découvrez l’exécution de requêtes KQL natives via des points de terminaison TDS.

* [Clients MS-TDS et Kusto](./clients.md) - Utilisez Azure Data Explorer à partir de clients connus qui se servent de MS-TDS/T-SQL.

* [Azure Data Explorer (Kusto) comme serveur lié à un serveur SQL](./linkedserver.md) - Configurez le cluster en tant que serveur lié à un serveur SQL local. 

* [MS-TDS avec Azure Active Directory](./aad.md) - Utilisez Azure AD via TDS pour vous connecter à Azure Data Explorer.

* [Problèmes connus SQL](./sqlknownissues.md) - Découvrez les principales différences entre l’implémentation de T-SQL par SQL Server et Azure Data Explorer.
