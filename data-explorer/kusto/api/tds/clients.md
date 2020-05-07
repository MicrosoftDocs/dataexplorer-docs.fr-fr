---
title: Clients MS-TDS et Kusto-Azure Explorateur de données
description: Cet article décrit les clients MS-TDS et Kusto dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 10/30/2019
ms.openlocfilehash: b41f77fe97ce6adeeade63c00824818f4a3af721
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862035"
---
# <a name="ms-tds-clients-and-azure-data-explorer"></a>Clients MS-TDS et Azure Explorateur de données

Azure Explorateur de données implémente des points de terminaison compatibles TDS pour les clients MS-SQL. La compatibilité est au niveau du protocole. Toute bibliothèque ou application pouvant se connecter à la base de données SQL Azure avec l’authentification Azure Active Directory (Azure AD) fonctionne avec le serveur Azure Explorateur de données. Par conséquent, vous pouvez utiliser le nom de domaine du serveur comme celui du serveur de SQL Azure.

Azure Explorateur de données implémente un sous-ensemble du T-SQL et un sous-ensemble de l’émulation SQL Server. Pour plus d’informations, consultez [problèmes connus](./sqlknownissues.md) concernant les différences entre l’implémentation de SQL Server de T-SQL et d’Azure Explorateur de données.

## <a name="net-sql-client"></a>Client SQL .NET

Azure Explorateur de données prend en charge l’authentification Azure AD pour les clients SQL. Pour plus d’informations, consultez [client SQL .net (authentification utilisateur)](./aad.md#net-sql-client-user) et [client .net SQL (authentification de l’application)](./aad.md#net-sql-client-application) .

## <a name="jdbc"></a>JDBC

Le pilote Microsoft JDBC peut être utilisé pour se connecter à Azure Explorateur de données avec l’authentification Azure AD.

Créez une application pour utiliser l’une des versions de *MSSQL-JDBC* jar et *adal4j* jar, ainsi que toutes leurs dépendances.
Par exemple,

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

Créez une application pour utiliser la classe de pilote JDBC *com. Microsoft. SqlServer. JDBC. SQLServerDriver*.

Utilisez une chaîne de connexion semblable à la suivante.

```s
jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword
```

> [!NOTE]
> Si vous souhaitez utiliser le mode d’authentification intégrée Azure Active Directory, remplacez *ActiveDirectoryPassword* par *ActiveDirectoryIntegrated*. Pour plus d’informations, consultez [JDBC (authentification utilisateur)](./aad.md#jdbc-user) et [JDBC (authentification de l’application)](./aad.md#jdbc-application).

## <a name="odbc"></a>ODBC

Les applications qui prennent en charge ODBC peuvent se connecter à Azure Explorateur de données.

Créer une source de données ODBC :

1. Lancez l'Administrateur de sources de données ODBC.
2. Sélectionnez **Ajouter** pour créer une nouvelle source de données, puis définissez le *pilote ODBC 17 sur SQL Server*.
3. Donnez un nom à la source de données et spécifiez le nom du cluster Azure Explorateur de données dans le champ **serveur** . Par exemple, *mykusto.Kusto.Windows.net*.
4. Définissez **Active Directory intégré**, pour l’option d’authentification.
5. Sélectionnez **suivant** pour définir la base de données.
7. Vous pouvez simplement conserver les valeurs par défaut pour tous les autres paramètres des onglets qui suivent.
8. Sélectionnez **Terminer** pour ouvrir la fenêtre Résumé de la source de données, dans laquelle la connexion peut être testée.

Vous pouvez maintenant utiliser la source de données ODBC avec les applications.

Si l’application ODBC peut accepter une chaîne de connexion au lieu de, ou en plus de DSN, utilisez ce qui suit.

```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
```

Certaines applications ODBC ne fonctionnent pas correctement avec `NVARCHAR(MAX)` le type. Pour plus d'informations, consultez https://docs.microsoft.com/sql/relational-databases/native-client/features/using-large-value-types?view=sql-server-2017#sql-server-native-client-odbc-driver.

La solution de contournement courante consiste à effectuer un cast des données retournées en *nvarchar (n)*, avec une valeur pour n. Par exemple, *nvarchar (4000)*. Toutefois, une telle solution ne fonctionne pas pour Azure Explorateur de données, car Azure Explorateur de données n’a qu’un seul type de chaîne et pour les clients SQL, il est encodé en tant que *nvarchar (max)*.

Azure Explorateur de données offre une solution de contournement différente. Vous pouvez configurer Azure Explorateur de données pour encoder toutes les chaînes en tant que *nvarchar (n)* via une chaîne de connexion. Le champ Language dans la chaîne de connexion peut être utilisé pour spécifier les options de paramétrage dans le format, * language@OptionName1:OptionValue1, OptionName2 : OptionValue2*.

Par exemple, la chaîne de connexion suivante indique à Azure Explorateur de données de coder les chaînes en tant que *nvarchar (8000)*.

```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated,Language=any@MaxStringSize:8000"
```

### <a name="powershell"></a>PowerShell

Voici un exemple de script PowerShell qui utilise le pilote ODBC.

```powershell
$conn = [System.Data.Common.DbProviderFactories]::GetFactory("System.Data.Odbc").CreateConnection()
$conn.ConnectionString = "Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
$conn.Open()
$conn.GetSchema("Tables")
$conn.Close()
```

## <a name="linqpad"></a>LINQPad

Une application LINQ peut être utilisée avec Azure Explorateur de données, en la connectant comme s’il s’agissait d’un serveur SQL Server.
Utilisez LINQPad pour explorer la compatibilité LINQ et parcourir Azure Explorateur de données. Il peut également exécuter des requêtes SQL et est l’outil recommandé pour explorer les points de terminaison Azure Explorateur de données TDS (SQL).

Connectez-vous comme vous le feriez à l’Microsoft SQL Server. LINQPad prend en charge l’authentification Active Directory.

1. Sélectionnez **Ajouter une connexion**.
2. Définissez le **contexte de génération de données automatiquement**.
3. Définissez la valeur **par défaut (LINQ to SQL)**, le pilote LINQPad.
4. Définissez **SQL Azure**.
5. Pour le serveur, spécifiez le nom du cluster Azure Explorateur de données. Par exemple, *mykusto.Kusto.Windows.net*.
6. Définissez **authentification Windows (Active Directory)** pour la connexion.
7. Sélectionnez **test** pour vérifier la connectivité.
8. Sélectionnez **OK**. La fenêtre du navigateur affiche l’arborescence avec les bases de données.
9. Vous pouvez parcourir les bases de données, les tables et les colonnes.
10. Vous pouvez exécuter des requêtes SQL dans la fenêtre de requête. Spécifiez le langage SQL et sélectionnez une connexion à la base de données.
11. Vous pouvez également exécuter des requêtes LINQ dans la fenêtre de requête. Par exemple, sélectionnez une table dans la fenêtre du navigateur. Sélectionnez **nombre**et laissez-le s’exécuter.

## <a name="azure-data-studio-134-and-above"></a>Azure Data Studio (1.3.4 et versions ultérieures)

Établissez une nouvelle connexion.

1. Définissez le type de connexion sur **Microsoft SQL Server**.
2. Spécifiez le nom du cluster Azure Explorateur de données en tant que nom de serveur. Par exemple, *mykusto.Kusto.Windows.net*.
3. Définissez le type d’authentification **Azure Active Directory-Universal avec prise en charge MFA**.
4. Spécifiez le compte configuré dans la Azure AD. Par exemple : *myname@contoso.com* . Ajoutez le compte la première fois.
5. Utilisez le sélecteur de base de données pour sélectionner la base de données.
6. Sélectionnez **se connecter** pour accéder au tableau de bord de la base de données et définir la connexion.
7. Sélectionnez **nouvelle requête** pour ouvrir la fenêtre de requête, ou sélectionnez la nouvelle tâche de **requête** dans le tableau de bord.

## <a name="power-bi-desktop"></a>Power BI Desktop

Connectez-vous de la même manière à la base de données SQL Azure.

1. Sous **obtenir des données**, sélectionnez **plus**.
2. Définissez **Azure**, puis **Azure SQL Database**.
3. Spécifiez le nom du serveur de Explorateur de données Azure. Par exemple, *mykusto.Kusto.Windows.net*.
4. Sélectionnez **DirectQuery**.
5. Définissez **compte Microsoft** l’authentification (et non **Windows**), puis sélectionnez **se connecter**.
6. Le sélecteur de base de données affiche les bases de données disponibles. Continuez comme vous le feriez avec un serveur SQL Server réel.

## <a name="excel"></a>Excel

Connectez-vous de la même manière à la base de données SQL Azure.

1. Sélectionnez **accéder aux données** sous l’onglet **données** , puis définissez **à partir d’Azure** et **de Azure SQL Database**.
2. Spécifiez le nom du serveur de Explorateur de données Azure. Par exemple, *mykusto.Kusto.Windows.net*.
3. Définissez **compte Microsoft** l’authentification (et non **Windows**), puis sélectionnez **se connecter**.
5. Le sélecteur de base de données affiche les bases de données disponibles. Continuez comme vous le feriez avec un serveur SQL Server réel.
4. Une fois connecté, sélectionnez **se connecter**.
5. Le sélecteur de base de données affiche les bases de données disponibles. Continuez comme vous le feriez avec un serveur SQL Server réel.

## <a name="tableau"></a>Tableau

Créer une source de données ODBC. Pour plus d’informations, consultez la section [ODBC](./clients.md#odbc) .

1. Se connecter par le biais d' **autres bases de données (ODBC)**.
2. Définissez la source de données ODBC dans le **DSN**.
3. Sélectionnez **se connecter** pour établir une connexion.
4. Sélectionnez **se connecter**, une fois que le bouton est disponible, puis connectez-vous à Azure Explorateur de données.

## <a name="dbeaver-533-and-above"></a>DBeaver (5.3.3 et versions ultérieures)

Configurez DBeaver pour la gestion des jeux de résultats d’une manière compatible avec Azure Explorateur de données.

1. Sélectionnez **Préférences** dans le menu **fenêtre** .
2. Sélectionnez **éditeur de données** dans la section **éditeurs** .
3. Assurez-vous que l’option **Actualiser les données lors de la lecture de la page suivante** est marquée.

Créez une connexion à la base de données Azure Explorateur de données.

1. Sélectionnez **nouvelle connexion** dans le menu **base de données** .
2. Recherchez **Azure** et définissez **Azure SQL Database**. Sélectionnez **Suivant**.
3. Spécifiez l’hôte. Par exemple, *mykusto.Kusto.Windows.net*.
4. Spécifiez la base de données. Par exemple, *MyDatabase*.

> [!WARNING]
> N’utilisez pas *Master* comme nom de base de données. Azure Explorateur de données nécessite une connexion à une base de données spécifique.

5. Définissez **Active Directory-Password** pour *l’authentification*.
6. Spécifiez les informations d’identification de l’utilisateur Active Directory. Par exemple, *myname@contoso.com*, et définissent le mot de passe correspondant pour cet utilisateur.
7. Sélectionner **tester la connexion...** pour vérifier que les détails de connexion sont corrects.

## <a name="microsoft-sql-server-management-studio-v18x"></a>Management Studio Microsoft SQL Server (v18. x)

1. Sélectionnez **se connecter**, puis **moteur de base de données** sous l' **Explorateur d’objets**.
2. Spécifiez le nom du cluster Azure Explorateur de données en tant que nom de serveur. Par exemple, *mykusto.Kusto.Windows.net*.
3. Définissez **Active Directory intégrée** pour l’authentification.
4. Sélectionnez **options**.
5. Sélectionnez **Parcourir le serveur** sous **se connecter à la base de données** pour parcourir les bases de données disponibles.
6. Sélectionnez **Oui** pour poursuivre la navigation.
7. La fenêtre affiche une arborescence avec toutes les bases de données disponibles. Sélectionnez-en un pour vous connecter à la base de données.
8. Une autre possibilité consiste à sélectionner la **valeur par défaut** sous **se connecter à la base de données**, puis à sélectionner **se connecter**. L’Explorateur d’objets affiche toutes les bases de données.

> [!NOTE]
> La navigation dans les objets de base de données via SSMS n’est pas encore prise en charge, puisque SSMS utilise des sous-requêtes corrélées pour parcourir le schéma de base de données.
> Les sous-requêtes corrélées ne sont pas prises en charge par Azure Explorateur de données. Pour plus d’informations, consultez sous- [requêtes corrélées](./sqlknownissues.md#correlated-sub-queries).

10. Sélectionnez **nouvelle requête** pour ouvrir la fenêtre de requête et définir votre base de données.

Vous pouvez exécuter des requêtes SQL personnalisées à partir de la fenêtre de requête.

## <a name="matlab-via-jdbc"></a>MATLAB (via JDBC)

Ajoutez les fichiers JAR requis au début du CLASSPATH statique de MATLAB en créant un fichier *« javaclasspath. txt »* dans votre répertoire de préférences.

1. Exécutez la commande suivante dans la fenêtre de commande de Matlab.

``` s
edit(fullfile(prefdir,'javaclasspath.txt'))
```

2. Ajoutez les chemins d’accès complets aux fichiers JAR requis.

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

> [!NOTE]
> Vous avez besoin de la <**avant**> en haut, afin que ces fichiers soient ajoutés au début du CLASSPATH. Remplacez également *c:\full\path\to* par les chemins complets réels de ces fichiers.

3. Redémarrez MATLAB pour vous assurer que ces classes sont chargées.

4. Avant d’essayer de vous connecter, exécutez la commande suivante (fenêtre de commande MATLAB).

``` java
java.lang.System.clearProperty('javax.xml.transform.TransformerFactory')
```

> [!NOTE]
> Cela rétablit la valeur par défaut de **TransformerFactory** (Matlab la surcharge habituellement avec **Saxon**, mais cela est incompatible avec **ADAL4J**).

5. Connectez-vous au point de terminaison Azure Explorateur de données TDS à l’aide de la commande suivante (fenêtre de commande MATLAB).

```s
conn = database('<<KUSTO_DATABASE>>','<<AAD_USER>>','<<USER_PWD>>','com.microsoft.sqlserver.jdbc.SQLServerDriver',['jdbc:sqlserver://<<MYCLUSTER>>.kusto.windows.net:1433;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword;database='])
 ```

> [!NOTE]
> Il est OK de se terminer par **« Database = »** , puis aucune valeur. La fonction *de base de données* ajoute automatiquement la première entrée, le nom de la base de données, à cette chaîne.
> Si vous souhaitez utiliser Azure Active Directory mode d’authentification intégrée, remplacez *ActiveDirectoryPassword* par *ActiveDirectoryIntegrated*.

6. Testez la connexion et exécutez un exemple de requête. Soumettez les commandes suivantes (fenêtre de commande MATLAB).

```s
data = select(conn, 'SELECT * FROM <<KUSTO_TABLE>>')
data
```

> [!NOTE]
> Remplacez *KUSTO_TABLE* par une table existante dans Azure Explorateur de données.

## <a name="sending-t-sql-queries-over-the-rest-api"></a>Envoi de requêtes T-SQL sur l’API REST

L' [API REST d’Azure Explorateur de données](../rest/index.md) peut accepter et exécuter des requêtes T-SQL.

1. Envoyez la demande au point de terminaison de requête avec la propriété **CSL** définie sur le texte de la requête T-SQL.
2. Définissez la **[propriété de requête](../netfx/request-properties.md)** **OptionQueryLanguage** sur **SQL**.
