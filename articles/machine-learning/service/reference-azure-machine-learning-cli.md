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
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: d7baa4faf718852e5bddd89f99e4ffc1248a403c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249724"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Use a extensão CLI do Serviço do Azure Machine Learning

A CLI do Azure Machine Learning é uma extensão do [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), uma interface de linha de comando de plataforma cruzada para a plataforma Azure. Esta extensão fornece comandos para trabalhar com o serviço do Machine Learning na linha de comando. Ele permite que você crie scripts que automatizam seus fluxos de trabalho de aprendizado de máquina. Por exemplo, você pode criar scripts que executam as seguintes ações:

+ Executar experimentos para criar modelos de aprendizado de máquina

+ Registrar modelos de aprendizado de máquina para uso do cliente

+ Empacote, implante e rastreie o ciclo de vida de seus modelos de aprendizado de máquina

O CLI não é um substituto para o SDK do Azure Machine Learning. É uma ferramenta complementar que é otimizada para lidar com tarefas altamente parametrizadas, como:

* Criando recursos de computação

* Submissão de experimentos parametrizados

* Registro do modelo

* Criação de imagem

* Implantação de serviço

## <a name="prerequisites"></a>Pré-requisitos


* Para usar a CLI, você deve ter uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](http://aka.ms/AMLFree) hoje mesmo.

* O [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Instalar a extensão

Para instalar a extensão CLI do Machine Learning, use o seguinte comando:

```azurecli-interactive
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-1.0.6-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

Quando solicitado, selecione `y` para instalar a extensão.

Para verificar se a extensão foi instalada, use o seguinte comando para exibir uma lista de subcomandos específicos do ML:

```azurecli-interactive
az ml -h
```

> [!TIP]
> Para atualizar a extensão, você deve __remover__ e, em seguida, __instalá-la__. Isso instala a versão mais recente.

## <a name="remove-the-extension"></a>Remover a extensão

Para remover a extensão CLI, use o seguinte comando:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Gerenciamento de recursos

Os comandos a seguir demonstram como usar a CLI para gerenciar recursos usados pelo Azure Machine Learning.


+ Crie uma área de trabalho do Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -n myworkspace -g myresourcegroup
    ```

+ Defina um espaço de trabalho padrão:

    ```azurecli-interactive
    az configure --defaults aml_workspace=myworkspace group=myresourcegroup
    ```

+ Crie um destino de computação gerenciado para treinamento distribuído:

    ```azurecli-interactive
    az ml computetarget create amlcompute -n mycompute --max_nodes 4 --size Standard_NC6
    ```

* Atualize um destino de computação gerenciado:

    ```azurecli-interactive
    az ml computetarget update --name mycompute --workspace –-group --max_nodes 4 --min_nodes 2 --idle_time 300
    ```

* Anexe um destino de computação não gerenciado para treinamento ou implantação:

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a name="experiments"></a>Testes

Os comandos a seguir demonstram como usar a CLI para trabalhar com experimentos:

* Anexe um projeto (execute a configuração) antes de enviar uma experiência:

    ```azurecli-interactive
    az ml project attach --experiment-name myhistory
    ```

* Comece uma corrida de sua experiência. Ao usar esse comando, especifique o nome do arquivo `.runconfig` que contém a configuração de execução. O destino de computação usa a configuração de execução para criar o ambiente de treinamento para o modelo. Neste exemplo, a configuração de execução é carregada do arquivo `./aml_config/myrunconfig.runconfig`.

    ```azurecli-interactive
    az ml run submit -c myrunconfig train.py
    ```

    Arquivos `.runconfig` padrão denominados `docker.runconfig` e `local.runconfig` são criados quando você anexa a um projeto usando o comando `az ml project attach`. Talvez seja necessário modificá-los antes de usá-los para treinar um modelo. 

    Você também pode criar uma configuração de execução por meio de programação usando a classe [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py). Depois de criado, você pode usar o método `save()` para criar o arquivo `.runconfig`.

* Exiba uma lista dos experimentos enviados:

    ```azurecli-interactive
    az ml history list
    ```

## <a name="model-registration-image-creation--deployment"></a>Registro de modelo, criação e implantação de imagem

Os comandos a seguir demonstram como registrar um modelo treinado e implantá-lo como um serviço de produção:

+ Registre um modelo com o Azure Machine Learning:

  ```azurecli-interactive
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ Crie uma imagem que contenha seu modelo e dependências de aprendizado de máquina: 

  ```azurecli-interactive
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ Implante uma imagem em um destino de computação:

  ```azurecli-interactive
  az ml service create aci -n myaciservice --image-id myimage:1
  ```
