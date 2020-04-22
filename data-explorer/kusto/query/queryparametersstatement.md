---
title: Déclaration de déclaration de paramètres de requête - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la déclaration de déclaration des paramètres de requête dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b7193ada6967882306d9a977b6c90af8b247036d
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765500"
---
# <a name="query-parameters-declaration-statement"></a>Énoncé de déclaration sur les paramètres de requête

::: zone pivot="azuredataexplorer"

Les requêtes envoyées à Kusto peuvent inclure, en plus du texte de requête lui-même, un ensemble de paires de noms/valeurs **appelées paramètres de requête.** Le texte de requête peut alors faire référence à une ou plusieurs valeurs de paramètres de requête en spécifiant leurs noms et en tapant à travers un énoncé de **déclaration de paramètres de requête**.

Les paramètres de requête ont deux utilisations principales :

1. Comme mécanisme de protection contre **les attaques par injection**.
2. Comme un moyen de paramétriser les requêtes.

En particulier, les applications client qui combinent l’entrée fournie par l’utilisateur dans les requêtes qu’ils envoient ensuite à Kusto devraient utiliser ce mécanisme pour se protéger contre l’équivalent Kusto des attaques [d’injection SQL.](https://en.wikipedia.org/wiki/SQL_injection)

## <a name="declaring-query-parameters"></a>Déclarer les paramètres de requête

Pour pouvoir référencer les paramètres de requête, le texte de requête (ou fonctions qu’il utilise) doit d’abord déclarer quel paramètre de requête il utilise. Pour chaque paramètre, la déclaration fournit le nom et le type scalar. En option, le paramètre peut également avoir une valeur par défaut à utiliser si la demande ne fournit pas une valeur concrète pour le paramètre. Kusto analyse ensuite la valeur du paramètre de requête en fonction de ses règles normales d’analyse pour ce type.

**Syntaxe**

`declare``query_parameters` `:` `=` `,` *Type1* *Name1* *DefaultValue1*Nom1 Type1 [ DefaultValue1 ] [ ...] `(``);`

* *Nom1*: Le nom d’un paramètre de requête utilisé dans la requête.
* *Type1*: Le type correspondant (p. ex. `string`, `datetime`etc.) Les valeurs fournies par l’utilisateur sont codées comme des chaînes, à Kusto appliquera la méthode d’analyse appropriée au paramètre de requête pour obtenir une valeur fortement typée.
* *DefaultValue1*: Une valeur par défaut facultative pour le paramètre. Cela doit être un littéral du type scalaire approprié.

> [!NOTE]
> Comme [les fonctions définies par l’utilisateur,](functions/user-defined-functions.md)les paramètres de requête du type `dynamic` ne peuvent pas avoir de valeurs par défaut.

**Exemples**

```kusto
declare query_parameters(UserName:string, Password:string);
print n=UserName, p=hash(Password)
```

```kusto
declare query_parameters(percentage:long = 90);
T | where Likelihood > percentage
```

## <a name="specifying-query-parameters-in-a-client-application"></a>Spécifier les paramètres de requête dans une application client

Les noms et les valeurs des `string` paramètres de requête sont fournis comme valeurs par l’application faisant la requête. Aucun nom ne peut se répéter.

L’interprétation des valeurs se fait selon l’énoncé de déclaration des paramètres de requête. Chaque valeur est analysée comme si elle était littérale dans le corps d’une requête selon le type spécifié par l’énoncé de déclaration des paramètres de requête.

### <a name="rest-api"></a>API REST

Les paramètres de requête sont fournis `properties` par les applications client par la fente de l’objet JSON de l’organisme de demande, dans un sac de propriété imbriqué appelé `Parameters`. Par exemple, voici le corps d’un appel REST API à Kusto qui calcule l’âge d’un utilisateur (probablement en ayant l’application demander à l’utilisateur pour son anniversaire):

``` json
{
    "ns": null,
    "db": "myDB",
    "csl": "declare query_parameters(birthday:datetime); print strcat(\"Your age is: \", tostring(now() - birthday))",
    "properties": "{\"Options\":{},\"Parameters\":{\"birthday\":\"datetime(1970-05-11)\",\"courses\":\"dynamic(['Java', 'C++'])\"}}"
}
```

### <a name="kusto-net-sdk"></a>Kusto .NET SDK

Pour fournir les noms et les valeurs des paramètres de requête lors de l’utilisation `ClientRequestProperties` de la `HasParameter`bibliothèque `SetParameter`client `ClearParameter` Kusto .NET, on crée une nouvelle instance de l’objet, puis utilise le , et les méthodes pour manipuler les paramètres de requête. Notez que cette classe fournit un certain nombre `SetParameter`de surcharges fortement typées pour ; à l’interne, ils génèrent le littéral approprié `string` du langage de requête et l’envoient comme a par l’intermédiaire de l’API REST, tel que décrit ci-dessus. Le texte de requête lui-même doit encore [déclarer les paramters de requête](#declaring-query-parameters).

### <a name="kustoexplorer"></a>Kusto.Explorer

Pour définir les paramètres de requête envoyés lors de la demande au service, utilisez **l’icône "clé" des paramètres De requête** ().`ALT` + `P`

::: zone-end

::: zone pivot="azuremonitor"

Ce n’est pas pris en charge dans Azure Monitor

::: zone-end
