---
title: Principaux et fournisseurs d’identité - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les principaux et les fournisseurs d’identité d’Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a0638ba0031162dadbb4b9a2815940e66d4dcfb3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522642"
---
# <a name="principals-and-identity-providers"></a>Directeurs d’école et fournisseurs d’identité

Le modèle d’autorisation Kusto prend en charge plusieurs fournisseurs d’identité (idP) et plusieurs types principaux.
Cet article passe en revue les principaux types pris en charge et démontre leur utilisation avec [les commandes d’affectation de rôle .](../../management/security-roles.md)

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory (AAD) est le service d’annuaire cloud multi-locataires préféré d’Azure et son fournisseur d’identité, capable d’authentifier les principaux de sécurité ou de fédérer avec d’autres fournisseurs d’identité, tels que l’Active Directory de Microsoft.

AAD est la méthode préférée pour l’authentification à Kusto. Plusieurs scénarios d’authentification sont ainsi pris en charge :
* **Authentification de l’utilisateur** (ouverture de session interactive) : utilisée pour authentifier les principaux humains.
* **Authentification de l’application** (ouverture de session non interactive) : utilisée pour authentifier les services et les applications qui doivent s’exécuter/s’authentifier en l’absence d’utilisateur humain.

>REMARQUE : Azure Active Directory n’autorise pas l’authentification des comptes de service (qui sont par définition des entités AD pré-prématurées).
L’équivalent AAD du compte de service AD est l’application AAD.

#### <a name="aad-group-principals"></a>Directeurs du Groupe AAD
Kusto ne prend en charge que les directeurs du Groupe de sécurité (et non ceux du Groupe de distribution). Tentative de configurer l’accès pour un DG sur un cluster Kusto se traduira par une erreur.

#### <a name="aad-tenants"></a>Locataires AAD


>Si le locataire de l’AAD n’est pas explicitement spécifié, Kusto tentera de le `johndoe@fabrikam.com`résoudre à partir de l’UPN (UniversalPrincipalName, par exemple, ), si elle est fournie.
Si votre mandant n’inclut pas les renseignements sur le locataire (et non sous forme d’UPN), vous devez en parler explicitement en versant la pièce d’identité ou le nom du locataire au descripteur principal.

**Exemples pour les directeurs d’AAD**

|Locataire AAD |Type |Syntaxe |
|-----------|-----|-------|
|Implicite (UPN)  |Utilisateur  |`aaduser=`*UserEmailAddress (en)*
|Explicite (ID)   |Utilisateur  |`aaduser=`*UserEmailAddress*`;`*TenantId* ou `aaduser=` *ObjectID*`;`*TenantId*
|Explicite (Nom) |Utilisateur  |`aaduser=`*UserEmailAddress*`;`*TenantName* ou `aaduser=` *ObjectID*`;`*TenantName*
|Implicite (UPN)  |Groupe |`aadgroup=`*GroupEmailAddress*
|Explicite (ID)   |Groupe |`aadgroup=`*GroupObjectId*`;`*TenantId* ou`aadgroup=`*GroupDisplayName*`;`*TenantId*
|Explicite (Nom) |Groupe |`aadgroup=`*GroupObjectId*`;`*TenantName* ou`aadgroup=`*GroupDisplayName TenantName*`;`*TenantName*
|Explicite (UPN)  |Application   |`aadapp`=*ApplicationDisplayName*`;`*TenantId*
|Explicite (Nom) |Application   |`aadapp=`*ApplicationId*`;`*TenantName (en)*

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

**Exemples pour les directeurs de msA**

|Fournisseur d’identité (IdP)  |Type  |Syntaxe |
|-----|------|-------|
|Live.com |Utilisateur  |`msauser=`john.doe@live.com`

```kusto
.add database Test users ('msauser=john.doe@live.com') 'Test user (live.com)'
```

