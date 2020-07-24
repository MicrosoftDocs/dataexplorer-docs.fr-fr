---
title: Sécurité au niveau des lignes (version préliminaire)-Azure Explorateur de données
description: Cet article décrit Sécurité au niveau des lignes (version préliminaire) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: 9952a7a7d95f03ee431b699a1833aa23b21d341b
ms.sourcegitcommit: 4507466bdcc7dd07e6e2a68c0707b6226adc25af
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87106358"
---
# <a name="row-level-security-preview"></a>Sécurité au niveau des lignes (version préliminaire)

Utilisez l’appartenance à un groupe ou un contexte d’exécution pour contrôler l’accès aux lignes d’une table de base de données.

Sécurité au niveau des lignes (RLS) simplifie la conception et le codage de la sécurité. Elle vous permet d’appliquer des restrictions sur l’accès aux lignes de données dans votre application. Par exemple, limitez l’accès des utilisateurs aux lignes pertinentes pour leur service ou restreignez l’accès des clients uniquement aux données pertinentes pour leur entreprise.

La logique de restriction d’accès se trouve au niveau de la base de données, plutôt que de s’éloigner des données d’une autre couche application. Le système de base de données applique les restrictions d’accès chaque fois que l’accès aux données est tenté à partir de n’importe quel niveau. Cette logique rend votre système de sécurité plus fiable et plus robuste en réduisant la surface d’exposition de votre système de sécurité.

La sécurité au niveau des lignes vous permet de fournir un accès à d’autres applications et utilisateurs, uniquement à une certaine partie d’une table. Vous pouvez, par exemple, souhaiter effectuer les opérations suivantes :

* Accorder l’accès uniquement aux lignes qui répondent à certains critères
* Anonymiser les données dans certaines colonnes
* Toutes les options ci-dessus

Pour plus d’informations, consultez [commandes de contrôle pour la gestion de la stratégie de sécurité au niveau des lignes](../management/row-level-security-policy.md).

> [!NOTE]
> La stratégie RLS que vous configurez sur la base de données de production prendra également effet dans les bases de données suivantes. Vous ne pouvez pas configurer différentes stratégies RLS sur les bases de données de production et de suivi.

> [!TIP]
> Ces fonctions sont souvent utiles pour les requêtes de row_level_security :
> * [current_principal()](../query/current-principalfunction.md)
> * [current_principal_details()](../query/current-principal-detailsfunction.md)
> * [current_principal_is_member_of()](../query/current-principal-ismemberoffunction.md)

## <a name="limitations"></a>Limites

Il n’existe aucune limite quant au nombre de tables sur lesquelles Sécurité au niveau des lignes stratégie peut être configurée.

La stratégie RLS ne peut pas être activée sur une table :
* pour lequel l' [exportation de données continue](../management/data-export/continuous-data-export.md) est configurée.
* référencé par une requête d’une [stratégie de mise à jour](./updatepolicy.md).
* sur laquelle la stratégie d’accès à la [vue restreinte](./restrictedviewaccesspolicy.md) est configurée.

## <a name="examples"></a>Exemples

### <a name="limit-access-to-sales-table"></a>Limiter l’accès à la table Sales

Dans une table nommée `Sales` , chaque ligne contient des détails sur une vente. L’une des colonnes contient le nom du vendeur. Au lieu de donner à vos vendeurs un accès à tous les enregistrements dans `Sales` , activez une stratégie de sécurité au niveau des lignes sur cette table pour renvoyer uniquement les enregistrements dont le vendeur est l’utilisateur actuel :

```kusto
Sales | where SalesPersonAadUser == current_principal()
```

Vous pouvez également masquer le numéro de carte de crédit :

```kusto
Sales | where SalesPersonAadUser == current_principal() | extend CreditCardNumber = "****"
```

Si vous souhaitez que chaque commercial affiche toutes les ventes d’un pays donné, vous pouvez définir une requête semblable à la suivante :

```kusto
let UserToCountryMapping = datatable(User:string, Country:string)
[
  "john@domain.com", "USA",
  "anna@domain.com", "France"
];
Sales
| where Country in (UserToCountryMapping | where User == current_principal_details()["UserPrincipalName"] | project Country)
```

Si vous avez un groupe qui contient les gestionnaires, vous souhaiterez peut-être leur attribuer l’accès à toutes les lignes. Interrogez la stratégie de Sécurité au niveau des lignes.

```kusto
let IsManager = current_principal_is_member_of('aadgroup=sales_managers@domain.com');
let AllData = Sales | where IsManager;
let PartialData = Sales | where not(IsManager) and (SalesPersonAadUser == current_principal());
union AllData, PartialData
| extend CreditCardNumber = "****"
```

### <a name="expose-different-data-to-members-of-different-azure-ad-groups"></a>Exposer des données différentes à des membres de différents groupes de Azure AD

Si vous avez plusieurs groupes de Azure AD et que vous souhaitez que les membres de chaque groupe affichent un sous-ensemble de données différent, utilisez cette structure pour une requête RLS. Supposons qu’un utilisateur ne peut appartenir qu’à un seul groupe de Azure AD.

```kusto
let IsInGroup1 = current_principal_is_member_of('aadgroup=group1@domain.com');
let IsInGroup2 = current_principal_is_member_of('aadgroup=group2@domain.com');
let IsInGroup3 = current_principal_is_member_of('aadgroup=group3@domain.com');
let DataForGroup1 = Customers | where IsInGroup1 and <filtering specific for group1>;
let DataForGroup2 = Customers | where IsInGroup2 and <filtering specific for group2>;
let DataForGroup3 = Customers | where IsInGroup3 and <filtering specific for group3>;
union DataForGroup1, DataForGroup2, DataForGroup3
```

### <a name="apply-the-same-rls-function-on-multiple-tables"></a>Appliquer la même fonction RLS sur plusieurs tables

Tout d’abord, définissez une fonction qui reçoit le nom de la table en tant que paramètre de chaîne et fait référence à la table à l’aide de l' `table()` opérateur. 

Par exemple :

```
.create-or-alter function RLSForCustomersTables(TableName: string) {
    table(TableName)
    | ...
}
```

Configurez ensuite la sécurité au niveau des lignes sur plusieurs tables de cette manière :


```
.alter table Customers1 policy row_level_security enable "RLSForCustomersTables('Customers1')"
.alter table Customers2 policy row_level_security enable "RLSForCustomersTables('Customers2')"
.alter table Customers3 policy row_level_security enable "RLSForCustomersTables('Customers3')"
```

### <a name="produce-an-error-upon-unauthorized-access"></a>Générer une erreur en cas d’accès non autorisé

Si vous souhaitez que les utilisateurs d’une table non autorisée reçoivent une erreur au lieu de retourner une table vide, utilisez la `[assert()](../query/assert-function.md)` fonction. L’exemple suivant montre comment générer cette erreur dans une fonction RLS :

```
.create-or-alter function RLSForCustomersTables() {
    MyTable
    | where assert(current_principal_is_member_of('aadgroup=mygroup@mycompany.com') == true, "You don't have access")
}
```

Vous pouvez combiner cette approche avec d’autres exemples. Par exemple, vous pouvez afficher des résultats différents pour les utilisateurs dans différents groupes AAD et générer une erreur pour tous les autres utilisateurs.

## <a name="more-use-cases"></a>Autres cas d’utilisation

* Une personne du support technique du centre d’appels peut identifier les appelants par plusieurs chiffres de leur numéro de sécurité sociale ou de carte de crédit. Ces numéros ne doivent pas être entièrement exposés à la personne chargée du support technique. Une stratégie RLS peut être appliquée à la table pour masquer uniquement les quatre derniers chiffres d’un numéro de sécurité sociale ou de carte de crédit dans le jeu de résultats d’une requête.
* Définissez une stratégie RLS qui masque les informations d’identification personnelle (PII) et permet aux développeurs d’interroger les environnements de production à des fins de dépannage sans violer les réglementations de conformité.
* Un hôpital peut définir une stratégie RLS qui autorise les infirmières à afficher les lignes de données pour leurs patients uniquement.
* Une banque peut définir une stratégie RLS pour limiter l’accès aux lignes de données financières en fonction du rôle ou de la Division d’un employé.
* Une application mutualisée peut stocker des données de nombreux locataires dans un seul tableset (ce qui est efficace). Ils utilisent une stratégie RLS pour appliquer une séparation logique des lignes de données de chaque locataire à partir des lignes de chaque autre client. ainsi, chaque locataire ne peut voir que ses lignes de données.

## <a name="performance-impact-on-queries"></a>Impact sur les performances des requêtes

Lorsqu’une stratégie RLS est activée sur une table, il y aura un impact sur les performances des requêtes qui accèdent à cette table. L’accès à la table sera en fait remplacé par la requête RLS qui est définie sur cette table. L’impact sur les performances d’une requête RLS se compose normalement de deux parties :

* Vérifications d’appartenance dans Azure Active Directory
* Filtres appliqués aux données

Par exemple :

```kusto
let IsRestrictedUser = current_principal_is_member_of('aadgroup=some_group@domain.com');
let AllData = MyTable | where not(IsRestrictedUser);
let PartialData = MyTable | where IsRestrictedUser and (...);
union AllData, PartialData
```

Si l’utilisateur ne fait pas partie de *some_group@domain.com* , `IsRestrictedUser` est évalué à `false` . La requête qui sera évaluée est semblable à celle-ci :

```kusto
let AllData = MyTable;           // the condition evaluates to `true`, so the filter is dropped
let PartialData = <empty table>; // the condition evaluates to `false`, so the whole expression is replaced with an empty table
union AllData, PartialData       // this will just return AllData, as PartialData is empty
```

De même, si `IsRestrictedUser` prend la valeur `true` , seule la requête pour `PartialData` sera évaluée.

### <a name="improve-query-performance-when-rls-is-used"></a>Améliorer les performances des requêtes lorsque la sécurité au niveau des lignes est utilisée

* Si un filtre est appliqué à une colonne de cardinalité élevée, par exemple DeviceID, envisagez d’utiliser une stratégie de [partitionnement](./partitioningpolicy.md) ou une [stratégie d’ordre des lignes](./roworderpolicy.md)
* Si un filtre est appliqué à une colonne de faible moyenne de cardinalité, envisagez d’utiliser la [stratégie d’ordre des lignes](./roworderpolicy.md)

## <a name="performance-impact-on-ingestion"></a>Impact sur les performances de l’ingestion

Il n’y a aucun impact sur les performances de l’ingestion.
