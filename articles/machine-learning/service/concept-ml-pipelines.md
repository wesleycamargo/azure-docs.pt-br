---
title: Criar pipelines de machine learning – serviço do Azure Machine Learning
description: Neste artigo, aprenda sobre os pipelines de aprendizado de máquina que você pode criar com o SDK do Azure Machine Learning para Python e as vantagens de usar pipelines. Pipelines de ML (aprendizado de máquina) são usados por cientistas de dados para criar, otimizar e gerenciar seus fluxos de trabalho de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 09/24/2018
ms.openlocfilehash: 45aa954d2f85267b2c7c9aa2a7ba04e436765433
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023912"
---
# <a name="pipelines-and-azure-machine-learning"></a>Pipelines e Azure Machine Learning

Neste artigo, aprenda sobre os pipelines de aprendizado de máquina que você pode criar com o SDK do Azure Machine Learning para Python e as vantagens de usar pipelines.

## <a name="what-are-machine-learning-pipelines"></a>O que são pipelines de aprendizado de máquina?

Pipelines de ML (aprendizado de máquina) são usados por cientistas de dados para criar, otimizar e gerenciar seus fluxos de trabalho de aprendizado de máquina. Um pipeline típico envolve uma sequência de etapas que abordam as seguintes áreas:

+ Preparação de dados, como normalizações e transformações
+ Treinamento, como ajuste de hiperparâmetro e validação de modelo
+ Avaliação e implantação de modelo  

O diagrama a seguir mostra um exemplo de pipeline:

[ ![Pipelines de aprendizado de máquina nos serviços do Azure Machine Learning](./media/concept-ml-pipelines/pipelines.png) ] (./media/concept-ml-pipelines/machine-learning-pipelines-big.png#lightbox)

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Por que criar pipelines com o Azure Machine Learning?

O [SDK do Azure Machine Learning para Python](#the-python-sdk-for-pipelines) pode ser usado para criar pipelines de ML, bem como enviar e acompanhar execuções de pipeline individuais.

Com pipelines, você pode otimizar o fluxo de trabalho com simplicidade, velocidade, portabilidade e reutilização. Ao criar pipelines com o Azure Machine Learning, você pode se concentrar o que conhece melhor &mdash; aprendizado de máquina &mdash;, em vez de infraestrutura.

Usar etapas distintas torna possível executar novamente apenas as etapas de que você precisa, como ajustar e testar seu fluxo de trabalho. Uma etapa é uma unidade computacional no pipeline. Conforme mostrado no diagrama acima, a tarefa de preparação de dados pode envolver muitas etapas, incluindo, entre outras, normalização, transformação, validação e personalização.

Depois que o pipeline é criado, muitas vezes há mais ajustes finos em torno do loop de treinamento do pipeline. Quando você executa novamente um pipeline, a execução vai pula para as etapas que precisa executar novamente, como um script de treinamento atualizado, e ignora o que não mudou. O mesmo paradigma se aplica a scripts inalterados usados para a execução da etapa. 

Com o Azure Machine Learning, você pode usar vários kits de ferramentas e estruturas, como Microsoft Cognitive Toolkit ou TensorFlow, para cada etapa em seu pipeline. O Azure é coordenado entre os vários [destinos de computação](concept-azure-machine-learning-architecture.md) que você usa, de modo que seus dados intermediários possam ser facilmente compartilhados com os destinos de computação downstream. 

## <a name="key-advantages"></a>Principais vantagens

As principais vantagens da criação de pipelines para seus fluxos de trabalho de aprendizado de máquina são:

|Principal vantagem|DESCRIÇÃO|
|:-------:|-----------|
|**Execuções&nbsp;autônomas**|Agende algumas etapas para executar em paralelo ou em sequência de maneira confiável e autônoma. Uma vez que a preparação e a modelagem de dados podem durar dias ou semanas, agora você pode se concentrar em outras tarefas enquanto seu pipeline está em execução. |
|**Computação diversificada e mista**|Use vários pipelines coordenados de modo confiável entre armazenamentos e computações heterogêneos e escalonáveis. Etapas individuais de pipeline podem ser executadas em destinos de computação diferentes, como HDInsight, VMs de Ciência de Dados de GPU e Databricks, para fazer um uso eficiente das opções de computação disponíveis.|
|**Capacidade de reutilização**|Pipelines podem ser transformados em modelo para cenários específicos, como treinamento e pontuação em lote.  Eles podem ser disparados de sistemas externos por meio de chamadas REST simples.|
|**Acompanhamento e controle de versão**|Em vez de acompanhar manualmente dados e caminhos de resultado conforme você faz a iteração, use o SDK de pipelines para dar um nome e versão às suas origens de dados, entradas e saídas, bem como gerenciar scripts e dados separadamente para aumentar a produtividade.|

## <a name="the-python-sdk-for-pipelines"></a>O SDK do Python para pipelines

Use o Python para criar os pipelines de ML. O SDK do Azure Machine Learning oferece constructos obrigatórios para o sequenciamento e a paralelização de etapas em seus pipelines quando nenhuma dependência de dados está presente. Você pode interagir com eles em Jupyter Notebooks ou em outro IDE preferencial. 

Usando dependências de dados declarativas, você pode otimizar suas tarefas. O SDK inclui uma estrutura de módulos predefinidos para tarefas comuns, como transferência de dados, criação do destino de computação e publicação do modelo. A estrutura pode ser estendida para modelar suas próprias convenções implementando etapas personalizadas reutilizáveis em pipelines.

Pipelines podem ser salvos como modelos e implantados em um ponto de extremidade REST, portanto, você pode agendar trabalhos de novo treinamento ou pontuação de lote.

Confira os [documentos de referência do SDK do Python para pipelines](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e o bloco de anotações na próxima seção para aprender a criar os seus próprios.

## <a name="example-notebooks"></a>Blocos de anotações de exemplo
 
O seguinte caderno demonstra pipelines com o Aprendizado de Máquina do Azure: [pipeline / pipeline-batch-scoring.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb).
 
Veja este caderno:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
