---
title: predict_onnx_lf ()-Azure Explorateur de données
description: Cet article décrit la fonction définie par l’utilisateur predict_onnx_lf () dans Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/09/2020
ms.openlocfilehash: c4f997e25de4ff1f5dbc482f1c1e08cfa4adcf57
ms.sourcegitcommit: 993bc7b69096ab5516d3c650b9df97a1f419457b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614754"
---
# <a name="predict_onnx_lf"></a>predict_onnx_lf ()

La fonction `predict_onnx_lf()` prédit à l’aide d’un modèle de machine learning formé existant. Ce modèle a été converti au format [ONNX](https://onnx.ai/) , sérialisé en chaîne et enregistré dans une table Azure Explorateur de données standard.

> [!NOTE]
> `predict_onnx_lf()` est une [fonction définie par l’utilisateur (UDF)](../query/functions/user-defined-functions.md). Cette fonction contient python inline et nécessite l' [activation du plug-in Python ()](../query/pythonplugin.md#enable-the-plugin) sur le cluster. Pour plus d’informations, consultez [utilisation](#usage).

## <a name="syntax"></a>Syntaxe

`T | invoke predict_onnx_lf(`*models_tbl* `,` *MODEL_NAME* `,` *features_cols* `,` *pred_col*`)`

## <a name="arguments"></a>Arguments

* *models_tbl*: nom de la table contenant tous les modèles sérialisés. Cette table doit contenir les colonnes suivantes :
    * *nom*: le nom du modèle
    * *horodatage*: heure de l’apprentissage du modèle
    * *modèle*: représentation sous forme de chaîne du modèle sérialisé
* *MODEL_NAME*: nom du modèle spécifique à utiliser.
* *features_cols*: tableau dynamique contenant les noms des colonnes de fonctionnalités utilisées par le modèle pour la prédiction.
* *pred_col*: nom de la colonne qui stocke les prédictions.

## <a name="usage"></a>Usage

`predict_onnx_lf()` est une [fonction tabulaire](../query/functions/user-defined-functions.md#tabular-function) définie par l’utilisateur à appliquer à l’aide de l' [opérateur Invoke](../query/invokeoperator.md). Vous pouvez incorporer son code dans votre requête ou l’installer dans votre base de données. Il existe deux options d’utilisation : une utilisation ad hoc et une utilisation permanente. Consultez les onglets ci-dessous pour obtenir des exemples.

# <a name="ad-hoc"></a>[Ad hoc](#tab/adhoc)

Pour une utilisation ad hoc, incorporez le code à l’aide de l' [instruction Let](../query/letstatement.md). Aucune autorisation n’est requise.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let predict_onnx_lf=(samples:(*), models_tbl:(name:string, timestamp:datetime, model:string), model_name:string, features_cols:dynamic, pred_col:string)
{
    let model_str = toscalar(models_tbl | where name == model_name | top 1 by timestamp desc | project model);
    let kwargs = pack('smodel', model_str, 'features_cols', features_cols, 'pred_col', pred_col);
    let code =
    '\n'
    'import pickle\n'
    'import binascii\n'
    '\n'
    'smodel = kargs["smodel"]\n'
    'features_cols = kargs["features_cols"]\n'
    'pred_col = kargs["pred_col"]\n'
    'bmodel = binascii.unhexlify(smodel)\n'
    '\n'
    'features_cols = kargs["features_cols"]\n'
    'pred_col = kargs["pred_col"]\n'
    '\n'
    'import onnxruntime as rt\n'
    'sess = rt.InferenceSession(bmodel)\n'
    'input_name = sess.get_inputs()[0].name\n'
    'label_name = sess.get_outputs()[0].name\n'
    'df1 = df[features_cols]\n'
    'predictions = sess.run([label_name], {input_name: df1.values.astype(np.float32)})[0]\n'
    '\n'
    'result = df\n'
    'result[pred_col] = pd.DataFrame(predictions, columns=[pred_col])'
    '\n'
    ;
    samples | evaluate python(typeof(*), code, kwargs)
};
//
// Predicts room occupancy from sensors measurements, and calculates the confusion matrix
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature,Humidity,Light and CO2.
// Ground-truth labels were obtained from time stamped pictures that were taken every minute
//
OccupancyDetection 
| where Test == 1
| extend pred_Occupancy=bool(0)
| invoke predict_onnx_lf(ML_Models, 'ONNX-Occupancy', pack_array('Temperature', 'Humidity', 'Light', 'CO2', 'HumidityRatio'), 'pred_Occupancy')
| summarize n=count() by Occupancy, pred_Occupancy
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Pour une utilisation permanente, utilisez la [fonction. Create](../management/create-function.md). La création d’une fonction nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Installation unique

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\ML", docstring = "Predict using ONNX model")
predict_onnx_lf(samples:(*), models_tbl:(name:string, timestamp:datetime, model:string), model_name:string, features_cols:dynamic, pred_col:string)
{
    let model_str = toscalar(models_tbl | where name == model_name | top 1 by timestamp desc | project model);
    let kwargs = pack('smodel', model_str, 'features_cols', features_cols, 'pred_col', pred_col);
    let code =
    '\n'
    'import pickle\n'
    'import binascii\n'
    '\n'
    'smodel = kargs["smodel"]\n'
    'features_cols = kargs["features_cols"]\n'
    'pred_col = kargs["pred_col"]\n'
    'bmodel = binascii.unhexlify(smodel)\n'
    '\n'
    'features_cols = kargs["features_cols"]\n'
    'pred_col = kargs["pred_col"]\n'
    '\n'
    'import onnxruntime as rt\n'
    'sess = rt.InferenceSession(bmodel)\n'
    'input_name = sess.get_inputs()[0].name\n'
    'label_name = sess.get_outputs()[0].name\n'
    'df1 = df[features_cols]\n'
    'predictions = sess.run([label_name], {input_name: df1.values.astype(np.float32)})[0]\n'
    '\n'
    'result = df\n'
    'result[pred_col] = pd.DataFrame(predictions, columns=[pred_col])'
    '\n'
    ;
    samples | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>Usage

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Predicts room occupancy from sensors measurements, and calculates the confusion matrix
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature,Humidity,Light and CO2.
// Ground-truth labels were obtained from time stamped pictures that were taken every minute
//
OccupancyDetection 
| where Test == 1
| extend pred_Occupancy=bool(0)
| invoke predict_onnx_lf(ML_Models, 'ONNX-Occupancy', pack_array('Temperature', 'Humidity', 'Light', 'CO2', 'HumidityRatio'), 'pred_Occupancy')
| summarize n=count() by Occupancy, pred_Occupancy
```

---

Matrice de confusion :
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
Occupancy   pred_Occupancy  n
TRUE        TRUE            3006
FALSE       TRUE            112
TRUE        FALSE           15
FALSE       FALSE           9284
```
