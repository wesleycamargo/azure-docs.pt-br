---
title: Instale e use a CLI para as principais tarefas — Azure Machine Learning
description: Saiba como a instalar e usar a CLI para as tarefas de aprendizado de máquina mais comuns no Azure Machine Learning.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2018
ms.openlocfilehash: 0e37e1839d2248507a30de08e2ac4c975bd3b859
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "35630577"
---
# <a name="install-and-use-the-machine-learning-cli-for-top-tasks-in-azure-machine-learning"></a>Instalar e usar a CLI de aprendizado de máquina para as principais tarefas no Azure Machine Learning

Os serviços do Azure Machine Learning são uma solução integrada de análise avançada e de ciência de dados de ponta a ponta. Os cientistas de dados profissionais podem usar os serviços do Azure Machine Learning para preparar dados, desenvolver experimentos e implantar modelos em escala de nuvem. 

O Azure Machine Learning fornece uma CLI (interface de linha de comando) com a qual é possível:
+ Gerenciar projetos e espaço de trabalho
+ Configurar destinos de computação
+ Executar testes de treinamento
+ Exibir métricas e histórico de execuções anteriores
+ Implantar modelos em produção como serviços Web
+ Gerenciar serviços e modelos de produção

Este artigo apresenta alguns dos comandos CLI mais úteis para sua conveniência. 

![CLI do Azure Machine Learning](media/cli-for-azure-machine-learning/flow.png)

## <a name="what-you-need-to-get-started"></a>Para começar, você precisa do seguinte:

Você precisa ter acesso de colaborador para uma assinatura do Azure ou um grupo de recursos em que é possível implantar seus modelos. Além disso, você precisa instalar o Azure Machine Learning Workbench para executar a CLI. 

>[!IMPORTANT]
>A CLI entregue com os serviços do Azure Machine Learning é diferente da [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) que é usada para gerenciar recursos do Azure.

## <a name="get-and-start-cli"></a>Obter e iniciar a CLI

Para obter essa CLI, instale o Azure Machine Learning Workbench que pode ser baixado aqui:
    + Windows - https://aka.ms/azureml-wb-msi 
    + MacOS - https://aka.ms/azureml-wb-dmg 

Para iniciar a CLI:
+ No Azure Machine Learning Workbench, inicie a CLI a partir do menu **Arquivo -> Abrir Prompt de Comando.**

## <a name="get-command-help"></a>Obter ajuda de comando 

É possível obter informações adicionais sobre os comandos da CLI utilizando `--debug` ou `--help` após os comandos, como `az ml <xyz> --debug` em que `<xyz>` é o nome do comando. Por exemplo: 
```azurecli
az ml computetarget --debug 

az ml experiment --help
```

## <a name="common-cli-tasks-for-azure-machine-learning"></a>Tarefas comuns da CLI do Azure Machine Learning 

Nesta seção, saiba mais sobre as tarefas mais comuns que podem ser executadas com a CLI, incluindo:
+ [Configurar destinos de computação](#target)
+ [Enviar trabalhos para execução remota](#jobs)
+ [Trabalhar com notebook Jupyter](#jupyter)
+ [Interagir com históricos de execuções](#history)
+ [Configurar o ambiente para operar](#o16n)

<a name="target"></a>

### <a name="set-up-a-compute-target"></a>Configurar um destino de computação

É possível calcular o modelo de aprendizado de máquina em destinos diferentes, incluindo:
+ em modo local
+ em uma DSVM (VM do Data Science)
+ em um Cluster HDInsight

Para anexar um destino da VM do Data Science:
```azurecli
az ml computetarget attach remotedocker -n <target name> -a <ip address or FQDN> -u <username> -w <password>
``` 

Para anexar um destino do HDInsight:
```azurecli
az ml computetarget attach cluster -n <target name> -a <cluster name, e.g. myhdicluster-ssh.azurehdinsight.net> -u <ssh username> -w <ssh password>
```

Na pasta **aml_config** é possível alterar as dependências do conda. 

Além disso, é possível operar com PySpark, Python ou Python em DSVM de GPU. 

Para definir o modo de operação do Python:
+ Para Python, adicione `Framework:Python` em `<target name>.runconfig` 

+ Para PySpark, adicione `Framework:PySpark` em `<target name>.runconfig` 

+ Para Python em DSVM de GPU,
    1. Adicione `Framework:Python` em `<target name>.runconfig` 

    1. Além disso, adicione `baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9 and nvidiaDocker:true` em `<target name>.compute`

Para preparar o destino de computação:
```azurecli
az ml experiment prepare -c <target name>
```

>[!TIP]
>Para mostrar a assinatura:<br/>
>`az account show`<br/>
>
>Para configurar a assinatura:<br/>
>`az account set –s "my subscription name" `

<a name="jobs"></a>

### <a name="submit-remote-jobs"></a>Enviar trabalhos remotos

Para enviar um trabalho para um destino remoto:
```azurecli
az ml experiment submit -c <target name> myscript.py
```

<a name="jupyter"></a>

### <a name="work-with-jupyter-notebooks"></a>Trabalhar com notebooks Jupyter

Para iniciar um notebook Jupyter:
```azurecli
az ml notebook start
```

Esse comando inicia um notebook Jupyter no host local. É possível trabalhar em local selecionando o kernel Python 3 ou trabalhar na VM remota, selecionando o kernel `<target name>`.

<a name="history"></a>

### <a name="interact-with-and-explore-the-run-history"></a>Interagir com e explorar o histórico de execuções

A lista do histórico de execuções:
```azurecli
az ml history list -o table
```

Para listar todas as execuções concluídas:
```azurecli
az ml history list --query "[?status=='Completed']" -o table
```

Para localizar execuções com a melhor precisão:
```azurecli
az ml history list --query "@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy"
```

Também é possível baixar os arquivos gerados por cada execução. 

Para elevar um modelo salvo nas saídas da pasta:
```azurecli
az ml history promote -r <run id> -ap outputs/model.pkl -n <model name>
```

Para baixar esse modelo:
```azurecli
az ml asset download -l assets/model.pkl.link -d <model folder path>
```

<a name="o16n"></a>

### <a name="configure-your-environment-to-operationalize"></a>Configurar o ambiente para operar

Para configurar o ambiente de operação, é necessário criar:

> [!div class="checklist"]
> * Um grupo de recursos 
> * Uma conta de armazenamento
> * Um ACR (Registro de Contêiner do Azure)
> * Uma conta do Application Insights
> * Uma implantação do Kubernetes em um cluster do ACS (Serviço de Contêiner do Azure)


Para configurar uma implantação local para testar em um contêiner do Docker:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name>
```

Para configurar um cluster do ACS com Kubernetes:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name> --cluster
```

Para monitorar o status da implantação:
```azurecli
az ml env show -n <environment name> -g <resource group name>
```

Para definir o ambiente que deve ser utilizado:
```azurecli
az ml env set -n <environment name> -g <resource group name>
```

## <a name="next-steps"></a>Próximas etapas

Introduzir com um desses artigos: 
+ [Instalar e iniciar usando Azure Machine Learning](../service/quickstart-installation.md)
+ [Tutorial de classificação de dados Iris: Parte 1](tutorial-classifying-iris-part-1.md)

Obter detalhes com um destes artigos:
+ [Comando CLI para gerenciar modelos](model-management-cli-reference.md)
