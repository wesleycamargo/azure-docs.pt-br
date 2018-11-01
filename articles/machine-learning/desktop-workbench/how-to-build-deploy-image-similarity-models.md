---
title: Criar e implantar um modelo de similaridade de imagem usando o Pacote do Azure Machine Learning para Pesquisa Visual Computacional.
description: Saiba como criar, treinar, testar e implantar um modelo de similaridade de imagem de pesquisa visual computacional usando o Pacote do Azure Machine Learning para Pesquisa Visual Computacional.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 05/20/2018
ROBOTS: NOINDEX
ms.openlocfilehash: b1b4e3e19adb38a69e16aaa98300972df3bb71a8
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093642"
---
# <a name="build-and-deploy-image-similarity-models-with-azure-machine-learning"></a>Criar e implantar modelos de similaridade de imagem com o Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


Neste artigo, saiba como usar o AMLPCV (Pacote do Azure Machine Learning para Pesquisa Visual Computacional) para treinar, testar e implantar um modelo de similaridade de imagem. Para uma visão geral desse pacote e sua documentação de referência detalhada, veja https://aka.ms/aml-packages/vision.

Vários problemas no domínio da pesquisa visual computacional podem ser resolvidos por meio da similaridade de imagem. Esses problemas incluem a criação de modelos que respondem a perguntas como:
+ _Um OBJETO está presente na imagem? Por exemplo, "cachorro", "carro", "barco" e assim por diante_
+ _Qual classe de gravidade de doença ocular é comprovada pelo exame de retina desse paciente?_

Ao compilar e implantar esse modelo com o AMLPCV, percorra as etapas abaixo:
1. Criação do conjunto de dados
2. Visualização e anotação de imagem
3. Aumento de imagem
4. Definição de modelo de DNN (Rede Neural Profunda)
5. Treinamento do classificador
6. Avaliação e visualização
7. Implantação do serviço Web
8. Teste de carga do serviço Web

O [CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) é usado como a estrutura de aprendizado profundo, o treinamento é realizado localmente em um computador com GPU, como a ([VM de ciência de dados de aprendizado profundo](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)) e a implantação usa a CLI de operacionalização do Azure ML.

## <a name="prerequisites"></a>Pré-requisitos

1. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

1. As contas e os aplicativos abaixo devem ser configurados e instalados:
   - Uma conta de Experimentação do Azure Machine Learning 
   - Conta do Gerenciamento de Modelos do Azure Machine Learning
   - O Azure Machine Learning Workbench instalado

   Se esses três ainda não foram criados ou instalados, siga o artigo [Instalação do Início Rápido e do Azure Machine Learning Workbench](../desktop-workbench/quickstart-installation.md). 

1. O Pacote do Azure Machine Learning para Pesquisa Visual Computacional deve estar instalado. Saiba como instalar este pacote conforme descrito em https://aka.ms/aml-packages/vision.

## <a name="sample-data-and-notebook"></a>Bloco de anotações e dados de exemplo

### <a name="get-the-jupyter-notebook"></a>Obter o Jupyter Notebook

Baixe do bloco de notas para executar o exemplo descrito aqui por conta própria.

> [!div class="nextstepaction"]
> [Obter o Jupyter Notebook](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>Carregar os dados de exemplo

O exemplo a seguir usa um conjunto de dados que consiste em 63 imagens de utensílios de mesa. Cada imagem é rotulada como pertencente a uma de quatro classes (tigela, copo, talher, prato). O número de imagens neste exemplo é pequeno para que ele possa ser executado rapidamente. Na prática, pelo menos cem imagens por classe devem ser fornecidas. Todas as imagens estão localizadas em *"../sample_data/imgs_recycling/"*, em subdiretórios chamados"tigela","copo","talher"e"prato".

![Conjunto de dados do Azure Machine Learning](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o Pacote do Azure Machine Learning para Pesquisa Visual Computacional nestes artigos:

+ Saiba como [melhorar a precisão desse modelo](how-to-improve-accuracy-for-computer-vision-models.md).

+ Leia as [visão geral do pacote](https://aka.ms/aml-packages/vision).

+ Explore a [documentação de referência](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) do pacote.

+ Saiba mais sobre [outros pacotes do Python para o Azure Machine Learning](reference-python-package-overview.md).
