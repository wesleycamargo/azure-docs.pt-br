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
ms.openlocfilehash: 53e737f35904a90bb56ec15c8a8282f8775e3c3a
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49393484"
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
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-0.1.68-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

É possível remover a CLI usando este comando:
```AzureCLI
az extension remove -n azure-cli-ml
```

Você pode atualizar a CLI usando as etapas **remover** e **adicionar** acima.

## <a name="using-the-cli-vs-the-sdk"></a>Usar a CLI vs. o SDK
A CLI é mais adequada para automação por uma persona de dev-ops ou como parte de um pipeline de entrega e integração contínua. É otimizada para lidar com tarefas pouco frequentes e altamente parametrizadas. 

Os exemplos incluem:
- provisionamento de computação
- envio de experimento parametrizado
- registro de modelo, criação de imagem
- implantação de serviços

É recomendável aos cientistas de dados usar o SDK do Azure ML.

## <a name="common-machine-learning-cli-commands"></a>Comandos comuns da CLI de aprendizado de máquina
> [!NOTE]
> Os exemplos de arquivos que você pode usar para executar com êxito os comandos a seguir podem ser encontrados [aqui.](https://github.com/Azure/MachineLearningNotebooks/tree/cli/cli)

Utilize o conjunto de comandos `az ml` avançado para interagir com o serviço em qualquer ambiente de linha de comando, incluindo o Cloud Shell do portal do Azure.

A seguir, um exemplo de comandos comuns:

### <a name="workspace-creation--compute-setup"></a>Criação de workspace e configuração de computação

+ Crie um workspace do serviço do Azure Machine Learning, o recurso de nível superior para aprendizado de máquina.
   ```AzureCLI
   az ml workspace create -n myworkspace -g myresourcegroup
   ```

+ Defina a CLI para usar esse workspace por padrão.
   ```AzureCLI
   az configure --defaults aml_workspace=myworkspace group=myresourcegroup
   ```

+ Criar uma DSVM (VM de Ciência de Dados). Você também pode criar clusters da BatchAI para treinamento distribuído ou clusters do AKS para implantação.
  ```AzureCLI
  az ml computetarget setup dsvm -n mydsvm
  ```

### <a name="experiment-submission"></a>Envio de experimento
+ Anexe a um projeto (configuração de execução) para enviar um experimento. Isso é usado para acompanhar as execuções do experimento.
  ```AzureCLI
  az ml project attach --experiment-name myhistory
  ```

+ Faça um teste no serviço Azure Machine Learning no destino de computação da sua escolha. Este exemplo será executado em seu ambiente de computação local. Verifique se o arquivo de ambiente do Conda captura as dependências do Python.

  ```AzureCLI
  az ml run submit -c local train.py
  ```

+ Exiba uma lista de experimentos enviados.
```AzureCLI
az ml history list
```

### <a name="model-registration-image-ceation--deployment"></a>Registro de modelo, criação de imagem e implantação

+ Registre um modelo com Azure Machine Learning.
  ```AzureCLI
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ Crie uma imagem para conter o modelo de machine learning e dependências. 
  ```AzureCLI
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ Implante o modelo empacotado em destinos, incluindo ACI e AKS.
  ```AzureCLI
  az ml service create aci -n myaciservice --image-id myimage:1
  ```
    
## <a name="full-command-list"></a>Lista de comandos completa
É possível localizar a lista completa de comandos da extensão de CLI (e os parâmetros com suporte) executando ```az ml COMMANDNAME -h```. 
