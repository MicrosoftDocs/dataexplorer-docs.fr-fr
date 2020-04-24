---
title: Sécurité au niveau de la ligne (Avant-première) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la sécurité au niveau des rangées (Aperçu) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: 548b0e9e40cfd709b40e548fe7e0a8ad42aa4abc
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520279"
---
# <a name="row-level-security-preview"></a>Sécurité au niveau de la ligne (Avant-première)

Utilisez l’adhésion de groupe ou le contexte d’exécution pour contrôler l’accès aux rangées dans une table de base de données.

Row Level Security (RLS) simplifie la conception et le codage de la sécurité dans votre application en vous permettant d’appliquer des restrictions sur l’accès à la ligne de données. Par exemple, limitez l’accès des utilisateurs aux lignes pertinentes pour leur département, ou limitez l’accès des clients aux seules données pertinentes pour leur entreprise.

La logique de restriction d’accès se situe dans le niveau de base de données, plutôt que loin des données dans un autre niveau d’application. Le système de base de données applique les restrictions d’accès chaque fois que l’accès aux données est tenté à partir de n’importe quel niveau. Cela rend votre système de sécurité plus fiable et robuste en réduisant sa surface d’exposition.

RLS vous permet de fournir l’accès à d’autres applications et/ou utilisateurs uniquement à une certaine partie d’une table. Vous pouvez, par exemple, souhaiter effectuer les opérations suivantes :

* Accorder l’accès uniquement aux rangées qui répondent à certains critères
* Anonymiser les données dans certaines colonnes
* Les deux ci-dessus

> [!NOTE]
> La politique de sécurité au niveau des lignes ne peut pas être activée sur une table :
> * Pour lequel [l’exportation de données continue](../management/data-export/continuous-data-export.md) est configurée.
> * Cela est référencé par une requête de certaines politiques de [mise à jour](./updatepolicy.md).
> * Sur lequel [la politique d’accès à vue restreinte](./restrictedviewaccesspolicy.md) est configurée.

Pour en savoir plus sur les commandes de contrôle pour la gestion de la politique de sécurité au niveau de la ligne, [voir ici](../management/row-level-security-policy.md).

## <a name="examples"></a>Exemples

Dans un `Sales`tableau nommé, chaque rangée contient des détails sur une vente. L’une des colonnes contient le nom de la personne de vente.
Au lieu de donner à vos `Sales`vendeurs l’accès à tous les enregistrements , vous pouvez activer une politique de sécurité au niveau de ligne sur cette table pour ne retourner les enregistrements où le vendeur est l’utilisateur actuel:

```kusto
Sales | where SalesPersonAadUser == current_principal()
```

Vous pouvez également masquer le numéro de carte de crédit :

```kusto
Sales | where SalesPersonAadUser == current_principal() | extend CreditCardNumber = "****"
```

Si vous voulez que chaque vendeur voit toutes les ventes d’un pays spécifique, vous pouvez définir une requête similaire à ce qui suit :

```kusto
let UserToCountryMapping = datatable(User:string, Country:string)
[
  "john@domain.com", "USA",
  "anna@domain.com", "France"
];
Sales
| where Country in (UserToCountryMapping | where User == current_principal_details()["UserPrincipalName"] | project Country)
```

Si vous avez un groupe AAD qui contient les gestionnaires des vendeurs, vous voudrez peut-être qu’ils aient accès à toutes les lignes. Ceci peut être réalisé par la requête suivante dans la politique de sécurité au niveau de ligne :

```kusto
let IsManager = current_principal_is_member_of('aadgroup=sales_managers@domain.com');
let AllData = Sales | where IsManager;
let PartialData = Sales | where not(IsManager) and (SalesPersonAadUser == current_principal());
union AllData, PartialData
| extend CreditCardNumber = "****"
```

En général, si vous avez plusieurs groupes AAD, et que vous souhaitez que les membres de chaque groupe voient un sous-ensemble différent de données, vous pouvez suivre cette structure pour une requête RLS (en supposant qu’un utilisateur ne peut appartenir qu’à un seul groupe AAD):

```kusto
let IsInGroup1 = current_principal_is_member_of('aadgroup=group1@domain.com');
let IsInGroup2 = current_principal_is_member_of('aadgroup=group2@domain.com');
let IsInGroup3 = current_principal_is_member_of('aadgroup=group3@domain.com');
let DataForGroup1 = Customers | where IsInGroup1 and <filtering specific for group1>;
let DataForGroup2 = Customers | where IsInGroup2 and <filtering specific for group2>;
let DataForGroup3 = Customers | where IsInGroup3 and <filtering specific for group3>;
union DataForGroup1, DataForGroup2, DataForGroup3
```

## <a name="more-use-cases"></a>Plus de cas d’utilisation

* Une personne de support de centre d’appels peut identifier les appelants par plusieurs chiffres de leur numéro de sécurité sociale ou de leur numéro de carte de crédit. Ces chiffres ne devraient pas être entièrement exposés à la personne de soutien. Une politique RLS peut être appliquée sur la table pour masquer tous les quatre derniers chiffres, sauf les quatre derniers chiffres de tout numéro de sécurité sociale ou de carte de crédit dans l’ensemble de résultat de toute requête.
* Définissez une politique RLS qui masque des informations personnellement identifiables (IIP), permettant aux développeurs d’interroger les environnements de production à des fins de dépannage sans enfreindre les règlements de conformité.
* Un hôpital peut établir une politique RLS qui permet aux infirmières de visualiser les lignes de données pour leurs patients seulement.
* Une banque peut établir une politique RLS pour restreindre l’accès aux lignes de données financières en fonction de la division ou du rôle d’un employé.
* Une application multi-locataires peut stocker les données de nombreux locataires dans une seule table (qui est très efficace). Ils utiliseraient une politique RLS pour faire respecter une séparation logique des lignes de données de chaque locataire des rangées de tous les autres locataires, de sorte que chaque locataire ne peut voir que ses lignes de données.

## <a name="performance-impact-on-queries"></a>Impact sur la performance sur les requêtes

Lorsqu’une politique RLS est activée sur une table, il y aura un certain impact sur le rendement des requêtes qui accèdent à cette table. L’accès à la table sera en fait remplacé par la requête RLS qui est définie sur cette table. L’impact de performance d’une requête RLS se composera normalement de deux parties :

* Contrôles d’adhésion à Azure Active Directory
* Les filtres appliqués sur les données

Par exemple :

```kusto
let IsRestrictedUser = current_principal_is_member_of('aadgroup=some_group@domain.com');
let AllData = MyTable | where not(IsRestrictedUser);
let PartialData = MyTable | where IsRestrictedUser and (...);
union AllData, PartialData
```

Si l’utilisateur ne some_group@domain.comfait `IsRestrictedUser` pas partie `false`de , alors sera évalué à , de sorte que la requête qui sera évaluée sera similaire à celle-ci:

```kusto
let AllData = MyTable;           // the condition evaluates to `true`, so the filter is dropped
let PartialData = <empty table>; // the condition evaluates to `false`, so the whole expression is replaced with an empty table
union AllData, PartialData       // this will just return AllData, as PartialData is empty
```

De même, `IsRestrictedUser` si `true`l’évaluation est, `PartialData` alors seule la requête pour sera évaluée.

## <a name="performance-impact-on-ingestion"></a>Impact sur la performance sur l’ingestion

Aucun.