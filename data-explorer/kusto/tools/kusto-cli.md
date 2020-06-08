---
title: Kusto CLI-Azure Explorateur de données
description: Cet article décrit Kusto CLI dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: adc606c787ab20f228a9fbd132d1b82660aa57c6
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512484"
---
# <a name="kusto-cli"></a>Interface CLI Kusto

Kusto. CLI est un utilitaire de ligne de commande qui permet d’envoyer des requêtes à Kusto et d’afficher les résultats. Il peut s’exécuter dans l’un des modes suivants :

* *Mode REPL*: l’utilisateur entre des requêtes et des commandes, et l’outil affiche les résultats, puis attend la requête/la commande utilisateur suivante.
  (« REPL » signifie « lecture/eval/impression/boucle ».)

* *Mode exécution*: l’utilisateur entre une ou plusieurs requêtes et commandes à exécuter en tant qu’arguments de ligne de commande. Les arguments sont exécutés automatiquement en séquence et leurs résultats sont générés dans la console. Si vous le souhaitez, après avoir exécuté toutes les requêtes et commandes d’entrée, l’outil passe en mode REPL.

* *Mode script*: similaire au mode exécution, mais avec les requêtes et les commandes spécifiées par le biais d’un fichier « script ».

Kusto. CLI est principalement fourni pour l’automatisation des tâches sur un service Kusto qui requiert normalement l’écriture de code. Par exemple, un programme C# ou un script PowerShell.

## <a name="get-the-tool"></a>Obtenir l’outil

Kusto. CLI fait partie du package NuGet `Microsoft.Azure.Kusto.Tools` que [vous pouvez télécharger](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/).
Une fois téléchargé, extrayez le dossier du package `tools` dans le dossier cible. Aucune installation supplémentaire n’est nécessaire, car xcopy peut être installé.

## <a name="run-the-tool"></a>Exécution de l'outil

Kusto. CLI requiert qu’au moins un argument de ligne de commande s’exécute. En règle générale, cet argument est la chaîne de connexion au service Kusto à laquelle l’outil doit se connecter.
Pour plus d’informations, consultez [chaînes de connexion Kusto](../api/connection-strings/kusto.md). Si vous exécutez l’outil sans arguments de ligne de commande, avec un jeu d’arguments inconnu ou avec le `/help` commutateur, un message d’aide s’affiche sur la console.

Par exemple, utilisez la commande suivante pour exécuter Kusto. cli. La commande se connecte au `help` service Kusto et définit le contexte de la base de données sur la `Samples` base de données :

```
Kusto.Cli.exe "https://help.kusto.windows.net/Samples;Fed=true"
```

> [!NOTE]
> Utilisez des guillemets doubles autour de la chaîne de connexion pour empêcher les applications de l’interpréteur de commandes telles que PowerShell de mal interpréter le point-virgule ( `;` ) et les caractères similaires.

## <a name="command-line-arguments"></a>Arguments de ligne de commande

`Kusto.Cli.exe`*ConnectionString* [*commutateurs*]

*ConnectionString*
* [Chaîne de connexion Kusto](../api/connection-strings/kusto.md) qui contient toutes les informations de connexion Kusto.
  La valeur par défaut est `net.tcp://localhost/NetDefaultDB`.

`-execute:`*QueryOrCommand*
* S’il est spécifié, exécute Kusto. CLI en mode exécution et la requête ou la commande spécifiée est exécutée. Ce commutateur peut se répéter et les requêtes/commandes sont exécutées de manière séquentielle par ordre d’apparition.
  Ce commutateur ne peut pas être utilisé avec `-script` ou `-scriptml` .

`-keepRunning:`*EnableKeepRunning*
* S’il est spécifié, comme `true` ou `false` , il active ou désactive le mode REPL après le traitement de toutes les `-script` `-execute` valeurs ou.

`-script:`*ScriptFile*
* S’il est spécifié, exécute Kusto. CLI en mode script. Le fichier de script spécifié est chargé et les requêtes ou les commandes qu’il contient sont exécutées de manière séquentielle.
  Les nouvelles lignes sont utilisées pour délimiter les requêtes/commandes, sauf lorsque les lignes se terminent par une `&` `&&` combinaison ou, comme expliqué ci-dessous.
  Ce commutateur ne peut pas être utilisé avec `-execute` .

`-scriptml:`*ScriptFile*
* S’il est spécifié, exécute Kusto. CLI en mode script. Le fichier de script spécifié est chargé et les requêtes ou les commandes qu’il contient sont exécutées de manière séquentielle.
  La totalité du fichier de script est considérée comme une requête ou une commande unique.
  Ce commutateur ne peut pas être utilisé avec `-execute` .

`-echo:`*EnableEchoMode*
* S’il est spécifié, comme `true` ou `false` , il active ou désactive le mode Echo.
  Lorsque le mode Echo est activé, chaque requête ou commande est répétée dans la sortie.

`-transcript:`*TranscriptFile*  
* S’il est spécifié, écrit la sortie du programme dans *TranscriptFile*.

`-logToConsole:`*EnableLogToConsole*
* S’il est spécifié, comme `true` ou `false` , il active ou désactive l’affichage de la sortie du programme sur la console.

`-lineMode:`*EnableLineMode*
* Si ce paramètre est spécifié, bascule entre le mode de saisie de ligne par défaut, lorsqu’il est défini sur `true` et le mode de saisie de bloc, lorsqu’il a la valeur `false` . Voir ci-dessous une explication de ces deux modes, qui déterminent le mode de traitement des nouvelles lignes.

**Exemple**

```
Kusto.Cli.exe "https://kustolab.kusto.windows.net/;Fed=true" -script:"c:\mycommands.txt"
```

> [!NOTE] 
> Il ne doit y avoir aucun espace entre le signe deux-points et la valeur d’argument

## <a name="directives"></a>Directives

Kusto. CLI exécute un certain nombre de directives dans l’outil au lieu de les envoyer au service pour traitement.

|Directive                      |Description|
|-------------------------------|-----------|
|`?`<br>`#h`<br>`#help`         |Obtenir un bref message d’aide|
|`q`<br>`#quit`<br>`#exit`      |Quitter l’outil|
|`#a`<br>`#abort`               |Quitter l’outil abortively|
|`#clip`                        |Les résultats de la requête ou de la commande suivante seront copiés dans le presse-papiers|
|`#cls`                         |Effacer l’écran de la console|
|`#connect`*[ConnectionString*]|Établit une connexion à un autre service Kusto (si *ConnectionString* est omis, l’objet actuel est affiché)|
|`#crp`[*Nom* [ `=` *valeur*]]   |Définit la valeur d’une propriété de demande du client, ou simplement l’affiche, ou affiche toutes les valeurs|
|`#crp`( `-list` \| `-doc` ) [*Préfixe*]|Répertorie les propriétés de demande du client, par préfixe ou tout|
|`#dbcontext`[*DatabaseName*]  |Modifie la base de données « Context » utilisée par les requêtes et les commandes en *DatabaseName*. En cas d’omission, le contexte actuel affiche|
|`ke` *Texte*                    |Envoie le texte spécifié à un processus Kusto. Explorer en cours d’exécution|
|`#loop`*Compter* le *texte*         |Exécute le texte plusieurs fois|
|`#qp`[*Nom* [ `=` *valeur*]]   |Définit la valeur d’un paramètre de requête ou l’affiche simplement, ou affiche toutes les valeurs. Les guillemets simples/doubles au début/à la fin seront supprimés|
|`#save` *Nom de fichier*             |Les résultats de la requête ou de la commande suivante seront enregistrés dans le fichier CSV indiqué|
|`#script` *Nom de fichier*           |Exécute le script indiqué|
|`#scriptml` *Nom de fichier*         |Exécute le script multiligne indiqué|

## <a name="line-input-mode-and-block-input-mode"></a>Mode de saisie de ligne et mode de saisie de bloc

Par défaut, Kusto. CLI s’exécute en **mode d’entrée de ligne**. Chaque caractère de saut de ligne est interprété comme un délimiteur entre les requêtes/commandes, et la ligne est immédiatement envoyée pour exécution.

Dans ce mode, vous pouvez scinder une requête ou une commande longue en plusieurs lignes. Le `&` caractère en tant que dernier caractère d’une ligne, avant le saut de ligne, fait en sorte que Kusto. CLI continue à lire la ligne suivante. Le `&&` caractère comme dernier caractère d’une ligne, avant le saut de ligne, fait en sorte que Kusto. CLI ignore le saut de ligne et continue la lecture de la ligne suivante.

Kusto. CLI prend également en charge l’exécution en **mode de saisie de bloc**. En utilisant le commutateur de ligne de commande `-lineMode:false` ou en utilisant la directive `#blockmode` , vous pouvez ordonner à Kusto. CLI de supposer que chaque ligne est une continuation de la ligne précédente, afin que les requêtes et les commandes soient délimitées par une ligne d’entrée vide uniquement.

## <a name="comments"></a>Commentaires

Kusto. CLI interprète une `//` chaîne qui commence une nouvelle ligne comme une ligne de commentaire. Elle ignore le reste de la ligne et poursuit la lecture de la ligne suivante.

## <a name="tool-only-options"></a>Options de l’outil uniquement

Commandes                        | Effet                                                                            | Cours
--------------------------------|-----------------------------------------------------------------------------------|-----------
#timeon|#timeoff                | option d’activation/ `timing` de désactivation : afficher l’heure à laquelle les demandes ont été effectuées                    | VRAI
#tableon|#tableoff              | option d’activation/de désactivation `tableView` : mettre en forme les jeux de résultats sous forme de tables                  | VRAI
#marson|#marsoff                | option d’activation/désactivation `marsView` : afficher les jeux de résultats de second à dernier          | FAUX
#resultson|#resultsoff          | option d’activation/ `outputResultsSet` de désactivation : afficher les jeux de résultats                 | VRAI
#prettyon|#prettyoff            | option d’activation/désactivation `prettyErrors` : nettoyer les erreurs                             | VRAI
#markdownon|#markdownoff        | option Activer/désactiver `markdownView` : mettre en forme les tableaux en tant que démarque                   | FAUX
#progressiveon|#progressiveoff  | option d’activation/désactivation `progressiveView` : demander et afficher les résultats progressifs  | FAUX
#linemode|#blockmode            | option d’activation/désactivation `lineMode` : mode de saisie sur une seule ligne                          | VRAI

Commandes                              | Effet                                                                                                         | Default
--------------------------------------|----------------------------------------------------------------------------------------------------------------|-----------
#cridon|#cridoff                      | (activer|option Disable `crid` : afficher le ClientRequestId avant l’envoi de la demande)                          | FAUX
#csvheaderson|#csvheadersoff          | (activer|option Disable `csvHeaders` : inclure les en-têtes dans la sortie CSV)                                            | VRAI
#focuson|#focusoff                    | (activer|option de désactivation `focus` : supprimez toutes les attractifs supplémentaires et concentrez-vous sur les bonnes choses.                        | FAUX
#linemode|#blockmode                  | (activer|option Disable `lineMode` : mode de saisie sur une seule ligne)                                                      | VRAI
#markdownon|#markdownoff              | (activer|option de désactivation `markdownView` : mettre en forme les tableaux comme démarques)                                              | FAUX
#marson|#marsoff                      | (activer|option Disable `marsView` : afficher les jeux de résultats de second à dernier                                      | FAUX
#prettyon|#prettyoff                  | (activer|option de désactivation `prettyErrors` : nettoyer les erreurs)                                                        | VRAI
#querystreamingon|#querystreamingoff  | (activer|option de désactivation `queryStreaming` : utiliser le point de terminaison queryStreaming (équipe Kusto uniquement))                    | FAUX
#resultson|#resultsoff                | (activer|option Disable `outputResultsSet` : afficher les jeux de résultats)                                            | VRAI
#tableon|#tableoff                    | (activer|option Disable `tableView` : mettre en forme les jeux de résultats sous forme de tables)                                              | VRAI
#timeon|#timeoff                      | (activer|option de désactivation `timing` : affiche la durée d’exécution des requêtes.                               | VRAI
#typeon|#typeoff                      | (activer|option Disable `typeView` : affiche le type de chaque colonne dans la vue table. Force streaming = true)| VRAI
#v2protocolon|#v2protocoloff          | (activer|option Disable `v2protocol` : utiliser le protocole de requête v2, et non v1)                                        | VRAI

## <a name="use-kustocli-to-export-results-as-csv"></a>Utiliser Kusto. CLI pour exporter les résultats au format CSV

Kusto. CLI a une commande spéciale côté client `#save` qui exporte les résultats de la requête **suivante** dans un fichier local au format CSV. Par exemple, la ligne suivante exporte 10 enregistrements de la `StormEvents` table vers le `help.kusto.windows.net` cluster, `Samples` base de données :

```
Kusto.Cli.exe @help/Samples -execute:"#save c:\temp\test.log" -execute:"StormEvents | take 10"
```

## <a name="use-kustocli-to-control-a-running-instance-of-kustoexplorer"></a>Utilisez Kusto. CLI pour contrôler une instance en cours d’exécution de Kusto. Explorer

Vous pouvez demander à Kusto. CLI de communiquer avec l’instance « principale » de Kusto. Explorer en cours d’exécution sur l’ordinateur et de lui envoyer des requêtes. Ce mécanisme peut être utile pour les programmes qui souhaitent exécuter un certain nombre de requêtes, mais ne souhaitent pas démarrer le processus Kusto. Explorer à plusieurs reprises. Dans l’exemple suivant, Kusto. CLI est utilisé pour exécuter une requête sur le cluster d’aide :

```
#connect cluster('help').database('Samples')

#ke StormEvents | count
```

La syntaxe est simple : `#ke` , suivie d’un espace blanc et de la requête à exécuter.
La requête est ensuite envoyée à l’instance principale de Kusto. Explorer, le cas échéant, avec le cluster/la base de données en cours défini dans Kusto. cli.
 
