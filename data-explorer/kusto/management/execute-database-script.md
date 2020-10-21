---
title: . exécution du script de base de données-Azure Explorateur de données
description: Cet article décrit la fonctionnalité de script de base de données. Execute dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/14/2020
ms.openlocfilehash: c8fa3a000de67559c83745c598da40797e31f9b9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248340"
---
# <a name="execute-database-script"></a>.execute database script

Exécute un lot de commandes de contrôle dans l’étendue d’une base de données unique.

## <a name="syntax"></a>Syntaxe

`.execute` `database` `script`  
[ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ]   
`<|`  
 *Contrôle-commandes-script*

### <a name="parameters"></a>Paramètres

* *Control-Commands-script*: texte avec une ou plusieurs commandes de contrôle.
* *Étendue de base de données*: le script est appliqué à l' *étendue de base de données* spécifiée dans le cadre du contexte de la requête.

### <a name="optional-properties"></a>Propriétés facultatives

| Propriété            | Type            | Description                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `ContinueOnErrors`            | `bool`        | Si la valeur `false` est, le script s’arrête à la première erreur. Si la valeur est `true` , l’exécution du script se poursuit. Par défaut : `false`. |

## <a name="output"></a>Output

Chaque commande figurant dans le script est signalée comme un enregistrement distinct dans la table de sortie. Chaque enregistrement contient les champs suivants :

|Paramètre de sortie |Type |Description
|---|---|--- 
|OperationId  |Guid |Identificateur de la commande.
|CommandType  |String |Type de la commande.
|CommandText  |String |Texte de la commande spécifique.
|Résultat|String|Résultat de l’exécution de la commande spécifique.
|Motif|String|Informations détaillées sur le résultat de l’exécution de la commande.

>[!NOTE]
>* Le texte du script peut inclure des lignes vides et des commentaires entre les commandes.
>* Les commandes sont exécutées de manière séquentielle, dans l’ordre dans lequel elles apparaissent dans le script d’entrée.
>* L’exécution du script n’est pas transactionnelle et aucune restauration n’est effectuée en cas d’erreur. Il est recommandé d’utiliser la forme idempotent de commandes lors de l’utilisation de `.execute database script` .
>* Le comportement par défaut de la commande-échoue sur la première erreur, il peut être modifié à l’aide de l’argument de propriété.
>* Les commandes de contrôle en lecture seule (. Show Commands) ne sont pas exécutées et sont signalées par l’état `Skipped` .

## <a name="example"></a>Exemple

```kusto
.execute database script <|
//
// Create tables
.create-merge table T(a:string, b:string)
//
// Apply policies
.alter-merge table T policy retention softdelete = 10d 
//
// Create functions
.create-or-alter function
  with (skipvalidation = "true") 
  SampleT1(myLimit: long) { 
    T1 | limit myLimit
}
```

|OperationId|CommandType|CommandText|Résultat|Motif|
|---|---|---|---|---|
|1d28531b-58c8-4023-a5d3-16fa73c06cfa|TableCreate|. Create-Merge table T (a :String, b :String)|Completed||
|67d0ea69-baa4-419a-93d3-234c03834360|RetentionPolicyAlter|. Alter-Merge table T Retention de la stratégie SoftDelete = 10D|Completed||
|0b0e8769-d4e8-4ff9-adae-071e52a650c7|FunctionCreateOrAlter|.create-or-alter function<br>with (SkipValidation = "true")<br>SampleT1 (myLimit : long) {<br>T1 \| limite MyLimit<br>}|Completed||
