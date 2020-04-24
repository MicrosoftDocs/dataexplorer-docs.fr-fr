---
title: Politique RowLevelSecurity (Avant-première) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit rowLevelSecurity policy (Preview) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: bf8ee8bc4c43c4ed3bcd320ce5b4778f33c3cc64
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520296"
---
# <a name="rowlevelsecurity-policy-preview"></a>Politique RowLevelSecurity (Avant-première)

Cet article décrit les commandes utilisées pour configurer une [row_level_security stratégie](rowlevelsecuritypolicy.md) pour les tables de base de données.

## <a name="displaying-the-policy"></a>Affichage de la politique

Pour afficher la stratégie, utilisez la commande suivante :

```kusto
.show table <table_name> policy row_level_security
```

## <a name="configuring-the-policy"></a>Configurer la politique

Activez row_level_security politique sur une table :

```kusto
.alter table <table_name> policy row_level_security enable "<query>"
```

Désactiver la politique de row_level_security sur une table :

```kusto
.alter table <table_name> policy row_level_security disable "<query>"
```

Même lorsque la police est désactivée, vous pouvez la forcer à s’appliquer à une seule requête. Entrez la ligne suivante avant la requête :

`set query_force_row_level_security;`

Ceci est utile si vous voulez essayer diverses requêtes pour row_level_security, mais ne veulent pas qu’il prenne effet réellement sur les utilisateurs. Une fois que vous avez confiance en la requête, activez la politique.

> [!NOTE]
> Les restrictions suivantes `query`s’appliquent au :
>
> * La requête devrait produire exactement le même schéma que la table sur laquelle la politique est définie. Autrement dit, le résultat de la requête devrait retourner exactement les mêmes colonnes que le tableau d’origine, dans le même ordre, avec les mêmes noms et types.
> * La requête ne peut utiliser `extend`que `where` `project`les `project-away` `project-rename`opérateurs `project-reorder` `union`suivants: , , , , et .
> * La requête ne peut pas faire référence à d’autres que celle sur laquelle la politique est définie.
> * La requête peut être l’une des suivantes, ou une combinaison d’entre eux:
>    * Requête (par exemple, `<table_name> | extend CreditCardNumber = "****"`)
>    * Fonction (par `AnonymizeSensitiveData`exemple, )
>    * Datatable (par `datatable(Col1:datetime, Col2:string) [...]`exemple, )

> [!TIP]
> Ces fonctions sont souvent utiles pour les requêtes row_level_security :
> * [current_principal()](../query/current-principalfunction.md)
> * [current_principal_details()](../query/current-principal-detailsfunction.md)
> * [current_principal_is_member_of()](../query/current-principal-ismemberoffunction.md)

**Exemple**

```kusto
.create-or-alter function with () TrimCreditCardNumbers() {
    let UserCanSeeFullNumbers = current_principal_is_member_of('aadgroup=super_group@domain.com');
    let AllData = Customers | where UserCanSeeFullNumbers;
    let PartialData = Customers | where not(UserCanSeeFullNumbers) | extend CreditCardNumber = "****";
    union AllData, PartialData
}

.alter table Customers policy row_level_security enable "TrimCreditCardNumbers"
```
**Note**de `UserCanSeeFullNumbers` performance : sera évaluée `AllData` d’abord, puis soit ou `PartialData` sera évaluée, mais pas les deux, ce qui est le résultat attendu.
Vous pouvez en savoir plus sur l’impact des performances de RLS [ici](rowlevelsecuritypolicy.md#performance-impact-on-queries).

## <a name="deleting-the-policy"></a>Suppression de la politique

```kusto
.delete table <table_name> policy row_level_security
```