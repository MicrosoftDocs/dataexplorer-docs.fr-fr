---
title: Azure Data Explorer Python SDK - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Azure Data Explorer Python SDK dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 5bee1fce1ca76069c34602872b2d4dedeb762ec2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502854"
---
# <a name="azure-data-explorer-python-sdk"></a>Azure Data Explorer Python SDK

Azure Data Explorer *Kusto Python Client* library fournit la capacité de interroger les clusters Azure Data Explorer à l’aide de Python. Il est compatible Python 2.x/3.x et prend en charge tous les types de données à l’aide de l’interface aPI Python DB familière.

Il est possible d’utiliser la bibliothèque, par exemple, à partir de [Jupyter Notebooks](https://jupyter.org/) qui sont attachés à des clusters Spark, y compris, mais pas exclusivement, [Azure Databricks](https://azure.microsoft.com/services/databricks/) instances.

*Kusto Python Ingest Client* est une bibliothèque python qui permet d’envoyer des données au service Azure Data Explorer - c’est-à-dire les données iningérer. 

* [Comment installer le package](https://github.com/Azure/azure-kusto-python#install)

* [Échantillon de requête Kusto](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py)

* [Échantillon d’ingérer de données](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-ingest/tests/sample.py)

* [Dépôt GitHub](https://github.com/Azure/azure-kusto-python)

    [![texte de remplacement](https://travis-ci.org/Azure/azure-kusto-python.svg?branch=master "azure-kusto-python")](https://travis-ci.org/Azure/azure-kusto-python)

* Forfaits Pypi:

    * [version PyPI azure-kusto-data](https://pypi.org/project/azure-kusto-data/)
    [![](https://badge.fury.io/py/azure-kusto-data.svg)](https://badge.fury.io/py/azure-kusto-data)
    * [version PyPI azure-kusto-ingest](https://pypi.org/project/azure-kusto-ingest/)
    [![](https://badge.fury.io/py/azure-kusto-ingest.svg)](https://badge.fury.io/py/azure-kusto-ingest)
    * [version azure-mgmt-kusto](https://pypi.org/project/azure-mgmt-kusto/)
    [![PyPI](https://badge.fury.io/py/azure-mgmt-kusto.svg)](https://badge.fury.io/py/azure-mgmt-kusto)