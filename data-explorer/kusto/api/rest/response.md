---
title: Réponse HTTP requête/gestion-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la réponse HTTP requête/gestion dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/05/2018
ms.openlocfilehash: 5a9166066ee664f15b07dff2b0a535044ebb929c
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799643"
---
# <a name="querymanagement-http-response"></a>Réponse HTTP d’interrogation ou de gestion

## <a name="response-status"></a>État de la réponse

La ligne d’état de la réponse HTTP suit les codes de réponse HTTP standard.
Par exemple, le code 200 indique une réussite. 

Les codes d’état suivants sont en cours d’utilisation, bien que tout code HTTP valide puisse être retourné.

|Code|Sous-code        |Description                                    |
|----|---------------|-----------------------------------------------|
|100 |Continue       |Le client peut continuer à envoyer la demande.       |
|200 |OK             |Le traitement de la requête a commencé.       |
|400 |BadRequest     |La demande est incorrecte et a échoué (de façon permanente).|
|401 |Non autorisé   |Le client doit d’abord s’authentifier.            |
|403 |Interdit      |La demande du client est refusée.                      |
|404 |NotFound       |La requête fait référence à une entité non existante.      |
|413 |PayloadTooLarge|La charge utile de la demande a dépassé les limites.               |
|429 |TooManyRequests|La demande a été refusée en raison de la limitation. |
|504 |Délai d'expiration        |Délai d’attente de la demande dépassé.                         |
|520 |ServiceError   |Le service a rencontré une erreur lors du traitement de la demande.|

> [!NOTE]
> Le code d’état 200 indique que le traitement de la requête a démarré et qu’il a été correctement effectué.
> Les échecs rencontrés lors du traitement d’une demande après le renvoi du code d’état 200 sont appelés « échecs de requêtes partielles », et lorsqu’ils sont rencontrés, des indicateurs spéciaux sont injectés dans le flux de réponse pour alerter le client qu’ils se sont produits.

## <a name="response-headers"></a>En-têtes de réponse

Les en-têtes personnalisés suivants sont retournés.

|En-tête personnalisé           |Description                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-client-request-id`|Identificateur unique de la demande envoyé dans l’en-tête de la demande portant le même nom, ou un identificateur unique.     |
|`x-ms-activity-id`      |Identificateur de corrélation global unique pour la demande. Elle est créée par le service.                    |

## <a name="response-body"></a>Corps de réponse

Si le code d’État est 200, le corps de la réponse est un document JSON qui encode les résultats de la commande de requête ou de contrôle sous la forme d’une séquence de tables rectangulaires.
Voir les détails ci-dessous.

> [!NOTE]
> La séquence de tables est reflétée par le kit de développement logiciel (SDK). Par exemple, lors de l’utilisation de la bibliothèque .NET Framework Kusto. Data, la séquence de tables devient alors le `System.Data.IDataReader` résultat de l’objet retourné par le kit de développement logiciel (SDK).

Si le code d’État indique une erreur 4xx ou 5xx, autre que 401, le corps de la réponse est un document JSON qui encode les détails de l’échec.
Pour plus d’informations, consultez [les instructions de l’API REST de Microsoft](https://github.com/microsoft/api-guidelines).

> [!NOTE]
> Si l' `Accept` en-tête n’est pas inclus dans la demande, le corps de la réponse d’un échec n’est pas nécessairement un document JSON.

## <a name="json-encoding-of-a-sequence-of-tables"></a>Encodage JSON d’une séquence de tables

L’encodage JSON d’une séquence de tables est un conteneur de propriétés JSON unique avec les paires nom/valeur suivantes.

|Nom  |Valeur                              |
|------|-----------------------------------|
|Tables|Tableau du conteneur des propriétés de table.|

Le conteneur de propriétés de table contient les paires nom/valeur suivantes.

|Nom     |Valeur                               |
|---------|------------------------------------|
|TableName|Chaîne qui identifie la table. |
|Colonnes  |Tableau du conteneur des propriétés de colonne.|
|Lignes     |Tableau du tableau de lignes.          |

Le jeu de propriétés de colonne contient les paires nom/valeur suivantes.

|Nom      |Valeur                                                          |
|----------|---------------------------------------------------------------|
|ColumnName|Chaîne qui identifie la colonne.                           |
|DataType  |Chaîne qui fournit le type .NET approximatif de la colonne.|
|ColumnType|Chaîne qui fournit le [type de données scalaire](../../query/scalar-data-types/index.md) de la colonne.|

Le tableau de lignes a le même ordre que le tableau de colonnes respectif.
Le tableau de lignes possède également un élément qui coïncide avec la valeur de la ligne pour la colonne appropriée.
Les types de données scalaires qui ne peuvent pas être représentés `datetime` dans `timespan`JSON, tels que et, sont représentés en tant que chaînes JSON.

L’exemple suivant illustre un objet de ce type, lorsqu’il contient une seule table `Table_0` nommée qui a une seule `Text` colonne de `string`type et une seule ligne.

```json
{
    "Tables": [{
        "TableName": "Table_0",
        "Columns": [{
            "ColumnName": "Text",
            "DataType": "String",
            "ColumnType": "string"
        }],
        "Rows": [["Hello, World!"]]
}
```

Autre exemple : 

:::image type="content" source="../images/rest-json-representation.png" alt-text="Rest-représentation JSON":::

## <a name="the-meaning-of-tables-in-the-response"></a>Signification des tables dans la réponse

Dans la plupart des cas, les commandes de contrôle retournent un résultat avec une seule table, contenant les informations générées par la commande de contrôle. Par exemple, la `.show databases` commande retourne une table unique avec les détails de toutes les bases de données accessibles dans le cluster.

Les requêtes retournent généralement plusieurs tables.
Pour chaque [instruction d’expression tabulaire](../../query/tabularexpressionstatements.md), une ou plusieurs tables sont générées dans l’ordre, représentant les résultats produits par l’instruction.

> [!NOTE]
> Il peut y avoir plusieurs tables de ce type en raison des [lots](../../query/batches.md) et des [opérateurs de fourche](../../query/forkoperator.md)).

Trois tables sont souvent produites :

* @ExtendedProperties Table qui fournit des valeurs supplémentaires, telles que des instructions de visualisation du client. Ces valeurs sont générées, par exemple, pour refléter les informations dans l' [opérateur Render](../../query/renderoperator.md)) et le [curseur Database](../../management/databasecursor.md).
* Table QueryStatus qui fournit des informations supplémentaires sur l’exécution de la requête elle-même, comme, si elle s’est terminée avec succès ou non, et les ressources consommées par la requête.
* Une table TableOfContents, qui est créée en dernier et répertorie les autres tables dans les résultats.
