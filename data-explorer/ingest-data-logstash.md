---
title: Ingérer des données depuis Logstash vers Azure Data Explorer
description: Dans cet article, vous allez apprendre à ingérer (charger) des données dans Azure Data Explorer depuis Logstash.
author: orspod
ms.author: orspodek
ms.reviewer: takamara
ms.service: data-explorer
ms.topic: how-to
ms.date: 11/26/2020
ms.openlocfilehash: 83d382de4f5bd683dff934267b5258ece475a8da
ms.sourcegitcommit: 1bdbfdc04c4eac405f3931059bbeee2dedd87004
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303356"
---
# <a name="ingest-data-from-logstash-to-azure-data-explorer-preview"></a>Ingérer des données Logstash dans Azure Data Explorer (préversion)

[Logstash](https://www.elastic.co/products/logstash) est un pipeline open source de traitement de données côté serveur qui ingère simultanément des données provenant de nombreuses sources, les transforme, puis les envoie à votre « remise » préférée. Dans cet article, vous allez envoyer ces données à Azure Data Explorer, c’est-à-dire à un service rapide et hautement scalable d’exploration de données de journal et de télémétrie. Vous allez commencer par créer une table et un mappage de données dans un cluster de test, puis vous allez demander à Logstash d’envoyer des données dans la table et valider les résultats.

> [!NOTE]
> Ce connecteur prend actuellement en charge seulement le format de données JSON.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’en avez pas, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Un [cluster de test et une base de données](create-cluster-database-portal.md) Azure Data Explorer
* [Instructions d’installation](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html) de Logstash version 6+

## <a name="create-a-table"></a>Créer une table

Une fois que vous avez un cluster et une base de données, le moment est venu de créer une table.

1. Exécutez la commande suivante dans votre fenêtre de requête de base de données pour créer une table :

    ```Kusto
    .create table logs (timestamp: datetime, message: string)
    ```

1. Exécutez la commande suivante pour vérifier que la nouvelle table `logs` a été créée et qu’elle est vide :
    ```Kusto
    logs
    | count
    ```

## <a name="create-a-mapping"></a>Créer un mappage

Le mappage est utilisé par Azure Data Explorer pour transformer les données entrantes en schéma de table cible. La commande suivante crée un mappage nommé `basicmsg` qui permet d’extraire les propriétés du json entrant comme indiqué par `path` et d’en générer la sortie dans `column`.

Exécutez la commande suivante dans la fenêtre de requête :

```Kusto
.create table logs ingestion json mapping 'basicmsg' '[{"column":"timestamp","path":"$.@timestamp"},{"column":"message","path":"$.message"}]'
```

## <a name="install-the-logstash-output-plugin"></a>Installer le plug-in de sortie de Logstash

Le plug-in de sortie de Logstash communique avec Azure Data Explorer pour envoyer les données au service.
Exécutez la commande suivante dans le répertoire racine de Logstash pour installer le plug-in :

```sh
bin/logstash-plugin install logstash-output-kusto
```

## <a name="configure-logstash-to-generate-a-sample-dataset"></a>Configurer Logstash pour générer un exemple de jeu de données

Logstash peut générer des exemples d’événements que vous pouvez utiliser pour tester un pipeline de bout en bout.
Si vous utilisez déjà Logstash et que vous avez accès à votre propre flux d’événements, passez à la section suivante. 

> [!NOTE]
> Si vous utilisez vos propres données, modifiez la table et les objets de mappage définis au cours des étapes précédentes.

1. Éditez un nouveau fichier texte qui contiendra les paramètres nécessaires du pipeline (en utilisant vi) :

    ```sh
    vi test.conf
    ```

1. Collez les paramètres suivants qui indiqueront à Logstash de générer 1 000 événements de test :

    ```ruby
    input {
        stdin { }
        generator {
            message => "Test Message 123"
            count => 1000
        }
    }
    ```

Cette configuration inclut également le plug-in d’entrée `stdin` qui vous permet d’écrire plus de messages par vous-même (veillez à utiliser *Entrée* pour les envoyer dans le pipeline).

## <a name="configure-logstash-to-send-data-to-azure-data-explorer"></a>Configurer Logstash pour envoyer des données à Azure Data Explorer

Collez les paramètres suivants dans le même fichier config que celui utilisé à l’étape précédente. Remplacez tous les espaces réservés par les valeurs appropriées à votre configuration. Pour plus d’informations, consultez [Création d’une application AAD](./provision-azure-ad-app.md). 

```ruby
output {
    kusto {
            path => "/tmp/kusto/%{+YYYY-MM-dd-HH-mm-ss}.txt"
            ingest_url => "https://ingest-<cluster name>.kusto.windows.net/"
            app_id => "<application id>"
            app_key => "<application key/secret>"
            app_tenant => "<tenant id>"
            database => "<database name>"
            table => "<target table>" # logs as defined above
            mapping => "<mapping name>" # basicmsg as defined above
    }
}
```

| Nom du paramètre | Description |
| --- | --- |
| **path** | Le plug-in Logstash écrit des événements dans des fichiers temporaires avant de les envoyer à Azure Data Explorer. Ce paramètre inclut un chemin indiquant l’emplacement auquel les fichiers doivent être écrits, ainsi qu’une expression temporelle de rotation de fichiers qui déclenche un chargement vers le service Azure Data Explorer.|
| **ingest_url** | Point de terminaison Kusto pour la communication liée à l’ingestion.|
| **app_id**, **app_key** et **app_tenant**| Informations d’identification nécessaires pour la connexion à Azure Data Explorer. Veillez à utiliser une application dotée de privilèges d’ingestion. |
| **database**| Nom de la base de données où placer des événements. |
| **table** | Nom de la table cible où placer des événements. |
| **mapping** | Le mappage est utilisé pour mapper une chaîne json d’événement entrant sur le format de ligne approprié (il définit quelle propriété va dans quelle colonne). |

## <a name="run-logstash"></a>Exécuter Logstash

Nous sommes maintenant prêts à exécuter Logstash pour tester nos paramètres.

1. Dans le répertoire racine de Logstash, exécutez la commande suivante :

    ```sh
    bin/logstash -f test.conf
    ```

    Des informations doivent apparaître à l’écran, ainsi que les 1 000 messages générés par notre exemple de configuration. À ce stade, vous pouvez également entrer d’autres messages manuellement.

1. Après quelques minutes, exécutez la requête Data Explorer suivante pour voir les messages dans la table que vous avez définie :

    ```Kusto
    logs
    | order by timestamp desc
    ```

1. Sélectionnez Ctrl+C pour quitter Logstash.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Exécutez la commande suivante dans votre base de données pour nettoyer la table `logs` :

```Kusto
.drop table logs
```

## <a name="next-steps"></a>Étapes suivantes

* [Écrire des requêtes](write-queries.md)
