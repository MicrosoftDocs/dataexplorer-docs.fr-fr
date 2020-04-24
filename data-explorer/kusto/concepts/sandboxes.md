---
title: Sandboxes - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Sandboxes dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: faa7a8225aecd5992e3064fd10cfcc0e559c5127
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522965"
---
# <a name="sandboxes"></a>Bacs à sable

Le service Data Engine de Kusto est capable de faire fonctionner des bacs à sable pour l’exécution de flux spécifiques qui nécessitent une isolement sécurisé.
Des exemples pour ces flux sont des scripts définis par l’utilisateur, qui sont exécutés à l’aide du [plugin Python](../query/pythonplugin.md) ou du [plugin R](../query/rplugin.md).

Pour l’exécution de ces bacs à sable, Kusto utilise une évolution du projet [Drawbridge](https://www.microsoft.com/research/project/drawbridge/) de Microsoft. Cette solution est utilisée par d’autres services Microsoft pour exécuter des objets définis par l’utilisateur dans un environnement multi-locataire.

Les débits qui sont exécutés dans les bacs à sable sont non seulement isolés, mais sont également locaux (près des données), ce qui signifie qu’il n’y a pas de latence supplémentaire ajoutée pour les appels à distance.

## <a name="prerequisites"></a>Prérequis

* Le moteur de données ne doit **pas** avoir [le chiffrement du disque](https://docs.microsoft.com/azure/data-explorer/security#data-encryption) activé.
  * On s’attend à ce que le soutien pour avoir les deux caractéristiques fonctionnant côte à côte est prévu à l’avenir.
* Les paquets (images) requis pour l’exécution des bacs à sable sont déployés sur chacun des nœuds du moteur de données, et nécessitent un espace SSD dédié pour fonctionner
  * La taille estimée est de 20 Go, qui, par exemple, est d’environ 2,5% la capacité SSD d’un D14_v2 VM, soit 0,7% de la capacité SSD d’un L16_v1 VM.
  * Cela affecte la capacité de données du cluster et peut donc affecter le [coût](https://azure.microsoft.com/pricing/details/data-explorer) du cluster.

## <a name="runtime"></a>Runtime

* Un opérateur de requêtes bac à sable peut utiliser un ou plusieurs bacs à sable pour son exécution.
  * Ceux-ci ne sont utilisés que pour une seule course, ne sont pas partagés sur plusieurs pistes, et sont éliminés une fois que l’exécution terminée.
* Chaque nœud conserve une quantité prédéfinmise de bacs à sable qui sont prêts à exécuter les demandes entrantes.
  * Une fois qu’un bac à sable est utilisé, un nouveau est automatiquement alloué pour le remplacer.
* Dans le cas où il n’y a pas de bacs à sable pré-attribués disponibles pour servir un opérateur de requête, il sera étranglé.
  (voir [Erreurs](#errors), jusqu’à ce que de nouveaux bacs à sable soient alloués (peut prendre jusqu’à 10-15 secondes par bac à sable, selon le SKU et les ressources disponibles sur le nœud de données).

## <a name="limitations"></a>Limites

Certaines de ces limitations peuvent être contrôlées à l’aide d’une politique de bac à sable au niveau des [grappes,](../management/sandboxpolicy.md)pour chaque type de bac à sable.

* **Nombre de bacs à sable par nœud :** Le nombre de bacs à sable par nœud est limité.
  * Les demandes qui rencontrent un état où il n’y a pas de bac à sable disponible seront étranglées.
* **Réseau:** Un bac à sable ne peut interagir avec aucune ressource sur la VM ou à l’extérieur de celui-ci.
  * Un bac à sable ne peut pas interagir avec un autre bac à sable.
* **CPU:** Le taux maximum de processeur qu’un bac à sable peut consommer `50%`des processeurs de son hôte est limité (par défaut à ).
  * Lorsque cette limite est atteinte, l’utilisation du processeur du bac à sable est étranglée, mais l’exécution se poursuit.
* **Mémoire:** La quantité maximale de RAM qu’un bac à sable peut `20GB`consommer de la RAM de son hôte est limitée (par défaut à ).
  * Atteindre cette limite se traduira par la fin du bac à sable (et une erreur d’exécution de requête).
* **Disque:** Un bac à sable a un répertoire unique attaché à elle, à part pour lequel, il ne peut pas accéder au système de fichiers de l’hôte.
  * Ce dossier donne accès à l’image/paquet qui correspond au type du bac à sable (par exemple, le paquet Python ou R non personnalisable).
* **Processus pour enfants :** Le bac à sable est bloqué à la frai des processus pour enfants.

> [!NOTE]
> L’utilisation des ressources du bac à sable dépend non seulement de la taille des données traitées dans le cadre de la demande, mais aussi de la logique qui s’exécute dans le bac à sable, et de la mise en œuvre des bibliothèques utilisées par elle.
> (p. ex. `python` pour `r` les plugins et les plugins, ce dernier signifie le script fourni par l’utilisateur et les bibliothèques Python ou R qu’il consomme au moment de l’exécution)

## <a name="errors"></a>Erreurs

|Code                      |Message                                                                                        |Raison potentielle                                                                                                    |
|--------------------------|-----------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
|E_SB_QUERY_THROTTLED_ERROR|La requête bacée à sable a été avortée en raison de la limitation. La réessayation après un certain backoff pourrait réussir   |Il n’y a pas de bacs à sable disponibles sur le nœud cible. De nouveaux bacs à sable devraient être disponibles en quelques secondes         |
|E_SB_QUERY_THROTTLED_ERROR|Les bacs à sable de type ''type' n’ont pas encore été paralés                                       |La politique des bacs à sable a récemment changé. De nouveaux bacs à sable obéissant à la nouvelle politique seront disponibles dans quelques secondes|