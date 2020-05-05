---
title: Bacs à sable (sandbox)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les bacs à sable (sandbox) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 5dec95e8d4a73bcff4e8ad037577c51a20fcc64c
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741959"
---
# <a name="sandboxes"></a>Bacs à sable

Le service de moteur de données de Kusto peut exécuter des bacs à sable pour exécuter des flux spécifiques qui nécessitent un isolement sécurisé.
Les exemples de ces flux sont des scripts définis par l’utilisateur, qui sont exécutés à l’aide du [plug-in Python](../query/pythonplugin.md) ou du [plug-in R](../query/rplugin.md).

Pour l’exécution de ces bacs à sable, Kusto utilise une évolution du projet [DRAWbridge](https://www.microsoft.com/research/project/drawbridge/) de Microsoft. Cette solution est utilisée par d’autres services Microsoft pour exécuter des objets définis par l’utilisateur dans un environnement multi-locataire.

Les flux qui sont exécutés dans les bacs à sable (sandbox) ne sont pas isolés, mais sont également locaux (proches des données), ce qui signifie qu’aucune latence supplémentaire n’a été ajoutée pour les appels distants.

## <a name="prerequisites"></a>Prérequis

* Le moteur de données **ne doit pas** avoir le [chiffrement de disque](https://docs.microsoft.com/azure/data-explorer/security#data-encryption) activé.
  * La prise en charge de l’exécution des deux fonctionnalités côte à côte est attendue dans le futur.
* Les packages requis (images) pour l’exécution des bacs à sable sont déployés sur chacun des nœuds du moteur de données et nécessitent un espace SSD dédié pour pouvoir être exécutés.
  * La taille estimée est de 20 Go, ce qui correspond, par exemple, à environ 2,5% de la capacité de disque SSD d’une machine virtuelle D14_v2, ou à 0,7% de la capacité de disque SSD d’une machine virtuelle L16_v1.
  * Cela affecte la capacité des données du cluster et peut donc affecter le [coût](https://azure.microsoft.com/pricing/details/data-explorer) du cluster.

## <a name="runtime"></a>Runtime

* Un opérateur de requête sandbox peut utiliser un ou plusieurs bacs à sable (sandbox) pour son exécution.
  * Un bac à sable (sandbox) n’est utilisé que pour une seule exécution, n’est pas partagé entre plusieurs exécutions et est supprimé une fois l’exécution terminée.
  * Les bacs à sable sont initialisés tardivement sur un nœud la première fois qu’une requête requiert un bac à sable (sandbox) pour son exécution.
    * Cela signifie que la première exécution d’un plug-in qui utilise des bacs à sable (sandbox) sur un nœud comprendra une brève période de préchauffage.
  * Lorsqu’un nœud est redémarré (par exemple, dans le cadre d’une mise à niveau du service), tous les bacs à sable en cours d’exécution sont supprimés.
* Chaque nœud conserve un volume prédéfini de bacs à sable (sandbox) prêts pour l’exécution des requêtes entrantes.
  * Une fois qu’un bac à sable (sandbox) est utilisé, un nouveau sandbox est automatiquement alloué pour le remplacer.
* Si aucun bac à sable (sandbox) pré-alloué n’est disponible pour servir d’opérateur de requête, il sera limité.
  (voir les [Erreurs](#errors), jusqu’à ce que de nouveaux bacs à sable (sandbox) soient alloués (peut prendre jusqu’à 10-15 secondes par bac à sable, en fonction de la référence et des ressources disponibles sur le nœud de données).

## <a name="limitations"></a>Limites

Certaines de ces limitations peuvent être contrôlées à l’aide d’une [stratégie de bac à sable (sandbox](../management/sandboxpolicy.md)) au niveau du cluster, pour chaque type de bac à sable.

* **Nombre de bacs à sable par nœud :** Le nombre de bacs à sable par nœud est limité.
  * Les demandes qui rencontrent un État dans lequel aucun bac à sable (sandbox) n’est disponible seront limitées.
* **Réseau :** Un bac à sable (sandbox) ne peut pas interagir avec une ressource sur la machine virtuelle ou en dehors de celui-ci.
  * Un bac à sable (sandbox) ne peut pas interagir avec un autre sandbox.
* **UC :** Le taux maximal de processeur qu’un bac à sable peut utiliser pour `50%`les processeurs de son hôte est limité (par défaut).
  * Lorsque cette limite est atteinte, l’utilisation du processeur du bac à sable (sandbox) est limitée, mais l’exécution continue.
* **Mémoire :** La quantité maximale de RAM qu’un bac à `20GB`sable peut consommer de la mémoire RAM de son hôte est limitée (par défaut).
  * L’atteinte de cette limite entraînera l’arrêt du bac à sable (sandbox) (et une erreur d’exécution de la requête).
* **Disque :** Un bac à sable (sandbox) est associé à un répertoire unique, qui ne peut pas accéder au système de fichiers de l’hôte.
  * Ce dossier donne accès à l’image/au package qui correspond au genre du bac à sable (par exemple, le package Python ou R non personnalisable).
* **Processus enfants :** Le bac à sable (sandbox) est bloqué de la génération de processus enfants.

> [!NOTE]
> L’utilisation des ressources du bac à sable (sandbox) dépend non seulement de la taille des données traitées dans le cadre de la requête, mais également de la logique qui s’exécute dans le bac à sable (sandbox) et de l’implémentation des bibliothèques utilisées par celle-ci.
> (par exemple, pour `python` les `r` plug-ins et, ce dernier signifie le script fourni par l’utilisateur et les bibliothèques python ou R qu’il consomme au moment de l’exécution)

## <a name="errors"></a>Erreurs

|Code                      |Message                                                                                        |Raison potentielle                                                                                                    |
|--------------------------|-----------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
|E_SB_QUERY_THROTTLED_ERROR|La requête bac à sable (sandbox) a été abandonnée en raison d’une limitation. Une nouvelle tentative peut être effectuée après un certain intervalle   |Il n’y a aucun bac à sable (sandbox) disponible sur le nœud cible. De nouveaux bacs à sable doivent être disponibles en quelques secondes         |
|E_SB_QUERY_THROTTLED_ERROR|Les bacs à sable (sandbox) de type « {Kind} » n’ont pas encore été initialisés                                       |La stratégie du bac à sable (sandbox) a récemment changé. Les nouveaux bacs à sable qui obéissent à la nouvelle stratégie sont disponibles en quelques secondes.|
