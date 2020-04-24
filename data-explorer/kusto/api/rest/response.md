---
title: Réponse HTTP de requête/gestion - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la réponse de requête/gestion HTTP dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/05/2018
ms.openlocfilehash: 13937bbc9d17ed570c40926497ccf9b5c942fe72
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503075"
---
# <a name="querymanagement-http-response"></a>Réponse HTTP d’interrogation ou de gestion

## <a name="response-status"></a>État de réponse

La ligne d’état de réponse HTTP suit les codes de réponse standard HTTP (p. ex., 200 indique le succès). Les codes d’état suivants sont actuellement utilisés (mais notez que tout code HTTP valide peut être retourné) :

|Code|Sous-code       |Description                                    |
|----|---------------|-----------------------------------------------|
|100 |Continue       |Le client peut continuer à envoyer la demande.       |
|200 |OK             |La demande a commencé à traiter avec succès.       |
|400 |BadRequest     |La demande est mal formée et a échoué (de façon permanente).|
|401 |Non autorisé   |Le client doit d’abord s’authentifier.            |
|403 |Interdit      |La demande du client est refusée.                      |
|404 |NotFound       |Demande fait référence à une entité non existante.      |
|413 |Charge utileTooLarge|La charge utile de demande a dépassé les limites.               |
|429 |TooManyRequests|La demande a été rejetée en raison de la limitation.     |
|504 |Délai d'expiration        |Demande a chronométré.                         |
|520 |ServiceError   |Le service a rencontré une erreur traitant la demande.|

> [!NOTE]
> Il est important de se rendre compte que le code d’état 200 représente que le traitement des demandes a commencé avec succès, et non pas qu’il a terminé avec succès. Les défaillances rencontrées lors du traitement des demandes, mais après 200 ont été retournées sont appelées « défaillances partielles de requête », et lorsqu’elles sont rencontrées, des indicateurs spéciaux sont injectés dans le flux de réponse pour alerter le client qu’ils se sont produits.

## <a name="response-headers"></a>En-têtes de réponse

Les en-têtes personnalisés suivants seront retournés.

|En-tête personnalisé           |Description                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-client-request-id`|L’identifiant de demande unique envoyé dans l’en-tête de demande du même nom, ou un identifiant unique.     |
|`x-ms-activity-id`      |Un identifiant de corrélation unique à l’échelle mondiale pour la demande (créé par le service).                        |

## <a name="response-body"></a>Response body

Si le code d’état est de 200, le corps de réponse est un document JSON qui code les résultats de la requête ou de contrôle de la commande comme un séquecne de tables rectangulaires.
Voir les détails ci-dessous.

> [!NOTE]
> Cette séquence de tableaux est reflétée par le SDK. Par exemple, lors de l’utilisation de la bibliothèque .NET Framework Kusto.Data, la séquence des tableaux devient alors les résultats de l’objet `System.Data.IDataReader` retourné par le SDK.

Si le code d’état indique une erreur 4xx ou une erreur 5xx (autre que 401), le corps de réponse est un document JSON qui code les détails de l’échec, conformément aux [lignes directrices microsoft REST API](https://github.com/microsoft/api-guidelines).

> [!NOTE]
> Si `Accept` l’en-tête n’est pas inclus dans la demande, l’organe de réponse d’une défaillance n’est pas nécessairement un document JSON.

## <a name="json-encoding-of-a-sequence-of-tables"></a>JSON codage d’une séquence de tables

L’encodage JSON d’une séquence de tables est un seul sac de propriété JSON avec les paires de nom/valeur suivantes :

|Nom  |Valeur                              |
|------|-----------------------------------|
|Tables|Un tableau du sac de la table.|

Le sac de propriété Table a les paires de nom/valeur suivantes :

|Nom     |Valeur                               |
|---------|------------------------------------|
|TableName|Une chaîne qui identifie la table. |
|Colonnes  |Un tableau du sac de propriété De la Colonne.|
|Lignes     |Un tableau du tableau Row.          |

Le sac de propriété Colonne a les paires de nom/valeur suivantes :

|Nom      |Valeur                                                          |
|----------|---------------------------------------------------------------|
|ColumnName|Chaîne qui identifie la colonne.                           |
|DataType  |Une chaîne qui fournit le type approximatif .NET de la colonne.|
|ColumnType|Une chaîne qui fournit le [type de données scalaire](../../query/scalar-data-types/index.md) de la colonne.|

Le tableau Row a le même ordre que le tableau de colonnes respective, et a un élément avec la valeur de la ligne pour la colonne pertinente.
Les types de données scalaires qui ne `datetime
and `peuvent pas être représentés dans JSON (comme le timespan') sont représentés comme des chaînes JSON.

L’exemple suivant montre un tel objet possible, `Table_0` lorsqu’il `Text` contient `string` une seule table appelée qui contient une seule colonne de type et une seule ligne.

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

Un autre exmaple:

![Représentation de réponse JSON](../images/rest-json-representation.png "rest-json-représentation")

## <a name="the-meaning-of-tables-in-the-response"></a>Le sens des tableaux dans la réponse

Dans la plupart des cas, les commandes de contrôle renvoient un résultat avec une seule table, tenant les informations générées par la commande de contrôle. Par exemple, `.show databases` la commande renvoie une seule table avec les détails de toutes les bases de données accessibles dans le cluster.

Les requêtes, d’autre part, retournent généralement plusieurs tables. Pour chaque [énoncé d’expression tabulaire,](../../query/tabularexpressionstatements.md)un ou plusieurs tableaux sont émis en ordre, représentant les résultats produits par l’énoncé (il peut y avoir plusieurs tableaux de ce type en raison de lots et [d’opérateurs](../../query/forkoperator.md)de fourchettes). [batches](../../query/batches.md)

En outre, trois tables sont généralement produites :

* Un @ExtendedProperties tableau, fournissant des valeurs supplémentaires telles que les instructions de visualisation du client (émis, par exemple, pour refléter les informations dans [l’opérateur de rendu)](../../query/renderoperator.md)et les informations [de curseur de base de données).](../../management/databasecursor.md)
* Une table De RequêteStatus, fournissant des informations supplémentaires concernant l’exécution de la requête elle-même, par exemple si elle a terminé avec succès ou non, et quelles étaient les ressources consommées par la requête.
* Une table TableOfContents, qui est émise en dernier et répertorie les autres tableaux dans les résultats.

