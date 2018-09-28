---
title: Sobre a extensão de CLI do Microsoft Azure Machine Learning
description: Saiba mais sobre a extensão de CLI de aprendizado de máquina do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 09/24/2018
ms.openlocfilehash: 5d14373b265ea30d235cc5bc7b87ee13c4fd8105
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991786"
---
# <a name="what-is-the-azure-machine-learning-cli"></a>O que é a CLI do Azure Machine Learning?

A extensão de CLI (Interface de Linha de Comando) do Azure Machine Learning é para desenvolvedores e cientistas de dados que trabalham com o serviço do Azure Machine Learning. Ele permite automatizar rapidamente os fluxos de trabalho de aprendizado de máquina e colocá-los em produção, como:
+ Executar experimentos para criar modelos de aprendizado de máquina

+ Registrar modelos de aprendizado de máquina para uso do cliente

+ Empacote, implantar e acompanhar o ciclo de vida dos modelos de machine learning

Essa CLI de aprendizado de máquina é uma extensão da [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) e foi criada sobre o <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> com base no Python do serviço do Azure Machine Learning.

> [!NOTE]
> Atualmente, a CLI está em versão prévia e será atualizada.

## <a name="installing-and-uninstalling"></a>Instalar e desinstalar

É possível instalar a CLI usando este comando no índice do PyPi de versão prévia:
```AzureCLI
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-0.1.50-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

É possível remover a CLI usando este comando:
```AzureCLI
az extension remove -n azure-cli-ml
```

## <a name="using-the-cli-vs-the-sdk"></a>Usar a CLI vs. o SDK
A CLI é mais adequada para automação por uma persona de dev-ops ou como parte de um pipeline de entrega e integração contínua. É otimizada para lidar com tarefas pouco frequentes e altamente parametrizadas. 

Os exemplos incluem:
- provisionamento de computação
- envio de experimento parametrizado
- registro de modelo, criação de imagem
- implantação de serviços

É recomendável aos cientistas de dados usar o SDK do Azure ML.

## <a name="common-machine-learning-cli-commands"></a>Comandos comuns da CLI de aprendizado de máquina

Utilize o conjunto de comandos `az ml` avançado para interagir com o serviço em qualquer ambiente de linha de comando, incluindo o Cloud Shell do portal do Azure.

A seguir, um exemplo de comandos comuns:

### <a name="workspace-creation--compute-setup"></a>Criação de espaço de trabalho e configuração de computação

+ Crie um Workspace do Azure Machine Learning, o recurso de nível superior para aprendizado de máquina.
  ```AzureCLI
  az ml workspace create -n myworkspace -g myresourcegroup
  ```

+ Defina a CLI para usar esse espaço de trabalho por padrão.
```AzureCLI
az configure --defaults aml_workspace=myworkspace group=myresourcegroup
```

+ Crie uma DSVM (VM de Ciência de Dados) para modelos de treinamento. Também é possível criar clusters BatchAI para treinamento distribuído.
  ```AzureCLI
  az ml computetarget setup dsvm -n mydsvm
  ```

### <a name="experiment-submission"></a>Envio de experimento
+ Anexe a um projeto (configuração de execução) para enviar um experimento. Isso é usado para acompanhar as execuções do experimento.
  ```AzureCLI
  az ml project attach --experiment-name myhistory
  ```

+ Envie um experimento no serviço do Azure Machine Learning no destino de computação de sua escolha (este exemplo usa uma Máquina Virtual de Ciência de Dados)
  ```AzureCLI
  az ml run submit -c mydsvm train.py
  ```

+ Exiba uma lista de experimentos enviados.
```AzureCLI
az ml history list
```

### <a name="model-registration-image-ceation--deployment"></a>Registro de modelo, criação de imagem e implantação

+ Registre um modelo com Azure Machine Learning.
  ```AzureCLI
  az ml model register -n mymodel -m mymodel.pkl  -w myworkspace -g myresourcegroup
  ```

+ Crie uma imagem para conter o modelo de machine learning e dependências. 
  ```AzureCLI
  az ml image create -n myimage -r python -m rfmodel.pkl -f score.py -c myenv.yml
  ```

+ Implante o modelo empacotado em destinos, incluindo ACI e AKS.
  ```AzureCLI
  az ml service create aci -n myaciservice -i myimage:1
  ```
    
## <a name="full-command-list"></a>Lista de comandos completa
É possível localizar a lista completa de comandos da extensão de CLI (e os parâmetros com suporte) executando ```az ml COMMANDNAME -h```. 
