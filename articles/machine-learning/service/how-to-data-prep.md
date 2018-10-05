---
title: Preparar os dados com o SDK de preparação de dados do Machine Learning para Python – Azure
description: Saiba como usar o SDK de preparação de dados do Azure Machine Learning para o Python para carregar dados de vários formatos, transformá-los para serem mais utilizáveis e gravá-los em um local para serem acessados por seus modelos.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 673b8ddf3ed07507cbce4b54e9b8a488cb3ac689
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166598"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Preparar dados para modelagem com o Azure Machine Learning
 
A preparação de dados é uma parte importante de um fluxo de trabalho de aprendizado de máquina. Seus modelos poderão ser mais precisos e eficientes se tiverem acesso para limpar dados em um formato que é mais fácil de consumir. 

Você pode preparar seus dados em Python usando o [SDK de preparação de dados do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py). 

## <a name="data-preparation-pipeline"></a>Pipeline de preparação de dados

As principais etapas de preparação de dados são:

1. [Carregar dados](how-to-load-data.md), que podem estar em vários formatos
2. [Transformar](how-to-transform-data.md)-los em uma estrutura mais utilizável
3. [Gravar](how-to-write-data.md) esses dados em um local acessível aos seus modelos

![Processo de preparação de dados](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>Próximas etapas
Examine um [notebook de exemplo](https://github.com/Microsoft/PendletonDocs/blob/master/Scenarios/GettingStarted/getting-started.ipynb) de preparação de dados usando o SDK de preparação de dados do Azure Machine Learning.
