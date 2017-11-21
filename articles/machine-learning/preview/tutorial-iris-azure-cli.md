---
title: "Artigo de tutorial para recursos de versão prévia do Azure Machine Learning – interface de linha de comando | Microsoft Docs"
description: "Este tutorial percorre todas as etapas necessárias para concluir uma classificação de íris de ponta a ponta usando a interface de linha de comando."
services: machine-learning
author: ahgyger
ms.author: ahgyger, ritbhat
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 8ffa1c6dd4794cbea6b2c1904e08557ba2a68ba2
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2017
---
# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>Tutorial: classificando a íris usando a interface de linha de comando
Os serviços do Azure Machine Learning (versão prévia) são uma solução integrada de análise avançada e de ciência de dados de ponta a ponta para cientistas de dados profissionais prepararem dados, desenvolverem testes e implantarem modelos em escala de nuvem.

Neste tutorial, você aprenderá a usar as ferramentas da CLI (interface de linha de comando) nos recursos de versão prévia do Azure Machine Learning para: 
> [!div class="checklist"]
> * Configurar uma conta de experimentação e criar um espaço de trabalho
> * Criar um projeto
> * Enviar um teste para vários destinos de computação
> * Promover e registrar um modelo treinado
> * Implantar um serviço Web para pontuar novos dados

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
- Você precisa ter acesso a uma assinatura do Azure e permissões para criar recursos nessa assinatura. 
- Você precisa instalar o aplicativo Azure Machine Learing Workbench seguindo o [Guia de início rápido para instalação e criação](quickstart-installation.md). 

  >[!NOTE]
  >Você só precisa instalar o Azure Machine Learning Workbench localmente. Basta seguir as etapas nas seções intituladas Instalar o Azure Machine Learning Workbench, pois as etapas para criar a conta e criar um novo projeto serão executadas pela linha de comando neste artigo.
 
## <a name="getting-started"></a>Introdução
A CLI (interface de linha de comando) do Azure Machine Learning permite que você execute todas as tarefas necessárias para um fluxo de trabalho de ciência de dados de ponta a ponta. Você pode acessar as ferramentas da CLI das seguintes maneiras:

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>Opção 1. Inicie a CLI do Azure ML por meio da caixa de diálogo de logon do Azure ML Workbench
Quando você iniciar o Azure ML Workbench e fizer logon pela primeira vez, e se você ainda não tiver acesso a uma conta de experimentação, será exibida a tela a seguir:

![nenhuma conta encontrada](media/tutorial-iris-azure-cli/no_account_found.png)

Clique no link **Janela de linha de comando** na caixa de diálogo para iniciar a janela de linha de comando.

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>Opção 2. Inicie a CLI do Azure ML por meio do aplicativo Azure ML Workbench
Se você já tiver acesso a uma conta de experimentação, será possível fazer logon com êxito. E, em seguida, você poderá abrir a janela de linha de comando clicando no menu **Arquivo** --> **Abrir prompt de comando**.

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>Opção 3. habilitar a CLI do Azure ML em uma janela de linha de comando qualquer
Você também pode habilitar a CLI do Azure ML em qualquer janela de linha de comando. Simplesmente inicie uma janela de comando e digite os seguintes comandos:

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
Para que a alteração se torne permanente, você pode usar `SETX` no Windows. Para macOS, você pode usar `setenv`.

>[!TIP]
>Você pode habilitar a CLI do Azure na sua janela de terminal favorita, definindo as variáveis de ambiente acima.

## <a name="step-1-log-in-to-azure"></a>Etapa 1. Fazer logon no Azure
A primeira etapa é abrir a CLI por meio do aplicativo AMLWorkbench (Arquivo > Abrir prompt de comando). Isso garante que o ambiente Python certo seja usado e que os comandos da CLI do ML estejam disponíveis. 

Em seguida, precisamos definir o contexto certo em sua CLI para acessar e gerenciar recursos do Azure.
 
```azure-cli
# log in
$ az login

# list all subscriptions
$ az account list -o table

# set the current subscription
$ az account set -s <subscription id or name>
```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>Etapa 2. Criar uma nova conta de experimentação do Azure Machine Learning e um novo espaço de trabalho
Vamos começar criando uma nova conta de experimentação e um novo espaço de trabalho. Consulte [Azure Machine Learning concepts](overview-general-concepts.md) (Conceitos do Azure Machine Learning) para obter mais detalhes sobre as contas de experimentação e os espaços de trabalho.

> [!NOTE]
> As contas de experimentação exigem uma conta de armazenamento, que é usada para armazenar as saídas de execução de teste. O nome da conta de armazenamento deve ser exclusivo globalmente no Azure, porque há uma URL associada a ele. Se você não especificar uma conta de armazenamento existente, o nome da conta experimentação será usado para criar uma nova conta de armazenamento. Use um nome exclusivo ou você receberá um erro como _"A conta de armazenamento chamada \<nome_da_conta_de_armazenamento> está em uso"._ Como alternativa, você pode usar o argumento `--storage` para informar uma conta de armazenamento existente.

```azure-cli
# create a resource group 
$ az group create --name <resource group name> --location <supported Azure region>

# create a new experimentation account with a new storage account
$ az ml account experimentation create --name <experimentation account name> --resource-group <resource group name>

# create a new experimentation account with an existing storage account
$ az ml account experimentation create --name <experimentation account name>  --resource-group <resource group name> --storage <storage account Azure Resource ID>

# create a workspace in the experimentation account
az ml workspace create --name <workspace name> --account <experimentation account name> --resource-group <resource group name>
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>Etapa 2.a (opcional) Compartilhar um espaço de trabalho com um colega de trabalho
Aqui, exploraremos como compartilhar o acesso a um espaço de trabalho com um colega de trabalho. As etapas para compartilhar o acesso a uma conta de experimentação ou a um projeto serão as mesmas. Apenas a forma de obter a ID de recurso do Azure precisará ser atualizada.

```azure-cli
# find the workspace Azure Resource ID
$az ml workspace show --name <workspace name> --account <experimentation account name> --resource-group <resource group name>

# add Bob to this workspace as a new owner
$az role assignment create --assignee bob@contoso.com --role owner --scope <workspace Azure Resource ID>
```

> [!TIP]
> `bob@contoso.com` no comando acima deve ser uma identidade do Azure AD válida no diretório ao qual a assinatura atual pertence.

## <a name="step-3-create-a-new-project"></a>Etapa 3. Criar um novo projeto
Nossa próxima etapa será criar um novo projeto. Há várias maneiras de começar um novo projeto.

### <a name="create-a-new-blank-project"></a>Criar um novo projeto em branco

```azure-cli
# create a new project
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path>
```

### <a name="create-a-new-project-with-a-default-project-template"></a>Criar um novo projeto com um modelo de projeto padrão
Você pode criar um novo projeto com um modelo padrão.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>Criar um novo projeto associado a um repositório Git de nuvem
Podemos criar um novo projeto associado a um repositório Git do VSTS (Visual Studio Team Service). Sempre que um experimento é enviado, um instantâneo de toda a pasta do projeto é confirmado no repositório Git remoto. Consulte [Using Git repository with an Azure Machine Learning Workbench project](using-git-ml-project.md) (Usando o repositório Git com um projeto do Azure Machine Learning Workbench) para obter mais detalhes.

> [!NOTE]
> O Azure Machine Learning tem suporte apenas aos repositórios Git vazios criados no VSTS.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --repo <VSTS repo URL>
```
> [!TIP]
> Se estiver recebendo um erro "A URL do repositório pode ser inválida ou o usuário pode não ter acesso", você poderá criar um token de segurança no VSTS (no menu _Segurança_, _Adicionar tokens de acesso pessoal_) e usar o argumento `--vststoken` ao criar seu projeto. 

### <a name="sample_create"></a>Criar um novo projeto de uma amostra
Neste exemplo, criamos um novo projeto usando um projeto de exemplo como modelo.

```azure-cli
# List the project samples, find the Classifying Iris sample
$ az ml project sample list

# Create a new project from the sample
az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```
Quando o projeto é criado, use o comando `cd` para inserir diretório do projeto.

## <a name="step-4-run-the-training-experiment"></a>Etapa 4 Executar o teste de treinamento 
As etapas a seguir consideram que você tem um projeto com o exemplo de íris (consulte [Criar um novo projeto usando um exemplo online](#sample_create)).

### <a name="prepare-your-environment"></a>Prepare o seu ambiente 
Para o exemplo de íris, precisamos instalar matplotlib.

```azure-cli
$ pip install matplotlib
```

###  <a name="submit-the-experiment"></a>Enviar o teste

```azure-cli
# Execute the file
$ az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>Iterar no teste com decrescente taxas de regularização descendentes
Com um pouco de criatividade é simples criar um script Python que envie experimentos com taxas de regularização diferentes. (Talvez seja necessário editar o arquivo para apontar para o caminho do projeto correto).

```azure-cli
$ python run.py
```

## <a name="step-5-view-run-history"></a>Etapa 5. Exibir histórico de execuções
O comando a seguir lista todas as execuções anteriores realizadas. 

```azure-cli
$ az ml history list -o table
```
Executar o comando acima exibirá uma lista de todas as execuções que pertencem a este projeto. Veja que as métricas de taxa de precisão e de regularização também são listadas. Isso facilita a identificação da melhor execução na lista.

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>Etapa 5.a Exibir o anexo criado por uma determinada execução 
Para exibir o anexo associado a uma determinada execução, podemos usar o comando de informações do histórico de execução. Localize uma ID de execução de uma execução específica na lista acima.

```azure-cli
$ az ml history info --run <run id> --artifact driver_log
```

Para baixar os artefatos de uma execução, você pode usar o comando abaixo:

```azure-cli
# Stream a given attachment 
$ az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>Etapa 6. Promover os artefatos de uma execução 
Uma das execuções que fizemos tem um AUC melhor, então vamos usá-la para criar um serviço Web de pontuação a ser implantado na produção. Para fazer isso, primeiro é necessário promover os artefatos em um ativo.

```azure-cli
$ az ml history promote --run <run id> --artifact-path outputs/model.pkl --name model.pkl
```

Isso cria uma pasta `assets` no diretório do projeto com um arquivo `model.pkl.link`. Esse arquivo de link é usado para fazer referência a um ativo promovido.

## <a name="step-7-download-the-files-to-be-operationalized"></a>Etapa 7. Baixar os arquivos a serem operacionalizados
Agora precisamos baixar o modelo promovido para que possamos usá-lo para criar nosso serviço Web de previsão. 

```azure-cli
$ az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-setup-your-model-management-environment"></a>Etapa 8. Configurar o ambiente de gerenciamento de modelos 
É possível criar um ambiente para implantar os serviços Web. Podemos executar o serviço Web no computador local usando o Docker. Ou implantá-lo em um cluster do ACS para operações de grande escala. 

```azure-cli
# Create new local operationalization environment
$ az ml env setup -l <supported Azure region> -n <env name>
# Once setup is complete, set your environment for current context
$ az ml env set -g <resource group name> -n <env name>
```

## <a name="step-9-create-a-model-management-account"></a>Etapa 9. Criar uma conta de gerenciamento de modelos 
Uma conta de gerenciamento de modelos é necessária para implantar e acompanhar os modelos em produção. 

```azure-cli
$ az ml account modelmanagement create -n <model management account name> -g <resource group name> -l <supported Azure region>
```

## <a name="step-10-create-a-web-service"></a>Etapa 10. Criar um serviço Web
Em seguida, criamos um serviço Web que retorna uma previsão usando o modelo implantado. 

```azure-cli
$ az ml service create realtime -m asset_download/model.pkl -f score_iris.py -r python –n <web service name>
```

## <a name="step-10-run-the-web-service"></a>Etapa 10. Executar o serviço Web
Usando a ID do serviço Web da saída da etapa anterior, podemos chamar o serviço Web e testá-lo. 

```azure-cli
# Get web service usage infomration
$ az ml service usage realtime -i <web service id>

# Call the web service with the run command:
$ az ml service run realtime -i <web service id> -d <input data>
```

## <a name="deleting-all-the-resources"></a>Excluindo todos os recursos 
Vamos concluir este tutorial excluindo todos os recursos que criamos, a não ser que você deseje continuar trabalhando neles! 

Para fazer isso, basta excluir o grupo de recursos que contém todos os nossos recursos. 

```azure-cli
az group delete --name <resource group name>
```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a usar os recursos de versão prévia do Azure Machine Learning para 
> [!div class="checklist"]
> * Configurar uma conta de experimentação e criar um espaço de trabalho
> * Criar projetos
> * Enviar testes para vários destino de computação
> * Promover e registrar um modelo treinado
> * Criar uma conta de gerenciamento de modelos para gerenciamento de modelos
> * Criar um ambiente para implantar um serviço Web
> * Implantar um serviço Web e pontuar com novos dados
