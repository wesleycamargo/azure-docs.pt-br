---
title: Personalizar a imagem de Contêiner usada para implantar os Modelos de ML do Azure | Microsoft Docs
description: Este artigo descreve como personalizar uma imagem de contêiner para os modelos do Azure Machine Learning
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: mldocs, raymondl
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 03/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: e49743de817bc1fe92afcbc80764771f6df66c56
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271249"
---
# <a name="customize-the-container-image-used-for-azure-ml-models"></a>Personalizar a imagem de Contêiner usada para implantar os Modelos de ML do Azure

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Este artigo descreve como personalizar uma imagem de contêiner para os modelos do Azure Machine Learning.  O Workbench de ML do Azure usa contêineres para a implantação de modelos de aprendizado de máquina. Os modelos são implantados juntamente com suas dependências e o ML do Azure cria uma imagem de modelo, as dependências e os arquivos associados.

## <a name="how-to-customize-the-docker-image"></a>Como personalizar a imagem do Docker
Personalize a imagem do Docker que o ML do Azure implanta usando:

1. Um arquivo `dependencies.yml`: para gerenciar as dependências que podem ser instaladas de [PyPi]( https://pypi.python.org/pypi), utilize o arquivo `conda_dependencies.yml` do projeto do Workbench ou crie seu próprio. Essa é a abordagem recomendada para a instalação de dependências do Python que podem ser instaladas com pip.

   Exemplo de comando CLI do Azure:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> -c amlconfig\conda_dependencies.yml
   ```

   Exemplo de arquivo conda_dependencies: 
   ```yaml
   name: project_environment
   dependencies:
      - python=3.5.2
      - scikit-learn
      - ipykernel=4.6.1
      
      - pip:
        - azure-ml-api-sdk==0.1.0a11
        - matplotlib
   ```
        
2. Um arquivo de etapas do Docker: usando essa opção, personalizar a imagem implantada instalando dependências que não podem ser instaladas do PyPi. 

   O arquivo deve incluir etapas de instalação do Docker como um DockerFile. Todos os comandos são permitidos no arquivo: 

    EXECUTAR, ENV, ARG, ROTULAR, EXPOR

   Exemplo de comando CLI do Azure:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> --docker-file <myDockerStepsFileName> 
   ```

   Comandos de Imagem, Manifesto e Serviço aceitam o sinalizador do arquivo do docker.

   Exemplo de arquivo das etapas do Docker:
   ```docker
   # Install tools required to build the project
   RUN apt-get update && apt-get install -y git libxml2-dev
   # Install library dependencies
   RUN dep ensure -vendor-only
   ```

> [!NOTE]
> A imagem base para contêineres do ML do Azure é Ubuntu e não pode ser alterada. Se você especificar uma imagem de base diferente, ela será ignorada.

## <a name="next-steps"></a>Próximas etapas
Agora que você personalizou sua imagem de contêiner, você pode implantá-la a um cluster para uso em larga escala.  Para obter detalhes sobre como configurar um cluster para implantação de serviço Web, consulte [Configuração de Gerenciamento de Modelos](deployment-setup-configuration.md). 
