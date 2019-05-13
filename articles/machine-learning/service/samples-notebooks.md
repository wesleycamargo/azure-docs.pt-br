---
title: Exemplos de Notebooks Jupyter
titleSuffix: Azure Machine Learning service
description: Encontre e use notebooks Jupyter de exemplo para explorar o serviço de Azure Machine Learning no Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: cd88fd85ce6d18287c700a54e42b6237a42ea5c9
ms.sourcegitcommit: eea74d11a6d6ea6d187e90e368e70e46b76cd2aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035371"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Usar notebooks Jupyter no serviço do Azure Machine Learning

Para sua conveniência, desenvolvemos uma série de notebooks Jupyter Python que você pode usar para explorar o serviço do Azure Machine Learning. 

Saiba como usar o serviço com a documentação neste site e usar esses notebooks para personalizá-los conforme sua situação. 

Use um dos caminhos abaixo para executar um servidor de notebook com estes notebooks de exemplo.  Quando o servidor estiver em execução, encontre notebooks do tutorial na pasta **tutoriais** ou explore recursos diferentes da pasta **how-to-use-azureml**.

## <a name="a-managed-cloud-notebook-server"></a>Um servidor do notebook de nuvem gerenciado

É fácil começar a usar seu próprio servidor de notebook baseado em nuvem. O notebook de exemplo e o [SDK do Azure Machine Learning para Python](https://aka.ms/aml-sdk) já está instalado e configurado para você após a criação desse recurso de nuvem.  

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Os exemplos estão disponíveis na página do notebook.

## <a name="a-data-science-virtual-machine-dsvm"></a>Uma DSVM (Máquina Virtual de Ciência de Dados)

O [SDK do Azure Machine Learning para Python](https://aka.ms/aml-sdk) e o notebook já estão instalados e configurados para você em uma DSVM. 

Depois de [criar uma DSVM](how-to-configure-environment.md#dsvm), siga estas etapas na DSVM para executar os notebooks.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="your-own-jupyter-notebook-server"></a>Seu próprio servidor Jupyter Notebook

Use estas etapas para criar um servidor de notebook local do Jupyter no seu computador.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

As instruções de instalação instalarão os pacotes necessários para executar o início rápido e os notebooks do tutorial.  Outros notebooks de exemplo podem exigir a instalação de componentes adicionais.  Para obter mais informações sobre esses componentes, veja [Instalar o SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="azure-notebooks"></a>Azure Notebooks

Os notebooks de exemplo e o [SDK do Azure Machine Learning para Python](https://aka.ms/aml-sdk) já estão instalados e configurados para você em [Azure Notebooks](https://notebooks.azure.com/). A instalação e as atualizações futuras são gerenciadas automaticamente por meio dos serviços do Azure.

Use o [portal do Azure](https://portal.azure.com) para começar a usar o Azure Notebooks.  Abra seu workspace e, na seção **Visão geral**, selecione **Introdução ao Azure Notebooks**.

## <a name="next-steps"></a>Próximas etapas

+ Explore os notebooks de exemplo para o serviço do Azure Machine Learning neste repositório GitHub: https://aka.ms/aml-notebooks

Tente estes tutoriais:
+ [Treinar e implantar um modelo de classificação de imagem com MNIST](tutorial-train-models-with-aml.md)

+ [Preparar os dados e usar aprendizado de máquina automatizado para treinar um modelo de regressão com o conjunto de dados de táxi de NYC](tutorial-data-prep.md)