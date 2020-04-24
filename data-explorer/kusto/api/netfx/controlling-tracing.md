---
title: Contrôle ou suppression du tracé côté client kusto SDK - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le contrôle ou la suppression du retraçage du côté client de Kusto SDK dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 65964d7e990cdb639bd5bfe319d11874ea3de15d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502735"
---
# <a name="controlling-or-suppressing-kusto-sdk-client-side-tracing"></a>Contrôle ou suppression du tracé côté client kusto SDK

Les bibliothèques clientes de Kusto utilisent une plate-forme commune pour le traçage. La plate-forme utilise un`System.Diagnostics.TraceSource`grand nombre de sources de traces (), chacune connectée à l’ensemble par défaut des auditeurs de traces (`System.Diagnostics.Trace.Listeners`) pendant sa construction.

Une conséquence de ceci est que si une `System.Diagnostics.Trace` application a des auditeurs de trace associés à l’instance par défaut (par exemple, par l’intermédiaire de son `app.config` fichier), alors les bibliothèques de clients de Kusto émettra des traces à ces auditeurs.

Ce comportement peut être supprimé ou contrôlé programmatiquement ou par le biais d’un fichier config.

## <a name="suppress-tracing-programmatically"></a>Supprimer le traçage programmatique

Pour supprimer la traçabilité des bibliothèques clientes de Kusto sur le programme, invoquez dès le début le code suivant lors du chargement de la bibliothèque concernée :

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="suppressing-tracing-by-using-a-config-file"></a>Supprimer le traçage à l’aide d’un fichier config

Pour supprimer la traçabilité des bibliothèques de clients Kusto par le biais d’un fichier config, modifiez le fichier `Kusto.Cloud.Platform.dll.tweaks` (qui est inclus dans la `Kusto.Data` bibliothèque) de sorte que le « tweak » approprié se lit maintenant comme suit :

```xml
    <!-- Overrides the default trace verbosity level -->
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

(Notez que pour que la modification prenne effet, il `key`ne doit pas y avoir de signe moins dans la valeur de .)

Un autre moyen est de faire ce qui suit :

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="how-to-enable-the-kusto-client-libraries-tracing"></a>Comment permettre aux bibliothèques clientes de Kusto de retracer

Pour permettre le traçage des bibliothèques clients Kusto, activez le traçage .NET dans le fichier app.config de votre application. Supposons, par exemple, que l’application `MyApp.exe` utilise la bibliothèque cliente Kusto.Data. Ensuite, la `MyApp.exe.config` modification du fichier pour inclure ce qui suit permettra Kusto.Data traçant la prochaine fois que l’application commence:

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

Cela configurera un auditeur de traces qui écrit aux fichiers `RollingLogs` CSV dans un sous-répertoire appelé situé dans l’annuaire du processus. (Bien sûr, tout . Net-compatible classe d’auditeur de trace peut être employé aussi bien.) 

## <a name="how-to-enable-the-aad-client-libraries-adal-tracing"></a>Comment permettre aux bibliothèques de clients AAD (ADAL) de retracer

Une fois que le tracé des bibliothèques clientes de Kusto est activé, le tracé émis par les bibliothèques de clients de l’AAD est activé (les bibliothèques clientes de Kusto configurent automatiquement le traçage ADAL)

