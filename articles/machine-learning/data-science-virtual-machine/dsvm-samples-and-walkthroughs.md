---
title: Exemplos e instruções passo a passo para a Máquina Virtual de Ciência de Dados – Azure | Microsoft Docs
description: Exemplos e instruções passo a passo para a Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: a1f15b805d2f27152d9ba85608ce0dc1d1aac21e
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392550"
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>Exemplos de Máquinas Virtuais de Ciência de Dados (DSVM)

As DSVMs incluem um conjunto abrangente de código de exemplo dois na forma de Jupyter Notebooks, bem como scripts em linguagens como Python e R.    
> [!NOTE]
> Consulte a seção [Acessar o Jupyter](#access-jupyter) para obter mais informações de como executar os Jupyter Notebooks na sua DSVM.

## <a name="quick-reference-of-samples"></a>Referência rápida de amostras
| Categoria de exemplos | DESCRIÇÃO | Locais |
| ------------- | ------------- | ------------- |
| Linguagem **R**  | Amostras em **R** explicando cenários como conectar-se aos armazenamentos de dados de nuvem do Azure, comparar R de Software Livre e R Microsoft e operacionalizar modelos no Microsoft R Server ou no SQL Server. <br/> [Captura de tela](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Linguagem **Python**  | Exemplos em **Python** explicando cenários como conectar-se a armazenamentos de dados de nuvem do Azure e trabalhar com **Azure Machine Learning**.  <br/> [Captura de tela](#python-language) | <br/>`~notebooks` <br/><br/>|
| Linguagem **Julia**  | Exemplo em **Julia** que detalha Plotagem em Julia, aprendizado profundo em Julia, chamada de C e Python de Julia etc. <br/> [Captura de tela](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| **Azure Machine Learning** AzureML  | Crie modelos de ML e de aprendizado profundo com o serviço **Azure Machine Learning** e implementando modelos em qualquer lugar. Aproveite funcionalidades como a ML automatizada, o ajuste de parâmetro hiperinteligente, o gerenciamento de modelos e o treinamento distribuído. <br/> [Captura de tela](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Notebooks do **PyTorch**  | Amostras de aprendizado profundo que utilizam redes neurais baseadas no **PyTorch**. Há uma variedade de blocos de anotações que variam de cenários de iniciantes a avançados.  <br/> [Captura de tela](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| **TensorFlow**  | Vários Exemplos de Rede Neural diferentes e técnicas implementadas usando a estrutura **TensorFlow**. <br/> [Captura de tela](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| **CNTK** <br/> (Kit de Ferramentas Cognitivas da Microsoft)  | Exemplos de aprendizado profundo publicados pela equipe do Kit de Ferramentas Cognitivas na Microsoft.  <br/> [Captura de tela](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| **caffe2** | Exemplos de Aprendizado Profundo utilizando redes neurais baseadas em **caffe2**. Há vários blocos de anotações que desenvolvidos para familiarizar os usuários com o caffe2 e como usá-los com eficiência, inclusive exemplos de como pré-processamento de imagem, criação de conjunto de dados, Regressão e uso de modelos pré-treinados. <br/> [Captura de tela](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| **H2O**   | Exemplos com base em Python utilizando **H2O** para muitos problemas de cenário do mundo real. <br/> [Captura de tela](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Linguagem **SparkML**  | Exemplo de uso de recursos e funcionalidades do kit de ferramentas **MLlib** do Spark por meio do **pySpark** e do **MMLSpark – Microsoft Machine Learning para Apache Spark** no **Apache Spark 2.x**.  <br/> [Captura de tela](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| **XGBoost** | Exemplos padrão de Machine Learning em **XGBoost** para cenários como classificação, regressão etc. <br/> [Captura de tela](#xgboost) | <br/>**Windows**:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Acessar o Jupyter 

Você pode acessar o Jupyter clicando no ícone `Jupyter` no menu do aplicativo ou da área de trabalho. Você também pode acessar o Jupyter nas edições do Linux do DSVM remotamente usando um navegador da Web visitando **`https://<Full Domain Name or IP Address of the DSVM>:8000`** no Ubuntu.

Consulte a captura de tela para adicionar a exceção e habilitar o acesso do Jupyter pelo navegador.


![Habilitar a exceção do Jupyter](./media/ubuntu-jupyter-exception.png)


Entre com a mesma senha usada para o logon na DSVM.
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

