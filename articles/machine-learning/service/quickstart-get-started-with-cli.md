---
title: Introdução ao serviço Azure Machine Learning usando a extensão CLI | Microsoft Docs
description: Neste início rápido, você aprenderá como começar com o serviço Azure Machine Learning usando a extensão de CLI do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
author: rastala
ms.author: roastala
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 856f9629e97f8cf7cf811e7d591cbcad6067f47a
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237153"
---
# <a name="quickstart-get-started-with-azure-machine-learning-using-the-cli-extension"></a>Início rápido: introdução ao Azure Machine Learning usando a extensão de CLI

Neste início rápido, você usará uma extensão da CLI de aprendizado de máquina para obter uma introdução ao [serviço Azure Machine Learning](overview-what-is-azure-ml.md) (versão prévia).

Usando a CLI, você aprenderá como:

1. Criar um workspace na assinatura do Azure. O workspace é usado por um ou mais usuários para armazenar os respectivos recursos de computação, modelos, implantações e históricos de execução na nuvem.
1. Anexar um projeto ao workspace.   Um projeto é uma pasta local que contém os scripts e arquivos de configuração necessários para solucionar um problema de aprendizado de máquina.  
1. Execute um script do Python em seu projeto que registra alguns valores em várias iterações.
1. Exiba os valores registrados no histórico de execuções de seu workspace.

> [!NOTE]
> Para sua conveniência, os seguintes recursos do Azure são adicionados automaticamente ao seu workspace quando regionalmente disponíveis: [Registro de Contêiner](https://azure.microsoft.com/services/container-registry/), [Armazenamento](https://azure.microsoft.com/services/storage/), [Application Insights](https://azure.microsoft.com/services/application-insights/) e [Key Vault](https://azure.microsoft.com/services/key-vault/).

Os recursos que você cria podem ser usados como pré-requisitos em outros tutoriais e artigos de instruções do Azure Machine Learning.

Essa CLI foi criada com base no <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> baseado em Python para o serviço Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você cumpre os seguintes pré-requisitos antes de começar as etapas do início rápido:

+ Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
+ [Python 3.5 ou posterior](https://www.python.org/) instalado
+ [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) instalada

## <a name="install-the-cli-extension"></a>Instalar a extensão de CLI

Em seu computador, abra um editor de linha de comando e instale [a extensão de aprendizado de máquina para a CLI do Azure](reference-azure-machine-learning-cli.md).  A instalação poderá levar vários minutos para ser concluída.

```azurecli
az extension add azureml-sdk
```

## <a name="install-the-sdk"></a>Instalar o SDK

[!INCLUDE [aml-install-sdk](../../../includes/aml-install-sdk.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. Usando a CLI do Azure, entre no Azure, especifique a assinatura e crie um grupo de recursos.

Em uma janela de linha de comando, entre com o comando da CLI do Azure, `az login`. Siga os prompts para o logon interativo:
    
   ```azurecli
   az login
   ```

Liste as assinaturas do Azure disponíveis e especifique aquela que você deseja usar:
   ```azurecli
   az account list --output table
   az account set --subscription <your-subscription-id>
   az account show
   ```
   em que \<id-da-sua-assinatura\> é o valor da ID da assinatura que você deseja usar. Não inclua os colchetes.

Crie um grupo de recursos para conter seu workspace.
Neste início rápido:
   + O nome do grupo de recursos é `docs-aml`.
   + A região é `eastus2`. 

   ```azurecli
   az group create -n docs-aml -l eastus2
   ```

## <a name="create-a-workspace-and-a-project-folder"></a>Criar um workspace e uma pasta de projeto

Na janela de linha de comando, crie um workspace do serviço do Azure Machine Learning no grupo de recursos.


   Neste início rápido:
   + O nome do workspace é `docs-ws`.
   + O nome do grupo de recursos é `docs-aml`

   ```azurecli
   az ml workspace create -n docs-ws -g docs-aml
   ```

Na janela de linha de comando, crie uma pasta no computador local para seu projeto do Azure Machine Learning.

   ```
   mkdir docs-prj
   cd docs-prj
   ```

## <a name="create-a-python-script"></a>Executar um script do Python

[!INCLUDE [aml-create-script-pi](../../../includes/aml-create-script-pi.md)]

## <a name="run-the-script"></a>Execute o script

Anexe a pasta como um projeto para o workspace. O argumento `--history` especifica um nome para o arquivo de histórico de execução que captura as métricas para cada execução.

   ```azurecli
   az ml project attach --history my_history -w docs-ws -g docs-aml
   ```

Execute o script no computador local.

   ```azurecli
   az ml run submit -c local pi.py
   ```

   Este comando executa o código e gera um link da Web para o console. Copie e cole o link em seu navegador da Web.

Em um navegador da Web, visite a URL. Um portal da Web é exibido com os resultados da execução. Você pode inspecionar os resultados da execução ou de execuções anteriores, caso haja alguma.

O painel do portal é compatível apenas com os navegadores Microsoft Edge, Chrome e Firefox.

   ![exibir histórico](./media/quickstart-get-started/web-results.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas
Agora você criou os recursos necessários para começar a experimentar e a implantar modelos. Você também criou um projeto, executou um script e explorou o histórico de execuções do script.

Para uma experiência de fluxo de trabalho detalhada, siga o tutorial do Azure Machine Learning para criação, treinamento e implantação de um modelo.

> [!div class="nextstepaction"]
> [Tutorial: criar, treinar e implantar](tutorial-train-models-with-aml.md)