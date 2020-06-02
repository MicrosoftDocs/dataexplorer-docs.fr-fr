---
title: Fonctions scalaires-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les fonctions scalaires dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: ccbd01faae3e71941c1bf4542f473410753155cf
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294574"
---
# <a name="scalar-functions"></a>Fonctions scalaires

## <a name="binary-functions"></a>Fonctions binaires

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[binary_and()](binary-andfunction.md)|Retourne un résultat de l’opération and au niveau du bit entre deux valeurs.|
|[binary_not()](binary-notfunction.md)|Retourne une négation au niveau du bit de la valeur d’entrée.|
|[binary_or()](binary-orfunction.md)|Retourne un résultat de l’opération or au niveau du bit des deux valeurs.|
|[binary_shift_left()](binary-shift-leftfunction.md)|Retourne l’opération de décalage binaire vers la gauche sur une paire de nombres : un << n.|
|[binary_shift_right()](binary-shift-rightfunction.md)|Retourne l’opération de décalage binaire vers la droite sur une paire de nombres : un >> n.|
|[binary_xor()](binary-xorfunction.md)|Retourne un résultat de l’opération de bits XOR des deux valeurs.|
|[bitset_count_ones()](bitset-count-onesfunction.md)|Retourne le nombre de bits définis dans la représentation binaire d’un nombre.|

## <a name="conversion-functions"></a>Fonctions de conversion

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[tobool()](toboolfunction.md)|Convertit l’entrée en représentation booléenne (8 bits signée).|
|[todatetime()](todatetimefunction.md)|Convertit l’entrée en valeur scalaire DateTime.|
|[ToDouble ()/TOREAL ()](todoublefunction.md)|Convertit l’entrée en une valeur de type Real. (ToDouble () et TOREAL () sont des synonymes.)|
|[ToString ()](tostringfunction.md)|Convertit l’entrée en une représentation sous forme de chaîne.|
|[totimespan()](totimespanfunction.md)|Convertit l’entrée en scalaire TimeSpan.|

## <a name="datetimetimespan-functions"></a>Fonctions DateTime/TimeSpan

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[ago()](agofunction.md)|Soustrait l’intervalle de temps donné de l’heure UTC actuelle.|
|[datetime_add()](datetime-addfunction.md)|Calcule une nouvelle valeur DateTime à partir d’une partie de date spécifiée, multipliée par une quantité spécifiée, ajoutée à une valeur DateTime spécifiée.|
|[datetime_part()](datetime-partfunction.md)|Extrait la partie de date demandée sous la forme d’une valeur entière.|
|[datetime_diff()](datetime-difffunction.md)|Retourne la fin de l’année contenant la date, décalée d’un décalage, si elle est fournie.|
|[dayofmonth()](dayofmonthfunction.md)|Retourne le nombre entier représentant le nombre de jours du mois donné.|
|[dayofweek()](dayofweekfunction.md)|Retourne le nombre entier de jours depuis le dimanche précédent, sous la forme d’un intervalle de temps.|
|[dayofyear()](dayofyearfunction.md)|Retourne le nombre entier représente le nombre de jours de l’année donnée.|
|[endofday()](endofdayfunction.md)|Retourne la fin de la journée contenant la date, décalée d’un décalage, si elle est fournie.|
|[endofmonth()](endofmonthfunction.md)|Retourne la fin du mois contenant la date, décalée d’un décalage, si elle est fournie.|
|[endofweek()](endofweekfunction.md)|Retourne la fin de la semaine contenant la date, décalée d’un décalage, si elle est fournie.|
|[endofyear()](endofyearfunction.md)|Retourne la fin de l’année contenant la date, décalée d’un décalage, si elle est fournie.|
|[format_datetime()](format-datetimefunction.md)|Met en forme un paramètre DateTime basé sur le paramètre de modèle de format.|
|[format_timespan()](format-timespanfunction.md)|Met en forme un paramètre format-TimeSpan basé sur le paramètre de modèle de format.|
|[getmonth()](getmonthfunction.md)|Obtient le numéro du mois (1 à 12) à partir d’une valeur datetime.|
|[getyear()](getyearfunction.md)|Retourne la partie année de l’argument DateTime.|
|[hourofday()](hourofdayfunction.md)|Retourne le nombre entier qui représente le nombre d’heures de la date donnée.|
|[make_datetime()](make-datetimefunction.md)|Crée une valeur scalaire DateTime à partir de la date et de l’heure spécifiées.|
|[make_timespan()](make-timespanfunction.md)|Crée une valeur scalaire TimeSpan à partir de la période spécifiée.|
|[monthofyear()](monthofyearfunction.md)|Retourne le nombre entier représentant le mois de l’année donnée.|
|[now()](nowfunction.md)|Retourne l’heure UTC actuelle, décalée éventuellement en fonction d’un intervalle de temps donné.|
|[startofday()](startofdayfunction.md)|Retourne le début de la journée contenant la date, décalée d’un décalage, si elle est fournie.|
|[startofmonth()](startofmonthfunction.md)|Retourne le début du mois contenant la date, décalée par un décalage, s’il est fourni.|
|[startofweek()](startofweekfunction.md)|Retourne le début de la semaine contenant la date, décalée d’un décalage, si elle est fournie.|
|[startofyear()](startofyearfunction.md)|Retourne le début de l’année contenant la date, décalée d’un décalage, si elle est fournie.|
|[todatetime()](todatetimefunction.md)|Convertit l’entrée en valeur scalaire DateTime.|
|[totimespan()](totimespanfunction.md)|Convertit l’entrée en scalaire TimeSpan.|
|[weekofyear()](weekofyearfunction.md)|Retourne un entier représentant le numéro de la semaine.|


## <a name="dynamicarray-functions"></a>Fonctions dynamiques/de tableau

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[array_concat()](arrayconcatfunction.md)|Concatène un certain nombre de tableaux dynamiques en un seul tableau.|
|[array_iif()](arrayifffunction.md)|Applique la fonction IIF au niveau des éléments sur les tableaux.|
|[array_index_of()](arrayindexoffunction.md)|Recherche l’élément spécifié dans le tableau et retourne sa position.|
|[array_length()](arraylengthfunction.md)|Calcule le nombre d’éléments dans un tableau dynamique.|
|[array_slice()](arrayslicefunction.md)|Extrait un segment d’un tableau dynamique.|
|[array_split()](arraysplitfunction.md)|Génère un tableau de tableaux fractionnés à partir du tableau d’entrée.|
|[bag_keys()](bagkeysfunction.md)|Énumère toutes les clés racines dans un objet de jeu de propriétés dynamique.|
|[pack()](packfunction.md)|Crée un objet dynamique (jeu de propriétés) à partir d’une liste de noms et de valeurs.|
|[pack_all()](packallfunction.md)|Crée un objet dynamique (jeu de propriétés) à partir de toutes les colonnes de l’expression tabulaire.|
|[pack_array()](packarrayfunction.md)|Compresse toutes les valeurs d’entrée dans un tableau dynamique.|
|[repeat()](repeatfunction.md)|Génère un tableau dynamique contenant une série de valeurs égales.|
|[set_difference()](setdifferencefunction.md)|Retourne un tableau de l’ensemble de toutes les valeurs distinctes qui se trouvent dans le premier tableau, mais qui ne sont pas dans d’autres tableaux.|
|[set_has_element()](sethaselementfunction.md)|Détermine si le tableau spécifié contient l’élément spécifié.|
|[set_intersect()](setintersectfunction.md)|Retourne un tableau de l’ensemble de toutes les valeurs distinctes qui se trouvent dans tous les tableaux.|
|[set_union()](setunionfunction.md)|Retourne un tableau de l’ensemble de toutes les valeurs distinctes qui se trouvent dans l’un des tableaux fournis.|
|[treepath()](treepathfunction.md)|Énumère toutes les expressions de chemin qui identifient les feuilles dans un objet dynamique.|
|[zip()](zipfunction.md)|La fonction zip accepte un nombre quelconque de tableaux dynamiques. Retourne un tableau dont les éléments sont chacun un tableau avec les éléments des tableaux d’entrée du même index.|


## <a name="window-scalar-functions"></a>Fonctions scalaires de fenêtre

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[next()](nextfunction.md)|Pour l’ensemble de lignes sérialisé, retourne une valeur d’une colonne spécifiée à partir de la ligne ultérieure en fonction de l’offset.|
|[prev()](prevfunction.md)|Pour l’ensemble de lignes sérialisé, retourne une valeur d’une colonne spécifiée à partir de la ligne précédente en fonction de l’offset.|
|[row_cumsum()](rowcumsumfunction.md)|Calcule la somme cumulée d’une colonne.|
|[row_number()](rownumberfunction.md)|Retourne le nombre d’une ligne dans le jeu de lignes sérialisées, nombres consécutifs à partir d’un index donné ou à partir de 1 par défaut.|

## <a name="flow-control-functions"></a>Fonctions de contrôle de Flow

|Nom de fonction            |Description                                             |
|-------------------------|--------------------------------------------------------|
|[toscalar()](toscalarfunction.md)|Retourne une valeur de constante scalaire de l’expression évaluée.|

## <a name="mathematical-functions"></a>Fonctions mathématiques

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[abs()](abs-function.md)|Calcule la valeur absolue de l’entrée.|
|[acos()](acosfunction.md)|Retourne l’angle dont le cosinus est le nombre spécifié (l’opération inverse de COS ()).|
|[asin()](asinfunction.md)|Retourne l’angle dont le sinus est le nombre spécifié (l’opération inverse de Sin ()).|
|[atan()](atanfunction.md)|Retourne l’angle dont la tangente est le nombre spécifié (l’opération inverse de Tan ()).|
|[atan2()](atan2function.md)|Calcule l’angle, en radians, entre l’axe des abscisses positif et le rayon depuis l’origine jusqu’au point (y, x).|
|[beta_cdf()](beta-cdffunction.md)|Retourne la fonction de distribution bêta cumulative standard.|
|[beta_inv()](beta-invfunction.md)|Retourne l’inverse de la fonction de densité bêta cumulative de probabilité bêta.|
|[beta_pdf()](beta-pdffunction.md)|Retourne la fonction de densité de probabilité bêta.|
|[cos()](cosfunction.md)|Retourne la fonction cosinus.|
|[cot()](cotfunction.md)|Calcule la cotangente trigonométrique de l’angle spécifié, en radians.|
|[degrees()](degreesfunction.md)|Convertit la valeur d’angle en radians en valeur en degrés, à l’aide de la formule degrés = (180/PI) * angle-in-radians.|
|[exp()](exp-function.md)|Fonction exponentielle base-e de x, qui est e élevée à la puissance x : e ^ x.|
|[exp10()](exp10-function.md)|Fonction exponentielle en base 10 de x, qui est 10 élevée à la puissance x : 10 ^ x.|
|[exp2()](exp2-function.md)|Fonction exponentielle en base 2 de x, qui est 2 élevée à la puissance x : 2 ^ x.|
|[gamma()](gammafunction.md)|Calcule la fonction gamma.|
|[hash()](hashfunction.md)|Retourne une valeur de hachage pour la valeur d’entrée.|
|[hash_combine()](hash_combinefunction.md)|Combine deux valeurs de hachage ou plus.|
|[hash_many()](hash_manyfunction.md)|Retourne une valeur de hachage combinée de plusieurs valeurs.|
|[isfinite()](isfinitefunction.md)|Retourne une valeur indiquant si l’entrée est une valeur finie (n’est pas infinie ou NaN).|
|[isinf()](isinffunction.md)|Retourne une valeur indiquant si l’entrée est une valeur infinie (positive ou négative).|
|[isnan()](isnanfunction.md)|Retourne une valeur indiquant si l’entrée n’est pas un nombre (NaN).|
|[log()](log-function.md)|Retourne la fonction de logarithme népérien.|
|[log10()](log10-function.md)|Retourne la fonction de logarithme (base 10) courant.|
|[log2()](log2-function.md)|Retourne la fonction de logarithme en base 2.|
|[loggamma()](loggammafunction.md)|Calcule le journal de la valeur absolue de la fonction gamma.|
|[Not ()](notfunction.md)|Inverse la valeur de son argument bool.|
|[pi()](pifunction.md)|Retourne la valeur constante de pi (π).|
|[pow()](powfunction.md)|Retourne un résultat de l’élévation à Power.|
|[radians()](radiansfunction.md)|Convertit la valeur d’angle en degrés en radians, à l’aide de la formule radians = (PI/180) * angle-in-degrees.|
|[Rand ()](randfunction.md)|Retourne un nombre aléatoire.|
|[Plage ()](rangefunction.md)|Génère un tableau dynamique contenant une série de valeurs uniformément espacées.|
|[round()](roundfunction.md)|Retourne la source arrondie à la précision spécifiée.|
|[sign()](signfunction.md)|Signe d’une expression numérique.|
|[sin()](sinfunction.md)|Retourne la fonction sinus.|
|[sqrt()](sqrtfunction.md)|Retourne la fonction racine carrée.|
|[tan()](tanfunction.md)|Retourne la fonction tangente.|
|[welch_test()](welch-testfunction.md)|Calcule la valeur p de la [fonction Welch-test](https://en.wikipedia.org/wiki/Welch%27s_t-test).|


## <a name="metadata-functions"></a>Fonctions de métadonnées

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[column_ifexists()](columnifexists.md)|Prend un nom de colonne en tant que chaîne et une valeur par défaut. Retourne une référence à la colonne, le cas échéant ; sinon, retourne la valeur par défaut.|
|[current_cluster_endpoint()](current-cluster-endpoint-function.md)|Retourne le cluster en cours qui exécute la requête.|
|[current_database()](current-database-function.md)|Retourne le nom de la base de données dans l’étendue.|
|[current_principal()](current-principalfunction.md)|Retourne le principal actuel qui exécute cette requête.|
|[current_principal_details()](current-principal-detailsfunction.md)|Retourne les détails du principal qui exécute la requête.|
|[current_principal_is_member_of()](current-principal-ismemberoffunction.md)|Vérifie l’appartenance au groupe ou l’identité principale du principal actuel exécutant la requête.|
|[cursor_after()](cursorafterfunction.md)|Utilisé pour accéder aux enregistrements qui ont été ingérés après la valeur précédente du curseur.|
|[estimate_data_size()](estimate-data-sizefunction.md)|Retourne une estimation de la taille des données des colonnes sélectionnées de l’expression tabulaire.|
|[extent_id()](extentidfunction.md)|Retourne un identificateur unique qui identifie le partition de données (« Extent ») dans lequel réside l’enregistrement en cours.|
|[extent_tags()](extenttagsfunction.md)|Retourne un tableau dynamique avec les balises du partition de données (« Extent ») dans lequel réside l’enregistrement en cours.|
|[ingestion_time()](ingestiontimefunction.md)|Récupère les $IngestionTime colonne DateTime masquée de l’enregistrement, ou null.|


## <a name="rounding-functions"></a>Arrondir les fonctions

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[bin()](binfunction.md)|Arrondit les valeurs à l’entier inférieur multiple d’une taille bin donnée.|
|[bin_at()](binatfunction.md)|Arrondit les valeurs à un « chutier » de taille fixe, avec un contrôle sur le point de départ de l’emplacement. (Voir aussi fonction bin.)|
|[ceiling()](ceilingfunction.md)|Calcule le plus petit entier supérieur ou égal à l’expression numérique spécifiée.|
|[Floor ()](floorfunction.md)|Arrondit les valeurs à l’entier inférieur multiple d’une taille bin donnée.|


## <a name="conditional-functions"></a>Fonctions conditionnelles

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[case()](casefunction.md)|Évalue une liste de prédicats et retourne la première expression de résultat dont le prédicat est respecté.|
|[COALESCE ()](coalescefunction.md)|Évalue une liste d’expressions et retourne la première expression non null (ou non vide pour String).|
|[IIF ()/IFF ()](iiffunction.md)|Évalue le premier argument (le prédicat) et retourne la valeur du deuxième ou du troisième argument, selon que le prédicat est évalué à true (deuxième) ou false (troisième).|
|[max_of()](max-offunction.md)|Retourne la valeur maximale de plusieurs expressions numériques évaluées.|
|[min_of()](min-offunction.md)|Retourne la valeur minimale de plusieurs expressions numériques évaluées.|

## <a name="series-element-wise-functions"></a>Fonctions d’élément de série

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[series_add()](series-addfunction.md)|Calcule l’ajout au niveau des éléments de deux entrées de série numérique.|
|[series_divide()](series-dividefunction.md)|Calcule la division au niveau des éléments de deux entrées de série numérique.|
|[series_equals()](series-equalsfunction.md)|Calcule l’opération logique d’égalité au niveau `==` des éléments () de deux entrées de série numérique.|
|[series_greater()](series-greaterfunction.md)|Calcule l’opération logique supérieure () au niveau `>` des éléments de deux entrées de série numérique.|
|[series_greater_equals()](series-greater-equalsfunction.md)|Calcule l’opération logique supérieure ou égale à l’élément ( `>=` ) de deux entrées de série numérique.|
|[series_less()](series-lessfunction.md)|Calcule l’opération logique au niveau de l’élément ( `<` ) de deux entrées de série numérique.|
|[series_less_equals()](series-less-equalsfunction.md)|Calcule l' `<=` opération logique d’infériorité ou d’égalité des éléments de deux entrées de série numérique.|
|[series_multiply()](series-multiplyfunction.md)|Calcule la multiplication par élément de deux entrées de série numérique.|
|[series_not_equals()](series-not-equalsfunction.md)|Calcule l’opération logique au niveau de l’élément ( `!=` ) de deux entrées de série numérique.|
|[series_subtract()](series-subtractfunction.md)|Calcule la soustraction au niveau des éléments de deux entrées de série numérique.|

## <a name="series-processing-functions"></a>Fonctions de traitement des séries

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[series_decompose()](series-decomposefunction.md)|Effectue une décomposition de la série en composants.|
|[series_decompose_anomalies()](series-decompose-anomaliesfunction.md)|Recherche les anomalies dans une série en fonction de la décomposition de série.|
|[series_decompose_forecast()](series-decompose-forecastfunction.md)|Prévisions basées sur la décomposition des séries.|
|[series_fill_backward()](series-fill-backwardfunction.md)|Effectue l’interpolation de remplissage arrière des valeurs manquantes dans une série.|
|[series_fill_const()](series-fill-constfunction.md)|Remplace les valeurs manquantes dans une série par une valeur de constante spécifiée.|
|[series_fill_forward()](series-fill-forwardfunction.md)|Effectue l’interpolation de remplissage par progression des valeurs manquantes dans une série.|
|[series_fill_linear()](series-fill-linearfunction.md)|Effectue une interpolation linéaire des valeurs manquantes dans une série.|
|[series_fir()](series-firfunction.md)|Applique un filtre à réponse impulsionnelle finie sur une série.|
|[series_fit_2lines()](series-fit-2linesfunction.md)|Applique deux segments de régression linéaire sur une série, en retournant plusieurs colonnes.|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|Applique deux segments de régression linéaire sur une série, en retournant un objet dynamique.|
|[series_fit_line()](series-fit-linefunction.md)|Applique la régression linéaire sur une série, en retournant plusieurs colonnes.|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|Applique la régression linéaire sur une série, en retournant un objet dynamique.|
|[series_iir()](series-iirfunction.md)|Applique un filtre à réponse impulsionnelle infinie sur une série.|
|[series_outliers()](series-outliersfunction.md)|Notation des points d’anomalies dans une série.|
|[series_pearson_correlation()](series-pearson-correlationfunction.md)|Calcule le coefficient de corrélation de Pearson de deux séries.|
|[series_periods_detect()](series-periods-detectfunction.md)|Recherche les périodes les plus significatives qui existent dans une série chronologique.|
|[series_periods_validate()](series-periods-validatefunction.md)|Vérifie si une série chronologique contient des modèles périodiques de longueurs données.|
|[series_seasonal()](series-seasonalfunction.md)|Recherche le composant saisonnier de la série.|
|[series_stats()](series-statsfunction.md)|Retourne des statistiques pour une série dans plusieurs colonnes.|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|Retourne des statistiques pour une série dans un objet dynamique.|

## <a name="string-functions"></a>Fonctions de chaîne

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[base64_encode_tostring()](base64_encode_tostringfunction.md)|Encode une chaîne au format de chaîne base64.|
|[base64_decode_tostring()](base64_decode_tostringfunction.md)|Décode une chaîne base64 en une chaîne UTF-8.|
|[base64_decode_toarray()](base64_decode_toarrayfunction.md)|Décode une chaîne base64 en un tableau de valeurs longues.|
|[countof ()](cotfunction.md)|Compte les occurrences d’une sous-chaîne dans une chaîne. Les correspondances de chaînes brutes peuvent se chevaucher ; les correspondances Regex ne sont pas.|
|[extract()](extractfunction.md)|Obtient une correspondance pour une expression régulière à partir d’une chaîne de texte.|
|[extract_all()](extractallfunction.md)|Obtenir toutes les correspondances pour une expression régulière à partir d’une chaîne de texte.|
|[extractjson()](extractjsonfunction.md)|Obtient un élément spécifié à partir d’un texte JSON à l’aide d’une expression de chemin.|
|[indexof()](indexoffunction.md)|La fonction signale l’index de base zéro de la première occurrence d’une chaîne spécifiée dans la chaîne d’entrée.|
|[isempty()](isemptyfunction.md)|Retourne la valeur true si l’argument est une chaîne vide ou a la valeur null.|
|[isnotempty()](isnotemptyfunction.md)|Retourne la valeur true si l’argument n’est pas une chaîne vide ou une valeur null.|
|[IsNotNull ()](isnotnullfunction.md)|Retourne la valeur true si l’argument n’a pas la valeur null.|
|[isnull()](isnullfunction.md)|Évalue son argument unique et retourne une valeur booléenne indiquant si l’argument correspond à une valeur null.|
|[parse_csv()](parsecsvfunction.md)|Fractionne une chaîne donnée représentant des valeurs séparées par des virgules et retourne un tableau de chaînes avec ces valeurs.|
|[parse_ipv4()](parse-ipv4function.md)|Convertit l’entrée en une représentation de nombre longue (signée 64 bits).|
|[parse_ipv4_mask()](parse-ipv4-maskfunction.md)|Convertit la chaîne d’entrée et le masque de préfixe IP en une représentation de nombre longue (signée 64 bits).|
|[parse_ipv6 ()](parse-ipv6function.md)|Convertit une chaîne IPv6 ou IPv4 en une représentation sous forme de chaîne IPv6 canonique.|
|[parse_ipv6_mask ()](parse-ipv6-maskfunction.md)|Convertit la chaîne IPv6 ou IPv4 et le masque réseau en une représentation sous forme de chaîne IPv6 canonique.|
|[parse_json()](parsejsonfunction.md)|Interprète une chaîne comme une valeur JSON) et retourne la valeur comme dynamique.|
|[parse_url()](parseurlfunction.md)|Analyse une chaîne d’URL absolue et retourne un objet dynamique qui contient toutes les parties de l’URL.|
|[parse_urlquery()](parseurlqueryfunction.md)|Analyse une chaîne de requête d’URL et retourne un objet dynamique contenant les paramètres de requête.|
|[parse_version()](parse-versionfunction.md)|Convertit la représentation sous forme de chaîne d’entrée d’une version en nombre décimal comparable.|
|[remplacer ()](replacefunction.md)|Remplace toutes les correspondances d’expression régulière par une autre chaîne.|
|[reverse()](reversefunction.md)|La fonction effectue l’inversion de la chaîne d’entrée.|
|[split()](splitfunction.md)|Fractionne une chaîne donnée en fonction d’un délimiteur donné et retourne un tableau de chaînes avec les sous-chaînes contenues.|
|[strcat()](strcatfunction.md)|Concatène entre 1 et 64 arguments.|
|[strcat_delim()](strcat-delimfunction.md)|Concatène entre 2 et 64 arguments, avec le délimiteur, fourni comme premier argument.|
|[strcmp()](strcmpfunction.md)|Compare deux chaînes.|
|[strlen()](strlenfunction.md)|Retourne la longueur, en caractères, de la chaîne d’entrée.|
|[strrep()](strrepfunction.md)|Répète le nombre de fois spécifié pour la chaîne (valeur par défaut : 1).|
|[Substring ()](substringfunction.md)|Extrait une sous-chaîne d’une chaîne source à partir d’un index jusqu’à la fin de la chaîne.|
|[ToUpper ()](toupperfunction.md)|Convertit une chaîne en majuscules.|
|[translate()](translatefunction.md)|Remplace un jeu de caractères (« searchList ») par un autre jeu de caractères (« replacementList ») dans une chaîne donnée.|
|[trim()](trimfunction.md)|Supprime toutes les correspondances de début et de fin de l’expression régulière spécifiée.|
|[trim_end()](trimendfunction.md)|Supprime la correspondance de fin de l’expression régulière spécifiée.|
|[trim_start()](trimstartfunction.md)|Supprime la correspondance de début de l’expression régulière spécifiée.|
|[url_decode ()](urldecodefunction.md)|La fonction convertit l’URL encodée en une représentation d’URL régulière.|
|[url_encode ()](urlencodefunction.md)|La fonction convertit les caractères de l’URL d’entrée dans un format qui peut être transmis sur Internet.|

## <a name="ipv4ipv6-functions"></a>Fonctions IPv4/IPv6

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[ipv4_compare ()](ipv4-comparefunction.md)|Compare deux chaînes IPv4.|
|[ipv4_is_match ()](ipv4-is-matchfunction.md)|Correspond à deux chaînes IPv4.|
|[parse_ipv4()](parse-ipv4function.md)|Convertit une chaîne d’entrée en une représentation de nombre longue (signée 64 bits).|
|[parse_ipv4_mask()](parse-ipv4-maskfunction.md)|Convertit la chaîne d’entrée et le masque de préfixe IP en une représentation de nombre longue (signée 64 bits).|
|[ipv6_compare ()](ipv6-comparefunction.md)|Compare deux chaînes IPv4 ou IPv6.|
|[ipv6_is_match ()](ipv6-is-matchfunction.md)|Correspond à deux chaînes IPv4 ou IPv6.|
|[parse_ipv6 ()](parse-ipv6function.md)|Convertit une chaîne IPv6 ou IPv4 en une représentation sous forme de chaîne IPv6 canonique.|
|[parse_ipv6_mask ()](parse-ipv6-maskfunction.md)|Convertit la chaîne IPv6 ou IPv4 et le masque réseau en une représentation sous forme de chaîne IPv6 canonique.|

## <a name="type-functions"></a>Fonctions de type

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[gettype()](gettypefunction.md)|Retourne le type au moment de l’exécution de son argument unique.|

## <a name="scalar-aggregation-functions"></a>Fonctions d’agrégation scalaires

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[dcount_hll()](dcount-hllfunction.md)|Calcule le CpteDom à partir des résultats de HLL (qui a été généré par HLL ou HLL-Merge).|
|[hll_merge()](hllmergefunction.md)|Fusionne les résultats de HLL (version scalaire de la version d’agrégation HLL-Merge ()).|
|[percentile_tdigest()](percentile-tdigestfunction.md)|Calcule le résultat de centile à partir des résultats de tdigest (qui a été généré par tdigest ou Merge-tdigests).|
|[percentrank_tdigest()](percentrank-tdigestfunction.md)|Calcule le rang en pourcentage d’une valeur dans un DataSet.|
|[rank_tdigest()](rank-tdigest.md)|Calcule le rang relatif d’une valeur dans un jeu.|
|[tdigest_merge()](tdigest-mergefunction.md)|Fusionne les résultats de tdigest (version scalaire de la version d’agrégation tdigest-Merge ()).|

## <a name="geospatial-functions"></a>Fonctions géospatiales

|Nom de fonction|Description|
|--------------------------------------------------------------------------|--------------------------------------------------------|
|[geo_distance_2points()](geo-distance-2points-function.md)|Calcule la distance la plus petite entre deux coordonnées géospatiales sur la terre.|
|[geo_geohash_to_central_point()](geo-geohash-to-central-point-function.md)|Calcule les coordonnées géospatiales qui représentent le centre d’une zone rectangulaire géohash.|
|[geo_point_in_circle()](geo-point-in-circle-function.md)|Calcule si les coordonnées géospatiales se trouvent à l’intérieur d’un cercle sur la terre.|
|[geo_point_to_geohash()](geo-point-to-geohash-function.md)|Calcule la valeur de la chaîne de hachage pour un emplacement géographique.|