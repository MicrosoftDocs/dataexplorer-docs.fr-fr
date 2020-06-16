---
title: Stratégie RowLevelSecurity (version préliminaire)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la stratégie RowLevelSecurity (préversion) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: 3f36fa4c35ceb88c82b4dfcb7557e4839fed4aa2
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780539"
---
# <a name="rowlevelsecurity-policy-preview"></a>Stratégie RowLevelSecurity (préversion)

Cet article décrit les commandes utilisées pour configurer une [stratégie de row_level_security](rowlevelsecuritypolicy.md) pour les tables de base de données.

## <a name="displaying-the-policy"></a>Affichage de la stratégie

Pour afficher la stratégie, utilisez la commande suivante :

```kusto
.show table <table_name> policy row_level_security
```

## <a name="configuring-the-policy"></a>Configuration de la stratégie

Activer la stratégie de row_level_security sur une table :

```kusto
.alter table <table_name> policy row_level_security enable "<query>"
```

Désactiver la stratégie de row_level_security sur une table :

```kusto
.alter table <table_name> policy row_level_security disable "<query>"
```

Même lorsque la stratégie est désactivée, vous pouvez la forcer à s’appliquer à une seule requête. Entrez la ligne suivante avant la requête :

`set query_force_row_level_security;`

Cela est utile si vous souhaitez essayer différentes requêtes pour row_level_security, mais que vous ne voulez pas qu’elle prenne effectivement effet sur les utilisateurs. Une fois que vous êtes certain de la requête, activez la stratégie.

> [!NOTE]
> Les restrictions suivantes s’appliquent à `query` :
>
> * La requête doit produire exactement le même schéma que la table sur laquelle la stratégie est définie. Autrement dit, le résultat de la requête doit retourner exactement les mêmes colonnes que la table d’origine, dans le même ordre, avec les mêmes noms et types.
> * La requête ne peut utiliser que les opérateurs suivants : `extend` , `where` , `project` ,,, `project-away` `project-rename` `project-reorder` `join` et `union` .
> * La requête ne peut pas faire référence à d’autres tables sur lesquelles la sécurité au niveau des lignes est activée.
> * La requête peut être l’une des suivantes, ou une combinaison des deux :
>    * Requête (par exemple, `<table_name> | extend CreditCardNumber = "****"` )
>    * Fonction (par exemple, `AnonymizeSensitiveData` )
>    * DataTable (par exemple, `datatable(Col1:datetime, Col2:string) [...]` )

> [!TIP]
> Ces fonctions sont souvent utiles pour les requêtes de row_level_security :
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

**Note de performance**: `UserCanSeeFullNumbers` sera évalué en premier, puis `AllData` ou `PartialData` sera évalué, mais pas les deux, ce qui correspond au résultat attendu.
Vous pouvez en savoir plus sur l’impact sur les performances de la sécurité au niveau des lignes [ici](rowlevelsecuritypolicy.md#performance-impact-on-queries).

## <a name="deleting-the-policy"></a>Suppression de la stratégie

```kusto
.delete table <table_name> policy row_level_security
```
