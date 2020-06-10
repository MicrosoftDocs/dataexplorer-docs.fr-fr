---
title: Principaux et fournisseurs d’identité-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les principaux et les fournisseurs d’identité dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4e34c724799cffe38db93869e96fcfae83a92b55
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664991"
---
# <a name="principals-and-identity-providers"></a>Principaux et fournisseurs d’identité

Le modèle d’autorisation Kusto prend en charge plusieurs fournisseurs d’identité (fournisseurs) et plusieurs types principaux.
Cet article passe en revue les types de principal pris en charge et illustre leur utilisation avec les [commandes d’attribution de rôle](../../management/security-roles.md).

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory (AAD) est le service d’annuaire et le fournisseur d’identité Cloud mutualisés d’Azure, qui peuvent authentifier les principaux de sécurité ou se fédérer avec d’autres fournisseurs d’identité, tels que les Active Directory de Microsoft.

AAD est la méthode recommandée pour l’authentification auprès de Kusto. Plusieurs scénarios d’authentification sont ainsi pris en charge :
* **Authentification de l’utilisateur** (ouverture de session interactive) : utilisée pour authentifier les principaux humains.
* **Authentification de l’application** (ouverture de session non interactive) : utilisée pour authentifier les services et les applications qui doivent s’exécuter/s’authentifier en l’absence d’utilisateur humain.

> [!NOTE]
> Azure Active Directory n’autorise pas l’authentification de comptes de service (qui sont par définition sur des entités AD local).
L’équivalent AAD du compte de service Active Directory est l’application AAD.

#### <a name="aad-group-principals"></a>Principaux de groupe AAD
Kusto prend uniquement en charge les principaux de groupe de sécurité (et non les groupes de distribution). La tentative de configuration de l’accès pour une DG sur un cluster Kusto génère une erreur.

#### <a name="aad-tenants"></a>Locataires AAD

Si le locataire AAD n’est pas explicitement spécifié, Kusto tente de le résoudre à partir de l’UPN (UniversalPrincipalName, par exemple, `johndoe@fabrikam.com` ), s’il est fourni. Si votre principal n’inclut pas les informations sur le locataire (pas dans le formulaire UPN), vous devez le mentionner explicitement en ajoutant l’ID ou le nom du locataire au descripteur principal.

**Exemples pour les principaux AAD**

|Locataire AAD |Type |Syntaxe |
|-----------|-----|-------|
|Implicite (UPN)  |Utilisateur  |`aaduser=`*UserEmailAddress*
|Explicite (ID)   |Utilisateur  |`aaduser=`*UserEmailAddress* `;` *TenantId* ou `aaduser=` *ObjectID* `;` *TenantId*
|Explicite (nom) |Utilisateur  |`aaduser=`*UserEmailAddress* `;` *TenantName* ou `aaduser=` *ObjectID* `;` *TenantName*
|Implicite (UPN)  |Groupe |`aadgroup=`*GroupEmailAddress*
|Explicite (ID)   |Groupe |`aadgroup=`*GroupObjectId* `;` *TenantId* ou `aadgroup=` *groupDisplayName* `;` *TenantId*
|Explicite (nom) |Groupe |`aadgroup=`*GroupObjectId* `;` *TenantName* ou `aadgroup=` *groupDisplayName* `;` *TenantName*
|Explicite (UPN)  |Application   |`aadapp`=*ApplicationDisplayName* `;` *TenantId*
|Explicite (nom) |Application   |`aadapp=`*ApplicationID* `;` *TenantName*

```kusto
// No need to specify AAD tenant for UPN, as Kusto performs the resolution by itself
.add database Test users ('aaduser=imikeoein@fabrikam.com') 'Test user (AAD)'

// AAD SG on 'fabrikam.com' tenant
.add database Test users ('aadgroup=SGDisplayName;fabrikam.com') 'Test group @fabrikam.com (AAD)'

// AAD App on 'fabrikam.com' tenant - by tenant name
.add database Test users ('aadapp=4c7e82bd-6adb-46c3-b413-fdd44834c69b;fabrikam.com') 'Test app @fabrikam.com (AAD)'
```

### <a name="microsoft-accounts-msas"></a>Comptes Microsoft (MSA)
Les comptes Microsoft (MSA) désignent tous les comptes d’utilisateur non organisationnels gérés par Microsoft, par exemple, `hotmail.com`, `live.com`, `outlook.com`.
Kusto prend en charge l’authentification de l’utilisateur pour les MSA (notez qu’il n’existe aucun concept de groupe de sécurité), identifiés par leur UPN (nom d’utilisateur principal).
Quand un principal MSA est configuré sur une ressource Kusto, Kusto ne tente **pas** de résoudre l’UPN fourni.

**Exemples pour les principaux MSA**

|Fournisseur d’identité  |Type  |Syntaxe |
|-----|------|-------|
|Live.com |Utilisateur  |`msauser=`john.doe@live.com`

```kusto
.add database Test users ('msauser=john.doe@live.com') 'Test user (live.com)'
```

