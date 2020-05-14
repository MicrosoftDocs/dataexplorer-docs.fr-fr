---
title: Contrôle Kusto ou suppression du suivi côté client du SDK-Azure Explorateur de données
description: Cet article décrit le contrôle et la suppression du suivi côté client du kit de développement logiciel (SDK) Kusto dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 10/23/2018
ms.openlocfilehash: 8ef966cb2be70ddca6fb5ab10813abe12cb5bce1
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382350"
---
# <a name="controlling-and-suppressing-kusto-sdk-client-side-tracing"></a>Contrôle et suppression du suivi côté client du kit de développement logiciel (SDK) Kusto

Les bibliothèques clientes Kusto utilisent une plateforme commune pour le suivi. La plateforme utilise un grand nombre de sources de suivi ( `System.Diagnostics.TraceSource` ) et chacune est connectée à l’ensemble par défaut d’écouteurs de suivi ( `System.Diagnostics.Trace.Listeners` ) pendant sa construction.

Si une application a des écouteurs de suivi associés à l' `System.Diagnostics.Trace` instance par défaut (par exemple, par le biais de son `app.config` fichier), les bibliothèques clientes Kusto émettent des traces à ces écouteurs.

Le suivi peut être supprimé ou contrôlé par programme ou par le biais d’un fichier de configuration.

## <a name="suppress-tracing-programmatically"></a>Supprimer le suivi par programmation

Pour supprimer le suivi des bibliothèques clientes Kusto par programme, appelez ce morceau de code lors du chargement de la bibliothèque appropriée :

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="use-a-config-file-to-suppress-tracing"></a>Utiliser un fichier de configuration pour supprimer le suivi 

Pour supprimer le suivi des bibliothèques clientes Kusto via un fichier de configuration, modifiez le fichier `Kusto.Cloud.Platform.dll.tweaks` (qui est inclus dans la `Kusto.Data` bibliothèque).

```xml
    <!-- Overrides the default trace verbosity level -->
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

> [!NOTE]
> Pour que la modification prenne effet, il ne doit pas y avoir de signe moins dans la valeur de`key`

Une alternative est :

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="enable-the-kusto-client-libraries-tracing"></a>Activer le suivi des bibliothèques clientes Kusto

Pour activer le traçage à partir des bibliothèques clientes Kusto, activez le traçage .NET dans le *fichier app. config*de votre application. Par exemple, supposons que l’application `MyApp.exe` utilise la bibliothèque cliente Kusto. Data. La modification du fichier *MyApp. exe. config* pour inclure les éléments suivants active le `Kusto.Data` suivi lors du démarrage suivant de l’application.

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

Le code configure un écouteur de suivi qui écrit dans des fichiers CSV dans un sous-répertoire appelé *RollingLogs*. Le sous-répertoire se trouve dans le répertoire du processus.

> [!NOTE]
> Aux. La classe d’écouteur de suivi compatible NET peut également être utilisée

## <a name="enable-the-azure-ad-client-libraries-adal-tracing"></a>Activer Azure AD le suivi ADAL (client Libraries)

Une fois le suivi des bibliothèques clientes Kusto activé, le suivi est fait par les bibliothèques clientes Azure AD. Les bibliothèques clientes Kusto configurent automatiquement le suivi ADAL.
