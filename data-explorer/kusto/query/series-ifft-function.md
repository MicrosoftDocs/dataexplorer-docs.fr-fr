---
title: series_ifft ()-Azure Explorateur de données
description: Cet article décrit la fonction series_ifft () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 08/13/2020
ms.openlocfilehash: c5862e9c18959726f27ea7d4237058f36a408b5c
ms.sourcegitcommit: 31ebf208d6bfd901f825d048ea69c9bb3d8b87af
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88502132"
---
# <a name="series_ifft"></a>series_ifft ()

Applique la transformation de Fourier rapide inverse (IFFT) sur une série.  

La fonction series_ifft () prend une série de nombres complexes dans le domaine de fréquence et la transforme en domaine temporel/spatial à l’aide de la [transformation de Fourier rapide](https://en.wikipedia.org/wiki/Fast_Fourier_transform). Cette fonction est la fonction complémentaire de [series_fft](series-fft-function.md). En général, la série d’origine est transformée en domaine de fréquence pour le traitement spectral, puis remontée dans le domaine temporel/spatial.

## <a name="syntax"></a>Syntaxe

`series_ifft(`*fft_real* [ `,` *fft_imaginary*]`)`

## <a name="arguments"></a>Arguments

* *fft_real*: tableau dynamique de valeurs numériques représentant le composant réel de la série à transformer.
* *fft_imaginary*: tableau dynamique similaire représentant le composant imaginaire de la série. Ce paramètre est facultatif et doit être spécifié uniquement si la série d’entrée contient des nombres complexes.

## <a name="returns"></a>Retours

La fonction retourne la FFT inverse complexe dans deux séries. Première série pour le composant réel et la deuxième pour le composant imaginaire.

## <a name="example"></a>Exemple

Voir [series_fft](series-fft-function.md#example)
