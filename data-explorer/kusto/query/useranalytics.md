---
title: Analyse utilisateur - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit User Analytics dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/17/2019
ms.openlocfilehash: 37b5962b9441c3eb7362a239404189e489b1c3ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504979"
---
# <a name="user-analytics"></a>Analyse des utilisateurs

Cette section décrit les étendues kusto (plugins) pour les scénarios d’analyse utilisateur.

|Scénario|Plug-in|Détails|Expérience utilisateur|
|--------|------|--------|-------|
| Compter les nouveaux utilisateurs au fil du temps | [activity_counts_metrics](activity-counts-metrics-plugin.md)|Les retours comptent/dcounts/nouveaux comptes pour chaque fenêtre de temps. Chaque fenêtre de temps est comparée à *toutes les* fenêtres de temps précédentes|Kusto.Explorer: Galerie de rapports|
| Période par rapport à la période : taux de rétention/churn et nouveaux utilisateurs | [activity_metrics](activity-metrics-plugin.md)|Retourne les dcomptes, le taux de rétention/curn pour chaque fois que les fenêtres. Chaque fenêtre de temps est comparée à la fenêtre de temps *précédente*|Kusto.Explorer: Galerie de rapports|
| Les utilisateurs comptent et dcomptent au-dessus de la fenêtre coulissante | [sliding_window_counts](sliding-window-counts-plugin.md)|Pour chaque fenêtre de temps, les retours compte et les dcomptes sur une période de retour, d’une manière coulissante de fenêtre|
| Cohorte de nouveaux utilisateurs : taux de rétention/churn et nouveaux utilisateurs | [new_activity_metrics](new-activity-metrics-plugin.md)|Compare les cohortes de nouveaux utilisateurs (tous les utilisateurs qui ont été vus 1er dans la fenêtre de temps). Chaque cohorte est comparée à toutes les cohortes antérieures. La comparaison tient compte de *toutes les* fenêtres de temps précédentes|Kusto.Explorer: Galerie de rapports|
|Utilisateurs actifs : des comptages distincts |[active_users_count](active-users-count-plugin.md)|Retourne les utilisateurs distincts pour chaque fenêtre de temps, où un utilisateur n’est considéré que s’il apparaît dans au moins X périodes distinctes dans une période de retour spcified.|
|Engagement des utilisateurs: DAU/WAU/MAU|[activity_engagement](activity-engagement-plugin.md)|Compare entre une fenêtre de temps intérieur (p. ex., quotidienne) et une externe (p. ex., hebdomadaire) pour l’engagement informatique (p. ex., DAU/WAU)|Kusto.Explorer: Galerie de rapports|
|Sessions : comptez les sessions actives|[session_count](session-count-plugin.md)|Compte des sessions, où une session est définie par une période de temps - un enregistrement utilisateur est considéré comme une nouvelle session, si elle n’a pas été vue dans la période de retour de l’enregistrement actuel|
||||
|Entonnoirs : analyse de séquences d’état précédentes et suivantes | [funnel_sequence](funnel-sequence-plugin.md)|Compte les utilisateurs distincts qui ont pris une séquence d’événements, et les événements prév / prochain qui ont conduit / ont été suivis par la séquence. Utile pour la construction de [diagrammes sankey](https://en.wikipedia.org/wiki/Sankey_diagram)||
|Entonnoirs : analyse de l’achèvement de la séquence|[funnel_sequence_completion](funnel-sequence-completion-plugin.md)|Calcule le nombre distinct d’utilisateurs qui ont *terminé* une séquence spécifiée dans chaque fenêtre de temps|
||||