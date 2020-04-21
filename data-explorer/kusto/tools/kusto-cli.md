---
title: Kusto CLI - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Kusto CLI dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 7443ad32b78a48f6b35be4f4b680ac6c728aedd2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524240"
---
# <a name="kusto-cli"></a>Kusto CLI

Kusto.Cli est un utilitaire de ligne de commande qui peut être utilisé pour envoyer des demandes à Kusto, et afficher leurs résultats. Kusto.Cli peut fonctionner dans l’un des nombreux modes:

* *Mode REPL*: L’utilisateur tape dans les requêtes et les commandes pour courir contre Kusto, et l’outil affiche les résultats, puis attend la prochaine requête/commande de l’utilisateur.
  (« REPL » signifie « lire/eval/print/loop ».)

* *Mode d’exécution*: L’utilisateur fournit une ou plusieurs requêtes et commandes pour exécuter comme arguments de commande-ligne à l’outil. Ceux-ci sont exécutés dans l’ordre automatiquement, et leurs résultats de sortie à la console. Optionnellement, après avoir exécuté toutes les requêtes et commandes d’entrée, l’outil passe en mode REPL.

* *Mode script*: Similaire à l’exécution du mode, mais avec les requêtes et les commandes spécifiées via un fichier (appelé "script").

Kusto.Cli est principalement prévu pour automatiser les tâches contre un service Kusto qui aurait normalement exigé d’écrire du code (par exemple un programme C ou un script PowerShell.)

## <a name="getting-the-tool"></a>Obtenir l’outil

Kusto.Cli fait partie du `Microsoft.Azure.Kusto.Tools`paquet NuGet , qui peut être téléchargé [ici](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/).
Une fois téléchargé, `tools` le dossier de l’emballage peut être extrait sur le dossier cible; aucune installation supplémentaire n’est requise (c’est-à-dire qu’elle est xcopy-installable).



## <a name="running-the-tool"></a>Exécution de l’outil

Kusto.Cli a besoin d’au moins un argument de ligne de commande pour fonctionner. Habituellement, cet argument est la chaîne de connexion au service Kusto que l’outil devrait se connecter à; voir [les chaînes de connexion Kusto](../api/connection-strings/kusto.md). Exécution de l’outil sans arguments de ligne de commande, `/help` ou avec un ensemble inconnu d’arguments, ou avec le commutateur, entraîne un message d’aide étant émis à la console.

Par exemple, utilisez la commande suivante pour exécuter Kusto.Cli et connectez-vous `help` `Samples` au service Kusto, et définissez le contexte de la base de données à la base de données :

```
Kusto.Cli.exe "https://help.kusto.windows.net/Samples;Fed=true"
```

> [!NOTE]
> Nous avons utilisé des citations autour de la chaîne de connexion pour empêcher les`;`applications de coquille, telles que PowerShell, d’interpréter le point-virgule ( ) et des caractères similaires dans la chaîne de connexion.

## <a name="command-line-arguments"></a>Arguments de ligne de commande

`Kusto.Cli.exe`*ConnectionString* [*Commutateurs*]

*Connectionstring*
* La [chaîne de connexion Kusto](../api/connection-strings/kusto.md) qui détient toutes les informations de connexion Kusto.
  La valeur par défaut est `net.tcp://localhost/NetDefaultDB`.

`-execute:`*DemandeOrCommand*
* Si spécifié, exécute Kusto.Cli en mode d’exécution; la requête ou la commande spécifiée est exécutée. Ce commutateur peut se répéter, auquel cas les requêtes/commandes sont exécutées séquentiellement par ordre d’apparence.
  Ce commutateur ne peut `-script` `-scriptml`pas être utilisé ensemble avec ou .

`-keepRunning:`*EnableKeepRunning*
* Si spécifié (comme l’un `true` ou l’autre ou `false` `-script` ), `-execute` permet ou désactive le passage au mode REPL après tout ou les valeurs ont été traitées.

`-script:`*ScriptFile ScriptFile*
* Si spécifié, exécute Kusto.Cli en mode script; le fichier de script spécifié est chargé et les requêtes ou commandes qui y sont exécutées de façon séquentielle.
  Les lignes neuves sont utilisées pour délimiter les requêtes/commandes, sauf lorsque les lignes se terminent avec `&` ou `&&` les combinaisons, comme expliqué ci-dessous.
  Ce commutateur ne peut `-execute`pas être utilisé avec .

`-scriptml:`*ScriptFile ScriptFile*
* Si spécifié, exécute Kusto.Cli en mode script; le fichier de script spécifié est chargé et les requêtes ou commandes qui y sont exécutées de façon séquentielle.
  L’ensemble du fichier de script est considéré comme une seule requête ou commande.
  Ce commutateur ne peut `-execute`pas être utilisé avec .

`-echo:`*EnableEchoMode*
* Si spécifié (comme l’un ou l’autre `true` ou `false`), permet ou désactive le mode écho.
  Lorsque le mode écho est activé, chaque requête ou commande est répétée dans la sortie.

`-transcript:`*TranscriptFile*  
* Si spécifié, écrit la sortie du programme à *TranscriptFile*.

`-logToConsole:`*EnableLogToConsole*
* Si spécifié (comme l’un ou l’autre `true` ou `false`), permet ou désactive la sortie du programme d’écriture à la console.

`-lineMode:`*EnableLineMode (en)*
* Si spécifié, passe d’un mode d’entrée `true`de ligne (par défaut `false`ou lorsqu’il est configuré en) et bloque le mode d’entrée (lorsqu’il est configuré à ). Voir ci-dessous pour une explication de ces deux modes, qui déterminent comment les nouvelles lignes sont traitées.

**Exemple**

```
Kusto.Cli.exe "https://kustolab.kusto.windows.net/;Fed=true" -script:"c:\mycommands.txt"
```

S’il vous plaît, notez qu’il ne devrait pas y avoir d’espace entre le côlon et la valeur de l’argument.

## <a name="directives"></a>Directives

Kusto.Cli prend en charge un certain nombre de directives qui exécutent dans l’outil plutôt que d’être envoyé au service pour le traitement:

|Directive                      |Description|
|-------------------------------|-----------|
|`?`<br>`#h`<br>`#help`         |Obtenez un court message d’aide.|
|`q`<br>`#quit`<br>`#exit`      |Sortez de l’outil.|
|`#a`<br>`#abort`               |Sortir de l’outil avorté.|
|`#clip`                        |Les résultats de la prochaine requête ou commande seront copiés sur le presse-papiers.|
|`#cls`                         |Effacer l’écran de la console.|
|`#connect`*[ConnectionString*]|Se connecte à un service Kusto différent (si *ConnectionString* est omis, l’actuel sera affiché.)|
|`#crp`[*Name* Nom`=` [ *Valeur*]]   |Définit la valeur d’une propriété de demande de client (ou simplement l’affiche, ou affiche toutes les valeurs).|
|`#crp` (`-list` | `-doc`) [*Préfix ]*|Répertorie les propriétés de demande de clients (par préfixe, ou la totalité).|
|`#dbcontext`[*DatabaseName*]  |Modifications de la base de données « contexte » utilisée par les requêtes et les commandes à *DatabaseName* (si omis, le contexte actuel sera affiché.)|
|`ke` *Texte*                    |Envoie le texte spécifié à un processus Kusto.Explorer en cours d’exécution.|
|`#loop`*Texte de compte* *Text*         |Exécute le texte un certain nombre de fois.|
|`#qp`[*Name* Nom`=` [ *Valeur*]]   |Définit la valeur d’un paramter de requête (ou l’affiche simplement, ou affiche toutes les valeurs). Les citations simples/doubles du début/fin seront coupées.|
|`#save`*Nom de fichier*             |Les résultats de la requête ou de la commande suivante seront enregistrés dans le fichier CSV indiqué.|
|`#script`*Nom de fichier*           |Exécute le script indiqué.|
|`#scriptml`*Nom de fichier*         |Exécute le script multiliné indiqué.|

## <a name="line-input-mode-and-block-input-mode"></a>Mode d’entrée de ligne et mode d’entrée de bloc

Par défaut, Kusto.Cli est en **mode entrée**en ligne : chaque personnage newline est interprété comme un délimitant entre les requêtes/commandes, et la ligne est immédiatement envoyée pour exécution.

Dans ce mode, il est possible de casser une longue requête ou une commande en plusieurs lignes. L’apparition `&` du personnage comme le dernier personnage d’une ligne (avant la nouvelle ligne) provoque Kusto.Cli de continuer à lire la ligne suivante. L’apparition `&&` du personnage comme le dernier personnage d’une ligne (avant la nouvelle ligne) provoque Kusto.Cli d’ignorer la nouvelle ligne et de continuer à lire la ligne suivante.

Alternativement, Kusto.Cli prend également en charge l’exécution en `-lineMode:false` mode entrée de `#blockmode` **bloc**: en utilisant soit l’interrupteur de ligne de commande ou en utilisant la commande, on peut demander à Kusto.Cli de supposer que chaque ligne est une continuation de la ligne précédente, de sorte que les requêtes et les commandes sont délimitées par une ligne d’entrée vide seulement.

## <a name="comments"></a>Commentaires

Kusto.Cli interprète `//` une chaîne qui commence une nouvelle ligne comme une ligne de commentaires. Il ignore le reste de la ligne et continue à lire la ligne suivante.

## <a name="tool-only-options"></a>Options à outils seulement

Commandes                        | Résultat                                                                            | Actuellement
--------------------------------|-----------------------------------------------------------------------------------|-----------
#timeon|#timeoff                | option d’activation/désactivation 'timing': afficher le temps des demandes prises                    | TRUE
#tableon|#tableoff              | option d’activation/désactivation 'tableView': les résultats du format se fixent sous forme de tableaux                  | TRUE
#marson|#marsoff                | option d’activation/désactivation 'marsView': afficher les 2ème-dernier ensembles de résultats             | FALSE
#resultson|#resultsoff          | option d’activation/désactivation 'outputResultsSet': afficher les ensembles de résultats                 | TRUE
#prettyon|#prettyoff            | activer/désactiver l’option 'prettyErrors': supprimer goo inutile des erreurs          | TRUE
#markdownon|#markdownoff        | option d’activation/désactivation 'markdownView': tables de format comme MarkDown                   | FALSE
#progressiveon|#progressiveoff  | option active/désactiver 'progressiveView': demandez et affichez des résultats progressifs  | FALSE
#linemode|#blockmode            | option d’activation/désactivation 'lineMode': mode d’entrée à ligne unique                          | TRUE

Commandes                              | Résultat                                                                                                         | Default
--------------------------------------|----------------------------------------------------------------------------------------------------------------|-----------
#cridon|#cridoff                      | (activer|option de désactivation 'crid': afficher le ClientRequestId avant d’envoyer la demande)                         | FALSE
#csvheaderson|#csvheadersoff          | (activer|option de désactivation 'csvHeaders': inclure des en-têtes dans la sortie CSV)                                            | TRUE
#focuson|#focusoff                    | (activer|désactiver l’option «focus»: supprimer tous les peluches supplémentaires et se concentrer sur les bonnes choses)                       | FALSE
#linemode|#blockmode                  | (activer|option de désactivation 'lineMode': mode d’entrée à ligne unique)                                                     | TRUE
#markdownon|#markdownoff              | (activer|option de désactivation 'markdownView': tables de format comme MarkDown)                                              | FALSE
#marson|#marsoff                      | (activer|option de désactivation 'marsView': afficher les 2ème-dernier ensembles de résultats)                                        | FALSE
#prettyon|#prettyoff                  | (activer|option de désactivation 'prettyErrors': supprimer goo inutile des erreurs)                                     | TRUE
#querystreamingon|#querystreamingoff  | (activer|option de désactivation 'requêteStreaming': utilisez le point de terminaison de requêtestreaming (équipe Kusto seulement))                    | FALSE
#resultson|#resultsoff                | (activer|option de désactivation 'outputResultsSet': afficher les ensembles de résultats)                                            | TRUE
#tableon|#tableoff                    | (activer|option désactive 'tableView': format résultats définit comme tables)                                             | TRUE
#timeon|#timeoff                      | (activer|option de désactivation 'timing': afficher le temps des demandes prises)                                               | TRUE
#typeon|#typeoff                      | (activer|option de désactivation 'typeView': afficher le type de chaque colonne dans la vue de table (forcera Streaming-true))  | TRUE
#v2protocolon|#v2protocoloff          | (activer|option désactive 'v2protocol': utiliser le protocole de requête v2, pas v1)                                        | TRUE

## <a name="using-kustocli-to-export-results-as-csv"></a>Utilisation de Kusto.Cli pour exporter les résultats du CSV

Kusto.Cli prend en charge une `#save`commande spéciale côté client, pour exporter les résultats de la **requête suivante** à un fichier local en format CSV. Par exemple, l’invocation suivante de Kusto.Cli exportera `StormEvents` 10 enregistrements hors de la table dans le `help.kusto.windows.net` cluster (base`Samples` de données) :

```
Kusto.Cli.exe @help/Samples -execute:"#save c:\temp\test.log" -execute:"StormEvents | take 10"
```

## <a name="using-kustocli-to-control-a-running-instance-of-kustoexplorer"></a>Utilisation de Kusto.Cli pour contrôler une instance en cours d’exécution de Kusto.Explorer

Il est possible d’ordonner à Kusto.Cli de communiquer avec l’instance « primaire » de Kusto.Explorer fonctionnant sur la machine, et de l’envoyer des requêtes à exécuter. Cela peut être très utile pour les programmes qui veulent exécuter un certain nombre de requêtes Kusto, mais ne veulent pas commencer le processus Kusto.Explorer encore et encore. Dans l’exemple suivant, Kusto.Cli est utilisé pour exécuter une requête à nouveau le cluster d’aide:

```
#connect cluster('help').database('Samples')

#ke StormEvents | count
```

La syntaxe est `#ke`très simple: , suivie par l’espace blanc et la requête à exécuter.
La requête est ensuite envoyée à l’exemple principal de Kusto.Explorer (si l’on existe) avec le cluster/base de données actuel établi dans Kusto.Cli.