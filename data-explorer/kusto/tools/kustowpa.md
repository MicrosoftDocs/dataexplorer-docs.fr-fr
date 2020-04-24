---
title: Kusto WPA - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Kusto WPA dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 2d8c5a9b11d8045897d8a9bd798e40ceb0f48b6a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503296"
---
# <a name="kusto-wpa"></a>Kusto WPA

Kusto WPA ajoute la possibilité d’exécuter et de visualiser les résultats de requête Kusto dans Windows Performance Analyzer (WPA). Il se compose de deux parties principales:

1. Un outil de lanceur qui peut accepter les requêtes `Share` &gt; `Query to Clipboard`partagées par Kusto.Explorer (via ) et générer des fichiers de requêtes (`.kpq`) à traiter par WPA.

1. Un plugin WPA qui peut "ouvrir"`.kpq`les fichiers de requête générés ( ). L’ouverture d’un tel fichier envoie automatiquement la requête spécifiée dans le fichier à un cluster Kusto et le chargement des résultats comme s’ils provenaient d’un fichier ETL "standard".

Voirhttps://aka.ms/kustowpa[ ] pour plus de détails et télécharger des informations.