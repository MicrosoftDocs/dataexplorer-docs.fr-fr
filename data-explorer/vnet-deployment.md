---
title: Déployer Azure Data Explorer dans votre réseau virtuel
description: Découvrir comment déployer Azure Data Explorer dans votre réseau virtuel
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/31/2019
ms.openlocfilehash: 5a7f680dc2ab76a9f952efa52d60b59c7b1d1c93
ms.sourcegitcommit: 041272af91ebe53a5d573e9902594b09991aedf0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91452848"
---
# <a name="deploy-azure-data-explorer-cluster-into-your-virtual-network"></a>Déployer un cluster Azure Data Explorer dans votre réseau virtuel

Cet article décrit les ressources présentes lorsque vous déployez un cluster Azure Data Explorer dans un réseau virtuel Azure personnalisé. Ces informations vous aideront à déployer un cluster dans un sous-réseau de votre réseau virtuel (VNet). Pour plus d’informations sur les réseaux virtuels Azure, consultez [Qu’est-ce que le réseau virtuel Azure ?](/azure/virtual-network/virtual-networks-overview)

:::image type="content" source="media/vnet-deployment/vnet-diagram.png" alt-text="Diagramme montrant l’architecture réseau virtuelle schématique"::: 

Azure Data Explorer prend en charge le déploiement d’un cluster dans un sous-réseau de votre réseau virtuel (VNet). Cette fonctionnalité vous permet d’effectuer les opérations suivantes :

* Appliquer des règles du [groupe de sécurité réseau](/azure/virtual-network/security-overview) (NSG) sur le trafic de votre cluster Azure Data Explorer.
* Connectez votre réseau local au sous-réseau du cluster Azure Data Explorer.
* Sécurisez vos sources de connexion de données ([Event Hub](/azure/event-hubs/event-hubs-about) et [Event Grid](/azure/event-grid/overview)) avec des [points de terminaison de service](/azure/virtual-network/virtual-network-service-endpoints-overview).

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>Accéder à votre cluster Azure Data Explorer sur votre réseau virtuel

Vous pouvez accéder à votre cluster Azure Data Explorer à l’aide des adresses IP suivantes pour chaque service (moteur et services de gestion des données) :

* **IP privée** : Utilisé pour accéder au cluster à l’intérieur du réseau virtuel.
* **Adresse IP publique** : Utilisé pour accéder au cluster à partir de l’extérieur du réseau virtuel pour la gestion et la surveillance et en tant qu’adresse source pour les connexions sortantes démarrées à partir du cluster.

Les enregistrements DNS suivants sont créés pour accéder au service : 

* `[clustername].[geo-region].kusto.windows.net` (moteur) `ingest-[clustername].[geo-region].kusto.windows.net` (gestion des données) sont mappés à l’adresse IP publique de chaque service. 

* `private-[clustername].[geo-region].kusto.windows.net` (moteur) `ingest-private-[clustername].[geo-region].kusto.windows.net`\\`private-ingest-[clustername].[geo-region].kusto.windows.net` (gestion des données) sont mappés à l’adresse IP privée de chaque service.

## <a name="plan-subnet-size-in-your-vnet"></a>Planifier la taille du sous-réseau dans votre réseau virtuel

La taille du sous-réseau utilisé pour héberger un cluster Azure Data Explorer ne peut pas être modifiée après le déploiement du sous-réseau. Dans votre réseau virtuel, Azure Data Explorer utilise une adresse IP privée pour chaque machine virtuelle et deux adresses IP privées pour les équilibreurs de charge internes (gestion des données et des moteurs). La mise en réseau Azure utilise également cinq adresses IP pour chaque sous-réseau. Azure Data Explorer configure deux machines virtuelles pour le service de gestion des données. Les machines virtuelles du service de moteur sont approvisionnées par capacité de mise à l’échelle de la configuration utilisateur.

Nombre total d’adresses IP :

| Utilisation | Nombre d’adresses |
| --- | --- |
| Service du moteur | 1 par instance |
| Service de gestion des données | 2 |
| Équilibreurs de charge internes | 2 |
| Adresses réservées Azure | 5 |
| **Total** | **#engine_instances + 9** |

> [!IMPORTANT]
> La taille du sous-réseau doit être planifiée à l’avance, car elle ne peut pas être modifiée après le déploiement d’Azure Data Explorer. Par conséquent, réservez la taille de sous-réseau nécessaire en conséquence.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Points de terminaison de service pour la connexion à Azure Data Explorer

[Les points de terminaison de service Azure](/azure/virtual-network/virtual-network-service-endpoints-overview) vous permettent de sécuriser vos ressources multi-abonnés Azure sur votre réseau virtuel.
Le déploiement du cluster Azure Data Explorer dans votre sous-réseau vous permet de configurer des connexions de données avec [Event Hub](/azure/event-hubs/event-hubs-about) ou [Event Grid](/azure/event-grid/overview) tout en limitant les ressources sous-jacentes pour le sous-réseau Azure Data Explorer.

> [!NOTE]
> Quand vous utilisez une configuration EventGrid avec [Stockage](/azure/storage/common/storage-introduction) et [Hub d’événements](/azure/event-hubs/event-hubs-about), le compte de stockage utilisé dans l’abonnement peut être verrouillé avec des points de terminaison de service sur le sous-réseau d’Azure Data Explorer tout en autorisant les services de plateforme Azure approuvés dans la [configuration du pare-feu](/azure/storage/common/storage-network-security), alors que le hub d’événements ne peut pas activer le point de terminaison de service car il ne prend pas en charge les [services de plateforme Azure](/azure/event-hubs/event-hubs-service-endpoints) approuvés.

## <a name="private-endpoints"></a>Points de terminaison privés

Les [points de terminaison privés](/azure/private-link/private-endpoint-overview) autorisent un accès privé aux ressources Azure (comme Stockage/Event Hub/Data Lake Gen 2) et utilisent une adresse IP privée de votre réseau virtuel pour apporter la ressource dans votre réseau virtuel.
Créez un [point de terminaison privé](/azure/private-link/private-endpoint-overview) pour les ressources utilisées par les connexions de données, comme Stockage et Event Hub, et pour les tables externes, comme Stockage, Data Lake Gen 2 et SQL Database, de votre réseau virtuel pour accéder aux ressources sous-jacentes en privé.

 > [!NOTE]
 > La configuration d’un point de terminaison privé demande de [configurer DNS](/azure/private-link/private-endpoint-dns). Nous prenons en charge la configuration d’une [zone DNS privée Azure](/azure/dns/private-dns-privatednszone) uniquement. Les serveurs DNS personnalisés ne sont pas pris en charge. 

## <a name="dependencies-for-vnet-deployment"></a>Dépendances pour le déploiement de réseau virtuel

### <a name="network-security-groups-configuration"></a>Configuration des Groupes de sécurité réseau

Les [groupes de sécurité réseau (NSG)](/azure/virtual-network/security-overview) permettent de contrôler l’accès réseau au sein d’un réseau virtuel. Accédez à Azure Data Explorer à l’aide de deux points de terminaison : HTTPs (443) et TDS (1433). Les règles NSG suivantes doivent être configurées pour autoriser l’accès à ces points de terminaison pour la gestion, la surveillance et le fonctionnement correct de votre cluster. Les règles supplémentaires dépendent de vos directives de sécurité.

#### <a name="inbound-nsg-configuration"></a>Configuration NSG entrante

| **Utilisation**   | **From**   | **To**   | **Protocole**   |
| --- | --- | --- | --- |
| Gestion  |[Adresses de gestion ADX](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement(ServiceTag) | Sous-réseau ADX : 443  | TCP  |
| Surveillance de l’intégrité  | [Adresses de surveillance de l’intégrité ADX](#health-monitoring-addresses)  | Sous-réseau ADX : 443  | TCP  |
| Communication interne ADX  | Sous-réseau ADX : Tous les ports  | Sous-réseau ADX : tous les ports  | Tous  |
| Autoriser le trafic entrant provenant de l’équilibreur de charge (probe d’intégrité)  | AzureLoadBalancer  | Sous-réseau ADX : 80, 443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Configuration NSG sortante

| **Utilisation**   | **From**   | **To**   | **Protocole**   |
| --- | --- | --- | --- |
| Dépendance sur le Stockage Azure  | Sous-réseau ADX  | Stockage : 443  | TCP  |
| Dépendance sur Azure Data Lake  | Sous-réseau ADX  | AzureDataLake : 443  | TCP  |
| Ingestion EventHub et surveillance des services  | Sous-réseau ADX  | EventHub : 443, 5671  | TCP  |
| Publier les mesures  | Sous-réseau ADX  | AzureMonitor : 443 | TCP  |
| Active Directory (le cas échéant) | Sous-réseau ADX | AzureActiveDirectory : 443 | TCP |
| Autorité de certification | Sous-réseau ADX | Internet : 80 | TCP |
| Communications internes  | Sous-réseau ADX  | Sous-réseau ADX : tous les ports  | Tous  |
| Ports utilisés pour les plug-ins `sql\_request` et `http\_request`  | Sous-réseau ADX  | Internet : personnalisé  | TCP  |

### <a name="relevant-ip-addresses"></a>Adresses IP principales

#### <a name="azure-data-explorer-management-ip-addresses"></a>Adresses IP de gestion Azure Data Explorer

> [!NOTE]
> Pour les futurs déploiements, utiliser l’étiquette de service AzureDataExplorer

| Région | Adresses |
| --- | --- |
| Centre de l’Australie | 20.37.26.134 |
| Australie Centre 2 | 20.39.99.177 |
| Australie Est | 40.82.217.84 |
| Sud-Australie Est | 20.40.161.39 |
| BrazilSouth | 191.233.25.183 |
| Centre du Canada | 40.82.188.208 |
| Est du Canada | 40.80.255.12 |
| Inde centrale | 40.81.249.251, 104.211.98.159 |
| USA Centre | 40.67.188.68 |
| EUAP USA Centre | 40.89.56.69 |
| Chine orientale 2 | 139.217.184.92 |
| Chine Nord 2 | 139.217.60.6 |
| Asie Est | 20.189.74.103 |
| USA Est | 52.224.146.56 |
| USA Est 2 | 52.232.230.201 |
| EUAP USA Est 2 | 52.253.226.110 |
| France Centre | 40.66.57.91 |
| France Sud | 40.82.236.24 |
| Japon Est | 20.43.89.90 |
| OuJapon Est | 40.81.184.86 |
| Centre de la Corée | 40.82.156.149 |
| Corée du Sud | 40.80.234.9 |
| Centre-Nord des États-Unis | 40.81.45.254 |
| Europe Nord | 52.142.91.221 |
| Afrique du Sud Nord | 102.133.129.138 |
| Afrique du Sud Ouest | 102.133.0.97 |
| États-Unis - partie centrale méridionale | 20.45.3.60 |
| Asie Sud-Est | 40.119.203.252 |
| Inde Sud | 40.81.72.110, 104.211.224.189 |
| Sud du Royaume-Uni | 40.81.154.254 |
| Ouest du Royaume-Uni | 40.81.122.39 |
| US DoD - Centre | 52.182.33.66 |
| USDoD Est | 52.181.33.69 |
| Gouvernement des États-Unis - Arizona | 52.244.33.193 |
| Gouvernement des États-Unis - Texas | 52.243.157.34 |
| USGov Virginia | 52.227.228.88 |
| Centre-USA Ouest | 52.159.55.120 |
| Europe Ouest | 51.145.176.215 |
| Inde Ouest | 40.81.88.112, 104.211.160.120 |
| USA Ouest | 13.64.38.225 |
| USA Ouest 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>Adresses de surveillance de l’intégrité

| Région | Adresses |
| --- | --- |
| Centre de l’Australie | 191.239.64.128 |
| Centre de l’Australie 2 | 191.239.64.128 |
| Australie Est | 191.239.64.128 |
| Sud-Australie Est | 191.239.160.47 |
| Brésil Sud | 23.98.145.105 |
| Centre du Canada | 168.61.212.201 |
| Est du Canada | 168.61.212.201 |
| Inde centrale | 23.99.5.162 |
| USA Centre | 168.61.212.201, 23.101.115.123 |
| EUAP USA Centre | 168.61.212.201, 23.101.115.123 |
| Chine orientale 2 | 40.73.96.39 |
| Chine Nord 2 | 40.73.33.105 |
| Asie Est | 168.63.212.33 |
| USA Est | 137.116.81.189, 52.249.253.174 |
| USA Est 2 | 137.116.81.189, 104.46.110.170 |
| USA Est 2 (EUAP) | 137.116.81.189, 104.46.110.170 |
| France Centre | 23.97.212.5 |
| France Sud | 23.97.212.5 |
| Japon Est | 138.91.19.129 |
| OuJapon Est | 138.91.19.129 |
| Centre de la Corée | 138.91.19.129 |
| Corée du Sud | 138.91.19.129 |
| Centre-Nord des États-Unis | 23.96.212.108 |
| Europe Nord | 191.235.212.69, 40.127.194.147 |
| Afrique du Sud Nord | 104.211.224.189 |
| Afrique du Sud Ouest | 104.211.224.189 |
| États-Unis - partie centrale méridionale | 23.98.145.105, 104.215.116.88 |
| Inde Sud | 23.99.5.162 |
| Asie Sud-Est | 168.63.173.234 |
| Sud du Royaume-Uni | 23.97.212.5 |
| Ouest du Royaume-Uni | 23.97.212.5 |
| US DoD - Centre | 52.238.116.34 |
| USDoD Est | 52.238.116.34 |
| Gouvernement des États-Unis - Arizona | 52.244.48.35 |
| Gouvernement des États-Unis - Texas | 52.238.116.34 |
| USGov Virginia | 23.97.0.26 |
| Centre-USA Ouest | 168.61.212.201 |
| Europe Ouest | 23.97.212.5, 213.199.136.176 |
| Inde Ouest | 23.99.5.162 |
| USA Ouest | 23.99.5.162, 13.88.13.50 |
| USA Ouest 2 | 23.99.5.162, 104.210.32.14, 52.183.35.124 |

## <a name="disable-access-to-azure-data-explorer-from-the-public-ip"></a>Désactiver l’accès à Azure Data Explorer à partir de l’adresse IP publique

Si vous voulez désactiver complètement l’accès à Azure Data Explorer via l’adresse IP publique, créez une autre règle de trafic entrant dans le NSG. Cette règle doit avoir une [priorité](/azure/virtual-network/security-overview#security-rules) inférieure (un nombre plus élevé). 

| **Utilisation**   | **Source** | **Balise du service source** | **Plages de ports source**  | **Destination** | **Plages de ports de destination** | **Protocole** | **Action** | **Priorité** |
| ---   | --- | --- | ---  | --- | --- | --- | --- | --- |
| Désactiver l’accès à partir d’Internet | Étiquette du service | Internet | *  | VirtualNetwork | * | Quelconque | Deny | nombre plus élevé que les règles ci-dessus |

Cette règle vous permet de vous connecter au cluster Azure Data Explorer uniquement via les enregistrements DNS suivants (mappés à l’adresse IP privée de chaque service) :
* `private-[clustername].[geo-region].kusto.windows.net` (moteur)
* `private-ingest-[clustername].[geo-region].kusto.windows.net` (gestion des données)

## <a name="expressroute-setup"></a>Configuration ExpressRoute

Utilisez ExpressRoute pour connecter votre réseau local au réseau virtuel Azure. Une configuration courante consiste à publier l’itinéraire par défaut (0.0.0.0/0) via la session Border Gateway Protocol (BGP). Cela force le trafic sortant du réseau virtuel à être transféré vers le réseau local du client qui peut supprimer le trafic, provoquant ainsi l’interruption des flux sortants. Pour contourner ce paramètre par défaut, [Itinéraire défini par l’utilisateur (UDR)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0) peut être configuré et le tronçon suivant sera *Internet*. Étant donné que l’UDR a la priorité sur le protocole BGP, le trafic est destiné à Internet.

## <a name="securing-outbound-traffic-with-firewall"></a>Sécurisation du trafic sortant avec pare-feu

Si vous souhaitez sécuriser le trafic sortant à l’aide du [Pare-feu Azure](/azure/firewall/overview) ou d’une appliance virtuelle pour limiter les noms de domaine, les noms de domaine complets (FQDN) suivants doivent être autorisés dans le pare-feu.

```
prod.warmpath.msftcloudes.com:443
gcs.prod.monitoring.core.windows.net:443
production.diagnostics.monitoring.core.windows.net:443
graph.windows.net:443
*.update.microsoft.com:443
shavamanifestcdnprod1.azureedge.net:443
login.live.com:443
wdcp.microsoft.com:443
login.microsoftonline.com:443
azureprofilerfrontdoor.cloudapp.net:443
*.core.windows.net:443
*.servicebus.windows.net:443,5671
shoebox2.metrics.nsatc.net:443
prod-dsts.dsts.core.windows.net:443
ocsp.msocsp.com:80
*.windowsupdate.com:80
ocsp.digicert.com:80
go.microsoft.com:80
dmd.metaservices.microsoft.com:80
www.msftconnecttest.com:80
crl.microsoft.com:80
www.microsoft.com:80
adl.windows.com:80
crl3.digicert.com:80
```

> [!NOTE]
> Si vous utilisez le [pare-feu Azure](/azure/firewall/overview), ajoutez une **règle réseau** avec les propriétés suivantes : <br>
> **Protocole** : TCP <br> **Type de source** : Adresse IP <br> **Source** : * <br> **Étiquettes de service** : AzureMonitor <br> **Ports de destination** : 443

Vous devez aussi définir la [table de route](/azure/virtual-network/virtual-networks-udr-overview) sur le sous-réseau avec les [adresses de gestion](#azure-data-explorer-management-ip-addresses) et les [adresses de surveillance de l’intégrité](#health-monitoring-addresses) avec le dernier tronçon *Internet* pour éviter des problèmes d’itinéraire asymétriques.

Par exemple, pour la région **USA Ouest**, les UDR suivants doivent être définis :

| Nom | Préfixe d’adresse | Tronçon suivant |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Déployer un cluster Azure Data Explorer dans votre réseau virtuel avec un modèle Azure Resource Manager

Pour déployer un cluster Azure Data Explorer dans votre réseau virtuel, utilisez le [cluster déployer Azure Data Explorer dans votre modèle Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) de réseau virtuel.

Ce modèle crée le cluster, le réseau virtuel, le sous-réseau, le groupe de sécurité réseau et les adresses IP publiques.
