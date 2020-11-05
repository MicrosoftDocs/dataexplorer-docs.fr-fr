---
title: Bacs à sable (sandbox)-Azure Explorateur de données
description: Cet article décrit les bacs à sable (sandbox) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 5732d0fa9773d0c8fe5420026cb855d1040b8706
ms.sourcegitcommit: 42cc7d11f41a5bfa9e021764b044dcd68d99a258
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93403763"
---
# <a name="sandboxes"></a>Bacs à sable

Le service de moteur de données de Kusto peut exécuter des bacs à sable pour des flux spécifiques qui nécessitent une isolation sécurisée.
Les scripts définis par l’utilisateur qui s’exécutent à l’aide du [plug-in Python](../query/pythonplugin.md) ou du [plug-in R](../query/rplugin.md)sont des exemples de ces flux.

Pour exécuter ces bacs à sable, Kusto utilise une version évoluée du projet [DRAWbridge](https://www.microsoft.com/research/project/drawbridge/) de Microsoft. Cette solution est utilisée par d’autres services Microsoft pour exécuter des objets définis par l’utilisateur dans un environnement multi-locataire.

Les flux qui s’exécutent dans les bacs à sable ne sont pas isolés. Ils sont également locaux (proches des données). Cela signifie qu’il n’y a aucune latence supplémentaire ajoutée pour les appels distants.

## <a name="prerequisites"></a>Prérequis

* Le [chiffrement de disque](../../security.md#data-encryption) du ne doit pas du moteur de données est activé.
  * La prise en charge des deux fonctionnalités exécutées côte à côte est attendue dans le futur.
* Les packages requis (images) pour exécuter les bacs à sable (sandbox) sont déployés sur chacun des nœuds du moteur de données et nécessitent un espace SSD dédié pour s’exécuter
  * La taille estimée est de 20 Go, soit environ 2,5% de la capacité de disque SSD d’une machine virtuelle D14_v2, par exemple, ou 0,7% de la capacité de disque SSD d’une machine virtuelle L16_v1.
  * Cela affecte la capacité des données du cluster et peut affecter le [coût](https://azure.microsoft.com/pricing/details/data-explorer) du cluster.

## <a name="runtime"></a>Runtime

* Un opérateur de requête sandbox peut utiliser un ou plusieurs bacs à sable (sandbox) pour son exécution.
  * Un bac à sable (sandbox) n’est utilisé que pour une seule exécution, n’est pas partagé entre plusieurs exécutions et est supprimé une fois l’exécution terminée.
  * Les bacs à sable sont initialisés tardivement sur un nœud, la première fois qu’une requête requiert un bac à sable (sandbox) pour son exécution.
    * Cela signifie que la première exécution d’un plug-in qui utilise des bacs à sable (sandbox) sur un nœud inclura une brève période de préchauffage.
  * Lorsqu’un nœud est redémarré, par exemple dans le cadre d’une mise à niveau de service, tous les bacs à sable en cours d’exécution sur celui-ci sont supprimés.
* Chaque nœud conserve un nombre prédéfini de bacs à sable (sandbox) prêts pour exécuter les demandes entrantes.
  * Une fois qu’un bac à sable (sandbox) est utilisé, un nouveau sandbox est automatiquement mis à la disposition du remplacement.
* Si aucun bac à sable (sandbox) pré-alloué n’est disponible pour servir d’opérateur de requête, il sera limité jusqu’à ce que de nouveaux bacs à sable soient disponibles. Pour plus d’informations, consultez [Erreurs](#errors). La nouvelle allocation du bac à sable (sandbox) peut prendre jusqu’à 10-15 secondes par bac à sable (sandbox), en fonction de la référence et des ressources disponibles sur le nœud de données.

## <a name="limitations"></a>Limitations

Certaines des limitations peuvent être contrôlées à l’aide d’une [stratégie de bac à sable (sandbox](../management/sandboxpolicy.md)) au niveau du cluster, pour chaque type de bac à sable.

* **Nombre de bacs à sable par nœud :** Le nombre de bacs à sable par nœud est limité.
  * Les demandes effectuées lorsqu’il n’existe aucun bac à sable (sandbox) disponible seront limitées.
* **Réseau :** Un bac à sable (sandbox) ne peut pas interagir avec une ressource sur la machine virtuelle ou en dehors de celui-ci.
  * Un bac à sable (sandbox) ne peut pas interagir avec un autre sandbox.
* **UC :** Le taux maximal de processeur qu’un bac à sable peut consommer est limité (la valeur par défaut est `50%` ).
  * Lorsque la limite est atteinte, l’utilisation du processeur du bac à sable (sandbox) est limitée, mais l’exécution continue.
* **Mémoire :** La quantité maximale de RAM qu’un bac à sable peut consommer de la mémoire RAM de son hôte est limitée (la valeur par défaut est `20GB` ).
  * L’atteinte de la limite entraîne l’arrêt du bac à sable (sandbox) et une erreur d’exécution de la requête.
* **Disque :** Un bac à sable (sandbox) est associé à un répertoire unique et indépendant. Il ne peut pas accéder au système de fichiers de l’hôte.
  * Le dossier unique donne accès à l’image/au package qui correspond au type du bac à sable (sandbox). Par exemple, le package Python ou R non personnalisable.
* **Processus enfants :** Le bac à sable (sandbox) est bloqué de la génération de processus enfants.

> [!NOTE]
> Les ressources utilisées avec le bac à sable (sandbox) dépendent non seulement de la taille des données traitées dans le cadre de la requête, mais également de la logique qui s’exécute dans le bac à sable (sandbox) et de l’implémentation des bibliothèques utilisées par celle-ci.
> Par exemple, pour les `python` `r` plug-ins et, ce dernier signifie le script fourni par l’utilisateur et les bibliothèques python ou R qu’il consomme au moment de l’exécution.

## <a name="errors"></a>Erreurs

|ErrorCode                 |Statut                     |Message                                                                                            |Raison potentielle                                                                                                    |
|--------------------------|---------------------------|---------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
|E_SB_QUERY_THROTTLED_ERROR|TooManyRequests (429)      |La requête bac à sable (sandbox) a été abandonnée en raison de la limitation. Une nouvelle tentative peut être effectuée après un certain intervalle   |Il n’y a aucun bac à sable (sandbox) disponible sur le nœud cible. De nouveaux bacs à sable doivent être disponibles en quelques secondes         |
|E_SB_QUERY_THROTTLED_ERROR|TooManyRequests (429)      |Les bacs à sable (sandbox) de type « {Kind} » n’ont pas encore été initialisés                                            |La stratégie du bac à sable (sandbox) a récemment changé. Les nouveaux bacs à sable qui obéissent à la nouvelle stratégie sont disponibles en quelques secondes.|
|                          |InternalServiceError (520) |La requête bac à sable (sandbox) a été abandonnée en raison d’un échec lors de l’initialisation des bacs à sable                         |Une défaillance inattendue de l’infrastructure.                         |
