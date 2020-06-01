---
title: Kusto CLI-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Kusto CLI dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: ab82698054e4bcb851f9f05acdd62af569e0704b
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84258111"
---
# <a name="kusto-cli"></a>Interface CLI Kusto

Kusto. CLI est un utilitaire de ligne de commande qui peut être utilisé pour envoyer des requêtes à Kusto et afficher ses résultats. Kusto. CLI peut s’exécuter dans l’un des modes suivants :

* *Mode REPL*: l’utilisateur tape les requêtes et les commandes à exécuter sur Kusto, et l’outil affiche les résultats, puis attend la requête/la commande utilisateur suivante.
  (« REPL » signifie « lecture/eval/impression/boucle ».)

* *Mode exécution*: l’utilisateur fournit une ou plusieurs requêtes et commandes à exécuter en tant qu’arguments de ligne de commande à l’outil. Celles-ci sont exécutées en séquence automatiquement et leurs résultats sont générés dans la console. Si vous le souhaitez, après avoir exécuté toutes les requêtes et commandes d’entrée, l’outil passe en mode REPL.

* *Mode script*: similaire au mode exécution, mais avec les requêtes et les commandes spécifiées par le biais d’un fichier (appelé « script »).

Kusto. CLI est principalement fourni pour l’automatisation des tâches sur un service Kusto qui aurait normalement dû écrire du code (par exemple, un programme C# ou un script PowerShell).

## <a name="getting-the-tool"></a>Obtention de l’outil

Kusto. CLI fait partie du package NuGet `Microsoft.Azure.Kusto.Tools` , qui peut être téléchargé [ici](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/).
Une fois téléchargée, le `tools` dossier du package peut être extrait dans le dossier cible. aucune installation supplémentaire n’est nécessaire (par exemple, il est possible d’installer xcopy).



## <a name="running-the-tool"></a>Exécution de l’outil

Kusto. CLI requiert qu’au moins un argument de ligne de commande s’exécute. En règle générale, cet argument est la chaîne de connexion au service Kusto à laquelle l’outil doit se connecter ; consultez [chaînes de connexion Kusto](../api/connection-strings/kusto.md). L’exécution de l’outil sans arguments de ligne de commande, ou avec un jeu d’arguments inconnu, ou avec le `/help` commutateur, entraîne l’émission d’un message d’aide dans la console.

Par exemple, utilisez la commande suivante pour exécuter Kusto. CLI et vous connecter au `help` service Kusto, puis définissez le contexte de base de données sur la `Samples` base de données :

```
Kusto.Cli.exe "https://help.kusto.windows.net/Samples;Fed=true"
```

> [!NOTE]
> Nous avons utilisé des guillemets autour de la chaîne de connexion pour empêcher les applications de Shell, telles que PowerShell, d’interpréter le point-virgule ( `;` ) et les caractères similaires dans la chaîne de connexion.

## <a name="command-line-arguments"></a>Arguments de ligne de commande

`Kusto.Cli.exe`*ConnectionString* [*commutateurs*]

*ConnectionString*
* [Chaîne de connexion Kusto](../api/connection-strings/kusto.md) qui contient toutes les informations de connexion Kusto.
  La valeur par défaut est `net.tcp://localhost/NetDefaultDB`.

`-execute:`*QueryOrCommand*
* S’il est spécifié, exécute Kusto. CLI en mode exécution ; la requête ou la commande spécifiée est exécutée. Ce commutateur peut être répété. dans ce cas, les requêtes/commandes sont exécutées de manière séquentielle par ordre d’apparition.
  Ce commutateur ne peut pas être utilisé avec `-script` ou `-scriptml` .

`-keepRunning:`*EnableKeepRunning*
* S’il est spécifié (en tant que `true` ou `false` ), active ou désactive le basculement en mode REPL après le traitement de toutes les `-script` `-execute` valeurs ou.

`-script:`*ScriptFile*
* S’il est spécifié, exécute Kusto. CLI en mode script ; le fichier de script spécifié est chargé et les requêtes ou les commandes qu’il contient sont exécutées de manière séquentielle.
  Les nouvelles lignes sont utilisées pour délimiter les requêtes/commandes, sauf lorsque les lignes se terminent par des `&` `&&` combinaisons ou, comme expliqué ci-dessous.
  Ce commutateur ne peut pas être utilisé avec `-execute` .

`-scriptml:`*ScriptFile*
* S’il est spécifié, exécute Kusto. CLI en mode script ; le fichier de script spécifié est chargé et les requêtes ou les commandes qu’il contient sont exécutées de manière séquentielle.
  La totalité du fichier de script est considérée comme une requête ou une commande unique.
  Ce commutateur ne peut pas être utilisé avec `-execute` .

`-echo:`*EnableEchoMode*
* S’il est spécifié (en tant que `true` ou `false` ), active ou désactive le mode Echo.
  Lorsque le mode Echo est activé, chaque requête ou commande est répétée dans la sortie.

`-transcript:`*TranscriptFile*  
* S’il est spécifié, écrit la sortie du programme dans *TranscriptFile*.

`-logToConsole:`*EnableLogToConsole*
* S’il est spécifié (en tant que `true` ou `false` ), active ou désactive l’écriture de la sortie du programme sur la console.

`-lineMode:`*EnableLineMode*
* Si ce paramètre est spécifié, bascule entre le mode de saisie de ligne (valeur par défaut ou lorsque la valeur est définie sur `true` ) et le mode de saisie de bloc (lorsque la valeur est définie sur `false` ). Voir ci-dessous une explication de ces deux modes, qui déterminent le mode de traitement des nouvelles lignes.

**Exemple**

```
Kusto.Cli.exe "https://kustolab.kusto.windows.net/;Fed=true" -script:"c:\mycommands.txt"
```

Notez qu’il ne doit y avoir aucun espace entre le signe deux-points et la valeur de l’argument.

## <a name="directives"></a>Directives

Kusto. CLI prend en charge un certain nombre de directives qui s’exécutent dans l’outil au lieu d’être envoyées au service pour traitement :

|Directive                      |Description|
|-------------------------------|-----------|
|`?`<br>`#h`<br>`#help`         |Recevez un bref message d’aide.|
|`q`<br>`#quit`<br>`#exit`      |Quittez l’outil.|
|`#a`<br>`#abort`               |Quittez l’outil abortively.|
|`#clip`                        |Les résultats de la requête ou de la commande suivante seront copiés dans le presse-papiers.|
|`#cls`                         |Effacez l’écran de la console.|
|`#connect`*[ConnectionString*]|Établit une connexion à un autre service Kusto (si *ConnectionString* est omis, l’objet actuel est affiché.)|
|`#crp`[*Nom* [ `=` *valeur*]]   |Définit la valeur d’une propriété de demande du client (ou l’affiche simplement, ou affiche toutes les valeurs).|
|`#crp`( `-list` \| `-doc` ) [*Préfixe*]|Répertorie les propriétés de demande du client (par préfixe ou tous).|
|`#dbcontext`[*DatabaseName*]  |Modifie la base de données « Context » utilisée par les requêtes et les commandes en *DatabaseName* (en cas d’omission, le contexte actuel est affiché.)|
|`ke` *Texte*                    |Envoie le texte spécifié à un processus Kusto. Explorer en cours d’exécution.|
|`#loop`*Compter* le *texte*         |Exécute le texte plusieurs fois.|
|`#qp`[*Nom* [ `=` *valeur*]]   |Définit la valeur d’un paramètre de requête (ou l’affiche simplement, ou affiche toutes les valeurs). Les guillemets simples/doubles entre le début et la fin seront supprimés.|
|`#save`*Nom du fichier*             |Les résultats de la requête ou de la commande suivante seront enregistrés dans le fichier CSV indiqué.|
|`#script`*Nom du fichier*           |Exécute le script indiqué.|
|`#scriptml`*Nom du fichier*         |Exécute le script multiligne indiqué.|

## <a name="line-input-mode-and-block-input-mode"></a>Mode de saisie de ligne et mode de saisie de bloc

Par défaut, Kusto. CLI s’exécute en **mode de saisie de ligne**: chaque caractère de saut de ligne est interprété comme un délimiteur entre les requêtes/commandes, et la ligne est immédiatement envoyée pour exécution.

Dans ce mode, il est possible de fractionner une longue requête ou une commande en plusieurs lignes. L’apparence du `&` caractère en tant que dernier caractère d’une ligne (avant le saut de ligne) entraîne la poursuite de la lecture de la ligne suivante par Kusto. cli. L’apparence du `&&` caractère comme dernier caractère d’une ligne (avant le saut de ligne) amène Kusto. CLI à ignorer le saut de ligne et à poursuivre la lecture de la ligne suivante.

Kusto. CLI prend également en charge l’exécution en **mode de saisie de bloc**: à l’aide du commutateur de ligne de commande ou à l' `-lineMode:false` aide de la commande `#blockmode` , vous pouvez demander à Kusto. CLI de supposer que chaque ligne est une continuation de la ligne précédente, afin que les requêtes et les commandes soient délimitées par une ligne d’entrée vide uniquement.

## <a name="comments"></a>Commentaires

Kusto. CLI interprète une `//` chaîne qui commence une nouvelle ligne comme une ligne de commentaire. Elle ignore le reste de la ligne et poursuit la lecture de la ligne suivante.

## <a name="tool-only-options"></a>Options de l’outil uniquement

Commandes                        | Résultat                                                                            | Cours
--------------------------------|-----------------------------------------------------------------------------------|-----------
#timeon|#timeoff                | activation/désactivation de l’option « timing » : afficher l’heure à laquelle les demandes ont été effectuées                    | VRAI
#tableon|#tableoff              | activer/désactiver l’option’tableView' : mettre en forme les jeux de résultats sous forme de tables                  | VRAI
#marson|#marsoff                | activer/désactiver l’option « marsView » : afficher les jeux de résultats de 2e à dernière             | FAUX
#resultson|#resultsoff          | activer/désactiver l’option « outputResultsSet » : afficher les jeux de résultats                 | VRAI
#prettyon|#prettyoff            | activer/désactiver l’option « prettyErrors » : supprimer les goo inutiles des erreurs          | VRAI
#markdownon|#markdownoff        | activer/désactiver l’option « markdownView » : mettre en forme les tableaux en tant que démarque                   | FAUX
#progressiveon|#progressiveoff  | activer/désactiver l’option « progressiveView » : demander et afficher les résultats progressifs  | FAUX
#linemode|#blockmode            | option d’activation/désactivation de « lineMode » : mode d’entrée sur une seule ligne                          | VRAI

Commandes                              | Résultat                                                                                                         | Default
--------------------------------------|----------------------------------------------------------------------------------------------------------------|-----------
#cridon|#cridoff                      | (activer|désactiver l’option « CRID » : afficher le ClientRequestId avant l’envoi de la demande)                         | FAUX
#csvheaderson|#csvheadersoff          | (activer|désactiver l’option « csvHeaders » : inclure les en-têtes dans la sortie CSV)                                            | VRAI
#focuson|#focusoff                    | (activer|désactiver l’option « Focus » : supprimer tous les attractifs supplémentaires et se concentrer sur les bonnes choses.                       | FAUX
#linemode|#blockmode                  | (activer|désactiver l’option « lineMode » : mode d’entrée sur une seule ligne)                                                     | VRAI
#markdownon|#markdownoff              | (activer|désactiver l’option « markdownView » : mettre en forme les tableaux comme démarques)                                              | FAUX
#marson|#marsoff                      | (activer|désactiver l’option « marsView » : afficher les jeux de résultats de 2e à dernière fois                                        | FAUX
#prettyon|#prettyoff                  | (activer|désactiver l’option « prettyErrors » : supprimer les goo inutiles des erreurs)                                     | VRAI
#querystreamingon|#querystreamingoff  | (activer|désactiver l’option’queryStreaming' : utiliser le point de terminaison queryStreaming (Kusto Team uniquement))                    | FAUX
#resultson|#resultsoff                | (activer|désactiver l’option « outputResultsSet » : afficher les jeux de résultats)                                            | VRAI
#tableon|#tableoff                    | (activer|désactiver l’option’tableView' : mettre en forme les jeux de résultats sous forme de tables)                                             | VRAI
#timeon|#timeoff                      | (activer|désactiver l’option « timing » : afficher les demandes de temps effectuées.                                               | VRAI
#typeon|#typeoff                      | (activer|désactiver l’option « typeView » : afficher le type de chaque colonne dans la vue table (force la diffusion en continu = true))  | VRAI
#v2protocolon|#v2protocoloff          | (activer|désactiver l’option « v2protocol » : utiliser le protocole de requête v2, et non v1)                                        | VRAI

## <a name="using-kustocli-to-export-results-as-csv"></a>Utilisation de Kusto. CLI pour exporter les résultats au format CSV

Kusto. CLI prend en charge une commande spéciale côté client, `#save` , pour exporter les résultats de la requête **suivante** dans un fichier local au format CSV. Par exemple, l’appel suivant de Kusto. CLI exportera 10 enregistrements de la `StormEvents` table dans le `help.kusto.windows.net` cluster ( `Samples` base de données) :

```
Kusto.Cli.exe @help/Samples -execute:"#save c:\temp\test.log" -execute:"StormEvents | take 10"
```

## <a name="using-kustocli-to-control-a-running-instance-of-kustoexplorer"></a>Utilisation de Kusto. CLI pour contrôler une instance en cours d’exécution de Kusto. Explorer

Il est possible d’ordonner à Kusto. CLI de communiquer avec l’instance « principale » de Kusto. Explorer en cours d’exécution sur l’ordinateur et de lui envoyer des requêtes à exécuter. Cela peut être très utile pour les programmes qui souhaitent exécuter un certain nombre de requêtes Kusto, mais ne souhaitent pas redémarrer le processus Kusto. Explorer. Dans l’exemple suivant, Kusto. CLI est utilisé pour exécuter une requête à nouveau sur le cluster d’aide :

```
#connect cluster('help').database('Samples')

#ke StormEvents | count
```

La syntaxe est très simple : `#ke` , suivie d’un espace blanc et de la requête à exécuter.
La requête est ensuite envoyée à l’instance principale de Kusto. Explorer (le cas échéant) avec le cluster/la base de données actuel défini dans Kusto. cli.
