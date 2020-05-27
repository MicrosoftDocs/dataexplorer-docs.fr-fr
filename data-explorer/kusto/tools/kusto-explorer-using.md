---
title: Utilisation de Kusto. Explorer
description: Découvrez comment utiliser Kusto. Explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 98a42fd72a28089e6add53aed5346ce2d0e5d993
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83866133"
---
# <a name="using-kustoexplorer"></a>Utilisation de Kusto. Explorer

Kusto. Explorer est une application de bureau qui vous permet d’explorer vos données à l’aide du langage de requête Kusto dans une interface utilisateur facile à utiliser. Cet article explique comment utiliser les modes de recherche et de requête, partager vos requêtes et gérer les clusters, les bases de données et les tables.

## <a name="search-mode"></a>Mode de recherche + +

Le mode de recherche + + vous permet de rechercher un terme à l’aide de la syntaxe de recherche sur une ou plusieurs tables.

1. Dans l’onglet dossier de démarrage, dans la liste déroulante requête, sélectionnez **Rechercher + +**.
1. Sélectionnez **plusieurs tables**.
1. Sous **choisir des tables**, définissez les tables à rechercher.
1. Dans la zone d’édition, entrez votre expression de recherche, puis sélectionnez **OK**.
1. Une carte thermique de la grille de l’emplacement de table/heure affiche les termes qui apparaissent et leur emplacement.

:::image type="content" source="images/kusto-explorer-using/search-plus-plus.png" alt-text="Rechercher + + Kusto Explorer":::

1. Sélectionnez une cellule dans la grille et sélectionnez **afficher les détails** pour afficher les entrées correspondantes dans le volet des résultats.

:::image type="content" source="images/kusto-explorer-using/search-plus-plus-results.png" alt-text="Résultats de la recherche et de l’Explorateur Kusto":::

## <a name="query-mode"></a>mode Requête

Kusto. Explorer comprend un puissant mode de script qui vous permet d’écrire, de modifier et d’exécuter des requêtes ad hoc. Le mode de script est fourni avec la mise en surbrillance de la syntaxe et IntelliSense, ce qui vous permet de faire rapidement progresser vos connaissances du langage de requête Kusto.

Ce document explique comment exécuter des requêtes de base dans Kusto. Explorer et comment ajouter des paramètres à vos requêtes.

## <a name="basic-queries"></a>Requêtes de base

Si vous avez des journaux de table, vous pouvez commencer à les explorer :

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
StormEvents | count 
```

Lorsque le curseur se trouve sur cette ligne, sa couleur est grisée. Appuyez sur **F5** pour exécuter la requête. 

Voici quelques exemples de requêtes :

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
// Take 10 lines from the table. Useful to get familiar with the data
StormEvents | limit 10 
```

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
// Filter by EventType == 'Flood' and State == 'California' (=~ means case insensitive) 
// and take sample of 10 lines
StormEvents 
| where EventType == 'Flood' and State =~ 'California'
| limit 10
```

:::image type="content" source="images/kusto-explorer-using/basic-query.png" alt-text="Requête de base Kusto Explorer":::

En savoir plus sur le [langage de requête Kusto](https://docs.microsoft.com/azure/kusto/query/).

## <a name="client-side-query-parameterization"></a>Paramétrage des requêtes côté client

> [!Note]
> Il existe deux types de techniques de requête parametrization dans Kusto :
> * La [parametrization de requête intégrée au langage](../query/queryparametersstatement.md) est implémentée dans le cadre du moteur de requête et est destinée à être utilisée par les applications qui interrogent le service par programme. Cette méthode n’est pas décrite dans ce document.
>
> * La requête côté client parametrization, décrite ci-dessous, est une fonctionnalité de l’application Kusto. Explorer uniquement. Cela équivaut à utiliser des opérations de remplacement de chaîne sur les requêtes avant de les envoyer à exécuter par le service. La syntaxe décrite ci-dessous ne fait pas partie du langage de requête lui-même et ne peut pas être utilisée lors de l’envoi de requêtes au service par d’autres moyens que Kusto. Explorer.

Si vous utilisez la même valeur dans plusieurs requêtes ou dans plusieurs onglets, il est très difficile de modifier cette valeur à chaque fois qu’elle est utilisée. C’est pourquoi Kusto. Explorer prend en charge les paramètres de requête. Les paramètres de requête sont partagés entre les onglets afin qu’ils puissent être facilement réutilisés. Les paramètres sont dénotés par des {} crochets. Par exemple : `{parameter1}`

L’éditeur de script met en surbrillance les paramètres de requête :

:::image type="content" source="images/kusto-explorer-using/parametrized-query-1.png" alt-text="Paramétrée requête 1":::

Vous pouvez facilement définir et modifier des paramètres de requête existants :


:::image type="content" source="images/kusto-explorer-using/parametrized-query-2.png" alt-text="Modifier la requête paramétrée 2":::


:::image type="content" source="images/kusto-explorer-using/parametrized-query-3.png" alt-text="Modifier la requête paramétrée 3":::

L’éditeur de script dispose également d’IntelliSense pour les paramètres de requête qui sont déjà définis :

:::image type="content" source="images/kusto-explorer-using/parametrized-query-4.png" alt-text="Requête Paramaterized IntelliSense":::

Vous pouvez avoir plusieurs jeux de paramètres (répertoriés dans la zone de liste déroulante **Parameters Set** ).
Sélectionnez **Ajouter nouveau** ou **supprimer le en cours** pour manipuler la liste des jeux de paramètres.

:::image type="content" source="images/kusto-explorer-using/parametrized-query-5.png" alt-text="Liste des jeux de paramètres":::

## <a name="share-queries-and-results"></a>Partager des requêtes et des résultats

Dans Kusto. Explorer, vous pouvez partager des requêtes et des résultats par courrier électronique. Vous pouvez également créer des liens détaillés qui ouvrent et exécutent une requête dans le navigateur.

### <a name="share-queries-and-results-by-email"></a>Partager des requêtes et des résultats par courrier électronique

Kusto. Explorer offre un moyen pratique de partager des requêtes et des résultats de requête par courrier électronique.

1. [Exécutez votre requête](#basic-queries) dans Kusto. Explorer.
1. Dans l’onglet dossier de démarrage, dans la section partager, sélectionnez **Exporter vers le presse-papiers** (ou appuyez sur Ctrl + Maj + C).

:::image type="content" source="images/kusto-explorer-using/menu-export.png" alt-text="Exporter dans le presse-papiers":::

    Kusto.Explorer pastes the following to the clipboard:
    * Your query
    * The query results (table or chart)
    * The connection details for the Kusto cluster and database
    * A link that will rerun the query automatically

1. Collez le contenu du presse-papiers dans un nouveau message électronique.

:::image type="content" source="images/kusto-explorer-using/share-results-2.png" alt-text="Partager les résultats par courrier électronique":::

### <a name="deep-linking-queries"></a>Requêtes de liaison profonde

Vous pouvez créer un URI qui, lorsqu’il est ouvert dans un navigateur, ouvre Kusto. Explorer localement et exécute une requête spécifique sur une base de données Kusto spécifiée.

### <a name="limitations"></a>Limites

Les requêtes sont limitées à environ 2000 caractères en raison des limitations du navigateur, des proxys HTTP et des outils qui valident les liens, tels que Microsoft Outlook. La limitation est approximative, car elle dépend de la longueur du nom de la base de données et du cluster. Pour plus d’informations, consultez [https://support.microsoft.com/kb/208427](https://support.microsoft.com/kb/208427). Pour réduire le risque d’atteindre la limite de caractères, consultez la rubrique [obtenir des liens plus courts](#getting-shorter-links), ci-dessous.

Le format de l’URI est le suivant :`https://<ClusterCname>.kusto.windows.net/<DatabaseName>web=0?query=<QueryToExecute>`

Par exemple :  [https://help.kusto.windows.net/Samples?web=0query=StormEvents+%7c+limit+10](https://help.kusto.windows.net/Samples?web=0query=StormEvents+%7c+limit+10)
 
Cet URI ouvre Kusto. Explorer, se connecte au `Help` cluster Kusto et exécute la requête spécifiée sur la `Samples` base de données. Si une instance de Kusto. Explorer est déjà en cours d’exécution, l’instance en cours d’exécution ouvrira un nouvel onglet et exécutera la requête dans celle-ci.

> [!Note] 
> Pour des raisons de sécurité, la liaison profonde est désactivée pour les commandes de contrôle.

### <a name="creating-a-deep-link"></a>Création d’un lien profond

Le moyen le plus simple de créer un lien profond consiste à créer votre requête dans Kusto. Explorer, puis `Export to Clipboard` à utiliser pour copier la requête (y compris le lien profond et les résultats) dans le presse-papiers. Vous pouvez ensuite le partager par courrier électronique.
        
En cas de copie dans un message électronique, le lien profond est affiché dans une petite police. Par exemple :

https://help.kusto.windows.net:443/Samples[[Cliquez pour exécuter la requête](https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d)] 

Le premier lien ouvre Kusto. Explorer et définit le contexte du cluster et de la base de données de manière appropriée.
Le deuxième lien ( `Click to run query` ) est le lien profond. Si vous déplacez le lien vers un message électronique et appuyez sur CTRL + K, vous pouvez voir l’URL réelle :

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

### <a name="deep-links-and-parametrized-queries"></a>Liens profond et requêtes paramétrée

Vous pouvez utiliser des requêtes paramétrée avec des liens approfondis.

1. Créer une requête à former en tant que requête paramétrée (par exemple, `KustoLogs | where Timestamp > ago({Period}) | count` ) 
1. Fournissez un paramètre pour chaque paramètre de requête dans l’URI dans le cas suivant :

https://mycluster.kusto.windows.net/MyDatabase?web=0&query=KustoLogs+%7c+where+Timestamp+>+ago({Period})+%7c+count&Period=1h

### <a name="getting-shorter-links"></a>Obtenir des liens plus courts

Les requêtes peuvent devenir longues. Pour réduire le risque que la requête dépasse la longueur maximale, utilisez la `String Kusto.Data.Common.CslCommandGenerator.EncodeQueryAsBase64Url(string query)` méthode disponible dans la bibliothèque cliente Kusto. Cette méthode produit une version plus compacte de la requête. Le format plus petit est également reconnu par Kusto. Explorer.

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

La requête est rendue plus compacte en appliquant la transformation suivante :

```csharp
 UrlEncode(Base64Encode(GZip(original query)))
```

## <a name="kustoexplorer-command-line-arguments"></a>Arguments de ligne de commande Kusto. Explorer

Les arguments de ligne de commande permettent de configurer l’outil pour exécuter des fonctions supplémentaires au démarrage. Par exemple, chargez un script et connectez-vous à un cluster. Par conséquent, les arguments de ligne de commande ne remplacent pas les fonctionnalités Kusto. Explorer.

Les arguments de ligne de commande sont passés dans le cadre de l’URL utilisée pour ouvrir l’application, de la même façon pour [interroger des liens profond](#creating-a-deep-link).

## <a name="command-line-argument-syntax"></a>Syntaxe des arguments de ligne de commande

Kusto. Explorer prend en charge plusieurs arguments de ligne de commande dans la syntaxe suivante (l’ordre est important) :

[*LocalScriptFile*] [*QueryString*]

* *LocalScriptFile* est le nom d’un fichier de script sur votre ordinateur local, qui doit avoir l’extension `.kql` . Si un tel fichier existe, Kusto. Explorer charge automatiquement ce fichier lors de son démarrage.
* *QueryString* est une chaîne qui utilise la mise en forme de la chaîne de requête http. Cette méthode fournit des propriétés supplémentaires, comme décrit dans le tableau ci-dessous.

Par exemple, pour démarrer Kusto. Explorer avec un fichier de script nommé `c:\temp\script.kql` et configuré pour communiquer avec `help` le cluster, base de données `Samples` , utilisez la commande suivante :

```kusto
Kusto.Explorer.exe c:\temp\script.kql uri=https://help.kusto.windows.net/Samples;Fed=true&name=Samples
```

|Argument  |Description                                                               |
|----------|--------------------------------------------------------------------------|
|**Requête à exécuter**                                                                 |
|`query`   |Requête à exécuter (encodée en base64). S’il est vide, utilisez `querysrc` .          |
|`querysrc`|URL d’un fichier ou d’un objet blob contenant la requête à exécuter (si `query` est vide).|
|**Connexion au cluster Kusto**                                                  |
|`uri`     |Chaîne de connexion du cluster Kusto auquel se connecter.                 |
|`name`    |Nom complet de la connexion au cluster Kusto.                  |
|**Groupe de connexions**                                                                 |
|`path`    |URL d’un fichier de groupe de connexions à télécharger (encodé URL).             |
|`group`   |Nom du groupe de connexions.                                         |
|`filename`|Fichier local contenant le groupe de connexions.                              |


## <a name="manage-clusters-databases-tables-or-function-authorized-principals"></a>Gérer des clusters, des bases de données, des tables ou des principaux autorisés de fonction

> [!Note]
> Seuls les [administrateurs](../management/access-control/role-based-authorization.md) peuvent ajouter ou supprimer des principaux autorisés dans leur propre étendue.

Cliquez avec le bouton droit sur l’entité cible dans le [panneau connexions](kusto-explorer.md#connections-tab), puis sélectionnez **gérer les principaux autorisés du cluster**. (Vous pouvez également sélectionner cette option dans le menu gestion.)

:::image type="content" source="images/kusto-explorer-using/right-click-manage-authorized-principals.png" alt-text="Gérer les principaux autorisés":::

:::image type="content" source="images/kusto-explorer-using/manage-authorized-principals-window.png" alt-text="Fenêtre gérer les entités de gestion autorisées":::

* Pour ajouter un nouveau principal autorisé, sélectionnez **Ajouter un principal**, fournissez les détails du principal et confirmez l’action.
    
    :::image type="content" source="images/kusto-explorer-using/add-authorized-principals-window.png" alt-text="Ajouter un principal autorisé":::

    :::image type="content" source="images/kusto-explorer-using/confirm-add-authorized-principals.png" alt-text="Confirmer l’ajout du principal autorisé":::

* Pour supprimer un principal autorisé existant, sélectionnez **Drop principal** et confirmez l’action.

    :::image type="content" source="images/kusto-explorer-using/confirm-drop-authorized-principals.png" alt-text="Confirmer la suppression du principal autorisé":::


## <a name="next-steps"></a>Étapes suivantes

* [Raccourcis clavier de Kusto. Explorer](kusto-explorer-shortcuts.md)
* [Options de Kusto. Explorer](kusto-explorer-options.md)
* [Résolution des problèmes de Kusto. Explorer](kusto-explorer-troubleshooting.md)

En savoir plus sur les outils et utilitaires Kusto. Explorer :
* [Analyseur de code Kusto. Explorer](kusto-explorer-code-analyzer.md)
* [Navigation dans le code Kusto. Explorer](kusto-explorer-codenav.md)
* [Refactorisation du code Kusto. Explorer](kusto-explorer-refactor.md)
* [Langage de requête Kusto (KQL)](https://docs.microsoft.com/azure/kusto/query/)
