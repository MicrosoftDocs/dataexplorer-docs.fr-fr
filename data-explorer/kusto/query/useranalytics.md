---
title: Analyse utilisateur-Azure Explorateur de données
description: Cet article décrit User Analytics dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/17/2019
ms.openlocfilehash: a63f68564bc0e2fec8b6e2d5faa12e50b7adcc7c
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741654"
---
# <a name="user-analytics"></a>Analyse des utilisateurs

Cette section décrit les extensions Kusto (plug-ins) pour les scénarios d’analyse utilisateur.

|Scénario|Plug-in|Détails|Expérience utilisateur|
|--------|------|--------|-------|
| Compter les nouveaux utilisateurs au fil du temps | [activity_counts_metrics](activity-counts-metrics-plugin.md)|Retourne les nombres/dcounts/New pour chaque fenêtre de temps. Chaque fenêtre de temps est comparée à *toutes les* fenêtres de temps précédentes|Kusto. Explorer : bibliothèque de rapports|
| Période-sur-période : taux de rétention/évolution et nouveaux utilisateurs | [activity_metrics](activity-metrics-plugin.md)|Retourne `dcount`, rétention/taux d’évolution pour chaque fenêtre de temps. Chaque fenêtre de temps est comparée à la fenêtre de temps *précédente*|Kusto. Explorer : bibliothèque de rapports|
| Nombre d’utilisateurs `dcount` et fenêtre glissante | [sliding_window_counts](sliding-window-counts-plugin.md)|Pour chaque fenêtre de temps, retourne le `dcount` nombre et sur une période lookback, dans une fenêtre glissante|
| Cohorte de nouveaux utilisateurs : taux de rétention/évolution et nouveaux utilisateurs | [new_activity_metrics](new-activity-metrics-plugin.md)|Compare entre les cohortes de nouveaux utilisateurs (tous les utilisateurs qui ont été vus pour la première fois dans la fenêtre de temps). Chaque cohorte est comparée à toutes les cohortes antérieures. La comparaison prend en compte *toutes les* fenêtres temporelles précédentes|Kusto. Explorer : bibliothèque de rapports|
|Utilisateurs actifs : nombres distincts |[active_users_count](active-users-count-plugin.md)|Retourne des utilisateurs distincts pour chaque fenêtre de temps. Un utilisateur n’est pris en compte que s’il apparaît dans au moins X périodes distinctes dans une période lookback spécifiée.|
|Engagement utilisateur : UAQ/WAU/MAU|[activity_engagement](activity-engagement-plugin.md)|Comparaison entre une fenêtre temporelle interne (par exemple, quotidienne) et une externe (par exemple, hebdomadaire) pour le calcul de l’engagement (par exemple, UAQ/WAU)|Kusto. Explorer : bibliothèque de rapports|
|Sessions : nombre de sessions actives|[session_count](session-count-plugin.md)|Compte les sessions, où une session est définie par une période : un enregistrement d’utilisateur est considéré comme une nouvelle session, s’il n’a pas été vu dans la période lookback à partir de l’enregistrement actuel.|
||||
|Entonnoirs : analyse de la séquence d’État précédente et suivante | [funnel_sequence](funnel-sequence-plugin.md)|Compte les utilisateurs distincts qui ont pris une séquence d’événements, ainsi que les événements précédents ou suivants qui ont conduit ou ont été suivis par la séquence. Utile pour construire des [diagrammes Sankey](https://en.wikipedia.org/wiki/Sankey_diagram)||
|Entonnoirs : analyse de fin de séquence|[funnel_sequence_completion](funnel-sequence-completion-plugin.md)|Calcule le nombre distinct d’utilisateurs qui ont *terminé* une séquence spécifiée dans chaque fenêtre de temps|
||||