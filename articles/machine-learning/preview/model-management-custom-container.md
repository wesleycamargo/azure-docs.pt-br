---
title: Personalizar a imagem de Contêiner usada para implantar os Modelos de ML do Azure | Microsoft Docs
description: Este artigo descreve como personalizar uma imagem de contêiner para os modelos do Azure Machine Learning
services: machine-learning
author: tedway
ms.author: tedway, raymondl
manager: mwinkle
ms.reviewer: mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 3/26/2018
ms.openlocfilehash: 858c8933565aeeb22dc1b685082dab2c6481737b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="customize-the-container-image-used-for-azure-ml-models"></a>Personalizar a imagem de Contêiner usada para implantar os Modelos de ML do Azure

Este artigo descreve como personalizar uma imagem de contêiner para os modelos do Azure Machine Learning.  O Workbench de ML do Azure usa contêineres para a implantação de modelos de aprendizado de máquina. Os modelos são implantados juntamente com suas dependências e o ML do Azure cria uma imagem de modelo, as dependências e os arquivos associados.

## <a name="how-to-customize-the-docker-image"></a>Como personalizar a imagem do Docker
Personalize a imagem do Docker que o ML do Azure implanta usando:

1. Um arquivo depenencies.yml: para gerenciar as dependências que podem ser instaladas de [PyPi]( https://pypi.python.org/pypi), você pode usar o arquivo conda_dependencies.yml do projeto do Workbench ou criar seus próprios. Essa é a abordagem recomendada para a instalação de dependências do Python que podem ser instaladas com pip.

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
