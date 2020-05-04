---
title: Instruction de déclaration des paramètres de requête-Azure Explorateur de données | Microsoft Docs
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
ms.openlocfilehash: a76755d04179b3d311e79798162c61db764455d7
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737808"
---
# <a name="query-parameters-declaration-statement"></a>Instruction de déclaration des paramètres de requête

::: zone pivot="azuredataexplorer"

Les requêtes envoyées à Kusto peuvent inclure, en plus du texte de la requête lui-même, un ensemble de paires nom/valeur appelées **paramètres de requête**. Le texte de la requête peut ensuite référencer une ou plusieurs valeurs de paramètres de requête en spécifiant leurs noms et le type à l’aide d’une **instruction de déclaration de paramètres de requête**.

Les paramètres de requête ont deux utilisations principales :

1. Comme mécanisme de protection contre **les attaques par injection**.
2. Comme un moyen de paramétrer des requêtes.

En particulier, les applications clientes qui combinent les entrées fournies par l’utilisateur dans les requêtes qu’elles envoient ensuite à Kusto doivent utiliser ce mécanisme pour se protéger contre l’équivalent Kusto des attaques par [injection SQL](https://en.wikipedia.org/wiki/SQL_injection) .

## <a name="declaring-query-parameters"></a>Déclaration des paramètres de requête

Pour pouvoir référencer des paramètres de requête, le texte de la requête (ou les fonctions qu’il utilise) doit d’abord déclarer le paramètre de requête qu’il utilise. Pour chaque paramètre, la déclaration fournit le nom et le type scalaire. Éventuellement, le paramètre peut également avoir une valeur par défaut à utiliser si la requête ne fournit pas de valeur concrète pour le paramètre. Kusto analyse ensuite la valeur du paramètre de requête en fonction de ses règles d’analyse normales pour ce type.

**Syntaxe**

`declare``query_parameters` `:` *Type1* `,` *Name1* `=` Nom1 type1 [DefaultValue1] [...] *DefaultValue1* `(``);`

* *Nom1*: nom d’un paramètre de requête utilisé dans la requête.
* *Type1*: le type correspondant (par exemple `string`, `datetime`, etc.) Les valeurs fournies par l’utilisateur sont encodées sous forme de chaînes, pour que Kusto applique la méthode d’analyse appropriée au paramètre de requête pour obtenir une valeur fortement typée.
* *DefaultValue1*: valeur par défaut facultative pour le paramètre. Il doit s’agir d’un littéral du type scalaire approprié.

> [!NOTE]
> À l’instar des [fonctions définies](functions/user-defined-functions.md)par l' `dynamic` utilisateur, les paramètres de requête de type ne peuvent pas avoir de valeurs par défaut.

**Exemples**

```kusto
declare query_parameters(UserName:string, Password:string);
print n=UserName, p=hash(Password)
```

```kusto
declare query_parameters(percentage:long = 90);
T | where Likelihood > percentage
```

## <a name="specifying-query-parameters-in-a-client-application"></a>Spécification des paramètres de requête dans une application cliente

Les noms et les valeurs des paramètres de requête sont `string` fournis en tant que valeurs par l’application qui effectue la requête. Aucun nom ne peut être répété.

L’interprétation des valeurs est effectuée en fonction de l’instruction de déclaration des paramètres de la requête. Chaque valeur est analysée comme s’il s’agissait d’un littéral dans le corps d’une requête en fonction du type spécifié par l’instruction de déclaration des paramètres de la requête.

### <a name="rest-api"></a>API REST

Les paramètres de requête sont fournis par les applications `properties` clientes par le biais de l’emplacement de l’objet JSON du corps de la `Parameters`demande, dans un conteneur de propriétés imbriquées appelé. Par exemple, voici le corps d’un appel d’API REST à Kusto qui calcule l’âge d’un utilisateur (vraisemblablement, en faisant en sorte que l’application demande à l’utilisateur son anniversaire) :

``` json
{
    "ns": null,
    "db": "myDB",
    "csl": "declare query_parameters(birthday:datetime); print strcat(\"Your age is: \", tostring(now() - birthday))",
    "properties": "{\"Options\":{},\"Parameters\":{\"birthday\":\"datetime(1970-05-11)\",\"courses\":\"dynamic(['Java', 'C++'])\"}}"
}
```

### <a name="kusto-net-sdk"></a>Kit de développement logiciel (SDK) .NET Kusto

Pour fournir les noms et les valeurs des paramètres de requête lors de l’utilisation de la bibliothèque cliente .net Kusto, l' `ClientRequestProperties` un crée une nouvelle instance `HasParameter`de `SetParameter`l’objet `ClearParameter` , puis utilise les méthodes, et pour manipuler les paramètres de requête. Notez que cette classe fournit un certain nombre de surcharges fortement typées `SetParameter`pour ; en interne, ils génèrent le littéral approprié du langage de requête et l’envoient sous forme de `string` par le biais de l’API REST, comme décrit ci-dessus. Le texte de la requête doit toujours [déclarer les requêtes](#declaring-query-parameters).

### <a name="kustoexplorer"></a>Kusto.Explorer

Pour définir les paramètres de requête envoyés lors d’une demande au service, utilisez l’icône « clé » des **paramètres** de`ALT` + `P`requête ().

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
