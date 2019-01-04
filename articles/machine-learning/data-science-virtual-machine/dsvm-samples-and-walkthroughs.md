---
title: Exemplos e instruções passo a passo para Máquinas Virtuais de Ciência de Dados – Azure | Microsoft Docs
description: Saiba mais sobre exemplos e instruções passo a passo que ensinam como realizar tarefas e cenários comuns com a Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: 64be6af340aa02c6c0b094013d2cbd286286aca7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101976"
---
# <a name="samples-on-data-science-virtual-machines"></a>Exemplos em Máquinas Virtuais de Ciência de Dados

As Máquinas Virtuais de Ciência de Dados do Azure incluem um conjunto abrangente de código de exemplo. O código de exemplo está na forma de notebooks do Jupyter e scripts em linguagens como o Python e R. 
> [!NOTE]
> Para obter mais informações sobre como executar notebooks do Jupyter em suas máquinas virtuais de ciência de dados, consulte a seção [Acessar o Jupyter](#access-jupyter).

## <a name="quick-reference-of-samples"></a>Referência rápida de exemplos
| Categoria de exemplos | DESCRIÇÃO | Locais |
| ------------- | ------------- | ------------- |
| Linguagem R  | Exemplos em R explicam cenários como a conexão com armazenamentos de dados em nuvem do Azure. Eles também explicam como comparar o R de software livre e o Microsoft R. E eles explicam como operacionalizar modelos no Microsoft R Server ou SQL Server. <br/> [Linguagem R](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Linguagem Python  | Exemplos em Python explicam cenários como conectar-se a armazenamentos de dados em nuvem do Azure e trabalhar com o Azure Machine Learning.  <br/> [Linguagem Python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Linguagem Julia  | Exemplo em Julia que detalha plotagem e aprendizagem aprofundada no Julia. Ele também explica como chamar C e Python no Julia. <br/> [Linguagem Julia](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Crie modelos de aprendizado de máquina e de aprendizagem profunda com o Machine Learning. Implante modelos em qualquer lugar. Use aprendizado de máquina automatizado e ajuste de hiperparâmetro inteligente. Use também o gerenciamento de modelos e treinamento distribuído. <br/> [Machine Learning](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Notebooks do PyTorch  | Exemplos de aprendizagem profunda que usam redes neurais baseadas no PyTorch. Os notebooks vão de cenários iniciantes a avançados.  <br/> [Notebooks do PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Exemplos de Rede Neural diferentes e técnicas implementadas usando a estrutura TensorFlow. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Kit de Ferramentas Cognitivas da Microsoft <br/>   | Exemplos de aprendizado profundo publicados pela equipe do Kit de Ferramentas Cognitivas na Microsoft.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| caffe2 | Exemplos de aprendizagem profunda que usam redes neurais baseadas no caffe2. Vários notebooks para familiarizar os usuários com o caffe2 e ajudar em como usá-lo efetivamente. Os exemplos incluem o pré-processamento de imagem e a criação de conjunto de dados. Eles também incluem regressão e como usar modelos pré-treinados. <br/> [caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Exemplos baseados em Python que usam o H2O para problemas de situações reais. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Linguagem SparkML  | Exemplo que usam recursos do kit de ferramentas MLLib do Spark por meio do pySpark e do MMLSpark – Microsoft Machine Learning para Apache Spark no Apache Spark 2.x.  <br/> [Linguagem SparkML](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Exemplos de aprendizado de máquina padrão em XGBoost para cenários como classificação e regressão. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Acessar o Jupyter 

Para acessar o Jupyter, selecione o ícone `Jupyter` no menu da área de trabalho ou do aplicativo. Você também pode acessar o Jupyter nas edições do Linux de Máquinas Virtuais de Ciência de Dados. Você pode acessar remotamente de um navegador da Web visitando `https://<Full Domain Name or IP Address of the DSVM>:8000` no Ubuntu.

Para adicionar exceções e disponibilizar o acesso ao Jupyter em um navegador, consulte a captura de tela a seguir.


![Habilitar exceção do Jupyter](./media/ubuntu-jupyter-exception.png)


Entre com a mesma senha que o logon das Máquinas Virtuais de Ciência de Dados.
<br/>

**Página inicial do Jupyter**
<br/>![Página inicial do Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Linguagem R 
<br/>![Exemplos de R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Linguagem Python
<br/>![Exemplos em Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Linguagem Julia 
<br/>![Exemplos de Julia](./media/julia-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![Exemplos de AzurekML](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Exemplos do PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Exemplos de TensorFlow](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![Exemplos do CNTK](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>caffe2 
<br/>![Exemplos de caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Exemplos de H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Exemplos de SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Exemplos de XGBoost](./media/xgboost-samples.png)<br/>

