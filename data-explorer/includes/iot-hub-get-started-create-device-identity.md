---
title: Fichier include
description: Fichier include
services: iot-hub
author: orspod
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: orspodek
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: e21bd487ff110068ceee4a737a27157068e1f17a
ms.sourcegitcommit: 1c836079c713db3aef33529e6a15af47e2984e7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92470831"
---
Dans cette section, vous utilisez Azure CLI pour créer une identité d’appareil pour cet article. Les ID d’appareil respectent la casse.

1. Ouvrez [Azure Cloud Shell](https://shell.azure.com/).

1. Dans Azure Cloud Shell, exécutez la commande suivante pour installer l’extension IoT Microsoft Azure pour Azure CLI :

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Créez une nouvelle identité d’appareil nommée `myDeviceId` et récupérez la chaîne de connexion d’appareil à l’aide des commandes suivantes :

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Prenez note de la chaîne de connexion de l’appareil à partir du résultat. Cette chaîne de connexion est utilisée par l’application d’appareil pour se connecter à votre IoT Hub en tant qu’appareil.

<!-- images and links -->
