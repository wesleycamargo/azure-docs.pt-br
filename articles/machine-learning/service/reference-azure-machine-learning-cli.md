---
title: Extensão de aprendizado de máquina
titleSuffix: Azure Machine Learning service
description: Aprenda sobre a extensão CLI do Azure Machine Learning para a CLI do Azure. A CLI do Azure é um utilitário de linha de comando de plataforma cruzada que permite trabalhar com recursos na nuvem do Azure. A extensão Machine Learning permite que você trabalhe com o Serviço de Aprendizado de Máquina do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 9cc6ad4f7b33de4d132efe63ff11c34f10b614af
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023378"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Use a extensão CLI do Serviço do Azure Machine Learning

A CLI do Azure Machine Learning é uma extensão do [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), uma interface de linha de comando de plataforma cruzada para a plataforma Azure. Esta extensão fornece comandos para trabalhar com o serviço do Machine Learning na linha de comando. Ele permite que você automatize os fluxos de trabalho de aprendizado de máquina. Por exemplo, você pode executar as seguintes ações:

+ Executar experimentos para criar modelos de aprendizado de máquina

+ Registrar modelos de aprendizado de máquina para uso do cliente

+ Empacote, implante e rastreie o ciclo de vida de seus modelos de aprendizado de máquina

O CLI não é um substituto para o SDK do Azure Machine Learning. É uma ferramenta complementar que é otimizada para lidar com tarefas altamente com parâmetros que se adequar às próprias bem a automação.

## <a name="prerequisites"></a>Pré-requisitos

* Para usar a CLI, você deve ter uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* O [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Instalar a extensão

Para instalar a extensão CLI do Machine Learning, use o seguinte comando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Arquivos de exemplo, você pode usar com os comandos a seguir podem ser encontrados [aqui](http://aka.ms/azml-deploy-cloud).

Quando solicitado, selecione `y` para instalar a extensão.

Para verificar se a extensão foi instalada, use o seguinte comando para exibir uma lista de subcomandos específicos do ML:

```azurecli-interactive
az ml -h
```

## <a name="remove-the-extension"></a>Remover a extensão

Para remover a extensão CLI, use o seguinte comando:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Gerenciamento de recursos

Os comandos a seguir demonstram como usar a CLI para gerenciar recursos usados pelo Azure Machine Learning.

+ Se você não tiver uma, crie um grupo de recursos:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Crie uma área de trabalho do Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

+ Anexe uma configuração de espaço de trabalho para uma pasta para habilitar o reconhecimento contextual da CLI.
    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

+ Anexe um contêiner de BLOBs do Azure como um repositório de dados.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```
    
+ Anexe a um cluster do AKS como um destino de computação.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a id="experiments"></a>Executar testes

* Comece uma corrida de sua experiência. Ao usar esse comando, especifique o nome do arquivo runconfig (o texto antes \*. runconfig se você estiver pensando em seu sistema de arquivos) com o parâmetro - c.

    ```azurecli-interactive
    az ml run submit-script -c local -e testexperiment train.py
    ```

* Exiba uma lista de testes:

    ```azurecli-interactive
    az ml experiment list
    ```

## <a name="model-registration-profiling--deployment"></a>Registro de modelo, a criação de perfil e a implantação

Os comandos a seguir demonstram como registrar um modelo treinado e implantá-lo como um serviço de produção:

+ Registre um modelo com o Azure Machine Learning:

  ```azurecli-interactive
  az ml model register -n mymodel -p sklearn_regression_model.pkl
  ```

+ Implantar o modelo para o AKS

  ```azurecli-interactive
  az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
  ```
