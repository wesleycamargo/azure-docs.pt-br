---
title: Início Rápido Executar um notebook em seu próprio servidor do notebook
titleSuffix: Azure Machine Learning service
description: Introdução ao Serviço do Azure Machine Learning. Use seu próprio servidor do notebook local para experimentar seu workspace.  Seu workspace é o bloco fundamental na nuvem que você usa para experimentar, treinar e implantar modelos de machine learning.
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
ms.reviewer: sgilley
author: sdgilley
ms.author: sgilley
ms.date: 03/21/2019
ms.custom: seodec18
ms.openlocfilehash: 8bb601ac5c1ed8e9ca6d2027fe1b774a938e1f0a
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58362313"
---
# <a name="quickstart-use-your-own-notebook-server-to-get-started-with-azure-machine-learning"></a>Início Rápido: Usar seu próprio servidor do notebook para começar a usar o Azure Machine Learning

Neste artigo, você usará seu próprio servidor do notebook para executar código registrado no [workspace do serviço do Azure Machine Learning](concept-azure-machine-learning-architecture.md). O workspace é o bloco fundamental na nuvem usado para experimentar, treinar e implantar modelos de machine learning com o Machine Learning.

Este início rápido usa seu próprio ambiente do Python e o Servidor do Jupyter Notebook. Para ver um início rápido sem nenhuma instalação, confira [Início Rápido: Usar um servidor do notebook baseado em nuvem para começar a usar o Azure Machine Learning](quickstart-run-cloud-notebook.md) 

Exiba uma versão em vídeo deste início rápido:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

Neste início rápido, você:

* Escrever código que registra em log os valores dentro do espaço de trabalho.
* Exibir os valores registrados em log em seu workspace.

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Teste hoje mesmo a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Pré-requisitos

* Um servidor de notebook do Python 3.6 com o SDK do Azure Machine Learning instalado
* Um workspace de serviço do Azure Machine Learning
* Um arquivo de configuração do workspace (**aml_config/config.json** ).

Obtenha todos esses pré-requisitos em [Criar um workspace de serviço do Azure Machine Learning](setup-create-workspace.md#portal).


## <a name="use-the-workspace"></a>Usar o workspace

Crie um script ou inicie um notebook no mesmo diretório que o do seu arquivo de configuração do workspace. Execute este código que usa as APIs básicas do SDK para acompanhar execuções do experimento.

1. Crie um experimento no workspace.
1. Registre em log um único valor para o experimento.
1. Faça uma lista de valores para o experimento.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>Exibir os valores registrados em log

Quando a execução for concluída, você poderá exibir o execução do experimento no portal do Azure. Para imprimir uma URL que direciona você aos resultados da última execução, use o seguinte código:

```python
print(run.get_portal_url())
```

Use o link para exibir os valores registrados no portal do Azure no seu navegador.

![Valores registrados em log no portal do Azure](./media/quickstart-run-local-notebook/logged-values.png)

## <a name="clean-up-resources"></a>Limpar recursos 

>[!IMPORTANT]
>Use os recursos criados aqui como pré-requisitos para outros tutoriais e artigos de instruções sobre o Machine Learning.

Se você não pretende usar os recursos criados neste artigo, exclua-os para evitar a geração de encargos.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou os recursos necessários para experimentar e implantar modelos. Você executou um código em um notebook e explorou o histórico de execuções desse código no workspace na nuvem.

> [!div class="nextstepaction"]
> [Tutorial: treinar um modelo de classificação de imagem](tutorial-train-models-with-aml.md)

Você também pode explorar [exemplos mais avançados no GitHub](https://aka.ms/aml-notebooks) ou exibir o [guia do usuário do SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
