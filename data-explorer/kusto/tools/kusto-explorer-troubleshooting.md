---
title: Résoudre les problèmes courants dans Kusto. Explorer
description: En savoir plus sur les problèmes courants liés à l’installation et à l’exécution de Kusto. Explorer et de leurs solutions
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 9a697cfd37590f0368d5a8f0bacf91d02e1c8725
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92003164"
---
# <a name="troubleshooting"></a>Dépannage

Ce document présente les difficultés courantes liées à l’exécution et à l’utilisation de Kusto. Explorer, et propose des solutions. Ce document décrit également [Comment réinitialiser Kusto. Explorer](#reset-kustoexplorer).

## <a name="kustoexplorer-fails-to-start"></a>Kusto. Explorer ne parvient pas à démarrer

### <a name="kustoexplorer-shows-error-dialog-during-or-after-start-up"></a>Kusto. Explorer affiche la boîte de dialogue d’erreur pendant ou après le démarrage

#### <a name="symptom"></a>Symptôme

Au démarrage, Kusto. Explorer affiche une `InvalidOperationException` erreur.

#### <a name="possible-solution"></a>Solution possible

Cette erreur peut suggérer que le système d’exploitation est endommagé ou qu’il manque certains modules essentiels.
Pour vérifier les fichiers système manquants ou endommagés, suivez les étapes décrites ici :   
[https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system](https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system)

## <a name="kustoexplorer-always-downloads-even-when-there-are-no-updates"></a>Kusto. Explorer télécharge toujours les fichiers, même s’il n’y a aucune mise à jour

#### <a name="symptom"></a>Symptôme

Chaque fois que vous ouvrez Kusto. Explorer, vous êtes invité à installer une nouvelle version. Kusto. Explorer télécharge le package entier, sans mettre à jour la version déjà installée.

#### <a name="possible-solution"></a>Solution possible

Ce symptôme peut être dû à un endommagement dans votre magasin ClickOnce local. Vous pouvez effacer le magasin ClickOnce local en exécutant la commande suivante, dans une invite de commandes avec élévation de privilèges.

> [!IMPORTANT]
> 1. S’il existe d’autres instances d’applications ClickOnce ou de `dfsvc.exe` , terminez-les avant d’exécuter cette commande.
> 1. Toutes les applications ClickOnce se réinstallent automatiquement la prochaine fois que vous les exécutez, à condition que vous ayez accès à l’emplacement d’installation d’origine stocké dans le raccourci de l’application. Les raccourcis de l’application ne seront pas supprimés.

```kusto
rd /q /s %userprofile%\appdata\local\apps\2.0
```

Essayez d’installer Kusto. Explorer à nouveau à partir de l’un des [miroirs d’installation](kusto-explorer.md#installing-kustoexplorer).

### <a name="clickonce-error-cannot-start-application"></a>Erreur ClickOnce : impossible de démarrer l’application

#### <a name="symptoms"></a>Symptômes

Le programme ne parvient pas à démarrer et affiche l’une des erreurs suivantes : 
* `External component has thrown an exception`
* `Value does not fall within the expected range`
* `The application binding data format is invalid.` 
* `Exception from HRESULT: 0x800736B2`
* `The referenced assembly is not installed on your system. (Exception from HRESULT: 0x800736B3)`

Vous pouvez explorer les détails de l’erreur en cliquant `Details` dans la boîte de dialogue d’erreur suivante :

![Erreur ClickOnce](./Images/kusto-explorer-troubleshooting/clickonce-err-1.png)

```kusto
Following errors were detected during this operation.
    * System.ArgumentException
        - Value does not fall within the expected range.
        - Source: System.Deployment
        - Stack trace:
            at System.Deployment.Application.NativeMethods.CorLaunchApplication(UInt32 hostType, String applicationFullName, Int32 manifestPathsCount, String[] manifestPaths, Int32 activationDataCount, String[] activationData, PROCESS_INFORMATION processInformation)
            at System.Deployment.Application.ComponentStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.SubscriptionStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.Activate(DefinitionAppId appId, AssemblyManifest appManifest, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.ProcessOrFollowShortcut(String shortcutFile, String& errorPageUrl, TempFile& deployFile)
            at System.Deployment.Application.ApplicationActivator.PerformDeploymentActivation(Uri activationUri, Boolean isShortcut, String textualSubId, String deploymentProviderUrlFromExtension, BrowserSettings browserSettings, String& errorPageUrl)
            at System.Deployment.Application.ApplicationActivator.ActivateDeploymentWorker(Object state)
```

#### <a name="proposed-solution-steps"></a>Étapes de la solution proposée

1. Désinstallez Kusto. Explorer à l’aide `Programs and Features` de ( `appwiz.cpl` ).

1. Essayez d’exécuter `CleanOnlineAppCache` , puis réessayez d’installer Kusto. Explorer. 
    À partir d’une invite de commandes avec élévation de privilèges : 
    
    ```kusto
    rundll32 %windir%\system32\dfshim.dll CleanOnlineAppCache
    ```

    Installez Kusto. Explorer à nouveau à partir de l’un des [miroirs d’installation](kusto-explorer.md#installing-kustoexplorer).

1. Si l’application ne démarre toujours pas, supprimez le magasin ClickOnce local. Toutes les applications ClickOnce se réinstallent automatiquement la prochaine fois que vous les exécutez, à condition que vous ayez accès à l’emplacement d’installation d’origine stocké dans le raccourci de l’application. Les raccourcis de l’application ne seront pas supprimés.

    À partir d’une invite de commandes avec élévation de privilèges :

    ```kusto
    rd /q /s %userprofile%\appdata\local\apps\2.0
    ```

    Installer Kusto. Explorer à nouveau à partir de l’un des [miroirs d’installation](kusto-explorer.md#installing-kustoexplorer)

1. Si l’application ne démarre toujours pas :
    1. Supprimez les fichiers de déploiement temporaires.
    1. Renommez le dossier Kusto. Explorer local AppData.

        À partir d’une invite de commandes avec élévation de privilèges :

        ```kusto
        rd /s/q %userprofile%\AppData\Local\Temp\Deployment
        ren %LOCALAPPDATA%\Kusto.Explorer Kusto.Explorer.bak
        ```

    1. Installer Kusto. Explorer à nouveau à partir de l’un des [miroirs d’installation](kusto-explorer.md#installing-kustoexplorer)

    1. Pour restaurer vos connexions à partir de Kusto. Explorer. bak, à partir d’une invite de commandes avec élévation de privilèges :

        ```kusto
        copy %LOCALAPPDATA%\Kusto.Explorer.bak\User*.xml %LOCALAPPDATA%\Kusto.Explorer
        ```

#### <a name="enabling-clickonce-verbose-logging"></a>Activation de la journalisation détaillée ClickOnce

1. Si l’application ne démarre toujours pas :
    1. [Activez la journalisation ClickOnce détaillée](https://docs.microsoft.com/visualstudio/deployment/how-to-specify-verbose-log-files-for-clickonce-deployments) en créant une valeur de chaîne LogVerbosityLevel de 1 sous :

        ```kusto
        HKEY_CURRENT_USER\Software\Classes\Software\Microsoft\Windows\CurrentVersion\Deployment
        ```
    
    1. Rereproductionz-le à nouveau.
    1. Envoie la sortie détaillée à KEBugReport@microsoft.com . 

### <a name="clickonce-error-your-administrator-has-blocked-this-application-because-it-potentially-poses-a-security-risk-to-your-computer"></a>Erreur ClickOnce : votre administrateur a bloqué cette application, car elle risque de poser un problème de sécurité sur votre ordinateur

#### <a name="symptom"></a>Symptôme 
L’installation de l’application échoue avec l’une des erreurs suivantes :
* `Your administrator has blocked this application because it potentially poses a security risk to your computer`.
* `Your security settings do not allow this application to be installed on your computer.`

#### <a name="solution"></a>Solution

Ce symptôme peut être dû au fait qu’une autre application remplace le comportement par défaut de l’invite d’approbation ClickOnce. 
1. Affichez vos paramètres de configuration par défaut.
1. Comparez vos paramètres de configuration aux valeurs réelles sur votre ordinateur.
1. Réinitialisez vos paramètres de configuration si nécessaire, comme expliqué [dans cet article de procédure](https://docs.microsoft.com/visualstudio/deployment/how-to-configure-the-clickonce-trust-prompt-behavior).

### <a name="cleanup-application-data"></a>Nettoyer les données d’application

Parfois, lorsque les étapes de dépannage précédentes n’ont pas permis d’obtenir Kusto. Explorer pour démarrer, le nettoyage des données stockées localement peut être utile.

Les données stockées par l’application Kusto. Explorer se trouvent ici : `C:\Users\[your username]\AppData\Local\Kusto.Explorer` .

> [!NOTE]
> Le nettoyage des données entraîne une perte des onglets ouverts (dossier de récupération), des connexions enregistrées (dossier des connexions) et des paramètres d’application (dossier UserSettings).

## <a name="reset-kustoexplorer"></a>Réinitialiser Kusto. Explorer

Si nécessaire, vous pouvez réinitialiser complètement Kusto. Explorer. La procédure suivante décrit comment réinitialiser Kusto. Explorer de manière progressive, jusqu’à ce qu’il soit supprimé de votre ordinateur et qu’il soit nécessaire de l’installer à partir de zéro.

1. Dans Windows, ouvrez **modifier ou supprimer un programme** (également appelé **programmes et fonctionnalités**).
1. Sélectionnez chaque élément qui commence par `Kusto.Explorer` .
1. Sélectionner **Désinstaller**.

   Si cette procédure ne permet pas de désinstaller l’application (problème connu avec les applications ClickOnce), consultez [cet article pour obtenir des instructions](https://stackoverflow.com/questions/10896223/how-do-i-completely-uninstall-a-clickonce-application-from-my-computer).

1. Supprimez le dossier `%LOCALAPPDATA%\Kusto.Explorer` , qui supprime toutes les connexions, l’historique, etc.

1. Supprimez le dossier `%APPDATA%\Kusto` , qui supprime le cache de jetons Kusto. Explorer. Vous devrez vous authentifier à tous les clusters.

Il est également possible de revenir à une version spécifique de Kusto. Explorer :

1. Exécutez `appwiz.cpl`.
1. Sélectionnez **Kusto. Explorer** et sélectionnez **Désinstaller/Modifier**.
1. Sélectionnez **restaurer l’application à son état précédent**.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l' [interface utilisateur de Kusto. Explorer](kusto-explorer.md#overview-of-the-user-interface)
* En savoir plus sur [l’exécution de Kusto. Explorer à partir de la ligne de commande](kusto-explorer-using.md#kustoexplorer-command-line-arguments)
* En savoir plus sur le [langage de requête Kusto (KQL)](https://docs.microsoft.com/azure/kusto/query/)
