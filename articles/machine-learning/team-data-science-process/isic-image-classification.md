---
title: Classificação de imagem com o pacote do Azure Machine Learning para pesquisa visual computacional e TDSP (Processo de Ciência de Dados de Equipe) | Microsoft Docs
description: Descreve o uso de TDSP (Processo de Ciência de Dados de Equipe) e o pacote do Azure Machine Learning para pesquisa visual computacional para classificação de imagem.
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: ee2e797f3838b8b6b36174d14c73e97fe9790315
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49392805"
---
# <a name="skin-cancer-image-classification-with-the-azure-machine-learning-package-for-computer-vision-and-team-data-science-process"></a>Classificação de imagem de câncer de pele com o pacote do Azure Machine Learning para pesquisa visual computacional e TDSP (Processo de Ciência de Dados de Equipe)

Este artigo mostra como usar o [pacote do Azure Machine Learning para pesquisa visual computacional](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) para treinar, testar e implantar um modelo de *classificação de imagem*. O exemplo usa os modelos e a estrutura do TDSP (Processo de Ciência de Dados de Equipe) no [Azure Machine Learning Workbench](https://docs.microsoft.com/azure/machine-learning/service/quickstart-installation). Este passo a passo fornece o exemplo completo. Ele usa o [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) como a estrutura de aprendizado profundo, e o treinamento é feito em um computador de GPU da [Máquina virtual de Ciência de Dados](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview). A implantação usa a CLI de operacionalização do Azure Machine Learning.

Muitos aplicativos no domínio da pesquisa visual computacional podem ser enquadrados como problemas de classificação de imagem. Isso inclui a criação de modelos que respondem a perguntas simples como "Um objeto está presente na imagem?" em que o objeto pode ser um cachorro, carro ou barco. Também inclui respostas a perguntas mais complexas como "Qual classe de gravidade de doença ocular é comprovada pelo exame de retina desse paciente?" O pacote do Azure Machine Learning para a pesquisa visual computacional simplifica o pipeline de modelagem e o processamento de dados de classificação de imagem. 

## <a name="link-to-the-github-repository"></a>Link para o repositório do GitHub
Este artigo é um documento resumido sobre o exemplo. Encontre uma documentação mais extensiva no [site do GitHub](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-walkthrough"></a>Passo a passo do Processo de Ciência de Dados de Equipe

Este passo a passo usa o ciclo de vida do [Processo de Ciência de Dados de Equipe](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview). Este passo a passo abrange as seguintes etapas do ciclo de vida.

### <a name="1-data-acquisitionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Aquisição de dados](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
O conjunto de dados ISIC (International Skin Imaging Collaboration) é usado para a tarefa de classificação de imagem. O ISIC é uma parceria entre o meio acadêmico e o setor para facilitar a aplicação de imagens cutâneas digitais para estudar e ajudar a reduzir a mortalidade causada pelo melanoma. Os [arquivos do ISIC](https://isic-archive.com/#images) contêm mais de 13.000 imagens de lesões cutâneas com os rótulos de benigno ou maligno. Faça o download de uma amostra das imagens dos arquivos do ISIC.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modelagem](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
Na etapa de modelagem, as subetapas a seguir são executadas.

#### <a name="dataset-creation"></a>Criação do conjunto de dados

Gere um objeto de conjunto de dados no pacote do Azure Machine Learning para pesquisa visual computacional fornecendo o diretório raiz das imagens no disco local. 

#### <a name="image-visualization-and-annotation"></a>Visualização e anotação de imagem

Visualize as imagens no objeto de conjunto de dados e corrija os rótulos se for necessário.

#### <a name="image-augmentation"></a>Aumento de imagem

Aumente um objeto de conjunto de dados usando as transformações descritas na biblioteca [imgaug](https://github.com/aleju/imgaug).

#### <a name="dnn-model-definition"></a>Definição de modelo DNN

Defina a arquitetura de modelo usada na etapa de treinamento. Há suporte para seis modelos pré-treinados de rede neural avançada no pacote do Azure Machine Learning para pesquisa visual computacional: AlexNet, Resnet-18, Resnet-34, Resnet-50, Resnet-101 e Resnet-152.

#### <a name="classifier-training"></a>Treinamento do classificador

Treine as redes neurais com parâmetros padrão ou personalizados.

#### <a name="evaluation-and-visualization"></a>Avaliação e visualização

Fornece uma funcionalidade para avaliar o desempenho do modelo treinado em um conjunto de dados de teste independente. As métricas de avaliação incluem exatidão, precisão, recuperação e curva ROC.

As subetapas são explicadas em detalhes no Jupyter Notebook correspondente. O notebook também fornece diretrizes para ativar os parâmetros, como taxa de aprendizado, tamanho do minilote e taxa de abandono, para melhorar ainda mais o desempenho do modelo.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. Implantação](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

Essa etapa operacionaliza o modelo produzido na etapa de modelagem. Ela apresenta os pré-requisitos e a configuração necessária. O consumo do serviço Web também é explicado. Neste tutorial, você aprenderá a criar modelos de aprendizado avançado com o pacote do Azure Machine Learning para pesquisa visual computacional e operacionalização do modelo no Azure.

## <a name="next-steps"></a>Próximas etapas
- Leia outras documentações sobre o [pacote do Azure Machine Learning para pesquisa visual computacional](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest).
- Leia a documentação do [Processo de Ciência de Dados de Equipe](https://aka.ms/tdsp) para começar.


## <a name="references"></a>Referências

* [Pacote do Azure Machine Learning para pesquisa visual computacional](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/azure/machine-learning/service/quickstart-installation)
* [Máquina virtual de ciência de dados](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

