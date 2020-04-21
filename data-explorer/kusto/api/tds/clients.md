---
title: MS-TDS clients et Kusto - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les clients MS-TDS et Kusto dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 233eec65c14d76b25b76cc85c7ddd190e5171dfe
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523492"
---
# <a name="ms-tds-clients-and-kusto"></a>MS-TDS clients et Kusto

Kusto met en œuvre le critère d’évaluation conforme TDS pour les clients MS-SQL. Étant donné que la compatibilité est au niveau du protocole, toute bibliothèque ou application qui peut être connectée à la base de données SQL Azure avec l’authentification Azure Active Directory, fonctionnerait avec le serveur Kusto, orthogonalement au système d’exploitation ou à l’exécution-temps utilisé. Il suffit d’utiliser le nom de domaine serveur Kusto comme si c’était le serveur SQL Azure.

Au niveau de la langue SQL, Kusto implémente un sous-ensemble de T-SQL et un sous-ensemble d’émulation du serveur SQL. Voir [les problèmes connus](./sqlknownissues.md) pour certaines des principales différences entre la mise en œuvre de SQL Server de T-SQL et Kusto.

## <a name="net-sql-client"></a>.NET SQL client

Kusto prend en charge l’authentification Azure Active Directory pour les clients SQL.

Pour plus de détails voir [.NET SQL Client (authentification utilisateur)](./aad.md#net-sql-client-user) et [.NET SQL Client (authentification de l’application)](./aad.md#net-sql-client-application)



## <a name="jdbc"></a>JDBC

Le pilote Microsoft JDBC peut être utilisé pour se connecter à Kusto avec l’authentification AAD.

Faites l’application pour utiliser l’une des versions de *mssql-jdbc* JAR et *adal4j* JAR et tous leurs dependecies. Par exemple :

```s
mssql-jdbc-7.0.0.jre8.jar
adal4j-1.6.3.jar
accessors-smart-1.2.jar
activation-1.1.jar
asm-5.0.4.jar
commons-codec-1.11.jar
commons-lang3-3.5.jar
gson-2.8.0.jar
javax.mail-1.6.1.jar
jcip-annotations-1.0-1.jar
json-smart-2.3.jar
lang-tag-1.4.4.jar
nimbus-jose-jwt-6.5.jar
oauth2-oidc-sdk-5.64.4.jar
slf4j-api-1.7.21.jar
```

Faire de l’appliation pour utiliser la classe de conducteur JDBC`com.microsoft.sqlserver.jdbc.SQLServerDriver`

Utilisez la chaîne de connexion comme :

```s
jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword
```

Si vous souhaitez utiliser le mode auth intégré AAD, *remplacez ActiveDirectoryPassword* par *ActiveDirectoryIntegrated*

Pour plus de détails, voir [JDBC (authentification utilisateur)](./aad.md#jdbc-user) et [JDBC (authentification de l’application)](./aad.md#jdbc-application)

## <a name="odbc"></a>ODBC

Les applications qui prennent en charge ODBC peuvent se connecter à Kusto.

Créez la source de données ODBC :
1. Lancez l’administrateur de source de données ODBC.
2. Créer une nouvelle `Add`source de données (bouton ).
3. Sélectionnez `ODBC Driver 17 for SQL Server`.
3. Donnez un nom à la source de données `Server` et spécifiez le nom du cluster Kusto dans le champ, par exemple.`mykusto.kusto.windows.net`
4. Sélectionnez `Active Directory Integrated` l’option d’authentification.
5. L’onglet suivant permet de sélectionner la base de données.
7. Peut laisser par défaut pour tous les autres paramètres dans les onglets suivants.
8. `Finish`le bouton ouvre le dialogue sommaire de source de données où la connexion peut être testée.
9. La source ODBC peut maintenant être utilisée avec des applications.

Si l’application ODBC peut accepter la chaîne de connexion à la place ou en plus de DSN, utilisez la chaîne de connexion suivante :
```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
```

Certaines applications ODBC ne fonctionnent pas bien avec le https://docs.microsoft.com/sql/relational-databases/native-client/features/using-large-value-types?view=sql-server-2017#sql-server-native-client-odbc-driver type NVARCHAR(MAX) (voir pour plus de détails). La solution de contournement commune utilisée pour une telle application est de jeter les données retournées à NVARCHAR(n), avec une certaine valeur pour n, par exemple NVARCHAR(4000). Une telle solution de contournement ne fonctionnerait pas pour Kusto, puisque Kusto n’a qu’un seul type de chaîne et pour les clients SQL, il est codé comme NVARCHAR (MAX). Kusto offre une solution de contournement différente pour de telles applications. Il est possible de configurer Kusto pour coder toutes les chaînes comme NVARCHAR(n) via la chaîne de connexion. Le champ linguistique dans la chaîne de connexion peut `language@OptionName1:OptionValue1,OptionName2:OptionValue2`être utilisé pour spécifier les options de réglage dans un format: . 

Par exemple, la chaîne de connexion suivante demandera à Kusto d’encoder les chaînes sous le titre NVARCHAR(8000) :
```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated,Language=any@MaxStringSize:8000"
```

### <a name="powershell"></a>PowerShell

Exemple de script PowerShell qui utilise le pilote ODBC :

```powershell
$conn = [System.Data.Common.DbProviderFactories]::GetFactory("System.Data.Odbc").CreateConnection()
$conn.ConnectionString = "Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
$conn.Open()
$conn.GetSchema("Tables")
$conn.Close()  

```



## <a name="linqpad"></a>LINQPad

Il est possible d’utiliser des applications Linq avec Kusto en se connectant à Kusto comme s’il s’élivait le serveur SQL.
LINQPad peut être utilisé pour explorer la compatibilité Linq. Il peut également être utilisé pour parcourir Kusto et pour exécuter des requêtes SQL.
LINQPad est l’outil recommandé pour explorer le critère d’évaluation de Kusto TDS (SQL).

Connectez-vous comme vous vous connectez à Microsoft SQL Server. Avis, LINQPad prend en charge l’authentification active de répertoire.

1. Cliquez sur `Add connection`.
2. Choisissez `Build data context automatically`.
3. Choisissez le pilote `Default (LINQ to SQL)`LINQPad .
4. En tant `SQL Azure`que fournisseur choisir .
5. Pour le serveur spécifier le nom du cluster Kusto, par exemple.`mykusto.kusto.windows.net`
6. Pour la connexion `Windows Authentication (Active Directory)`peut choisir .
7. Cliquez `Test` sur le bouton pour vérifier la connectivité.
8. Cliquez `OK` sur le bouton. La fenêtre du navigateur affiche la vue de l’arbre avec des bases de données.
9. Vous pouvez parcourir les bases de données, les tableaux et les colonnes.
10. Dans la fenêtre de requête, vous pouvez exécuter des requêtes SQL. Spécifier la langue SQL et choisir la connexion à la base de données.
11. Dans la fenêtre de requête peut également exécuter des requêtes LINQ. Par exemple, cliquez à droite sur une table dans la fenêtre du navigateur. Option `Count` de sélection. Laisse-le s’enfuir.

## <a name="azure-data-studio-134-and-above"></a>Azure Data Studio (1.3.4 et plus)

1. Nouvelle connexion.
2. Sélectionnez le `Microsoft SQL Server`type de connexion: .
3. Spécifier le nom du cluster Kusto comme nom de serveur, par exemple.`mykusto.kusto.windows.net`
4. Sélectionnez le `Azure Active Directory - Universal with MFA support`type d’authentification: .
5. Spécifier le compte fourni dans `myname@contoso.com` AAD, par exemple (Ajouter le compte la première fois).
6. Le cueilleur de bases de données peut être utilisé pour sélectionner la base de données.
7. `Connect`vous amènerait au tableau de bord de base de données.
8. Cliquez à droite `New Query` sur la connexion et `New Query` sélectionnez pour ouvrir l’onglet de requête ou cliquez sur la tâche sur le tableau de bord.

## <a name="power-bi-desktop"></a>Power BI Desktop

Connectez-vous comme vous vous connectez à SQL Azure Database.

1. Dans `Get Data` `More`le `Azure` choix, puis et puis`Azure SQL Database`
2. Spécifier le nom du serveur Kusto par exemple.`mykusto.kusto.windows.net`
3. Utilisez l’option "DirectQuery".
4. Choisissez `Microsoft account` l’authentification (pas `Windows`) et cliquez . `sign in`
5. Le cueilleur affiche les bases de données disponibles. Continuez comme vous le feriez avec le serveur SQL réel.

## <a name="excel"></a>Excel

Connectez-vous comme vous vous connectez à SQL Azure Database.

1. En `Data` `Get Data`onglet, `From Azure`, ,`From Azure SQL Database`
2. Spécifier le nom du serveur Kusto par exemple.`mykusto.kusto.windows.net`
3. Choisissez `Microsoft account` l’authentification (pas `Windows`) et cliquez . `sign in`
4. Une fois connecté, cliquez . `Connect`
5. Le cueilleur affiche les bases de données disponibles. Continuez comme vous le feriez avec le serveur SQL réel.

## <a name="tableau"></a>Tableau

1. Créez la source de données ODBC, voir la section [ODBC.](./clients.md#odbc)
2. Connectez-vous via `Other Databases (ODBC)`.
3. Sélectionnez la source `DSN`de données ODBC dans .
4. Utilisez `Connect` le bouton pour établir la connexion.
5. Une `Sign In` fois le bouton disponible, connectez-vous à Kusto.

## <a name="dbeaver-533-and-above"></a>DBeaver (5.3.3 et plus)

Configurez DBeaver pour la manipulation des ensembles de résultats d’une manière compatible avec Kusto :

1. Dans `Window` la `Preferences`sélection de menu .
2. Dans `Editors` la `Data Editor`section sélectionnez .
3. Assurez-vous que l’option `Refresh data on next page reading` est vérifiée.

Créer une connexion à la base de données Kusto :

1. Créez une nouvelle`Database` connexion `New Connection` de base de données (menu et option).
2. Rechercher `Azure` et `Azure SQL Database`sélectionner . Appuyez sur `Next`.
3. Spécifier l’hôte, par exemple `mykusto.kusto.windows.net` `mydatabase`et la base de données, par exemple . Ne laissez pas le maître comme nom de base de données. Kusto nécessite une connexion à une base de données spécifique.
4. Pour l’option `Active Directory - Password`d’authentification sélectionnez .
5. Spécifier les informations d’identification `myname@contoso.com` de l’utilisateur d’annuaire actif, par exemple et mot de passe correspondant pour cet utilisateur.
6. Appuyez `Test Connection …` pour vérifier que les détails de connexion sont corrects.

## <a name="microsoft-sql-server-management-studio-v18x"></a>Microsoft SQL Server Management Studio (v18.x)

1. Dans `Object Explorer` `Connect`, `Database Engine`, .
2. Spécifier le nom du cluster Kusto comme nom de serveur, par exemple.`mykusto.kusto.windows.net`
3. Option `Active Directory - Integrated` d’utilisation pour l’authentification.
4. Cliquez sur `Options`.
5. En `Connect to database` combo, vous pouvez parcourir `Browse Server` les bases de données disponibles via l’option.
6. Cliquez `Yes` pour procéder à la navigation.
7. Le dialogue affiche la vue des arbres avec toutes les bases de données disponibles. Peut cliquer sur un pour procéder à la connexion à la base de données.
8. Alternativement, `Connect to database` en combo, `default`peut choisir . Cliquez sur `Connect`. Object Explorer afficherait toutes les bases de données.
9. La navigation des objets de base de données via SSMS n’est pas encore prise en charge, puisque SSMS utilise des sous-requêtes corrélées pour parcourir le schéma de base de données. Sous-requêtes corrélées ne sont pas prises en charge par Kusto, voir [sous-requêtes corrélées](./sqlknownissues.md#correlated-sub-queries).
10. Cliquez sur votre base de données. Cliquez `New Query` sur l’option pour ouvrir la fenêtre de requête.
11. Peut exécuter des requêtes SQL personnalisées à partir de la fenêtre de requête.

## <a name="matlab-via-jdbc"></a>MATLAB (via JDBC)

Ajoutez les fichiers JAR requis à l’avant du chemin de classe statique de MATLAB. Pour ce faire, créez un fichier *"javaclasspath.txt"* dans votre répertoire préférences. Exécutez la commande suivante dans la fenêtre de commande de Matlab : 

``` s
edit(fullfile(prefdir,'javaclasspath.txt'))
```

Et ajoutez les chemins complets aux fichiers JAR requis : 

``` s
<before>
c:\full\path\to\accessors-smart-1.2.jar
c:\full\path\to\activation-1.1.jar
c:\full\path\to\adal4j-1.6.3.jar
c:\full\path\to\asm-5.0.4.jar
c:\full\path\to\commons-codec-1.11.jar
c:\full\path\to\commons-lang3-3.5.jar
c:\full\path\to\gson-2.8.0.jar
c:\full\path\to\javax.mail-1.6.1.jar
c:\full\path\to\jcip-annotations-1.0-1.jar
c:\full\path\to\json-smart-2.3.jar
c:\full\path\to\lang-tag-1.4.4.jar
c:\full\path\to\mssql-jdbc-7.0.0.jre8.jar
c:\full\path\to\nimbus-jose-jwt-6.5.jar
c:\full\path\to\oauth2-oidc-sdk-5.64.4.jar
c:\full\path\to\slf4j-api-1.7.21.jar
```

> Vous avez vraiment besoin de la <*avant* de> en haut pour ajouter ces fichiers à l’avant de la classpath. Remplacez également «c: 'full’path’à" avec les chemins complets réels vers ces fichiers. 

Redémarrez MATLAB pour vous assurer que ces classes sont en fait chargées.

Avant d’essayer de connecter exécuter la commande suivante (fenêtre de commande MATLAB):

``` java
java.lang.System.clearProperty('javax.xml.transform.TransformerFactory')
```

> Cela réinitialise le *TransformerFactory* à la valeur par défaut (MATLAB surcharge généralement cela avec Saxon, mais cela est incompatible avec ADAL4J).

Pour vous connecter au point de terminaison Kusto TDS, soumettez la commande suivante (fenêtre de commande MATLAB) :

```s
conn = database('<<KUSTO_DATABASE>>','<<AAD_USER>>','<<USER_PWD>>','com.microsoft.sqlserver.jdbc.SQLServerDriver',['jdbc:sqlserver://<<MYCLUSTER>>.kusto.windows.net:1433;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword;database='])
 ```

> Il est exact ici que cela se termine simplement par *"base de données"* et puis aucun nom, la fonction "base de données" va automatiquement ajouter la première entrée (le nom de base de données) à cette chaîne.
> Si vous souhaitez utiliser le mode auth intégré AAD, *remplacez ActiveDirectoryPassword* par *ActiveDirectoryIntegrated*

Testez la connexion et exécutez une requête d’échantillon. Soumettre la matrice suivant les commandes (fenêtre de commande MATLAB) :

```s
data = select(conn, 'SELECT * FROM <<KUSTO_TABLE>>')
data
```

> Remplacer *KUSTO_TABLE* par une table existante à Kusto



## <a name="sending-t-sql-queries-over-the-rest-api"></a>Envoi de requêtes T-SQL sur l’API REST

[L’API Kusto REST](../rest/index.md) peut accepter et exécuter des requêtes T-SQL.
Pour ce faire, envoyez la demande au `csl` point de terminaison de la requête avec la propriété fixée au `sql`texte de la requête T-SQL elle-même, et la propriété [de](../netfx/request-properties.md) `OptionQueryLanguage` demande fixée à la valeur .