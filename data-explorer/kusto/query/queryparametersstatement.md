---
title: Instruction de déclaration des paramètres de requête-Azure Explorateur de données
description: Cet article décrit l’instruction de déclaration des paramètres de requête dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 54c09908096f9df4ac8b568cd5e897c6e4ecc8c2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345961"
---
# <a name="query-parameters-declaration-statement"></a>Instruction de déclaration des paramètres de requête

::: zone pivot="azuredataexplorer"

Les requêtes envoyées à Kusto peuvent inclure un jeu de paires nom/valeur. Les paires sont appelées *paramètres de requête*, ainsi que le texte de requête lui-même. La requête peut référencer une ou plusieurs valeurs, en spécifiant des noms et un type, dans une *instruction de déclaration de paramètres de requête*.

Les paramètres de requête ont deux utilisations principales :

* Comme mécanisme de protection contre les attaques par injection.
* Comme un moyen de paramétrer des requêtes.

En particulier, les applications clientes qui combinent les entrées fournies par l’utilisateur dans les requêtes qu’elles envoient ensuite à Kusto doivent utiliser le mécanisme pour se protéger contre l’équivalent Kusto des attaques par [injection SQL](https://en.wikipedia.org/wiki/SQL_injection) .

## <a name="declaring-query-parameters"></a>Déclaration des paramètres de requête

Pour référencer des paramètres de requête, le texte de requête ou les fonctions qu’il utilise, vous devez d’abord déclarer le paramètre de requête qu’il utilise. Pour chaque paramètre, la déclaration fournit le nom et le type scalaire. Éventuellement, le paramètre peut également avoir une valeur par défaut. La valeur par défaut est utilisée si la requête ne fournit pas de valeur concrète pour le paramètre. Kusto analyse ensuite la valeur du paramètre de requête, en fonction de ses règles d’analyse normales pour ce type.

## <a name="syntax"></a>Syntaxe

`declare``query_parameters` `(` *Nom1* `:` *type1* [ `=` *DefaultValue1*] [ `,` ...]`);`

* *Nom1*: nom d’un paramètre de requête utilisé dans la requête.
* *Type1*: type correspondant, tel que `string` ou `datetime` .
  Les valeurs fournies par l’utilisateur sont encodées sous forme de chaînes, pour que Kusto applique la méthode d’analyse appropriée au paramètre de requête pour obtenir une valeur fortement typée.
* *DefaultValue1*: valeur par défaut facultative pour le paramètre. Cette valeur doit être un littéral du type scalaire approprié.

> [!NOTE]
> À l’instar des [fonctions définies](functions/user-defined-functions.md)par l’utilisateur, les paramètres de requête de type `dynamic` ne peuvent pas avoir de valeurs par défaut.

## <a name="examples"></a>Exemples

```kusto
declare query_parameters(UserName:string, Password:string);
print n=UserName, p=hash(Password)
```

```kusto
declare query_parameters(percentage:long = 90);
T | where Likelihood > percentage
```

## <a name="specifying-query-parameters-in-a-client-application"></a>Spécification des paramètres de requête dans une application cliente

Les noms et les valeurs des paramètres de requête sont fournis en tant que `string` valeurs par l’application qui effectue la requête. Aucun nom ne peut être répété.

L’interprétation des valeurs est effectuée en fonction de l’instruction de déclaration des paramètres de la requête. Chaque valeur est analysée comme s’il s’agissait d’un littéral dans le corps d’une requête. L’analyse est effectuée en fonction du type spécifié par l’instruction de déclaration des paramètres de la requête.

### <a name="rest-api"></a>API REST

Les paramètres de requête sont fournis par les applications clientes par le biais `properties` de l’emplacement de l’objet JSON du corps de la demande, dans un conteneur de propriétés imbriquées appelé `Parameters` . Par exemple, voici le corps d’un appel d’API REST à Kusto qui calcule l’âge d’un utilisateur, vraisemblablement en demandant à l’application de demander l’anniversaire de l’utilisateur.

``` json
{
    "ns": null,
    "db": "myDB",
    "csl": "declare query_parameters(birthday:datetime); print strcat(\"Your age is: \", tostring(now() - birthday))",
    "properties": "{\"Options\":{},\"Parameters\":{\"birthday\":\"datetime(1970-05-11)\",\"courses\":\"dynamic(['Java', 'C++'])\"}}"
}
```

### <a name="kusto-net-sdk"></a>Kit de développement logiciel (SDK) .NET Kusto

Pour fournir les noms et les valeurs des paramètres de requête lors de l’utilisation de la bibliothèque cliente .NET Kusto, l’un crée une nouvelle instance de l’objet, puis `ClientRequestProperties` utilise les `HasParameter` `SetParameter` méthodes, et `ClearParameter` pour manipuler les paramètres de requête. Cette classe fournit un certain nombre de surcharges fortement typées pour `SetParameter` ; en interne, elle génère le littéral approprié du langage de requête et l’envoie en tant que `string` par le biais de l’API REST, comme décrit ci-dessus. Le texte de la requête doit toujours [déclarer les paramètres de requête](#declaring-query-parameters).

### <a name="kustoexplorer"></a>Kusto.Explorer

Pour définir les paramètres de requête envoyés lors d’une demande au service, utilisez l’icône « clé » des **paramètres de requête** ( `ALT`  +  `P` ).

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
