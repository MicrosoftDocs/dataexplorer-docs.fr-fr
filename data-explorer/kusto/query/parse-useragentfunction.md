---
title: parse_user_agent ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit parse_user_agent () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: a7261f6996aecdf10446c990dac54afa4638147e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246303"
---
# <a name="parse_user_agent"></a>parse_user_agent()

Interprète une chaîne User-agent, qui identifie le navigateur de l’utilisateur et fournit certains détails système aux serveurs hébergeant les sites Web visités par l’utilisateur. Le résultat est retourné sous la forme [`dynamic`](./scalar-data-types/dynamic.md) . 

## <a name="syntax"></a>Syntaxe

`parse_user_agent(`*user-agent-String*, *look-for*`)`

## <a name="arguments"></a>Arguments

* *user-agent-String*: expression de type `string` , représentant une chaîne User-agent.

* *Rechercher*: expression de type `string` ou `dynamic` représentant ce que la fonction doit rechercher dans la chaîne de l’agent utilisateur (cible d’analyse). Les options possibles sont les suivantes : « Browser », « OS », « Device ». Si une seule cible d’analyse est requise, il peut être passé à un `string` paramètre.
Si deux ou trois sont requis, ils peuvent être passés en tant que `dynamic array` .

## <a name="returns"></a>Retours

Objet de type `dynamic` qui contient les informations sur les cibles d’analyse demandées.

Navigateur : Family, MajorVersion, MinorVersion, patch                 

OperatingSystem : Family, MajorVersion, MinorVersion, patch, PatchMinor             

Appareil : famille, famille, modèle

> [!WARNING]
> L’implémentation de fonction repose sur des vérifications Regex de la chaîne d’entrée par rapport à un grand nombre de modèles prédéfinis. Par conséquent, le temps attendu et la consommation du processeur sont élevés.
Lorsque la fonction est utilisée dans une requête, assurez-vous qu’elle s’exécute de façon distribuée sur plusieurs ordinateurs.
Si les requêtes avec cette fonction sont fréquemment utilisées, vous pouvez précréer les résultats à l’aide de la [stratégie de mise à jour](../management/updatepolicy.md), mais vous devez prendre en compte que l’utilisation de cette fonction à l’intérieur de la stratégie de mise à jour augmente la latence d’ingestion.
 
## <a name="example"></a>Exemple

```kusto
print useragent = "Mozilla/5.0 (Windows; U; en-US) AppleWebKit/531.9 (KHTML, like Gecko) AdobeAIR/2.5.1"
| extend x = parse_user_agent(useragent, "browser") 
```

Le résultat attendu est un objet dynamique :

{« Browser » : {« Family » : « AdobeAIR », « MajorVersion » : « 2 », « MinorVersion » : « 5 », « patch » : « 1 »}}

```kusto
print useragent = "Mozilla/5.0 (SymbianOS/9.2; U; Series60/3.1 NokiaN81-3/10.0.032 Profile/MIDP-2.0 Configuration/CLDC-1.1 ) AppleWebKit/413 (KHTML, like Gecko) Safari/4"
| extend x = parse_user_agent(useragent, dynamic(["browser","os","device"])) 
```

Le résultat attendu est un objet dynamique :

{« Browser » : {« Family » : « Nokia OSS Browser », « MajorVersion » : « 3 », « MinorVersion » : « 1 », « patch » : « »}, OperatingSystem « : { » Family : « Symbian OS », « MajorVersion » : « 9 », « MinorVersion » : « 2 », « patch » : « », «PatchMinor » : « »}, «Device » : {« Family » : « Nokia N81 », « marquage » : « Nokia », « Model » : « N81-3 »}}