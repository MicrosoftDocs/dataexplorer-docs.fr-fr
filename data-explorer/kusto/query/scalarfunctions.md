---
title: Fonctions Scalar - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Scalar Functions in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: ef807c93f9ae9b125439f55f32a2bb1eb21d46b7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509552"
---
# <a name="scalar-functions"></a>Fonctions scalaires

## <a name="binary-functions"></a>Fonctions binaires

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[binary_and()](binary-andfunction.md)|Retourne le résultat du bitwise et de l’opération entre deux valeurs.|
|[binary_not()](binary-notfunction.md)|Retourne une négation un peu sage de la valeur d’entrée.|
|[binary_or()](binary-orfunction.md)|Retourne le résultat du bitwise ou de l’exploitation des deux valeurs.|
|[binary_shift_left()](binary-shift-leftfunction.md)|Retourne le décalage binaire à gauche sur une paire de numéros : un << n.|
|[binary_shift_right()](binary-shift-rightfunction.md)|Retourne l’opération binaire de décalage droit sur une paire de nombres : un >> n.|
|[binary_xor()](binary-xorfunction.md)|Retourne le résultat de l’opération xor bitwise des deux valeurs.|
|[bitset_count_ones()](bitset-count-onesfunction.md)|Retourne le nombre de bits de set dans la représentation binaire d’un nombre.|

## <a name="conversion-functions"></a>Fonctions de conversion

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[tobool()](toboolfunction.md)|Convertit l’entrée en représentation boolean (signé 8 bits).|
|[todatetime()](todatetimefunction.md)|Convertit l’entrée en scalar datetime.|
|[todouble()/toreal()](todoublefunction.md)|Convertit l’entrée en une valeur de type réel. (todouble() et toreal() sont synonymes.)|
|[tostring()](tostringfunction.md)|Convertit l’entrée en une représentation des cordes.|
|[totimespan()](totimespanfunction.md)|Convertit l’entrée en scalar de timespan.|


## <a name="datetimetimespan-functions"></a>Fonctions DateTime/Timespan

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[il ya()](agofunction.md)|Soustrait l’intervalle de temps donné de l’heure UTC actuelle.|
|[datetime_add()](datetime-addfunction.md)|Calcule une nouvelle date à partir d’une date spécifiée multipliée par un montant spécifié, ajouté à une date spécifiée.|
|[datetime_part()](datetime-partfunction.md)|Extrait la partie de date demandée comme valeur d’un intégreur.|
|[datetime_diff()](datetime-difffunction.md)|Rendements de la fin de l’année contenant la date, décalé par compensation, si fourni.|
|[dayofmonth ()](dayofmonthfunction.md)|Retourne le numéro d’integer représentant le numéro de jour du mois donné.|
|[dayofweek()](dayofweekfunction.md)|Retourne le nombre de jours integer depuis le dimanche précédent, comme une période.|
|[dayofyear()](dayofyearfunction.md)|Retourne le nombre d’intégraires représente le nombre de jours de l’année donnée.|
|[endofday()](endofdayfunction.md)|Retourne la fin de la journée contenant la date, décalée par un décalage, si elle est fournie.|
|[endofmonth()](endofmonthfunction.md)|Retourne la fin du mois contenant la date, décalée par un décalage, si elle est fournie.|
|[endofweek()](endofweekfunction.md)|Retourne la fin de la semaine contenant la date, décalée par un décalage, si elle est fournie.|
|[endofyear()](endofyearfunction.md)|Rendements de la fin de l’année contenant la date, décalé par compensation, si fourni.|
|[format_datetime()](format-datetimefunction.md)|Formats un paramètre d’heure de date basé sur le paramètre de modèle de format.|
|[format_timespan()](format-timespanfunction.md)|Formats un paramètre format-timespan basé sur le paramètre de modèle de format.|
|[getmonth ()](getmonthfunction.md)|Obtient le numéro du mois (1 à 12) à partir d’une valeur datetime.|
|[getyear()](getyearfunction.md)|Retourne la partie de l’année de l’argument de datetime.|
|[heure d’aujourd’hui ()](hourofdayfunction.md)|Retourne le numéro d’integer représentant le numéro d’heure de la date donnée.|
|[make_datetime()](make-datetimefunction.md)|Crée une valeur scalaire d’heure de date à partir de la date et de l’heure spécifiées.|
|[make_timespan()](make-timespanfunction.md)|Crée une valeur scalaire de timespan à partir de la période spécifiée.|
|[monthofyear()](monthofyearfunction.md)|Retourne le nombre d’intégraires représente le nombre de mois de l’année donnée.|
|[maintenant ()](nowfunction.md)|Retourne l’heure d’horloge UTC actuelle, compensée par une durée donnée.|
|[startofday()](startofdayfunction.md)|Retourne le début de la journée contenant la date, décalé par un décalage, si fourni.|
|[startofmonth()](startofmonthfunction.md)|Retourne le début du mois contenant la date, décalé par un décalage, si fourni.|
|[startofweek()](startofweekfunction.md)|Retourne le début de la semaine contenant la date, décalé par un décalage, si fourni.|
|[startofyear()](startofyearfunction.md)|Retourne le début de l’année contenant la date, décalé par un décalage, s’il est fourni.|
|[todatetime()](todatetimefunction.md)|Convertit l’entrée en scalar datetime.|
|[totimespan()](totimespanfunction.md)|Convertit l’entrée en scalar de timespan.|
|[weekofyear()](weekofyearfunction.md)|Retourne un intégrant représentant le numéro de semaine.|


## <a name="dynamicarray-functions"></a>Fonctions dynamiques/Array

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[array_concat()](arrayconcatfunction.md)|Concatenates un certain nombre de tableaux dynamiques à un seul tableau.|
|[array_iif()](arrayifffunction.md)|Applique la fonction iif élément-sage sur les tableaux.|
|[array_index_of()](arrayindexoffunction.md)|Recherche le tableau pour l’élément spécifié, et retourne sa position.|
|[array_length()](arraylengthfunction.md)|Calcule le nombre d’éléments dans un tableau dynamique.|
|[array_slice()](arrayslicefunction.md)|Extrait une tranche d’un tableau dynamique.|
|[array_split()](arraysplitfunction.md)|Construit un éventail de tableaux divisés à partir du tableau d’entrée.|
|[bag_keys()](bagkeysfunction.md)|Énumère toutes les touches de racine dans un objet dynamique de sac de propriété.|
|[pack()](packfunction.md)|Crée un objet dynamique (sac de propriété) à partir d’une liste de noms et de valeurs.|
|[pack_all()](packallfunction.md)|Crée un objet dynamique (sac de propriété) à partir de toutes les colonnes de l’expression tabulaire.|
|[pack_array()](packarrayfunction.md)|Emballe toutes les valeurs d’entrée dans un tableau dynamique.|
|[répéter()](repeatfunction.md)|Génère un tableau dynamique avec une série de valeurs égales.|
|[set_difference()](setdifferencefunction.md)|Retourne un tableau de l’ensemble de toutes les valeurs distinctes qui sont dans le premier tableau, mais ne sont pas dans d’autres tableaux.|
|[set_has_element()](sethaselementfunction.md)|Détermine si le tableau spécifié contient l’élément spécifié.|
|[set_intersect()](setintersectfunction.md)|Retourne un tableau de l’ensemble de toutes les valeurs distinctes qui sont dans tous les tableaux.|
|[set_union()](setunionfunction.md)|Retourne un éventail de l’ensemble de toutes les valeurs distinctes qui sont dans l’un des tableaux fournis.|
|[treepath()](treepathfunction.md)|Énumère toutes les expressions de chemin qui identifient les feuilles dans un objet dynamique.|
|[zip()](zipfunction.md)|La fonction zip accepte n’importe quel nombre de tableaux dynamiques, et renvoie un tableau dont les éléments sont chacun un tableau tenant les éléments des tableaux d’entrée du même index.|


## <a name="window-scalar-functions"></a>Fonctions Scalar fenêtre

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[suivant()](nextfunction.md)|Pour l’ensemble de rangées sérialisé, renvoie une valeur d’une colonne spécifiée de la rangée ultérieure selon le décalage.|
|[prev()](prevfunction.md)|Pour l’ensemble de rangées sérialisé, renvoie une valeur d’une colonne spécifiée de la rangée antérieure selon le décalage.|
|[row_cumsum()](rowcumsumfunction.md)|Calcule la somme cumulative d’une colonne.|
|[row_number()](rownumberfunction.md)|Renvoie le numéro d’une rangée dans l’ensemble de rangées sérialisées - nombres consécutifs à partir d’un index donné ou de 1 par défaut.|

## <a name="flow-control-functions"></a>Fonctions de contrôle des flux

|Nom de fonction            |Description                                             |
|-------------------------|--------------------------------------------------------|
|[toscalar()](toscalarfunction.md)|Retourne une valeur constante scalaire de l’expression évaluée.|

## <a name="mathematical-functions"></a>Fonctions mathématiques

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[abs()](abs-function.md)|Calcule la valeur absolue de l’entrée.|
|[acos ()](acosfunction.md)|Retourne l’angle dont la cosine est le nombre spécifié (l’opération inverse de la coopération)).|
|[asin ()](asinfunction.md)|Retourne l’angle dont le sinus est le nombre spécifié (l’opération inverse du péché)).|
|[atan ()](atanfunction.md)|Retourne l’angle dont la tangente est le nombre spécifié (le fonctionnement inverse du bronzage).)).|
|[atan2 ()](atan2function.md)|Calcule l’angle, en radians, entre l’axe X positif et le rayon de l’origine jusqu’au point (y, x).|
|[beta_cdf()](beta-cdffunction.md)|Retourne la fonction de distribution bêta cumulative standard.|
|[beta_inv()](beta-invfunction.md)|Retourne l’inverse de la fonction bêta de densité bêta de probabilité cumulée bêta bêta bêta bêta.|
|[beta_pdf()](beta-pdffunction.md)|Retourne la fonction bêta de densité de probabilité.|
|[cos ()](cosfunction.md)|Retourne la fonction cosine.|
|[lit bébé()](cotfunction.md)|Calcule le cotangent trigonométrique de l’angle spécifié, chez les radians.|
|[degrés()](degreesfunction.md)|Convertit la valeur d’angle en radianes en valeur en degrés, en utilisant des degrés de formule (180 / PI ) - angle-in-radians.|
|[exp()](exp-function.md)|La fonction exponentielle de base-e de x, qui est e augmenté à la puissance x: e x.|
|[exp10()](exp10-function.md)|La fonction exponentielle de base-10 de x, qui est de 10 soulevées à la puissance x: 10 x.|
|[exp2 ()](exp2-function.md)|La fonction exponentielle de base-2 de x, qui est 2 soulevée à la puissance x: 2 x.|
|[gamma()](gammafunction.md)|Calcule la fonction gamma.|
|[hachage ()](hashfunction.md)|Retourne une valeur de hachage pour la valeur d’entrée.|
|[hash_combine()](hash_combinefunction.md)|Combine deux valeurs de hachage ou plus.|
|[hash_many()](hash_manyfunction.md)|Retourne une valeur combinée de hachage de valeurs multiples.|
|[isfinite()](isfinitefunction.md)|Retourne si l’entrée est une valeur finie (n’est ni infinie ni NaN).|
|[isinf()](isinffunction.md)|Rendement si l’entrée est une valeur infinie (positive ou négative).|
|[isnan()](isnanfunction.md)|Retourne si l’entrée est not-a-Number (NaN) valeur.|
|[log()](log-function.md)|Retourne la fonction de logarithme naturel.|
|[log10()](log10-function.md)|Retuns la fonction comon (base-10) logarithm.|
|[log2()](log2-function.md)|Retourne la fonction de logarithme de base-2.|
|[loggamma()](loggammafunction.md)|Calcul de la valeur absolue de la fonction gamma.|
|[pas ()](notfunction.md)|Renverse la valeur de son argument bool.|
|[pi()](pifunction.md)|Retourne la valeur constante de Pi.|
|[pow()](powfunction.md)|Retourne le résultat de l’élévation au pouvoir.|
|[radians()](radiansfunction.md)|Convertit la valeur de l’angle en degrés en valeur chez les radians, à l’aide de radians formule (PI / 180 ) et angle-in-degrees.|
|[rand()](randfunction.md)|Retourne un nombre aléatoire.|
|[gamme ()](rangefunction.md)|Génère un tableau dynamique contenant une série de valeurs à espace également.|
|[ronde ()](roundfunction.md)|Renvoie la source arrondie à la précision spécifiée.|
|[signe()](signfunction.md)|Signe d’une expression numérique.|
|[péché()](sinfunction.md)|Retourne la fonction sinusoïne.|
|[sqrt()](sqrtfunction.md)|Retourne la fonction racine carrée.|
|[tan()](tanfunction.md)|Retourne la fonction tangente.|
|[welch_test()](welch-testfunction.md)|Calcule la valeur p de la [fonction Welch-test](https://en.wikipedia.org/wiki/Welch%27s_t-test).|


## <a name="metadata-functions"></a>Fonctions de métadonnées

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[column_ifexists()](columnifexists.md)|Prend un nom de colonne comme une chaîne et une valeur par défaut. Renvoie une référence à la colonne si elle existe, sinon - retourne la valeur par défaut.|
|[current_cluster_endpoint()](current-cluster-endpoint-function.md)|Retourne le cluster actuel en cours d’exécution de la requête.|
|[current_database()](current-database-function.md)|Retourne le nom de la base de données dans la portée.|
|[current_principal()](current-principalfunction.md)|Retourne le principal actuel en cours d’exécution de cette requête.|
|[current_principal_details()](current-principal-detailsfunction.md)|Retourne les détails du principal exécutant la requête.|
|[current_principal_is_member_of()](current-principal-ismemberoffunction.md)|Vérifie l’appartenance au groupe ou l’identité principale du principal actuel qui exécute la requête.|
|[cursor_after()](cursorafterfunction.md)|Utilisé pour accéder aux dossiers qui ont été ingérés après la valeur précédente du curseur.|
|[estimate_data_size()](estimate-data-sizefunction.md)|Renvoie une taille de données estimée des colonnes sélectionnées de l’expression tabulaire.|
|[extent_id()](extentidfunction.md)|Renvoie un identifiant unique qui identifie l’éclat de données (« étendue ») dans lequel se trouve l’enregistrement actuel.|
|[extent_tags()](extenttagsfunction.md)|Retourne un tableau dynamique avec les balises de l’éclat de données («étendue») dans lequel se trouve l’enregistrement actuel.|
|[ingestion_time()](ingestiontimefunction.md)|Récupère la colonne de date cachée de l’enregistrement $IngestionTime ou nulle.|


## <a name="rounding-functions"></a>Fonctions d’arrondissement

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[bin()](binfunction.md)|Arrondit les valeurs à l’entier inférieur multiple d’une taille bin donnée.|
|[bin_at()](binatfunction.md)|Arrondit les valeurs jusqu’à un "bin" de taille fixe, avec le contrôle sur le point de départ du bac. (Voir aussi la fonction bin.)|
|[plafond()](ceilingfunction.md)|Calcule le plus petit integer supérieur ou égal à l’expression numérique spécifiée.|
|[plancher()](floorfunction.md)|Arrondit les valeurs à l’entier inférieur multiple d’une taille bin donnée.|


## <a name="conditional-functions"></a>Fonctions conditionnelles

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[cas()](casefunction.md)|Évalue une liste de prédicats et renvoie la première expression de résultat dont le prédicat est satisfait.|
|[fusion ()](coalescefunction.md)|Évalue une liste d’expressions et renvoie la première expression non nulle (ou non vide pour la chaîne).|
|[iif()/iff()](iiffunction.md)|Évalue le premier argument (le prédicat) et renvoie la valeur du deuxième ou du troisième argument, selon que le prédicat évalué à vrai (deuxième) ou faux (troisième).|
|[max_of()](max-offunction.md)|Retourne la valeur maximale de plusieurs expressions numériques évaluées.|
|[min_of()](min-offunction.md)|Retourne la valeur minimale de plusieurs expressions numériques évaluées.|

## <a name="series-element-wise-functions"></a>Fonctions de série Élément-sage

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[series_add()](series-addfunction.md)|Calcule l’ajout élément-sage de deux entrées de série numérique.|
|[series_divide()](series-dividefunction.md)|Calcule la division élément-sage de deux entrées de série numérique.|
|[series_equals()](series-equalsfunction.md)|Calcule le fonctionnement de`==`l’élément-sage égal à la logique de deux entrées numériques.|
|[series_greater()](series-greaterfunction.md)|Calcule l’élément-sage`>`plus grand ( ) fonctionnement logique de deux entrées de série numérique.|
|[series_greater_equals()](series-greater-equalsfunction.md)|Calcule l’élément-sage plus`>=`ou égal ( ) fonctionnement logique de deux entrées de série numérique.|
|[series_less()](series-lessfunction.md)|Calcule le fonctionnement logique`<`de l’élément-sage moins () de deux entrées numériques.|
|[series_less_equals()](series-less-equalsfunction.md)|Calcule le fonctionnement logique de`<=`l’élément-sage moins ou égal () de deux entrées de série numérique.|
|[series_multiply()](series-multiplyfunction.md)|Calcule la multiplication élément-sage de deux entrées de série numérique.|
|[series_not_equals()](series-not-equalsfunction.md)|Calcule l’élément-sage pas`!=`égal ( ) fonctionnement logique de deux entrées de série numérique.|
|[series_subtract()](series-subtractfunction.md)|Calcule la soustraction élément-sage de deux entrées de série numérique.|

## <a name="series-processing-functions"></a>Fonctions de traitement de série

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[series_decompose()](series-decomposefunction.md)|Effectue une décomposition de la série en composants.|
|[series_decompose_anomalies()](series-decompose-anomaliesfunction.md)|Trouve des anomalies dans une série basée sur la décomposition de la série.|
|[series_decompose_forecast()](series-decompose-forecastfunction.md)|Prévisions basées sur la décomposition des séries.|
|[series_fill_backward()](series-fill-backwardfunction.md)|Effectue l’interpolation de remplissage vers l’arrière des valeurs manquantes dans une série.|
|[series_fill_const()](series-fill-constfunction.md)|Remplace les valeurs manquantes d’une série par une valeur constante spécifiée.|
|[series_fill_forward()](series-fill-forwardfunction.md)|Effectue l’interpolation de remplissage vers l’avant des valeurs manquantes dans une série.|
|[series_fill_linear()](series-fill-linearfunction.md)|Effectue l’interpolation linéaire des valeurs manquantes dans une série.|
|[series_fir()](series-firfunction.md)|Applique un filtre De réponse Finie Impulse sur une série.|
|[series_fit_2lines()](series-fit-2linesfunction.md)|Applique deux segments de régression linéaire sur une série, retournant plusieurs colonnes.|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|Applique deux segments de régression linéaire sur une série, retour objet dynamique.|
|[series_fit_line()](series-fit-linefunction.md)|Applique la régression linéaire sur une série, retournant plusieurs colonnes.|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|Applique la régression linéaire sur une série, retour d’objet dynamique.|
|[series_iir()](series-iirfunction.md)|Applique un filtre De réponse Infinite Impulse sur une série.|
|[series_outliers()](series-outliersfunction.md)|Marque des points d’anomalie dans une série.|
|[series_pearson_correlation()](series-pearson-correlationfunction.md)|Calcule le coefficient de corrélation Pearson de deux séries.|
|[series_periods_detect()](series-periods-detectfunction.md)|Trouve les périodes les plus importantes qui existent dans une série de temps.|
|[series_periods_validate()](series-periods-validatefunction.md)|Vérifie si une série de temps contient des modèles périodiques de longueurs données.|
|[series_seasonal()](series-seasonalfunction.md)|Trouve la composante saisonnière de la série.|
|[series_stats()](series-statsfunction.md)|Retourne les statistiques d’une série dans plusieurs colonnes.|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|Retourne les statistiques d’une série en objet dynamique.|

## <a name="string-functions"></a>Fonctions de chaîne

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[base64_encode_tostring()](base64_encode_tostringfunction.md)|Encode une chaîne comme chaîne base64.|
|[base64_decode_tostring()](base64_decode_tostringfunction.md)|Décode une chaîne de base64 sur une chaîne UTF-8.|
|[base64_decode_toarray()](base64_decode_toarrayfunction.md)|Décode une chaîne base64 à un éventail de valeurs longues.|
|[countof()](cotfunction.md)|Compte les occurrences d’une sous-chaîne dans une chaîne. Les correspondances de chaînes simples peuvent se chevaucher, mais pas les correspondances d’expression régulière.|
|[extrait()](extractfunction.md)|Obtient une correspondance pour une expression régulière à partir d’une chaîne de texte.|
|[extract_all()](extractallfunction.md)|Obtenez tous les matchs pour une expression régulière à partir d’une chaîne de texte.|
|[extractjson()](extractjsonfunction.md)|Obtient un élément spécifié à partir d’un texte JSON à l’aide d’une expression de chemin.|
|[indexof()](indexoffunction.md)|Fonction indique l’index zéro de la première occurrence d’une chaîne spécifiée dans la chaîne d’entrée.|
|[isempty ()](isemptyfunction.md)|Retourne vrai si l’argument est une chaîne vide ou est nul.|
|[isnotempty()](isnotemptyfunction.md)|Retourne vrai si l’argument n’est pas une chaîne vide ni il est nul.|
|[isnotnull ()](isnotnullfunction.md)|Revient vrai si l’argument n’est pas nul.|
|[isnull ()](isnullfunction.md)|Évalue son seul argument et renvoie une valeur bool indiquant si l’argument évalue à une valeur nulle.|
|[parse_csv()](parsecsvfunction.md)|Divise une chaîne donnée représentant les valeurs séparées de virgule et renvoie un tableau de cordes avec ces valeurs.|
|[parse_ipv4()](parse-ipv4function.md)|Convertit l’entrée en représentation longue (signée 64 bits).|
|[parse_json()](parsejsonfunction.md)|Interprète une chaîne comme une valeur JSON) et renvoie la valeur comme dynamique.|
|[parse_url()](parseurlfunction.md)|Parse une chaîne URL absolue et renvoie un objet dynamique contient toutes les parties de l’URL.|
|[parse_urlquery()](parseurlqueryfunction.md)|Parse une chaîne de requête url et renvoie un objet dynamique contient les paramètres De requête.|
|[parse_version()](parse-versionfunction.md)|Convertit la représentation de la version par chaîne d’entrée en un nombre décimal comparable.|
|[remplacer()](replacefunction.md)|Remplace toutes les correspondances d’expression régulière par une autre chaîne.|
|[inverse()](reversefunction.md)|Fonction rend l’inverse de la chaîne d’entrée.|
|[split()](splitfunction.md)|Divise une chaîne donnée selon un délimitant donné et renvoie un tableau de cordes avec les sous-cordes contenues.|
|[strcat()](strcatfunction.md)|Concatenates entre 1 et 64 arguments.|
|[strcat_delim()](strcat-delimfunction.md)|Concatenates entre 2 et 64 arguments, avec délimitation, fourni comme premier argument.|
|[strcmp()](strcmpfunction.md)|Compare deux chaînes.|
|[strlen ()](strlenfunction.md)|Retourne la longueur, en caractères, de la chaîne d’entrée.|
|[strrep()](strrepfunction.md)|Répétitions données chaîne fournie quantité de fois (par défaut - 1).|
|[sous-cordes()](substringfunction.md)|Extrait un sous-cordon d’une chaîne source à partir d’un indice à la fin de la chaîne.|
|[toupper ()](toupperfunction.md)|Convertit une chaîne en majuscules.|
|[traduire()](translatefunction.md)|Remplace un ensemble de caractères ('searchList') par un autre ensemble de caractères ('replacementList') dans une chaîne donnée.|
|[garniture()](trimfunction.md)|Supprime tous les matchs de tête et de fuite de l’expression régulière spécifiée.|
|[trim_end()](trimendfunction.md)|Supprime la correspondance de fuite de l’expression régulière spécifiée.|
|[trim_start()](trimstartfunction.md)|Supprime le match de tête de l’expression régulière spécifiée.|
|[url_decode()](urldecodefunction.md)|La fonction convertit l’URL codée en une représentation URL régulière.|
|[url_encode()](urlencodefunction.md)|La fonction convertit les caractères de l’URL d’entrée en un format qui peut être transmis sur Internet.|

## <a name="ip-v4-functions"></a>Fonctions IP v4

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[ipv4_compare()](ipv4-comparefunction.md)|Compare deux chaînes IPv4.|
|[ipv4_is_match()](ipv4-is-matchfunction.md)|Correspond à deux cordes IPv4.|
|[parse_ipv4()](parse-ipv4function.md)|Convertit la chaîne d’entrée en représentation longue (signée 64 bits).|
|[parse_ipv4_mask()](parse-ipv4-maskfunction.md)|Convertit la chaîne d’entrée et le masque IP-préfixe en représentation longue (signée 64 bits).|

## <a name="type-functions"></a>Fonctions de type

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[gettype()](gettypefunction.md)|Retourne le type de temps d’exécution de son seul argument.|


## <a name="scalar-aggregation-functions"></a>Fonctions d’agrégation Scalar

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[dcount_hll()](dcount-hllfunction.md)|Calcule le dcount à partir des résultats de hll (qui a été généré par le hll ou la fusion de hll).|
|[hll_merge()](hllmergefunction.md)|Fusionne les résultats de hll (version scalaire de la version globale hll-merge()).|
|[percentile_tdigest()](percentile-tdigestfunction.md)|Calcule le résultat percentile des résultats tdigest (qui a été généré par tdigest ou merge-tdigests).|
|[percentrank_tdigest()](percentrank-tdigestfunction.md)|Calcule le pourcentage de classement d’une valeur dans un jeu de données.|
|[rank_tdigest()](rank-tdigest.md)|Calcule le rang relatif d’une valeur dans un ensemble.|
|[tdigest_merge()](tdigest-mergefunction.md)|Fusionne les résultats les plus tdigestes (version scalaire de la version globale tdigest-merge()).|

## <a name="geo-spatial-functions"></a>Fonctions géo-spatiales

|Nom de fonction|Description|
|--------------------------------------------------------------------------|--------------------------------------------------------|
|[geo_distance_2points()](geo-distance-2points-function.md)|Calcule la distance la plus courte entre deux coordonnées géospatiales sur Terre.|
|[geo_geohash_to_central_point()](geo-geohash-to-central-point-function.md)|Calcule les coordonnées géospatiales qui représentent le centre d’une zone rectangulaire Geohash.|
|[geo_point_in_circle()](geo-point-in-circle-function.md)|Calcule si les coordonnées géospatiales sont à l’intérieur d’un cercle sur Terre.|
|[geo_point_to_geohash()](geo-point-to-geohash-function.md)|Calcule la valeur de la chaîne Geohash pour une emplacement géographique.|