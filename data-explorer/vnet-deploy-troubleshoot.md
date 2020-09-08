---
title: Résoudre les problèmes d’accès, d’ingestion et de fonctionnement de votre cluster Azure Data Explorer dans votre réseau virtuel
description: Résoudre les problèmes de connectivité, d’ingestion, de création de cluster et de fonctionnement de votre cluster Azure Data Explorer dans votre réseau virtuel
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/24/2020
ms.openlocfilehash: 6f3831580c998814d956b57a58acc8acd7269abb
ms.sourcegitcommit: f2f9cc0477938da87e0c2771c99d983ba8158789
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2020
ms.locfileid: "89502430"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>Résoudre les problèmes d’accès, d’ingestion et de fonctionnement de votre cluster Azure Data Explorer dans votre réseau virtuel

Dans cette section, vous allez apprendre à résoudre les problèmes de connectivité, les problèmes de fonctionnement et les problèmes de création de cluster pour un cluster déployé dans votre [réseau virtuel](/azure/virtual-network/virtual-networks-overview).

## <a name="access-issues"></a>Problèmes d’accès

Si vous rencontrez un problème lors de l’accès au cluster à l’aide du point de terminaison public (cluster.region.kusto.windows.net) ou privé (private-cluster.region.kusto.windows.net) et que vous pensez qu’il est lié à la configuration du réseau virtuel, procédez comme suit pour résoudre le problème.

### <a name="check-tcp-connectivity"></a>Vérifier la connectivité TCP

La première étape comprend la vérification de la connectivité TCP à l’aide du système d’exploitation Windows ou Linux.

# <a name="windows"></a>[Windows](#tab/windows)

1. Téléchargez [TCping](https://www.elifulkerson.com/projects/tcping.php) sur l’ordinateur qui se connecte au cluster.
1. Envoyez une commande ping à la destination à partir de la machine source en utilisant la commande suivante :

   ```cmd
   C:\> tcping -t yourcluster.kusto.windows.net 443 
   ** Pinging continuously.  Press control-c to stop **
   Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
   ```

# <a name="linux"></a>[Linux](#tab/linux)

1. Installez *netcat* sur la machine qui se connecte au cluster.

   ```bash
   $ apt-get install netcat
   ```

1. Envoyez une commande ping à la destination à partir de la machine source en utilisant la commande suivante :

   ```bash
   $ netcat -z -v yourcluster.kusto.windows.net 443
   Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
   ```
---

Si le test échoue, effectuez les étapes suivantes. Si le test réussit, le problème n’est pas dû à un problème de connectivité TCP. Accédez aux [problèmes opérationnels](#cluster-creation-and-operations-issues) pour approfondir la résolution des problèmes.

### <a name="check-the-network-security-group-nsg"></a>Vérifier le groupe de sécurité réseau (NSG)

Vérifiez que le [groupe de sécurité réseau](/azure/virtual-network/security-overview) (NSG) attaché au sous-réseau du cluster possède une règle de trafic entrant qui autorise l’accès à partir de l’adresse IP de l’ordinateur client pour le port 443.

### <a name="check-route-table"></a>Vérifier la table de routage

Si le sous-réseau du cluster dispose d’une configuration de tunneling forcé vers le pare-feu (sous-réseau avec une [table de routage](/azure/virtual-network/virtual-networks-udr-overview) contenant la route par défaut « 0.0.0.0/0 »), assurez-vous que l’adresse IP de l’ordinateur a une route avec le [type de tronçon suivant](/azure/virtual-network/virtual-networks-udr-overview) vers VirtualNetwork/Internet. Cet itinéraire est nécessaire pour éviter les problèmes de routage asymétrique.

## <a name="ingestion-issues"></a>Problèmes d’ingestion

Si vous rencontrez des problèmes d’ingestion et que vous soupçonnez qu’ils sont liés à une configuration de réseau virtuel, procédez comme suit.

### <a name="check-ingestion-health"></a>Vérifier l’intégrité d’ingestion

Vérifiez que les [métriques d’ingestion du cluster](using-metrics.md#ingestion-metrics) indiquent un état sain.

### <a name="check-security-rules-on-data-source-resources"></a>Vérifier les règles de sécurité sur les ressources de source de données

Si les métriques indiquent qu’aucun événement n’a été traité à partir de la source de données (métrique *Événements traités* (pour les hubs d’événements/IoT)), assurez-vous que les ressources de source de données (hub d’événements ou stockage) autorisent l’accès à partir du sous-réseau du cluster dans les règles de pare-feu ou les points de terminaison de service.

### <a name="check-security-rules-configured-on-clusters-subnet"></a>Vérifier les règles de sécurité configurées sur le sous-réseau du cluster

Vérifiez que les règles de groupe de sécurité réseau, de route définie par l’utilisateur et de pare-feu sont correctement configurées pour le sous-réseau du cluster. En outre, testez la connectivité réseau pour tous les points de terminaison dépendants. 

## <a name="cluster-creation-and-operations-issues"></a>Problèmes de création de cluster et de fonctionnement

Si vous rencontrez un problème de création de cluster ou de fonctionnement et que vous soupçonnez qu’il est lié à une configuration de réseau virtuel, procédez comme suit pour résoudre le problème.

### <a name="check-the-dns-servers-configuration"></a>Vérifier la configuration « Serveurs DNS »

Les serveurs DNS personnalisés ne sont pas pris en charge. Utilisez l’option par défaut dans la section de configuration **Serveurs DNS** de votre réseau virtuel.

### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>Diagnostiquer le réseau virtuel avec l’API REST

L’outil [ARMClient](https://chocolatey.org/packages/ARMClient) permet d’appeler l’API REST à l’aide de PowerShell. 

1. Se connecter à ARMClient

   ```powerShell
   armclient login
   ```

1. Appeler l’opération de diagnostic

    ```powershell
    $subscriptionId = '<subscription id>'
    $clusterName = '<name of cluster>'
    $resourceGroupName = '<resource group name>'
    $apiversion = '2019-11-09'
    
    armclient post "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Kusto/clusters/$clusterName/diagnoseVirtualNetwork?api-version=$apiversion" -verbose
    ```

1. Vérifier la réponse

    ```powershell
    HTTP/1.1 202 Accepted
    ...
    Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    ...
    ```

1. Attendre la fin de l’opération

    ```powershell
    armclient get https://management.azure.com/subscriptions/$subscriptionId/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "[Running/Failed/Completed]",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {...}
    }
    ```
    
   Attendez que la propriété *status* indique *Completed*, le champ *properties* doit alors indiquer :

    ```powershell
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "Completed",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {
        "Findings": [...]
      }
    }
    ```

Si la propriété *Findings* affiche un résultat vide, cela signifie que tous les tests réseau ont réussi et qu’aucune connexion n’est interrompue. Si l’erreur suivante s’affiche, *La dépendance sortante '{dependencyName}:{port}' n’est peut-être pas satisfaite (sortante)* , le cluster ne peut pas atteindre les points de terminaison du service dépendant. Procédez avec les étapes suivantes.

### <a name="check-network-security-group-nsg"></a>Vérifier le groupe de sécurité réseau (NSG)

Vérifiez que le [groupe de sécurité réseau](/azure/virtual-network/security-overview) est correctement configuré, conformément aux instructions figurant dans [Dépendances pour le déploiement de réseau virtuel](vnet-deployment.md#dependencies-for-vnet-deployment)

### <a name="check-route-table"></a>Vérifier la table de routage

Si le sous-réseau du cluster a le tunneling forcé configuré vers le pare-feu (sous-réseau avec une [table de routage](/azure/virtual-network/virtual-networks-udr-overview) qui contient la route par défaut « 0.0.0.0/0 »), assurez-vous que les [adresses IP de gestion](vnet-deployment.md#azure-data-explorer-management-ip-addresses) et les [adresses IP de surveillance de l’intégrité](vnet-deployment.md#health-monitoring-addresses) ont une route avec le [type de tronçon suivant](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *Internet* et un [préfixe d’adresse source](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) *'management-ip/32'* et *'health-monitoring-ip/32'* . Cet itinéraire nécessaire pour éviter les problèmes de routage asymétrique.

### <a name="check-firewall-rules"></a>Vérifier les règles de pare-feu

Si vous forcez le trafic sortant du sous-réseau de tunnel vers un pare-feu, vérifiez que les noms de domaine complet de toutes les dépendances (par exemple, *.blob.core.windows.net*) sont autorisés dans la configuration du pare-feu, comme décrit dans [Sécurisation du trafic sortant avec pare-feu](vnet-deployment.md#securing-outbound-traffic-with-firewall).
