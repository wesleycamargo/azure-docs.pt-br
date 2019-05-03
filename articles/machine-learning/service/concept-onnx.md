---
title: Alto desempenho, entre a inferência de tipos de plataforma com ONNX
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre ONNX e o tempo de execução para acelerar a modelos ONNX
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 04/24/2019
ms.custom: seodec18
ms.openlocfilehash: 6bda1af8095e7026c79b7d8ffe45b39f6cbb3508
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028689"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX e Azure Machine Learning: Criar e acelerar os modelos de ML

Saiba como usar o [Open Neural Network Exchange](https://onnx.ai) (ONNX) pode ajudar a otimizar seu modelos de aprendizado de máquina.

Otimizando modelos de machine learning para inferência de tipos é difícil, pois você precisará ajustar o modelo e a biblioteca de inferência de tipos para aproveitar ao máximo os recursos de hardware. O problema se torna extremamente difícil, se você quiser obter um desempenho ideal em diferentes tipos de plataformas (nuvem/borda, CPU/GPU, etc.), já que cada um tem características e recursos diferentes. A complexidade aumenta se você tiver modelos de uma variedade de estruturas que precisam ser executados em uma variedade de plataformas. Ele consome muito tempo para otimizar a todas as combinações diferentes de estruturas e hardware. Uma solução para treinar uma vez em sua estrutura preferencial e executados em qualquer lugar na nuvem ou na borda é necessária. Isso é onde entra ONNX.

A Microsoft e uma comunidade de parceiros criado ONNX como um padrão aberto para representar modelos de aprendizado de máquina. Modelos de [muitas estruturas](https://onnx.ai/supported-tools) incluindo TensorFlow, PyTorch, SciKit-Learn, Keras, Chainer, MXNet e MATLAB pode ser exportada ou convertido no formato ONNX padrão. Depois que os modelos estão no formato ONNX, eles podem ser executados em uma variedade de plataformas e dispositivos.

[Tempo de execução ONNX](https://github.com/Microsoft/onnxruntime) é um mecanismo de inferência de alto desempenho para a implantação de modelos ONNX em produção. Ele é otimizado para nuvem e a borda e funciona no Linux, Windows e Mac. Escritos em C++, ele também tem C, Python, e C# APIs. Tempo de execução ONNX fornece suporte para todos a especificação de ML ONNX e também se integra com os aceleradores de hardware diferentes, como o TensorRT em GPUs NVidia.

O tempo de execução ONNX é usado em serviços da Microsoft de alta escala, como Bing, Office e os serviços Cognitivos. Ganhos de desempenho são dependentes de uma série de fatores, mas esses serviços da Microsoft tem visto um __média de 2 vezes de ganho de desempenho na CPU__. Tempo de execução ONNX também é usado como parte do ML do Windows em centenas de milhões de dispositivos. Você pode usar o tempo de execução com os serviços do Azure Machine Learning. Usando o tempo de execução ONNX, você pode aproveitar as otimizações extensivo de nível de produção, melhorias contínuas e testes.

[![Diagrama de fluxo ONNX mostrando o treinamento, conversores e implantação](media/concept-onnx/onnx.png) ](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Obter modelos ONNX

Você pode obter modelos ONNX de várias maneiras:
+ Treinar o novo modelo ONNX no serviço do Azure Machine Learning (consulte os exemplos na parte inferior deste artigo)
+ Converter modelo existente de outro formato ONNX (consulte a [tutoriais](https://github.com/onnx/tutorials)) 
+ Obter um modelo ONNX previamente treinado do [Zoo de modelo ONNX](https://github.com/onnx/models) (consulte os exemplos na parte inferior deste artigo)
+ Gerar um modelo ONNX personalizado do [Serviço de Visão Personalizada do Azure](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Muitos modelos, incluindo a classificação de imagens, detecção de objetos e processamento de texto podem ser representados como modelos ONNX. No entanto alguns modelos não poderá ser convertido com êxito. Se você enfrentar essa situação, envie um problema no GitHub do respectivo conversor que você usou. Você pode continuar usando seu modelo de formato existente até que o problema seja resolvido.

## <a name="deploy-onnx-models-in-azure"></a>Implantar modelos do ONNX no Azure

Com o serviço do Azure Machine Learning, é possível implantar, gerenciar e monitorar os modelos do ONNX. Usando o [fluxo de trabalho de implantação](concept-model-management-and-deployment.md) padrão e o ONNX Runtime, é possível criar um ponto de extremidade REST hospedado na nuvem. Consulte os notebooks do Jupyter de exemplo no final deste artigo para experimentá-lo por conta própria. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Instalar e usar o tempo de execução ONNX com Python

Pacotes do Python para o tempo de execução ONNX estão disponíveis no [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Leia [requisitos de sistema](https://github.com/Microsoft/onnxruntime#system-requirements) antes da instalação. 

 Para instalar o tempo de execução ONNX para Python, use um dos seguintes comandos: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
``` 

Para chamar o ONNX Runtime no script do Python, use:    
```python   
import onnxruntime  
session = onnxruntime.InferenceSession("path to model") 
``` 

A documentação que acompanha o modelo geralmente informa as entradas e saídas para usar o modelo. Também é possível usar uma ferramenta de visualização como o [Netron](https://github.com/lutzroeder/Netron) para exibir o modelo. O Tempo de Execução do ONNX também permite consultar os metadados do modelo, as entradas e as saídas:    
```python   
session.get_modelmeta() 
first_input_name = session.get_inputs()[0].name 
first_output_name = session.get_outputs()[0].name   
``` 

Para fazer inferências no modelo, use `run` e passe a lista de saídas que deseja receber (deixe em branco se quiser todas) e um mapa dos valores de entrada. O resultado é uma lista das saídas.  
```python   
results = session.run(["output1", "output2"], {"input1": indata1, "input2": indata2})   
results = session.run([], {"input1": indata1, "input2": indata2})   
``` 

Para obter a referência completa da API do Python, consulte os [documentos de referência do ONNX Runtime](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Exemplos

Consulte [how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx), por exemplo, notebooks que criam e implantam modelos ONNX.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Mais informações

Saiba mais sobre ONNX ou colabore com o projeto:
+ [Site do projeto ONNX](https://onnx.ai)
+ [Código do ONNX no GitHub](https://github.com/onnx/onnx)

Saiba mais sobre o ONNX Runtime ou contribua para o projeto:
+ [Repositório GitHub do ONNX Runtime](https://github.com/Microsoft/onnxruntime)


