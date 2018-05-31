---
title: Classificação de imagem com o AMLPCV (Pacote do AML (Azure Machine Learning) para Pesquisa Visual Computacional) e o TDSP (Processo de Ciência de Dados de Equipe) | Microsoft Docs
description: Descreve o uso do TDSP (Processo de Ciência de Dados de Equipe) e AMLPCV para classificação de imagem
services: machine-learning, team-data-science-process
documentationcenter: ''
author: xibingao
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 18/05/2018
ms.author: xibingao
ms.openlocfilehash: cfb49f08a245fc08ba3fe78333e801ea2d358c4a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367938"
---
# <a name="skin-cancer-image-classification-with-azure-machine-learning-aml-package-for-computer-vision-amlpcv-and-team-data-science-process-tdsp"></a>Classificação de imagem de câncer de pele com o AMLPCV (Pacote do AML (Azure Machine Learning) para Pesquisa Visual Computacional) e TDSP (Processo de Ciência de Dados de Equipe)

## <a name="introduction"></a>Introdução

Este artigo mostra como usar o [AMLPCV (Pacote do Azure Machine Learning para Pesquisa Visual Computacional)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) para treinar, testar e implantar um modelo de **classificação de imagem**. A amostra usa os modelos e a estrutura do TDSP no [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation). A amostra completa é fornecida neste passo a passo. Ela usa o [CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) como a estrutura de aprendizado profundo e o treinamento é feito em um computador de GPU da [VM de Ciência de Dados](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview). A implantação usa a CLI de Operacionalização do Azure ML.

Muitos aplicativos no domínio da pesquisa visual computacional podem ser enquadrados como problemas de classificação de imagem. Isso inclui a criação de modelos que respondem a perguntas como "Um objeto está presente na imagem?" (o objeto pode ser um *cachorro*, um *carro* ou um *navio*) e perguntas mais complexas como "Qual classe de gravidade de doença ocular é comprovada pelo exame de retina desse paciente?" O AMLPCV simplifica o processamento de dados de classificação de imagem e o pipeline de modelagem. 

## <a name="link-to-github-repository"></a>Link para o repositório do GitHub
Fornecemos uma documentação resumida aqui sobre a amostra. Encontre uma documentação mais extensiva no [site do GitHub](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-tdsp-walkthrough-with-amlpcv"></a>Passo a passo do TDSP (Processo de Ciência de Dados de Equipe) com o AMLPCV

Este passo a passo usa o ciclo de vida do [TDSP (Processo de Ciência de Dados de Equipe)](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview).

Este passo a passo abrange as seguintes etapas do ciclo de vida:

### <a name="1-data-acquisionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Aquisição de dados](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
O conjunto de dados do ISIC é usado para a tarefa de classificação de imagem. O ISIC (International Skin Imaging Collaboration) é uma parceria entre o meio acadêmico e o setor para facilitar a aplicação de imagens cutâneas digitais para estudar e ajudar a reduzir a mortalidade causada pelo melanoma. Os [arquivos do ISIC](https://isic-archive.com/#images) contêm mais de 13.000 imagens de lesão cutânea com os rótulos benigno ou maligno. Baixamos uma amostra das imagens dos arquivos do ISIC.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modelagem](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
Na etapa de modelagem, as subetapas a seguir são executadas. 

<b>2.1 Criação do conjunto de dados</b><br>

Para gerar um objeto de Conjunto de Dados no AMLPCV, forneça um diretório raiz de imagens no disco local. 

<b>2.2 Visualização e anotação de imagem</b><br>

Visualize as imagens no objeto de conjunto de dados e corrija alguns dos rótulos se necessário.

<b>2.3 Aumento de imagem</b><br>

Aumente um objeto de conjunto de dados usando as transformações descritas na biblioteca [imgaug](https://github.com/aleju/imgaug).

<b>2.4 Definição de modelo DNN</b><br>

Defina a arquitetura de modelo usada na etapa de treinamento. Seis modelos pré-treinados diferentes de rede neural profunda são compatíveis com o AMLPCV: AlexNet, Resnet-18, Resnet-34, Resnet-50, Resnet-101 e Resnet-152.

<b>2.5 Treinamento do classificador</b><br>

Treine as redes neurais com parâmetros padrão ou personalizados.

<b>2.6 Avaliação e visualização</b><br>

A subetapa fornece uma funcionalidade para avaliar o desempenho do modelo treinado em um conjunto de dados de teste independente. As métricas de avaliação incluem exatidão, precisão e recuperação e curva ROC.

Essas subetapas são explicadas em detalhes no Jupyter Notebook correspondente. Também fornecemos diretrizes para ativar os parâmetros, como taxa de aprendizado, tamanho do minilote e taxa de abandono, para melhorar ainda mais o desempenho do modelo.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. Implantação](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

Essa etapa operacionaliza o modelo produzido na etapa de modelagem. Ela apresenta os pré-requisitos de operacionalização e a configuração. Por fim, o consumo do serviço Web também é explicado. Por meio deste tutorial, você pode aprender a criar modelos de aprendizado profundo com o AMLPCV e operacionalizar o modelo no Azure.

## <a name="next-steps"></a>Próximas etapas
Leia outros documentos sobre o [AMLPCV (Pacote do Azure Machine Learning para Pesquisa Visual Computacional)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) e o [TDSP (Processo de Ciência de Dados de Equipe)](https://aka.ms/tdsp) para começar.


## <a name="references"></a>Referências

* [AMLPCV (Pacote do Azure Machine Learning para Pesquisa Visual Computacional)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [VM de Ciência de Dados](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

