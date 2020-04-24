---
title: parse_user_agent() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit parse_user_agent() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 2ebe85c622dda116366e30ba22e2e495e4cb76ac
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511728"
---
# <a name="parse_user_agent"></a>parse_user_agent()

Interprète une chaîne d’agent utilisateur, qui identifie le navigateur de l’utilisateur et fournit certains détails du système aux serveurs hébergeant les sites Web que l’utilisateur visite. Le résultat est [`dynamic`](./scalar-data-types/dynamic.md)retourné comme . 

**Syntaxe**

`parse_user_agent(`*utilisateur-agent-corde*, *look-for*`)`

**Arguments**

* *utilisateur-agent-corde*: Une `string`expression de type , représentant une chaîne utilisateur-agent.

* *look-for*: Une `string` expression `dynamic`de type ou , représentant ce que la fonction devrait rechercher dans la chaîne utilisateur-agent (cible d’analyse). Les options possibles: "browser", "os", "device". Si une seule cible d’analyse est nécessaire, elle peut être adoptée comme un `string` paramètre.
Si deux ou trois sont nécessaires, `dynamic array`ils peuvent être adoptés comme un .

**Retourne**

Un objet `dynamic` de type qui contient les informations sur les cibles d’analyse demandées.

Navigateur: Famille, MajorVersion, MinorVersion, Patch                 

Système d’exploitation: Famille, MajorVersion, MinorVersion, Patch, PatchMinor             

Appareil: Famille, Marque, Modèle

> [!WARNING]
> La mise en œuvre de la fonction est construite sur des contrôles regex de la chaîne d’entrée contre un grand nombre de modèles prédéfinis. Par conséquent, le temps prévu et la consommation de processeur est élevé.
Lorsque la fonction est utilisée dans une requête, assurez-vous qu’elle fonctionne d’une manière distribuée sur plusieurs machines.
Si les requêtes avec cette fonction sont fréquemment utilisées, vous pouvez pré-créer les résultats via la [stratégie de mise](../management/updatepolicy.md)à jour , mais vous devez prendre en compte que l’utilisation de cette fonction à l’intérieur de la stratégie de mise à jour augmentera la latence d’ingestion.
 
**Exemple**

```kusto
print useragent = "Mozilla/5.0 (Windows; U; en-US) AppleWebKit/531.9 (KHTML, like Gecko) AdobeAIR/2.5.1"
| extend x = parse_user_agent(useragent, "browser") 
```

Le résultat attendu est un objet dynamique :

"Browser": "Famille": "AdobeAIR", "MajorVersion": "2", "MinorVersion": "5", "Patch": "1"

```kusto
print useragent = "Mozilla/5.0 (SymbianOS/9.2; U; Series60/3.1 NokiaN81-3/10.0.032 Profile/MIDP-2.0 Configuration/CLDC-1.1 ) AppleWebKit/413 (KHTML, like Gecko) Safari/4"
| extend x = parse_user_agent(useragent, dynamic(["browser","os","device"])) 
```

Le résultat attendu est un objet dynamique :

"Browser": "Family": "Nokia OSS Browser", "MajorVersion": "3", "MinorVersion": "1", "Patch": "" "OperatingSystem": "Famille": "Symbian OS", "MajorVersion": "9", "MinorVersion": "2", "Patch": "PatchMinor": "" ', "Device": 'Famille": "Nokia N81", "Brand": "Nokia", "Model": "N81-3"