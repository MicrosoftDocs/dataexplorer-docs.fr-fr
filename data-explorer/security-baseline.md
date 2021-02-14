---
title: Base de référence de sécurité Azure pour Azure Data Explorer
description: La base de référence de sécurité d’Azure Data Explorer fournit des instructions et des ressources pour l’implémentation des recommandations de sécurité spécifiées dans le Benchmark de sécurité Azure.
author: msmbaldwin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c6cf97837f0da34a63f3b9274d160650ab2349fb
ms.sourcegitcommit: c11e3871d600ecaa2824ad78bce9c8fc5226eef9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99554914"
---
# <a name="azure-security-baseline-for-azure-data-explorer"></a>Base de référence de sécurité Azure pour Azure Data Explorer

Cette base de référence de sécurité applique les instructions du [Benchmark de sécurité Azure version 1.0](/azure/security/benchmarks/overview-v1) à Azure Data Explorer. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure.
Le contenu est regroupé selon les **contrôles de sécurité** définis par le Benchmark de sécurité Azure et les conseils associés applicables à Azure Data Explorer. Les **contrôles** non applicables à Azure Data Explorer ont été exclus.

 
Pour voir comment Azure Data Explorer est entièrement mappé au Benchmark de sécurité Azure, consultez le [fichier de mappage complet de la base de référence de sécurité d’Azure Data Explorer](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : sécurité réseau](/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1 : Protéger les ressources Azure au sein des réseaux virtuels

**Aide** : Azure Data Explorer prend en charge le déploiement d’un cluster dans un sous-réseau de votre réseau virtuel. Cette fonctionnalité vous permet d’appliquer des règles de groupe de sécurité réseau (NSG) sur le trafic de votre cluster Azure Data Explorer, de connecter votre réseau local au sous-réseau du cluster Azure Data Explorer et de sécuriser vos sources de connexion de données (Event Hub et Event Grid) avec des points de terminaison de service.

- [Comment créer un cluster dans votre réseau virtuel](./vnet-create-cluster-portal.md)

- [Comment déployer votre cluster Azure Data Explorer dans un réseau virtuel](./vnet-deployment.md)

- [Comment résoudre les problèmes liés à la création, à la connectivité et au fonctionnement du cluster de votre réseau virtuel](./vnet-deploy-troubleshoot.md?tabs=windows)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2 : Superviser et journaliser la configuration et le trafic des réseaux virtuels, des sous-réseaux et des interfaces réseau

**Aide** : Activez les journaux de flux de groupe de sécurité réseau (NSG) et envoyez les journaux dans un compte de stockage pour auditer le trafic.

- [Guide pratique pour activer les journaux de flux NSG](/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Présentation de la sécurité réseau fournie par Azure Security Center](/azure/security-center/security-center-network-recommendations)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 : Refuser les communications avec des adresses IP connues comme étant malveillantes

**Conseils** : Activez Azure DDoS Protection Standard sur le réseau virtuel qui protège vos clusters Azure Data Explorer pour les protéger contre les attaques DDoS. Utilisez la fonctionnalité de renseignement sur les menaces intégrée à Azure Security Center pour refuser les communications avec des adresses IP Internet connues comme étant malveillantes ou inutilisées.

- [Guide pratique pour configurer la protection DDoS](/azure/virtual-network/manage-ddos-protection)

- [Présentation de la fonctionnalité Threat Intelligence intégrée à Azure Security Center](/azure/security-center/security-center-alerts-service-layer)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="15-record-network-packets"></a>1.5 : Enregistrer les paquets réseau

**Aide** : Activez les journaux de flux sur les groupes de sécurité réseau (NSG) utilisés pour protéger votre cluster Azure Data Explorer et envoyez les journaux dans un compte de stockage pour auditer le trafic.

- [Guide pratique pour activer les journaux de flux NSG](/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 : Réduire la complexité et les frais administratifs liés aux règles de sécurité réseau

**Aide** : Utilisez des étiquettes de service de réseau virtuel pour définir des contrôles d’accès réseau sur les groupes de sécurité réseau ou les pare-feu Azure associés à vos clusters Azure Data Explorer. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de la balise de service (par exemple, ApiManagement) dans le champ Source ou Destination approprié d'une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

- [Comprendre et utiliser les étiquettes de service](/azure/virtual-network/service-tags-overview)

- [Exigences de configuration des étiquettes de service pour Azure Data Explorer](./vnet-deployment.md#dependencies-for-vnet-deployment)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 : Gérer les configurations de sécurité standard pour les périphériques réseau

**Aide** : Le client définit et implémente des configurations de sécurité standard pour les ressources réseau avec Azure Policy.

Le client peut aussi utiliser Azure Blueprints pour simplifier les déploiements Azure à grande échelle en regroupant les artefacts d’environnement clés, comme les modèles Azure Resource Manager, les contrôles Azure RBAC et les affectations Azure Policy, au sein d’une même définition de blueprint. Appliquez facilement le blueprint aux nouveaux abonnements et environnements, et ajustez le contrôle et la gestion par le biais du versioning.

- [Guide pratique pour configurer et gérer Azure Policy](/azure/governance/policy/tutorials/create-and-manage)

- [Guide pratique pour créer un blueprint Azure](/azure/governance/blueprints/create-blueprint-portal)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10 : Règles de configuration du trafic de documents

**Conseils** : Utilisez des étiquettes pour les groupes de sécurité réseau et les autres ressources liées à la sécurité réseau et au flux de trafic pour vos clusters Azure Data Explorer. Concernant les règles NSG individuelles, utilisez le champ « Description » afin de spécifier le besoin métier et/ou la durée (etc.) pour toutes les règles qui autorisent le trafic vers/depuis un réseau.

- [Guide pratique pour créer et utiliser des étiquettes](/azure/azure-resource-manager/resource-group-using-tags)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 : Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les modifications

**Aide** : Utilisez Azure Policy pour valider (et/ou corriger) la configuration des ressources réseau.

- [Guide pratique pour configurer et gérer Azure Policy](/azure/governance/policy/tutorials/create-and-manage)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="logging-and-monitoring"></a>Journalisation et supervision

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : journalisation et supervision](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

**Aide** : Azure Data Explorer utilise les journaux de diagnostic pour obtenir des insights sur les réussites et les échecs d’ingestion. Vous pouvez exporter les journaux des opérations vers Stockage Azure, Event Hub ou Log Analytics afin de superviser l’état de l’ingestion.

- [Comment superviser les opérations d’ingestion d’Azure Data Explorer](./using-diagnostic-logs.md)

- [Comment ingérer et interroger les données de supervision dans Azure Data Explorer](./ingest-data-no-code.md)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Aide** : Activez les paramètres de diagnostic pour Azure Data Explorer afin d’accéder aux opérations propres au service et les journaliser. Les journaux d’activité Azure dans Azure Monitor, notamment la journalisation de haut niveau concernant la ressource, sont activés par défaut.

- [Comment superviser les opérations d’ingestion d’Azure Data Explorer](./using-diagnostic-logs.md)

- [Guide pratique pour collecter des journaux et des métriques de plateforme avec Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Vue d’ensemble des journaux de plateforme Azure](/azure/azure-monitor/platform/platform-logs-overview)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

**Aide** : Dans Azure Monitor, définissez la période de conservation de votre espace de travail Log Analytics en fonction des obligations réglementaires de votre organisation. Utilisez les comptes de stockage Azure pour le stockage à long terme/d’archivage.

- [Définir les paramètres de conservation des journaux pour les espaces de travail Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

**Conseils** : Analysez et supervisez les journaux pour détecter les comportements anormaux et examinez régulièrement les résultats. Après avoir activé les paramètres de diagnostic pour Azure Data Explorer, utilisez l’espace de travail Log Analytics d’Azure Monitor pour examiner les journaux et exécuter des requêtes sur leurs données.

- [Comprendre l’espace de travail Log Analytics](/azure/azure-monitor/log-query/get-started-portal)

- [Guide pratique pour effectuer des requêtes personnalisées dans Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : contrôle des accès et des identités](/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 : Tenir un inventaire des comptes d’administration

**Conseils** : Dans Azure Data Explorer, les rôles de sécurité définissent les principaux de sécurité (utilisateurs et applications) qui ont l’autorisation d’agir sur une ressource sécurisée, comme une base de données ou une table, et les opérations qui sont autorisées. Vous pouvez utiliser la requête Kusto pour lister les principaux du rôle d’administrateur pour les bases de données et les clusters Azure Data Explorer.

- [Gestion des rôles de sécurité dans Azure Data Explorer à l’aide d’une requête Kusto](/azure/kusto/management/security-roles)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2 : Modifier les mots de passe par défaut lorsque cela est possible

**Aide** : Azure Active Directory (Azure AD) n’intègre pas le concept des mots de passe par défaut. Selon le service, d’autres ressources Azure qui exigent un mot de passe forcent la création d’un mot de passe conforme à des exigences de complexité et d’une longueur minimale. Vous êtes responsable des applications tierces et des services de la place de marché susceptibles d’utiliser des mots de passe par défaut.

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3 : Utiliser des comptes d’administration dédiés

**Aide** : Le client crée des procédures standard autour de l’utilisation de comptes d’administration dédiés. Utilisez la gestion des identités et des accès dans Azure Security Center pour superviser le nombre de comptes d’administration.

Les clients peuvent également activer Just-In-Time (Juste-à-temps)/Just-Enough-Access (Accès minimal nécessaire) en utilisant des rôles privilégiés Azure AD Privileged Identity Management pour les services Microsoft et Azure ARM. 

- [Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4 : Utiliser l’authentification unique (SSO) Azure Active Directory

**Aide** : Dans la mesure du possible, le client utilise SSO avec Azure Active Directory (Azure AD) au lieu de configurer des informations d’identification autonomes individuelles par service. Suivez les recommandations liées à la gestion des identités et des accès dans Azure Security Center.

- [Présentation de l’authentification unique avec Azure AD](/azure/active-directory/manage-apps/what-is-single-sign-on)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 : Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory

**Aide** : Activez l’authentification multifacteur (MFA) Azure Active Directory (Azure AD) et suivez les recommandations de gestion des identités et des accès dans Azure Security Center.

- [Guide pratique pour activer l’authentification MFA dans Azure](/azure/active-directory/authentication/howto-mfa-getstarted)

- [Guide pratique pour superviser les identités et les accès dans Azure Security Center](/azure/security-center/security-center-identity-access)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6 : Utiliser des stations de travail sécurisées et gérées par Azure pour les tâches administratives

**Aide** : Utilisez des PAW (stations de travail avec accès privilégié) avec l’authentification multifacteur (MFA) configurée pour se connecter aux ressources Azure et les configurer.

- [En savoir plus sur les stations de travail à accès privilégié](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Guide pratique pour activer l’authentification MFA dans Azure](/azure/active-directory/authentication/howto-mfa-getstarted)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7 : Journaliser et générer des alertes en cas d’activités suspectes sur des comptes d’administration

**Aide** : Utilisez les rapports de sécurité Azure Active Directory (Azure AD) pour générer des journaux et des alertes quand une activité suspecte ou potentiellement dangereuse se produit dans l’environnement. Utiliser Azure Security Center pour superviser l’activité liée aux identités et aux accès

- [Guide pratique pour identifier les utilisateurs Azure AD pour lesquels une activité à risque a été signalée](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Guide pratique pour superviser l’activité liée aux identités et aux accès des utilisateurs dans Azure Security Center](/azure/security-center/security-center-identity-access)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8 : Gérer les ressources Azure à partir des emplacements approuvés uniquement

**Aide** : Le client utilise des emplacements nommés avec accès conditionnel pour autoriser l’accès uniquement à partir de regroupements logiques spécifiques de plages d’adresses IP ou de pays/régions.

- [Guide pratique pour configurer des emplacements nommés dans Azure](/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="39-use-azure-active-directory"></a>3.9 : Utiliser Azure Active Directory

**Aide** : Azure Active Directory (Azure AD) est la méthode par défaut pour l’authentification dans Azure Data Explorer. Azure AD prend en charge plusieurs scénarios d’authentification :

- Authentification de l’utilisateur (ouverture de session interactive) : utilisée pour authentifier les principaux humains.

- Authentification de l’application (ouverture de session non interactive) : utilisée pour authentifier les services et les applications qui doivent s’exécuter/s’authentifier en l’absence d’utilisateur humain.

Pour plus d’informations, consultez les références suivantes :

- [Vue d’ensemble du contrôle d’accès d’Azure Data Explorer](/azure/kusto/management/access-control)

- [Authentification avec Azure Active Directory](/azure/kusto/management/access-control/aad)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 : Examiner et rapprocher régulièrement l’accès utilisateur

**Aide** : Azure Active Directory (Azure AD) fournit des journaux pour vous aider à découvrir les comptes obsolètes. De plus, utilisez les révisions d’accès des identités Azure pour gérer efficacement les appartenances aux groupes, les accès aux applications d’entreprise et les attributions de rôles. L’accès de l’utilisateur peut être passé en revue régulièrement pour vérifier que seuls les utilisateurs appropriés bénéficient d’un accès permanent. 

- [Comment s’authentifier avec Azure AD pour accéder à Azure Data Explorer](/azure/kusto/management/access-control/how-to-authenticate-with-aad)

- [Génération de rapports Azure AD](/azure/active-directory/reports-monitoring/)

- [Comment utiliser les révisions d’accès des identités Azure](/azure/active-directory/governance/access-reviews-overview)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11 : Superviser les tentatives d’accès à des informations d’identification désactivées

**Aide** : Vous pouvez utiliser les sources de journal de l’activité de connexion, de l’audit et des événements à risque d’Azure Active Directory (Azure AD) pour la supervision, ce qui vous permet d’intégrer n’importe quel outil SIEM (Security Information and Event Management) ou de supervision.

 Vous pouvez simplifier ce processus en créant des paramètres de diagnostic pour les comptes d’utilisateur Azure Active Directory, et en envoyant les journaux d’audit et les journaux de connexion à un espace de travail Log Analytics. Le client peut configurer les alertes souhaitées dans un espace de travail Log Analytics.

- [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12 : Alerter en cas d’écart de comportement de connexion à un compte

**Aide** : Utilisez les détections de risque et la fonctionnalité Identity Protection d’Azure Active Directory (Azure AD) pour configurer des réponses automatiques aux actions suspectes détectées liées aux identités d’utilisateur. De plus, vous pouvez ingérer des données dans Azure Sentinel pour approfondir votre examen.

- [Guide pratique pour afficher les connexions risquées Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Guide pratique pour configurer et activer des stratégies de risque Identity Protection](/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13 : Fournir à Microsoft un accès aux données client pertinentes pendant les scénarios de support

**Aide** : Dans le cadre des scénarios de support où Microsoft a besoin d’accéder aux données client, Customer Lockbox fournit une interface qui permet aux clients de passer en revue, et d’approuver ou refuser les demandes d’accès aux données client.

- [Présentation de Customer Lockbox](/azure/security/fundamentals/customer-lockbox-overview)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : protection des données](/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 : Conserver un inventaire des informations sensibles

**Conseils** : Utilisez des étiquettes pour faciliter le suivi des ressources Azure qui stockent ou traitent des informations sensibles.

- [Guide pratique pour créer et utiliser des étiquettes](/azure/azure-resource-manager/resource-group-using-tags)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2 : Isoler les systèmes qui stockent ou traitent les informations sensibles

**Conseils** : Implémentez des abonnements et/ou des groupes d’administration distincts pour le développement, les tests et la production. Les clusters Azure Data Explorer doivent être séparés des autres ressources par un réseau virtuel/sous-réseau, étiquetés de manière appropriée et sécurisés dans un groupe de sécurité réseau (NSG) ou un pare-feu Azure. Les clusters Azure Data Explorer qui stockent ou traitent des données sensibles doivent être suffisamment isolés.

- [Guide pratique pour créer des abonnements Azure supplémentaires](/azure/billing/billing-create-subscription)

- [Guide pratique pour créer des groupes d’administration](/azure/governance/management-groups/create)

- [Guide pratique pour créer et utiliser des étiquettes](/azure/azure-resource-manager/resource-group-using-tags)

- [Comment déployer votre cluster Azure Data Explorer dans un réseau virtuel](./vnet-deployment.md)

- [Guide pratique pour créer un groupe NSG avec une configuration de sécurité](/azure/virtual-network/tutorial-filter-network-traffic)

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 : Chiffrer toutes les informations sensibles en transit

**Aide** : Le cluster Azure Data Explorer négocie TLS 1.2 par défaut. Assurez-vous que les clients qui se connectent à vos ressources Azure peuvent négocier TLS 1.2 ou une version ultérieure.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 : Utiliser un outil de découverte actif pour identifier les données sensibles

**Aide** : Les fonctionnalités d’identification des données, de classification des données et de protection contre la perte de données ne sont pas encore disponibles pour Azure Data Explorer. Implémentez une solution tierce si nécessaire à des fins de conformité.

Pour la plateforme sous-jacente managée par Microsoft, Microsoft considère tout le contenu client comme sensible et met tout en œuvre pour empêcher la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

- [Présentation de la protection des données client dans Azure](/azure/security/fundamentals/protection-customer-data)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6 : Utiliser le contrôle d’accès en fonction du rôle pour contrôler l’accès aux ressources

**Conseils** : Azure Data Explorer vous permet de contrôler l’accès aux bases de données et aux tables en utilisant un modèle de contrôle d’accès en fonction du rôle (RBAC) Azure. Avec ce modèle, les principaux (utilisateurs, groupes et applications) sont mappés aux rôles. Les principaux peuvent accéder aux ressources selon les rôles auxquels ils sont affectés.

- [Liste des rôles et des autorisations, et des instructions sur la configuration de RBAC pour Azure Data Explorer](./manage-database-permissions.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 : Chiffrer des informations sensibles au repos

**Aide** : Azure Disk Encryption vous aide à protéger et à préserver vos données de façon à répondre aux engagements de votre entreprise en matière de sécurité et de conformité. Il fournit un chiffrement de volume pour le système d’exploitation et les disques de données de vos machines virtuelles de cluster. Il est également intégré à Azure Key Vault, ce qui vous permet de contrôler et gérer les secrets et les clés de chiffrement de disque, et de garantir que toutes les données des disques de machine virtuelle sont chiffrées au repos quand elles se trouvent dans le stockage Azure.

- [Comment activer le chiffrement au repos pour les clusters Azure Data Explorer](./cluster-disk-encryption.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Aide** : Utilisez Azure Monitor avec le journal d’activité Azure pour créer des alertes quand des changements au niveau des ressources sont effectués sur vos clusters Azure Data Explorer.

- [Guide pratique pour créer des alertes sur les événements du journal d’activité Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

## <a name="vulnerability-management"></a>Gestion des vulnérabilités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des vulnérabilités](/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 : Utilisez un processus de classement des risques pour classer par ordre de priorité la correction des vulnérabilités découvertes.

**Conseils** : Utilisez les évaluations de risques par défaut (degré de sécurisation) fournies par Azure Security Center.

- [Présentation du degré de sécurisation Azure Security Center](/azure/security-center/security-center-secure-score)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

## <a name="inventory-and-asset-management"></a>Gestion des stocks et des ressources

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des stocks et des ressources](/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1 : Utiliser la solution de détection automatisée des ressources

**Conseils** : Utilisez Azure Resource Graph pour interroger et découvrir toutes les ressources dans vos abonnements. Vérifiez les autorisations (lecture) appropriées dans votre locataire et répertoriez tous les abonnements Azure, ainsi que les ressources dans vos abonnements.

Bien que les ressources Azure classiques puissent être découvertes via Resource Graph, il est vivement recommandé de créer et d’utiliser des ressources Azure Resource Manager à l’avenir.

- [Guide pratique pour créer des requêtes avec Azure Resource Graph](/azure/governance/resource-graph/first-query-portal)

- [Guide pratique pour afficher ses abonnements Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Présentation d’Azure RBAC](/azure/role-based-access-control/overview)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="62-maintain-asset-metadata"></a>6.2 : Gérer les métadonnées de ressources

**Conseils** : Appliquez des balises aux ressources Azure en fournissant des métadonnées pour les organiser de façon logique par catégories.

- [Guide pratique pour créer et utiliser des étiquettes](/azure/azure-resource-manager/resource-group-using-tags)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3 : Supprimer des ressources Azure non autorisées

**Aide** : Vous pouvez utiliser des conventions de nommage, des étiquettes, des groupes d’administration ou des abonnements séparés, le cas échéant, pour organiser et suivre les ressources. Vous pouvez utiliser Azure Resource Graph pour rapprocher régulièrement l’inventaire et vérifier que les ressources non autorisées sont supprimées de l’abonnement en temps utile. 

- [Guide pratique pour créer des abonnements Azure supplémentaires](/azure/billing/billing-create-subscription)

- [Guide pratique pour créer des groupes d’administration](/azure/governance/management-groups/create)

- [Guide pratique pour créer et utiliser des étiquettes](/azure/azure-resource-manager/resource-group-using-tags)

- [Guide pratique pour créer des requêtes avec Azure Resource Graph](/azure/governance/resource-graph/first-query-portal)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4 : Définir et tenir un inventaire des ressources Azure approuvées

**Aide** : Vous devez créer un inventaire des ressources Azure approuvées et des logiciels approuvés pour les ressources de calcul en fonction des besoins de votre organisation.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5 : Analyser les ressources Azure non approuvées

**Conseils** : Vous pouvez utiliser des stratégies Azure pour appliquer des restrictions aux types de ressources qui peuvent être créées dans les abonnements client en utilisant les définitions de stratégie intégrées suivantes :

- Types de ressources non autorisés

- Types de ressources autorisés

Vous serez en mesure de superviser les événements générés par la stratégie en utilisant les journaux d’activité qui peuvent être supervisés avec Azure Monitor.

En outre, vous pouvez utiliser Azure Resource Graph pour interroger/découvrir des ressources dans les abonnements.

- [Créer et gérer des stratégies pour assurer la conformité](/azure/governance/policy/tutorials/create-and-manage)

- [Démarrage rapide : Exécuter votre première requête Resource Graph en utilisant l’Explorateur Azure Resource Graph](/azure/governance/resource-graph/first-query-portal)

- [Créer, afficher et gérer des alertes de journal d’activité à l’aide d’Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="69-use-only-approved-azure-services"></a>6.9 : Utiliser des services Azure approuvés uniquement

**Aide** : Vous pouvez utiliser des stratégies Azure pour appliquer des restrictions aux types de ressources pouvant être créés dans les abonnements client en utilisant les définitions de stratégie intégrées suivantes :

 - Types de ressources non autorisés

 - Types de ressources autorisés

Pour plus d’informations, consultez les références suivantes :

- [Créer et gérer des stratégies pour assurer la conformité](/azure/governance/policy/tutorials/create-and-manage)

- [Exemples Azure Policy](/azure/governance/policy/samples/built-in-policies#general)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager

**Aide** : Utilisez l’accès conditionnel Azure pour limiter la capacité des utilisateurs à interagir avec Azure Resource Manager en configurant « Bloquer l’accès » pour l’application « Gestion Microsoft Azure ». De cette façon, vous empêchez la création de ressources dans vos abonnements Azure et leur modification. 

- [Gérer l’accès à la gestion Azure avec l’accès conditionnel](/azure/role-based-access-control/conditional-access-azure-management)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

## <a name="secure-configuration"></a>Configuration sécurisée

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Configuration sécurisée](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1 : Établir des configurations sécurisées pour toutes les ressources Azure

**Aide** : Utilisez des alias Azure Policy pour créer des stratégies personnalisées afin d’auditer ou d’appliquer la configuration de vos ressources Azure. Vous pouvez aussi utiliser des définitions Azure Policy intégrées.

Par ailleurs, Azure Resource Manager a la possibilité d’exporter le modèle au format JSON (JavaScript Object Notation), qui doit être examiné pour vérifier que les configurations répondent aux exigences de sécurité de votre organisation ou vont au-delà.

Vous pouvez aussi utiliser les recommandations d’Azure Security Center comme base de référence de configuration sécurisée pour vos ressources Azure.

- [Affichage des alias Azure Policy disponibles](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Tutoriel : Créer et gérer des stratégies pour assurer la conformité](/azure/governance/policy/tutorials/create-and-manage)

- [Exportation monoressource ou multiressource vers un modèle sur le portail Azure](/azure/azure-resource-manager/templates/export-template-portal)

- [Recommandations de sécurité - Guide de référence](/azure/security-center/recommendations-reference)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 : Gérer les configurations de ressources Azure sécurisées

**Conseils** : Utilisez Azure Policy [refuser] et [déployer s’il n’existe pas] pour appliquer des paramètres sécurisés à vos ressources Azure.  Vous pouvez utiliser des solutions comme Change Tracking, le tableau de bord de conformité des stratégies ou une solution personnalisée pour identifier facilement les changements de sécurité dans votre environnement.

- [Comprendre les effets d'Azure Policy](/azure/governance/policy/concepts/effects)

- [Créer et gérer des stratégies pour assurer la conformité](/azure/governance/policy/tutorials/create-and-manage)

- [Suivre les changements dans votre environnement avec la solution Change Tracking](/azure/automation/change-tracking)

- [Obtenir les données de conformité des ressources Azure](/azure/governance/policy/how-to/get-compliance-data)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 : Stocker en toute sécurité la configuration des ressources Azure

**Aide** : Utilisez Azure Repos pour stocker et gérer votre code de manière sécurisée, par exemple, les stratégies Azure personnalisées, les modèles Azure Resource Manager, les scripts Desired State Configuration, etc. Pour accéder aux ressources que vous gérez dans Azure DevOps, vous pouvez accorder ou refuser des autorisations à des utilisateurs spécifiques, à des groupes de sécurité intégrés ou à des groupes définis dans Azure Active Directory (Azure AD) s’ils sont intégrés à Azure DevOps, ou à Active Directory s’il est intégré à TFS.

- [Stocker du code dans Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [À propos des autorisations et des groupes dans Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7 : Déployer des outils de gestion de la configuration pour les ressources Azure

**Conseils** : Définissez et implémentez des configurations de sécurité standard pour les ressources Azure à l’aide d’Azure Policy. Utilisez des alias Azure Policy pour créer des stratégies personnalisées afin d’auditer ou d’appliquer la configuration réseau de vos ressources Azure. Vous pouvez également utiliser des définitions de stratégie intégrées en lien avec vos ressources spécifiques.  Par ailleurs, vous pouvez utiliser Azure Automation pour déployer les changements de configuration.

- [Configurer et gérer Azure Policy](/azure/governance/policy/tutorials/create-and-manage)

- [Utiliser des alias](/azure/governance/policy/concepts/definition-structure#aliases)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9 : Mettre en place une supervision automatisée de la configuration pour les ressources Azure

**Aide** : Utilisez des alias Azure Policy pour créer des stratégies personnalisées afin de créer des alertes, d’auditer et d’appliquer des configurations système. Utilisez une stratégie Azure [auditer], [refuser] et [déployer si elle n’existe pas] pour appliquer automatiquement des configurations pour vos ressources Azure.

- [Guide pratique pour configurer et gérer Azure Policy](/azure/governance/policy/tutorials/create-and-manage)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12 : Gérer les identités de façon sécurisée et automatique

**Conseils** : Utilisez des identités managées pour fournir aux services Azure une identité gérée automatiquement dans Azure Active Directory (Azure AD). Les identités managées vous permettent de vous authentifier auprès d’un service qui prend en charge l’authentification Azure AD, y compris Key Vault, sans informations d’identification dans votre code.

- [Configurer des identités managées](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

- [Configurer des identités managées pour votre cluster Azure Data Explorer](./managed-identities.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13 : Éliminer l’exposition involontaire des informations d’identification

**Conseils** : Exécuter le moteur d’analyse des informations d’identification pour identifier les informations d’identification dans le code. Le moteur d’analyse des informations d’identification encourage également le déplacement des informations d’identification découvertes vers des emplacements plus sécurisés, tels qu’Azure Key Vault. 

- [Configurer Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="malware-defense"></a>Défense contre les programmes malveillants

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Défense contre les programmes malveillants](/azure/security/benchmarks/security-control-malware-defense).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 : Pré-analyser les fichiers à charger sur des ressources Azure non liées au calcul

**Aide** : Le logiciel anti-programme malveillant de Microsoft est activé sur l’hôte sous-jacent qui prend en charge les services Azure (par exemple, Azure Data Explorer), mais il ne s’exécute pas sur le contenu client.

Préanalysez tout le contenu chargé dans des ressources Azure non liées au calcul, comme Azure Data Explorer, Data Lake Storage, Stockage Blob, Azure Database pour PostgreSQL, etc. Microsoft ne peut pas accéder à vos données dans ces instances.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="data-recovery"></a>Récupération des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : récupération de données](/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1 : Garantir des sauvegardes automatiques régulières

**Conseils** : Les données de votre compte de stockage Microsoft Azure utilisé par votre cluster Azure Data Explorer sont toujours répliquées pour en assurer la durabilité et la haute disponibilité. Stockage Azure copie vos données afin qu’elles soient protégées contre les événements planifiés ou non, notamment les défaillances matérielles temporaires, les pannes de réseau ou de courant et les catastrophes naturelles massives. Vous pouvez choisir de répliquer vos données dans le même centre de données, dans des centres de données zonaux d’une même région ou entre des régions géographiques différentes.

- [Présentation de la redondance et des contrats de niveau de service Stockage Azure](/azure/storage/common/storage-redundancy)

- [Exporter des données dans le stockage](/azure/kusto/management/data-export/export-data-to-storage)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : Effectuer des sauvegardes complètes du système et sauvegarder les clés managées par le client

**Aide** : Azure Data Explorer chiffre toutes les données dans un compte de stockage au repos. Par défaut, les données sont chiffrées avec des clés managées par Microsoft Pour plus de contrôle sur les clés de chiffrement, vous pouvez fournir des clés gérées par le client à utiliser pour le chiffrement des données. Les clés gérées par le client doivent être stockées dans Azure Key Vault.

- [Configurer des clés gérées par le client en utilisant le portail Azure](./customer-managed-keys-portal.md)

- [Configurer des clés gérées par le client en utilisant C#](./customer-managed-keys-csharp.md)

- [Configurer des clés gérées par le client à l’aide du modèle Azure Resource Manager](./customer-managed-keys-resource-manager.md)

- [Sauvegarder des certificats Azure Key Vault](/powershell/module/az.keyvault/backup-azkeyvaultcertificate?preserve-view=true&view=azps-4.8.0)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : Valider toutes les sauvegardes, y compris les clés gérées par le client

**Aide** : Testez régulièrement la restauration des données de vos secrets Azure Key Vault.

- [Restaurer des certificats Azure Key Vault](/powershell/module/az.keyvault/restore-azkeyvaultcertificate?preserve-view=true&view=azps-4.8.0)

- [Configurer des clés gérées par le client en utilisant C#](./customer-managed-keys-csharp.md)

- [Configurer des clés gérées par le client à l’aide du modèle Azure Resource Manager](./customer-managed-keys-resource-manager.md)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : Garantir la protection des sauvegardes et des clés managées par le client

**Aide** : Le client peut activer la suppression réversible dans Key Vault pour protéger les clés contre une suppression accidentelle ou malveillante.  Vous pouvez aussi activer la protection contre le vidage de sorte que si un coffre ou un objet est dans l’état supprimé, il ne peut pas être purgé avant la fin de la période de conservation.  

- [Guide pratique pour activer la suppression réversible et la protection contre la purge dans Key Vault](/azure/key-vault/key-vault-ovw-soft-delete)

- [Configurer des clés gérées par le client en utilisant C#](./customer-managed-keys-csharp.md)

- [Configurer des clés gérées par le client à l’aide du modèle Azure Resource Manager](./customer-managed-keys-resource-manager.md)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : réponse aux incidents](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1 : Créer un guide de réponse aux incidents

**Conseils** : Créez un guide de réponse aux incidents pour votre organisation. Assurez-vous qu’il existe des plans de réponse aux incidents écrits qui définissent tous les rôles du personnel, ainsi que les phases de gestion des incidents, depuis la détection jusqu’à la revue une fois l’incident terminé.
    

- [Aide sur la création de votre propre processus de réponse aux incidents de sécurité](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

    

- [Anatomie d’un incident dans le centre de réponse aux incidents de sécurité Microsoft](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

    

- [ Le client peut également tirer parti du guide de gestion des incidents de sécurité informatique du NIST pour faciliter la création de son propre plan de réponse aux incidents](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 : Créer une procédure de notation et de classement des incidents

**Conseils** : Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend du niveau de confiance que Security Center accorde à la recherche ou aux données analytiques utilisées pour émettre l’alerte, mais aussi de l’intention malveillante estimée de l’activité à l’origine de l’alerte.

En outre, marquez clairement les abonnements (par ex. production, non-production) à l’aide d’étiquettes et créez un système de nommage pour identifier et classer clairement les ressources Azure, en particulier celles qui traitent des données sensibles.  Il vous incombe de hiérarchiser le traitement des alertes en fonction de la criticité des ressources et de l’environnement Azure où l’incident s’est produit.

- [Alertes de sécurité dans le Centre de sécurité Azure](/azure/security-center/security-center-alerts-overview)

- [Organisation des ressources Azure à l’aide de catégories](/azure/azure-resource-manager/resource-group-using-tags)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="103-test-security-response-procedures"></a>10.3 : Tester les procédures de réponse de sécurité

**Conseils** : Effectuez des exercices pour tester les capacités de réponse aux incidents de vos systèmes à intervalles réguliers, afin de protéger vos ressources Azure. Identifiez les points faibles et les lacunes, et révisez le plan en fonction des besoins.
    

- [ Reportez-vous à la publication du NIST : Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guide de test, d’entraînement et d’utilisation des programmes destinés aux plans et fonctionnalités informatiques)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 : Fournir des informations de contact pour les incidents de sécurité et configurer des notifications d’alerte pour les incidents de sécurité

**Instructions** : Microsoft utilisera les informations de contact pour le signalement d’incidents de sécurité pour vous contacter si le Microsoft Security Response Center (MSRC) découvre que vos données ont été consultées de manière illégale ou par un tiers non autorisé. Examinez les incidents après les faits pour vous assurer que les problèmes sont résolus.
    

- [Comment définir le contact de sécurité d’Azure Security Center](/azure/security-center/security-center-provide-security-contact-details)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5 : Intégrer des alertes de sécurité à votre système de réponse aux incidents

**Aide** : Exportez vos alertes et recommandations Azure Security Center en utilisant la fonctionnalité d’exportation continue pour identifier les risques pesant sur les ressources Azure. L’exportation continue vous permet d’exporter les alertes et les recommandations manuellement, ou automatiquement de manière continue. Vous pouvez utiliser le connecteur de données Azure Security Center pour diffuser en continu les alertes vers Azure Sentinel.
    

- [Configurer l’exportation continue](/azure/security-center/continuous-export)

    

- [Transmettre en continu des alertes à Azure Sentinel](/azure/sentinel/connect-azure-security-center)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6 : Automatiser la réponse aux alertes de sécurité

**Aide** : Utilisez la fonctionnalité d’automatisation de workflow d’Azure Security Center pour déclencher automatiquement des réponses via « Logic Apps » aux alertes et aux recommandations de sécurité afin de protéger vos ressources Azure.
    

- [ Comment configurer l’automatisation des workflows et Logic Apps](/azure/security-center/workflow-automation)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Tests d’intrusion et exercices Red Team

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : tests d’intrusion et exercices Red Team](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1 : Procéder régulièrement à des tests d’intrusion des ressources Azure et veiller à corriger tous les problèmes de sécurité critiques détectés

**Aide** : Suivez les règles d’engagement de pénétration du cloud Microsoft pour vous assurer que vos tests d’intrusion sont conformes aux stratégies de Microsoft. Utilisez la stratégie et l’exécution de Red Teaming de Microsoft ainsi que les tests d’intrusion de site actif sur l’infrastructure cloud, les services et les applications gérés par Microsoft. 

- [Règles d’engagement des tests d’intrusion](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- En savoir plus sur les [bases de référence de la sécurité Azure](/azure/security/benchmarks/security-baselines-overview)
