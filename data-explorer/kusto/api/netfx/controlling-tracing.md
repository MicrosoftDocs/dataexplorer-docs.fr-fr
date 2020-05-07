---
title: Contrôle ou suppression du suivi côté client du kit de développement logiciel (SDK) Kusto-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit le contrôle ou la suppression du suivi côté client Kusto SDK dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 10/23/2018
ms.openlocfilehash: cbda69063e3b1a20549dbadb4641fc9fd3f51f57
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862052"
---
# <a name="controlling-or-suppressing-kusto-sdk-client-side-tracing"></a>Contrôle ou suppression du suivi côté client du kit de développement logiciel (SDK) Kusto

Les bibliothèques clientes Kusto utilisent une plateforme commune pour le suivi. La plateforme utilise un grand nombre de sources de suivi`System.Diagnostics.TraceSource`(), chacune connectée à l’ensemble par défaut d’écouteurs`System.Diagnostics.Trace.Listeners`de suivi () pendant sa construction.

L’une des conséquences est que si une application a des écouteurs de suivi associés à l' `System.Diagnostics.Trace` instance par défaut (par exemple, `app.config` par le biais de son fichier), les bibliothèques clientes Kusto émettent des traces à ces écouteurs.

Ce comportement peut être supprimé ou contrôlé par programme ou par le biais d’un fichier de configuration.

## <a name="suppress-tracing-programmatically"></a>Supprimer le suivi par programmation

Pour supprimer le suivi des bibliothèques clientes Kusto par programme, appelez le morceau de code suivant au début de lors du chargement de la bibliothèque pertinente :

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="suppressing-tracing-by-using-a-config-file"></a>Suppression du suivi à l’aide d’un fichier de configuration

Pour supprimer le suivi des bibliothèques clientes Kusto par le biais d’un fichier de `Kusto.Cloud.Platform.dll.tweaks` configuration, modifiez le fichier ( `Kusto.Data` qui est inclus avec la bibliothèque) de sorte que le « Tweak » approprié soit maintenant lu :

```xml
    <!-- Overrides the default trace verbosity level -->
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

(Notez que pour que la modification prenne effet, il ne doit y avoir aucun signe moins dans `key`la valeur de.)

Une autre méthode consiste à effectuer les opérations suivantes :

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="how-to-enable-the-kusto-client-libraries-tracing"></a>Comment activer le suivi des bibliothèques clientes Kusto

Pour activer le traçage à partir des bibliothèques clientes Kusto, activez le traçage .NET dans le fichier app. config de votre application. Par exemple, en supposant que l' `MyApp.exe` application utilise la bibliothèque cliente Kusto. Data. Le fait de modifier `MyApp.exe.config` le fichier pour inclure les éléments suivants activera le suivi Kusto. Data lors du prochain démarrage de l’application :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.diagnostics>
    <trace indentsize="4">
      <listeners>
        <add type="Kusto.Cloud.Platform.Utils.RollingCsvTraceListener2, Kusto.Cloud.Platform" name="RollingCsvTraceListener" initializeData="RollingLogs" />
        <remove name="Default" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

Cela permet de configurer un écouteur de suivi qui écrit dans des fichiers CSV dans un sous `RollingLogs` -répertoire appelé situé dans le répertoire du processus. (Bien sûr, tout. La classe d’écouteur de suivi compatible NET peut également être utilisée.)

## <a name="how-to-enable-the-aad-client-libraries-adal-tracing"></a>Comment activer le suivi des bibliothèques clientes AAD (ADAL)

Une fois que le suivi des bibliothèques clientes Kusto est activé, le suivi est donc émis par les bibliothèques clientes AAD (les bibliothèques clientes Kusto configurent automatiquement le suivi ADAL)
