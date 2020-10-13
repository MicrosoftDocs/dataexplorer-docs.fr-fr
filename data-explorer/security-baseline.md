---
title: Base de référence de sécurité Azure pour Data Explorer
description: Base de référence de sécurité Azure pour Data Explorer
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d56681eaee708fa66c2fc0ceedb08b003e259955
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2020
ms.locfileid: "91941959"
---
# <a name="azure-security-baseline-for-data-explorer"></a>Base de référence de sécurité Azure pour Data Explorer

La base de référence de sécurité Azure pour Data Explorer contient des recommandations pour vous aider à améliorer la posture de sécurité de votre déploiement.

La base de référence pour ce service est tirée du [benchmark de sécurité Azure version 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), qui fournit des recommandations sur la façon de sécuriser vos solutions cloud sur Azure en suivant nos conseils sur les bonnes pratiques.

Pour plus d’informations, consultez [Vue d’ensemble des lignes de base de sécurité Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Contrôle de sécurité : sécurité réseau](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1 : Protéger les ressources à l'aide de groupes de sécurité réseau ou du Pare-feu Azure sur votre réseau virtuel

**Aide** : Azure Data Explorer prend en charge le déploiement d’un cluster dans un sous-réseau de votre réseau virtuel. Cette fonctionnalité vous permet d’appliquer des règles de groupe de sécurité réseau (NSG) sur le trafic de votre cluster Azure Data Explorer, de connecter votre réseau local au sous-réseau du cluster Azure Data Explorer et de sécuriser vos sources de connexion de données (Event Hub et Event Grid) avec des points de terminaison de service.

Comment déployer votre cluster Azure Data Explorer dans un réseau virtuel : https://docs.microsoft.com/azure/data-explorer/vnet-deployment


**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2 : Superviser et journaliser la configuration et le trafic des réseaux virtuels, des sous-réseaux et des cartes réseau

**Aide** : Activez les journaux de flux de groupe de sécurité réseau (NSG) et envoyez les journaux dans un compte de stockage pour auditer le trafic.

Comment activer les journaux de flux NSG : https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Comprendre la sécurité réseau fournie par Azure Security Center : https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="13-protect-critical-web-applications"></a>1.3 : Protéger les applications web critiques

**Aide** : Non applicable. Cette recommandation concerne les applications web s’exécutant sur Azure App Service ou les ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 : Refuser les communications avec des adresses IP connues comme étant malveillantes

**Aide** : Activez Azure DDoS Protection Standard sur le réseau virtuel qui protège vos clusters Data Explorer pour les protéger contre les attaques DDoS. Utilisez la fonctionnalité de renseignement sur les menaces intégrée à Azure Security Center pour refuser les communications avec des adresses IP Internet connues comme étant malveillantes ou inutilisées.

Comment configurer la protection DDoS : https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Comprendre la fonctionnalité de renseignement sur les menaces intégrée à Azure Security Center : https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5 : Journaliser les paquets et les flux réseau

**Aide** : Activez les journaux de flux sur les groupes de sécurité réseau (NSG) utilisés pour protéger votre cluster Azure Data Explorer et envoyez les journaux dans un compte de stockage pour auditer le trafic.

Comment activer les journaux de flux NSG : https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6 : Déployer des systèmes de détection et de prévention des intrusions sur le réseau

**Aide** : Non applicable. Ce contrôle est effectué au niveau du point de terminaison ou du pare-feu.


**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7 : Gérer le trafic à destination de vos applications web

**Aide** : Non applicable. Cette recommandation a trait aux applications web s’exécutant sur Azure App Service ou des ressources de calcul.


**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 : Réduire la complexité et les frais administratifs liés aux règles de sécurité réseau

**Aide** : Utilisez des étiquettes de service de réseau virtuel pour définir des contrôles d’accès réseau sur les groupes de sécurité réseau ou les pare-feu Azure associés à vos clusters Azure Data Explorer. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de la balise de service (par exemple, ApiManagement) dans le champ Source ou Destination approprié d'une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

Comprendre et utiliser les étiquettes de service : https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Exigences de configuration des étiquettes de service pour Azure Data Explorer : https://docs.microsoft.com/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment


**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 : Gérer les configurations de sécurité standard pour les appareils réseau

**Aide** : Le client définit et implémente des configurations de sécurité standard pour les ressources réseau avec Azure Policy.

Le client peut aussi utiliser Azure Blueprints pour simplifier les déploiements Azure à grande échelle en regroupant les artefacts d’environnement clés, comme les modèles ARM, les contrôles RBAC et les stratégies, au sein d’une seule définition de blueprint. Appliquez facilement le blueprint aux nouveaux abonnements et environnements, et ajustez le contrôle et la gestion par le biais du versioning.

Comment configurer et gérer Azure Policy : https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Comment créer un blueprint Azure : https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10 : Documenter les règles de configuration du trafic

**Aide** : Utilisez des étiquettes pour les groupes de sécurité réseau (NSG), et d’autres ressources liées à la sécurité réseau et au flux de trafic pour vos clusters Data Explorer. Concernant les règles NSG individuelles, utilisez le champ « Description » afin de spécifier le besoin métier et/ou la durée (etc.) pour toutes les règles qui autorisent le trafic vers/depuis un réseau.

Comment créer et utiliser des étiquettes : https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 : Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les changements

**Aide** : Utilisez Azure Policy pour valider (et/ou corriger) la configuration des ressources réseau.

Configurer et gérer Azure Policy : https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="logging-and-monitoring"></a>Journalisation et supervision

*Pour plus d’informations, consultez [Contrôle de sécurité : journalisation et supervision](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1 : Utiliser des sources de synchronisation date/heure approuvées

**Aide** : Microsoft gère les sources d’heure des ressources Azure. Les clients peuvent mettre à jour la synchronisation de l’heure de leurs propres déploiements de calcul.

Comment configurer la synchronisation de l’heure pour les ressources de calcul Azure : https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

**Aide** : Azure Data Explorer utilise les journaux de diagnostic pour obtenir des insights sur les réussites et les échecs d’ingestion. Vous pouvez exporter les journaux des opérations vers Stockage Azure, Event Hub ou Log Analytics afin de superviser l’état de l’ingestion.

Comment superviser les opérations d’ingestion d’Azure Data Explorer :

https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Comment ingérer et interroger les données de supervision dans Azure Data Explorer :

https://docs.microsoft.com/azure/data-explorer/ingest-data-no-code

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Aide** : Activez les paramètres de diagnostic pour Azure Data Explorer afin d’accéder aux opérations propres au service et les journaliser. Les journaux d’activité Azure dans Azure Monitor, notamment la journalisation de haut niveau concernant la ressource, sont activés par défaut.

Comment superviser les opérations d’ingestion d’Azure Data Explorer : https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Comment collecter des journaux et des métriques de plateforme avec Azure Monitor : https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Vue d’ensemble des journaux de plateforme Azure : https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview


**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="24-collect-security-logs-from-operating-system"></a>2.4 : Collecter les journaux de sécurité des systèmes d’exploitation

**Aide** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

**Aide** : Dans Azure Monitor, définissez la période de conservation de votre espace de travail Log Analytics en fonction des obligations réglementaires de votre organisation. Utilisez les comptes de stockage Azure pour le stockage à long terme/d’archivage.

Comment définir les paramètres de conservation des journaux pour les espaces de travail Log Analytics : https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

**Aide** : Analysez et supervisez les journaux pour détecter les comportements anormaux et examinez régulièrement les résultats. Après avoir activé les paramètres de diagnostic pour Azure Data Explorer, utilisez l’espace de travail Log Analytics d’Azure Monitor pour examiner les journaux et exécuter des requêtes sur leurs données.

Comprendre l’espace de travail Log Analytics : https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Comment exécuter des requêtes personnalisées dans Azure Monitor : https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7 : Activer les alertes d’activité anormale

**Aide** : Non applicable. Azure Data Explorer n’a pas cette capacité.


**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="28-centralize-anti-malware-logging"></a>2.8 : Centraliser la journalisation anti-programme malveillant

**Aide** : Non applicable. Azure Data Explorer ne traite pas la journalisation anti-programme malveillant.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="29-enable-dns-query-logging"></a>2.9 : Activer la journalisation des requêtes DNS

**Aide** : Non applicable : la requête DNS n’est pas une fonction d’Azure Data Explorer.


**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="210-enable-command-line-audit-logging"></a>2.10 : Activer la journalisation d’audit en ligne de commande

**Aide** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

*Pour plus d’informations, consultez [Contrôle de sécurité : contrôle des accès et des identités](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1 : Tenir un inventaire des comptes d’administration

**Aide** : Dans Azure Data Explorer, les rôles de sécurité définissent les principaux de sécurité (utilisateurs et applications) qui ont l’autorisation d’agir sur une ressource sécurisée, comme une base de données ou une table, et les opérations qui sont autorisées.  Vous pouvez utiliser la requête Kusto pour lister les principaux du rôle d’administrateur pour les bases de données et les clusters Azure Data Explorer.
[Gestion des rôles de sécurité dans Azure Data Explorer à l’aide d’une requête Kusto](kusto/management/security-roles.md)



**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2 : Changer les mots de passe par défaut quand cela est possible

**Aide** : Azure AD n’intègre pas le concept des mots de passe par défaut. Selon le service, d’autres ressources Azure qui exigent un mot de passe forcent la création d’un mot de passe conforme à des exigences de complexité et d’une longueur minimale. Vous êtes responsable des applications tierces et des services de la place de marché susceptibles d’utiliser des mots de passe par défaut.


**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3 : Garantir l’utilisation de comptes d’administration dédiés

**Aide** : Le client crée des procédures standard autour de l’utilisation de comptes d’administration dédiés. Utilisez la gestion des identités et des accès dans Azure Security Center pour superviser le nombre de comptes d’administration.

Les clients peuvent également activer JIT (Juste-à-temps)/JEA (Just-Enough-Access) à l’aide de rôles privilégiés Azure AD Privileged Identity Management pour les services Microsoft et Azure ARM. 

Qu’est-ce qu’Azure AD Privileged Identity Management ? : https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4 : Utiliser l’authentification unique (SSO) avec Azure Active Directory

**Aide** : Dans la mesure du possible, le client utilise SSO avec Azure Active Directory (Azure AD) au lieu de configurer des informations d’identification autonomes individuelles par service. Suivez les recommandations liées à la gestion des identités et des accès dans Azure Security Center.

Comprendre SSO avec Azure AD : https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5 : Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory

**Aide** : Activez l’authentification multifacteur (MFA) Azure Active Directory (Azure AD) et suivez les recommandations de gestion des identités et des accès dans Azure Security Center.

Comment activer l’authentification multifacteur dans Azure : https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted 

Comment superviser les identités et les accès dans Azure Security Center : https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="36-use-of-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6 : Utilisation de machines dédiées (stations de travail avec accès privilégié) pour toutes les tâches administratives

**Aide** : Utilisez des PAW (stations de travail avec accès privilégié) avec l’authentification multifacteur (MFA) configurée pour se connecter aux ressources Azure et les configurer.

En savoir plus sur les stations de travail avec accès privilégié : https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Comment activer MFA dans Azure : https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="37-log-and-alert-on-suspicious-activity-on-administrative-accounts"></a>3.7 : Journaliser et générer des alertes en cas d’activité suspecte sur les comptes d’administration

**Aide** : Utilisez les rapports de sécurité Azure Active Directory (Azure AD) pour générer des journaux et des alertes quand une activité suspecte ou potentiellement dangereuse se produit dans l’environnement. Utiliser Azure Security Center pour superviser l’activité liée aux identités et aux accès

Comment identifier les utilisateurs Azure AD signalés pour une activité à risque : https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Comment superviser l’activité liée aux identités et aux accès des utilisateurs dans Azure Security Center : https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8 : Gérer les ressources Azure à partir d’emplacements approuvés uniquement

**Aide** : Le client utilise des emplacements nommés avec accès conditionnel pour autoriser l’accès uniquement à partir de regroupements logiques spécifiques de plages d’adresses IP ou de pays/régions.

Comment configurer des emplacements nommés dans Azure : https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="39-utilize-azure-active-directory"></a>3.9 : Utiliser Azure Active Directory

**Aide** : Azure Active Directory (Azure AD) est la méthode par défaut pour l’authentification dans Azure Data Explorer. Plusieurs scénarios d’authentification sont ainsi pris en charge :

Authentification de l’utilisateur (ouverture de session interactive) : utilisée pour authentifier les principaux humains.

Authentification de l’application (ouverture de session non interactive) : utilisée pour authentifier les services et les applications qui doivent s’exécuter/s’authentifier en l’absence d’utilisateur humain.

[Vue d’ensemble du contrôle d’accès d’Azure Data Explorer](kusto/management/access-control/index.md)

[Authentification avec Azure Active Directory](kusto/management/access-control/aad.md)



**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 : Revoir et harmoniser régulièrement les accès utilisateur

**Aide** : Azure Active Directory (Azure AD) fournit des journaux pour vous aider à découvrir les comptes obsolètes. De plus, utilisez les révisions d’accès des identités Azure pour gérer efficacement les appartenances aux groupes, les accès aux applications d’entreprise et les attributions de rôles. L’accès de l’utilisateur peut être passé en revue régulièrement pour vérifier que seuls les utilisateurs appropriés bénéficient d’un accès permanent. 

[Comment s’authentifier avec Azure AD pour accéder à Azure Data Explorer](kusto/management/access-control/how-to-authenticate-with-aad.md)

[Génération de rapports Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

[Comment utiliser les révisions d’accès des identités Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)


**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11 : Superviser les tentatives d’accès aux comptes désactivés

**Aide** : Vous pouvez utiliser les sources de journal de l’activité de connexion, de l’audit et des événements à risque d’Azure Active Directory (Azure AD) pour la supervision, ce qui vous permet d’intégrer n’importe quel outil SIEM (Security Information and Event Management) ou de supervision.

 Vous pouvez simplifier ce processus en créant des paramètres de diagnostic pour les comptes d’utilisateur Azure Active Directory, et en envoyant les journaux d’audit et les journaux de connexion à un espace de travail Log Analytics. Le client peut configurer les alertes souhaitées dans un espace de travail Log Analytics.

Comment intégrer des journaux d’activité Azure dans Azure Monitor : https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12 : Alerter en cas d’écart de comportement de connexion à un compte

**Aide** : Utilisez les détections de risque et la fonctionnalité Identity Protection d’Azure Active Directory (Azure AD) pour configurer des réponses automatiques aux actions suspectes détectées liées aux identités d’utilisateur. De plus, vous pouvez ingérer des données dans Azure Sentinel pour approfondir votre examen.

Comment voir les connexions risquées dans Azure AD : https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Comment configurer et activer des stratégies de risque Identity Protection : https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13 : Fournir à Microsoft un accès aux données client pertinentes pendant les scénarios de support

**Aide** : Dans le cadre des scénarios de support où Microsoft a besoin d’accéder aux données client, Customer Lockbox fournit une interface qui permet aux clients de passer en revue, et d’approuver ou refuser les demandes d’accès aux données client.

Comprendre Customer Lockbox :

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Contrôle de sécurité : protection des données](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 : Conserver un inventaire des informations sensibles

**Aide** : Utilisez des étiquettes pour faciliter le suivi des ressources Azure qui stockent ou traitent des informations sensibles.

Créer et utiliser des étiquettes :

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2 : Isoler les systèmes qui stockent ou traitent les informations sensibles

**Conseils** : Implémentez des abonnements et/ou des groupes d’administration distincts pour le développement, les tests et la production. Les clusters Azure Data Explorer doivent être séparés des autres ressources par un réseau virtuel/sous-réseau, étiquetés de manière appropriée et sécurisés dans un groupe de sécurité réseau (NSG) ou un pare-feu Azure. Les clusters Data Explorer qui stockent ou traitent des données sensibles doivent être suffisamment isolés.

Comment créer des abonnements Azure supplémentaires : https://docs.microsoft.com/azure/billing/billing-create-subscription

Comment créer des groupes d’administration : https://docs.microsoft.com/azure/governance/management-groups/create

Comment créer et utiliser des étiquettes : https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Comment déployer votre cluster Azure Data Explorer dans un réseau virtuel : https://docs.microsoft.com/azure/data-explorer/vnet-deployment

Comment créer un NSG avec une configuration de sécurité : https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. : Superviser et bloquer le transfert non autorisé d’informations sensibles

**Aide** : Non applicable. Pour la plateforme sous-jacente qui est gérée par Microsoft, Microsoft traite tout le contenu client comme étant sensible et déploie d’importants efforts pour vous protéger contre la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

Comprendre la protection des données client dans Azure :  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 : Chiffrer toutes les informations sensibles en transit

**Aide** : Le cluster Azure Data Explorer négocie TLS 1.2 par défaut. Assurez-vous que les clients qui se connectent à vos ressources Azure peuvent négocier TLS 1.2 ou une version ultérieure.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

### <a name="45-divuse-an-active-discovery-tool-to-identify-sensitive-datadiv"></a>4.5 : <div>Utiliser un outil de découverte actif pour identifier les données sensibles</div>

**Aide** : Les fonctionnalités d’identification des données, de classification des données et de protection contre la perte de données ne sont pas encore disponibles pour Azure Data Explorer. Implémentez une solution tierce si nécessaire à des fins de conformité.

Pour la plateforme sous-jacente managée par Microsoft, Microsoft considère tout le contenu client comme sensible et met tout en œuvre pour empêcher la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

Comprendre la protection des données client dans Azure :  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="46-divuse-azure-rbac-to-control-access-to-resourcesdiv"></a>4.6 : <div>Utiliser Azure RBAC pour contrôler l’accès aux ressources</div>

**Aide** : Azure Data Explorer vous permet de contrôler l’accès aux bases de données et aux tables à l’aide d’un modèle de contrôle d’accès en fonction du rôle (RBAC). Avec ce modèle, les principaux (utilisateurs, groupes et applications) sont mappés aux rôles. Les principaux peuvent accéder aux ressources selon les rôles auxquels ils sont affectés.

Liste des rôles et autorisations, et des instructions sur la configuration de RBAC pour Azure Data Explorer : https://docs.microsoft.com/azure/data-explorer/manage-database-permissions


**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7 : Utiliser la protection contre la perte de données basée sur l’hôte pour appliquer le contrôle d’accès

**Aide** : Non applicable. Cette recommandation a trait aux ressources de calcul.

Microsoft gère l’infrastructure sous-jacente d’Azure Data Explorer, et a implémenté des contrôles stricts pour empêcher la perte ou l’exposition des données client.

Comprendre la protection des données client dans Azure : https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 : Chiffrer des informations sensibles au repos

**Aide** : Azure Disk Encryption vous aide à protéger et à préserver vos données de façon à répondre aux engagements de votre entreprise en matière de sécurité et de conformité. Il fournit un chiffrement de volume pour le système d’exploitation et les disques de données de vos machines virtuelles de cluster. Il est également intégré à Azure Key Vault, ce qui vous permet de contrôler et gérer les secrets et les clés de chiffrement de disque, et de garantir que toutes les données des disques de machine virtuelle sont chiffrées au repos quand elles se trouvent dans le stockage Azure.

Comment activer le chiffrement au repos pour les clusters Azure Data Explorer : https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Aide** : Utilisez Azure Monitor avec le journal d’activité Azure pour créer des alertes quand des changements au niveau des ressources sont effectués sur vos clusters Azure Data Explorer.

Comment créer des alertes pour les événements du journal d’activité Azure : https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Comment créer des alertes pour les événements du journal d’activité Azure : https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

## <a name="vulnerability-management"></a>Gestion des vulnérabilités

*Pour plus d’informations, consultez [Contrôle de sécurité : Gestion des vulnérabilités](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 : Exécuter les outils d’analyse des vulnérabilités automatisés

**Aide** : Suivez les recommandations d’Azure Security Center sur la sécurisation de vos ressources Azure Data Explorer.

Microsoft assure aussi la gestion des vulnérabilités sur les systèmes sous-jacents qui prennent en charge Azure Data Explorer.

Comprendre les recommandations d’Azure Security Center : https://docs.microsoft.com/azure/security-center/recommendations-reference

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 : Déployer une solution de gestion des correctifs de système d’exploitation automatisée

**Aide** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3 : Déployer une solution de gestion automatisée des correctifs logiciels tiers

**Aide** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 : Comparer les analyses de vulnérabilités dos à dos

**Aide** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="55-utilize-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 : Utilisez un processus de classement des risques pour classer par ordre de priorité la correction des vulnérabilités découvertes.

**Aide** : Utilisez les évaluations de risques par défaut (degré de sécurisation) fournies par Azure Security Center.
Comprendre le degré de sécurisation Azure Security Center : https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

## <a name="inventory-and-asset-management"></a>Gestion des stocks et des ressources

*Pour plus d’informations, consultez [Contrôle de sécurité : Gestion des stocks et des ressources](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-utilize-azure-asset-discovery"></a>6.1 : Utiliser la découverte de ressources Azure

**Aide** : Utilisez Azure Resource Graph pour interroger/découvrir toutes les ressources (telles que calcul, stockage, réseau, ports et protocoles) dans vos abonnements. Vérifiez les autorisations (lecture) appropriées dans votre locataire et répertoriez tous les abonnements Azure, ainsi que les ressources dans vos abonnements.

Bien que les ressources Azure classiques puissent être découvertes via Resource Graph, il est vivement recommandé de créer et d’utiliser des ressources Azure Resource Manager à l’avenir.

Comment créer des requêtes avec Azure Resource Graph : https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Comment voir vos abonnements Azure : https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Comprendre Azure RBAC : https://docs.microsoft.com/azure/role-based-access-control/overview



**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="62-maintain-asset-metadata"></a>6.2 : Gérer les métadonnées de ressources

**Conseils** : Appliquez des balises aux ressources Azure en fournissant des métadonnées pour les organiser de façon logique par catégories.

Comment créer et utiliser des étiquettes : https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3 : Supprimer des ressources Azure non autorisées

**Aide** : Vous pouvez utiliser des conventions de nommage, des étiquettes, des groupes d’administration ou des abonnements séparés, le cas échéant, pour organiser et suivre les ressources. Vous pouvez utiliser Azure Resource Graph pour rapprocher régulièrement l’inventaire et vérifier que les ressources non autorisées sont supprimées de l’abonnement en temps utile. 

Comment créer des abonnements Azure supplémentaires : https://docs.microsoft.com/azure/billing/billing-create-subscription

Créer des groupes d’administration : https://docs.microsoft.com/azure/governance/management-groups/create

Créer et utiliser des balises : https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Comment créer des requêtes avec Azure Resource Graph : https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4 : Tenir un inventaire des ressources Azure et logiciels approuvés

**Aide** : Vous devez créer un inventaire des ressources Azure approuvées et des logiciels approuvés pour les ressources de calcul en fonction des besoins de votre organisation.  


**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5 : Superviser les ressources Azure non approuvées

**Aide** : Vous pouvez utiliser des stratégies Azure pour appliquer des restrictions aux types de ressources pouvant être créés dans les abonnements client en utilisant les définitions de stratégie intégrées suivantes :

- Types de ressources non autorisés

- Types de ressources autorisés

Vous pouvez superviser les événements générés par la stratégie à l’aide des 

journaux d’activité qui peuvent être supervisés avec Azure Monitor.

Par ailleurs, vous pouvez utiliser Azure Resource Graph pour interroger/découvrir des ressources dans les abonnements.

Tutoriel : Créer et gérer des stratégies pour assurer la conformité : https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Démarrage rapide : Exécuter votre première requête Resource Graph à l’aide de l’Explorateur Azure Resource Graph : https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Créez, consultez et gérez des alertes de journal d’activité à l’aide d’Azure Monitor : https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6 : Détecter les applications logicielles non approuvées dans les ressources de calcul

**Conseils** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7 : Supprimer les ressources Azure et les applications logicielles non approuvées

**Aide** : Non applicable. Cette recommandation concerne les ressources de calcul et Azure dans son ensemble.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="68-utilize-only-approved-applications"></a>6.8 : Utiliser seulement des applications approuvées

**Aide** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="69-utilize-only-approved-azure-services"></a>6.9 : Utiliser seulement des services Azure approuvés

**Aide** : Vous pouvez utiliser des stratégies Azure pour appliquer des restrictions aux types de ressources pouvant être créés dans les abonnements client en utilisant les définitions de stratégie intégrées suivantes :

- Types de ressources non autorisés

- Types de ressources autorisés

Tutoriel : Créer et gérer des stratégies pour assurer la conformité : https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Exemples Azure Policy : https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="610-implement-approved-application-list"></a>6.10 : Implémenter une liste d’applications approuvées

**Conseils** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager par le biais de scripts

**Aide** : Utilisez l’accès conditionnel Azure pour limiter la capacité des utilisateurs à interagir avec Azure Resource Manager en configurant « Bloquer l’accès » pour l’application « Gestion Microsoft Azure ». De cette façon, vous empêchez la création de ressources dans vos abonnements Azure et leur modification. 

Gérer l’accès à la gestion Azure avec l’accès conditionnel : https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12 : Limiter la capacité des utilisateurs à exécuter des scripts dans des ressources de calcul

**Aide** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13 : Séparer physiquement ou logiquement des applications à risque élevé

**Conseils** : Non applicable. Cette recommandation a trait aux applications web s’exécutant sur Azure App Service ou des ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

## <a name="secure-configuration"></a>Configuration sécurisée

*Pour plus d’informations, consultez [Contrôle de sécurité : Configuration sécurisée](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1 : Établir des configurations sécurisées pour toutes les ressources Azure

**Aide** : Utilisez des alias Azure Policy pour créer des stratégies personnalisées afin d’auditer ou d’appliquer la configuration de vos ressources Azure. Vous pouvez aussi utiliser des définitions Azure Policy intégrées.

Par ailleurs, Azure Resource Manager a la possibilité d’exporter le modèle au format JSON (JavaScript Object Notation), qui doit être examiné pour vérifier que les configurations répondent/dépassent les exigences de sécurité de votre organisation.

Vous pouvez aussi utiliser les recommandations d’Azure Security Center comme base de référence de configuration sécurisée pour vos ressources Azure.

Affichage des alias Azure Policy disponibles : https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Tutoriel : Créer et gérer des stratégies pour assurer la conformité : https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Exportation monoressource ou multiressource vers un modèle dans le portail Azure : https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal

Recommandations de sécurité - Guide de référence : https://docs.microsoft.com/azure/security-center/recommendations-reference



**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2 : Établir des configurations sécurisées pour votre système d’exploitation

**Aide** : Non applicable. Ces conseils concernent les ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3 : Maintenir des configurations sécurisées pour toutes les ressources Azure

**Aide** : Utilisez Azure Policy [refuser] et [déployer s’il n’existe pas] pour appliquer des paramètres sécurisés à vos ressources Azure.  Vous pouvez utiliser des solutions comme Change Tracking, le tableau de bord de conformité des stratégies ou une solution personnalisée pour identifier facilement les changements de sécurité dans votre environnement.

Comprendre les effets d’Azure Policy : https://docs.microsoft.com/azure/governance/policy/concepts/effects

Créer et gérer des stratégies pour appliquer la conformité : https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Suivre les changements dans votre environnement avec la solution Change Tracking : https://docs.microsoft.com/azure/automation/change-tracking

Obtenir les données de conformité des ressources Azure : https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data



**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4 : Maintenir des configurations sécurisées pour les systèmes d’exploitation

**Aide** : Non applicable. Ces conseils concernent les ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 : Stocker en toute sécurité la configuration des ressources Azure

**Aide** : Utilisez Azure Repos pour stocker et gérer votre code de manière sécurisée, par exemple, les stratégies Azure personnalisées, les modèles Azure Resource Manager, les scripts Desired State Configuration, etc. Pour accéder aux ressources que vous gérez dans Azure DevOps, vous pouvez accorder ou refuser des autorisations à des utilisateurs spécifiques, à des groupes de sécurité intégrés ou à des groupes définis dans Azure Active Directory (Azure AD) s’ils sont intégrés à Azure DevOps, ou à Active Directory s’il est intégré à TFS.

Comment stocker du code dans Azure DevOps : https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

À propos des autorisations et des groupes dans Azure DevOps : https://docs.microsoft.com/azure/devops/organizations/security/about-permissions



**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6 Stocker en toute sécurité des images de système d’exploitation personnalisées

**Aide** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="77-deploy-system-configuration-management-tools"></a>7.7 : Déployer les outils de gestion de la configuration système

**Aide** : Définissez et implémentez des configurations de sécurité standard pour les ressources Azure à l’aide d’Azure Policy. Utilisez des alias Azure Policy pour créer des stratégies personnalisées afin d’auditer ou d’appliquer la configuration réseau de vos ressources Azure. Vous pouvez également utiliser des définitions de stratégie intégrées en lien avec vos ressources spécifiques.  Par ailleurs, vous pouvez utiliser Azure Automation pour déployer les changements de configuration.

Comment configurer et gérer Azure Policy : https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Comment utiliser des alias : https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases



**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8 : Déployer les outils de gestion de la configuration système pour les systèmes d’exploitation

**Aide** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9 : Mettre en place la supervision de la configuration automatique pour les services Azure

**Aide** : Utilisez des alias Azure Policy pour créer des stratégies personnalisées afin de créer des alertes, d’auditer et d’appliquer des configurations système. Utilisez une stratégie Azure [auditer], [refuser] et [déployer si elle n’existe pas] pour appliquer automatiquement des configurations pour vos ressources Azure.

Comment configurer et gérer Azure Policy : https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 : Implémenter la supervision de la configuration automatique pour les systèmes d’exploitation

**Aide** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="711-securely-manage-azure-secrets"></a>7.11 : Gérer les secrets Azure en toute sécurité

**Aide** : Azure Disk Encryption fournit un chiffrement de volume pour les disques OS et de données de vos machines virtuelles de cluster Azure Data Explorer. Il est également intégré à Azure Key Vault, ce qui vous permet de contrôler et gérer les secrets et les clés de chiffrement de disque, et de garantir que toutes les données des disques de machine virtuelle sont chiffrées au repos quand elles se trouvent dans le stockage Azure.

Comment sécuriser votre cluster dans Azure Data Explorer : https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Microsoft

### <a name="712-securely-and-automatically-manage-identities"></a>7.12 : Gérer les identités de façon sécurisée et automatique

**Aide** : Utilisez des identités managées pour fournir aux services Azure une identité gérée automatiquement dans Azure AD. Les identités managées vous permettent de vous authentifier sur tous les services qui prennent en charge l’authentification Azure AD, y compris Key Vault, sans informations d’identification dans votre code. Comment configurer les identités managées : https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Configurer des identités managées pour votre cluster Azure Data Explorer : https://docs.microsoft.com/azure/data-explorer/managed-identities


**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13 : Éliminer l’exposition involontaire des informations d’identification

**Conseils** : Exécuter le moteur d’analyse des informations d’identification pour identifier les informations d’identification dans le code. Le moteur d’analyse des informations d’identification encourage également le déplacement des informations d’identification découvertes vers des emplacements plus sécurisés, tels qu’Azure Key Vault. 

Configuration de Credential Scanner : https://secdevtools.azurewebsites.net/helpcredscan.html

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="malware-defense"></a>Défense contre les programmes malveillants

*Pour plus d’informations, consultez [Contrôle de sécurité : Défense contre les programmes malveillants](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-utilize-centrally-managed-anti-malware-software"></a>8.1 : Utiliser un logiciel anti-programme malveillant géré de manière centralisée

**Aide** : Non applicable. Cette recommandation a trait aux ressources de calcul.

Le logiciel anti-programme malveillant de Microsoft est activé sur l’hôte sous-jacent qui prend en charge les services Azure (par exemple, Azure Data Explorer), mais il ne s’exécute pas sur le contenu client.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 : Pré-analyser les fichiers à charger sur des ressources Azure non liées au calcul

**Aide** : Le logiciel anti-programme malveillant de Microsoft est activé sur l’hôte sous-jacent qui prend en charge les services Azure (par exemple, Azure Data Explorer), mais il ne s’exécute pas sur le contenu client.

Préanalysez tout le contenu chargé dans des ressources Azure non liées au calcul, comme Azure Data Explorer, Data Lake Storage, Stockage Blob, Azure Database pour PostgreSQL, etc. Microsoft ne peut pas accéder à vos données dans ces instances.



**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 : Vérifier que les logiciels et signatures anti-programme malveillant sont à jour

**Aide** : Non applicable. Cette recommandation a trait aux ressources de calcul.

Le logiciel anti-programme malveillant de Microsoft est activé sur l’hôte sous-jacent qui prend en charge les services Azure, mais il ne s’exécute pas sur le contenu client.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

## <a name="data-recovery"></a>Récupération des données

*Pour plus d’informations, consultez [Contrôle de sécurité : récupération de données](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1 : Mettre en place des sauvegardes automatiques régulières

**Aide** : Les données de votre compte de stockage Microsoft Azure utilisé par votre cluster Data Explorer sont toujours répliquées pour assurer la durabilité et la haute disponibilité. Stockage Azure copie vos données afin qu’elles soient protégées contre les événements planifiés ou non, notamment les défaillances matérielles temporaires, les pannes de réseau ou de courant et les catastrophes naturelles massives. Vous pouvez choisir de répliquer vos données dans le même centre de données, dans des centres de données zonaux d’une même région ou entre des régions géographiques différentes.

Comprendre la redondance et les contrats de niveau de service du stockage Azure : https://docs.microsoft.com/azure/storage/common/storage-redundancy

[Exporter des données dans le stockage](kusto/management/data-export/export-data-to-storage.md)



**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : Effectuer des sauvegardes complètes du système et sauvegarder les clés gérées par le client

**Aide** : Azure Data Explorer chiffre toutes les données dans un compte de stockage au repos. Par défaut, les données sont chiffrées avec des clés managées par Microsoft Pour plus de contrôle sur les clés de chiffrement, vous pouvez fournir des clés gérées par le client à utiliser pour le chiffrement des données. Les clés gérées par le client doivent être stockées dans Azure Key Vault. 

Configurer des clés gérées par le client à l’aide de C# : https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Configurer des clés gérées par le client à l’aide du modèle Azure Resource Manager : https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager

Comment sauvegarder des certificats Azure Key Vault : https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0


**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : Valider toutes les sauvegardes, y compris les clés gérées par le client

**Aide** : Testez régulièrement la restauration des données de vos secrets Azure Key Vault.

Comment restaurer des certificats Azure Key Vault :

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0 

Configurer des clés gérées par le client à l’aide de C# : https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Configurer des clés gérées par le client à l’aide du modèle Azure Resource Manager : https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : Garantir la protection des sauvegardes et des clés gérées par le client

**Aide** : Le client peut activer la suppression réversible dans Key Vault pour protéger les clés contre une suppression accidentelle ou malveillante.  Vous pouvez aussi activer la protection contre le vidage de sorte que si un coffre ou un objet est dans l’état supprimé, il ne peut pas être purgé avant la fin de la période de conservation.  

Comment activer la suppression réversible et la protection contre le vidage dans Key Vault : https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete

Configurer des clés gérées par le client à l’aide de C# : https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Configurer des clés gérées par le client à l’aide du modèle Azure Resource Manager : https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Contrôle de sécurité : réponse aux incidents](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10.1 : Créer un guide de réponse aux incidents

**Conseils** : Créez un guide de réponse aux incidents pour votre organisation. Assurez-vous qu’il existe des plans de réponse aux incidents écrits qui définissent tous les rôles du personnel, ainsi que les phases de gestion des incidents, depuis la détection jusqu’à la revue une fois l’incident terminé.

Aide sur la création de votre propre processus de réponse aux incidents de sécurité : https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomie d’un incident dans le centre de réponse aux incidents de sécurité Microsoft : https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

Le client peut également tirer parti du guide de gestion des incidents de sécurité informatique du NIST pour faciliter la création de son propre plan de réponse aux incidents : https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2 : Créer une procédure de scoring et de hiérarchisation des incidents

**Aide** : Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend du niveau de confiance que Security Center accorde à la recherche ou aux données analytiques utilisées pour émettre l’alerte, mais aussi de l’intention malveillante estimée de l’activité à l’origine de l’alerte. 

En outre, marquez clairement les abonnements (par ex. production, non-production) à l’aide d’étiquettes et créez un système de nommage pour identifier et classer clairement les ressources Azure, en particulier celles qui traitent des données sensibles.  Il vous incombe de hiérarchiser le traitement des alertes en fonction de la criticité des ressources et de l’environnement Azure où l’incident s’est produit.

Alertes de sécurité dans Azure Security Center : https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

Utilisez des étiquettes pour organiser vos ressources Azure : https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="103-test-security-response-procedures"></a>10.3 : Tester les procédures de réponse de sécurité

**Aide** : Effectuez des exercices pour tester les capacités de réponse aux incidents de vos systèmes à intervalles réguliers, afin de protéger vos ressources Azure. Identifiez les points faibles et les lacunes, et révisez le plan en fonction des besoins.

Reportez-vous à la publication du NIST : « Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities » : https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4 : Fournir les informations de contact pour les incidents de sécurité et configurer des notifications d’alerte&nbsp;pour les incidents de sécurité

**Aide** : Microsoft utilisera les informations de contact pour le signalement d’incidents de sécurité pour vous contacter si le Microsoft Security Response Center (MSRC) découvre que vos données ont été consultées de manière illégale ou par un tiers non autorisé. Examinez les incidents après les faits pour vous assurer que les problèmes sont résolus.
    

Comment définir le contact de sécurité d’Azure Security Center : https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5 : Intégrer des alertes de sécurité à votre système de réponse aux incidents

**Aide** : Exportez vos alertes et recommandations Azure Security Center en utilisant la fonctionnalité d’exportation continue pour identifier les risques pesant sur les ressources Azure. L’exportation continue vous permet d’exporter les alertes et les recommandations manuellement, ou automatiquement de manière continue. Vous pouvez utiliser le connecteur de données Azure Security Center pour diffuser en continu les alertes vers Azure Sentinel.

Comment configurer l’exportation continue : https://docs.microsoft.com/azure/security-center/continuous-export

Comment diffuser en continu des alertes dans Azure Sentinel : https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6 : Automatiser la réponse aux alertes de sécurité

**Aide** : Utilisez la fonctionnalité d’automatisation de flux de travail disponible dans Azure Security Center pour déclencher automatiquement des réponses via « Logic Apps » aux alertes et aux recommandations de sécurité afin de protéger vos ressources Azure.

Comment configurer l’automatisation des workflows et Logic Apps : https://docs.microsoft.com/azure/security-center/workflow-automation

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Tests d’intrusion et exercices Red Team

*Pour plus d’informations, consultez [Contrôle de sécurité : tests d’intrusion et exercices Red Team](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1 : Effectuer régulièrement des tests d’intrusion de vos ressources Azure et résoudre tous les problèmes de sécurité critiques détectés dans un délai de 60 jours

**Aide** : Respectez les règles d’engagement de Microsoft pour garantir que vos tests d’intrusion sont conformes aux stratégies Microsoft : https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Vous trouverez plus d’informations sur la stratégie de Microsoft et l’exécution de Red Teaming et des tests de pénétration de site actif sur l’infrastructure, les services et les applications cloud managés par Microsoft ici : https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

## <a name="next-steps"></a>Étapes suivantes

- Consultez le [benchmark de sécurité Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Apprenez-en davantage sur les [Bases de référence de la sécurité Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
