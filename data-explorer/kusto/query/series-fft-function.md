---
title: series_fft ()-Azure Explorateur de données
description: Cet article décrit la fonction series_fft () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 08/13/2020
ms.openlocfilehash: 32b3b978f57f1a346ccd697fa2d95d962b558a15
ms.sourcegitcommit: 31ebf208d6bfd901f825d048ea69c9bb3d8b87af
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88502137"
---
# <a name="series_fft"></a>series_fft ()

Applique la transformation de Fourier rapide (FFT) sur une série.  

La fonction series_fft () prend une série de nombres complexes dans le domaine temporel/spatial et la transforme en domaine de fréquence à l’aide de la [transformation de Fourier rapide](https://en.wikipedia.org/wiki/Fast_Fourier_transform). La série complexe transformée représente l’amplitude et la phase des fréquences apparaissant dans la série d’origine. Utilisez la fonction complémentaire [series_ifft](series-ifft-function.md) pour effectuer une transformation à partir du domaine de fréquence vers le domaine temporel/spatial.

## <a name="syntax"></a>Syntaxe

`series_fft(`*x_real* [ `,` *x_imaginary*]`)`

## <a name="arguments"></a>Arguments

* *x_real*: tableau dynamique de valeurs numériques représentant le composant réel de la série à transformer.
* *x_imaginary*: tableau dynamique similaire représentant le composant imaginaire de la série. Ce paramètre est facultatif et doit être spécifié uniquement si la série d’entrée contient des nombres complexes.

## <a name="returns"></a>Retours

La fonction retourne la FFT inverse complexe dans deux séries. Première série pour le composant réel et la deuxième pour le composant imaginaire.

## <a name="example"></a>Exemple

* Générez une série complexe, où les composants réels et imaginaires sont des vagues sinusoïdales pures dans différentes fréquences. Utilisez la FFT pour la transformer en domaine de fréquence :

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    let sinewave=(x:double, period:double, gain:double=1.0, phase:double=0.0)
    {
        gain*sin(2*pi()/period*(x+phase))
    }
    ;
    let n=128;      //  signal length
    range x from 0 to n-1 step 1 | extend yr=sinewave(x, 8), yi=sinewave(x, 32)
    | summarize x=make_list(x), y_real=make_list(yr), y_imag=make_list(yi)
    | extend (fft_y_real, fft_y_imag) = series_fft(y_real, y_imag)
    | render linechart with(ysplit=panels)
    ```
    
    Cette requête retourne *fft_y_real* et *fft_y_imag*:  
    
    :::image type="content" source="images/series-fft-function/series-fft.png" alt-text="FFT de série" border="false":::
    
* Transformez une série en domaine de fréquence, puis appliquez la transformation inverse pour récupérer la série d’origine :

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    let sinewave=(x:double, period:double, gain:double=1.0, phase:double=0.0)
    {
        gain*sin(2*pi()/period*(x+phase))
    }
    ;
    let n=128;      //  signal length
    range x from 0 to n-1 step 1 | extend yr=sinewave(x, 8), yi=sinewave(x, 32)
    | summarize x=make_list(x), y_real=make_list(yr), y_imag=make_list(yi)
    | extend (fft_y_real, fft_y_imag) = series_fft(y_real, y_imag)
    | extend (y_real2, y_image2) = series_ifft(fft_y_real, fft_y_imag)
    | project-away fft_y_real, fft_y_imag   //  too many series for linechart with panels
    | render linechart with(ysplit=panels)
    ```
    
    Cette requête retourne *y_real2* et * y_imag2, qui sont identiques à *y_real* et *y_imag*:  
    
    :::image type="content" source="images/series-fft-function/series-ifft.png" alt-text="IFFT de la série" border="false":::
    