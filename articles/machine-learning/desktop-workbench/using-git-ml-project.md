---
title: Usar um repositório Git com um projeto do Azure Machine Learning Workbench | Microsoft Docs
description: Este artigo explica como usar um repositório Git em conjunto com um projeto do Azure Machine Learning Workbench.
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 16c102641321117f4776d761aba6c2148d15f1f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995629"
---
# <a name="use-a-git-repo-with-a-machine-learning-workbench-project"></a>Usar um repositório Git com um projeto do Machine Learning Workbench

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Saiba mais sobre como o Azure Machine Learning Workbench usa o Git para fornecer o controle de versão e garantir reprodutibilidade em sua experiência de ciência de dados. Saiba mais sobre como associar seu projeto com um repositório Git de nuvem (repositório).

O Workbench de Machine Learning foi projetado para integração com o Git. Ao criar um novo projeto, a pasta do projeto é automaticamente "inicializada por Git" como um repositório de Git local. Um segundo repositório de Git local e oculto também é criado, com um branch denominado AzureMLHistory /\<project GUID\>. O branch mantém o controle de alterações da pasta do projeto para cada execução. 

Associar o projeto Azure Machine Learning com um repositório de Git habilita o controle de versão automática, local e remotamente. O repositório Git é hospedado no Azure DevOps. Como o projeto Machine Learning é associado a um repositório de Git, qualquer pessoa que tenha acesso ao repositório remoto pode fazer o download do código-fonte mais recente para outro computador (móvel).  

> [!NOTE]
> O Azure DevOps tem a sua própria ACL (lista de controle de acesso), que é independente do serviço de Experimentação do Azure Machine Learning. O acesso de usuário pode variar entre um repositório de Git e um projeto ou espaço de trabalho de Machine Learning. Você pode precisar gerenciar o acesso. 
> 
> Se você desejar conceder a um membro da equipe acesso em nível de código ao seu projeto do Machine Learning ou apenas compartilhar o workspace, será necessário conceder ao usuário as permissões corretas para acessar o repositório Git do Azure DevOps. 

Para gerenciar o controle de versão com o Git, você pode usar o branch mestre ou criar outros branches no repositório. Você também pode usar o repositório de Git local e enviar por push ao repositório de Git remoto, se provisionado.

Esta diagrama ilustra a relação entre um repositório Git do Azure DevOps e um projeto do Machine Learning:

![Git do Azure Machine Learning](media/using-git-ml-project/aml_git.png)

Para começar a usar um repositório de Git remoto, conclua as etapas descritas nas seções a seguir.

> [!NOTE]
> No momento, o Azure Machine Learning dá suporte apenas a repositórios Git em organizações do Azure DevOps.

## <a name="step-1-create-a-machine-learning-experimentation-account"></a>Etapa 1. Criar uma conta de Experimentação do Machine Learning
Crie uma conta de Experimentação do Machine Learning e instale o aplicativo do Workbench do Azure Machine Learning. Para obter mais informações, consulte [Instalar e criar o Início Rápido](quickstart-installation.md).

## <a name="step-2-create-an-azure-devops-project-or-use-an-existing-project"></a>Etapa 2. Criar um projeto do Azure DevOps ou usar um projeto existente
No [Portal do Azure](https://portal.azure.com/), crie um novo projeto:
1. Selecione **+**.
2. Pesquise por **Projeto de Equipe**.
3. Insira as informações necessárias:
    - **Nome**: um nome de equipe.
    - **Controle de Versão**: Selecionar **Git**.
    - **Assinatura**: selecione uma assinatura que tenha uma conta de Experimentação do Machine Learning.
    - **Location**: idealmente, escolha uma região que esteja próxima dos seus recursos de Experimentação do Machine Learning.
4. Selecione **Criar**. 

![Criar um projeto no portal do Azure](media/using-git-ml-project/create_vsts_team.png)

Certifique-se de entrar usando a sua conta do AAD (Azure Active Directory) que você usa para acessar o Workbench do Machine Learning. Caso contrário, o sistema não pode acessar o Workbench do Machine Learning usando as suas credenciais do AD do Azure. Uma exceção é se você usar a linha de comando para criar o projeto de Machine Learning e fornecer um token de acesso pessoal para acessar o repositório do Git. Abordaremos isso em mais detalhes mais adiante neste artigo.

Para ir diretamente para o projeto que você criou, use a URL https://\<nome do projeto\>.visualstudio.com.

## <a name="step-3-set-up-a-machine-learning-project-and-git-repo"></a>Etapa 3. Configurar um projeto de Machine Learning e o repositório de Git

Para configurar um projeto de Machine Learning, você tem duas opções:
- Criar um projeto de Machine Learning que tenha um repositório de Git remoto
- Associar um projeto de Machine Learning existente a um repositório Git do Azure DevOps

### <a name="create-a-machine-learning-project-that-has-a-remote-git-repo"></a>Criar um projeto de Machine Learning que tenha um repositório de Git remoto
Abrir um Workbench de Machine Learning e criar um novo projeto. Na caixa **repositório Git**, digite a URL do repositório Git do Azure DevOps da Etapa 2. Normalmente, ela tem esta aparência: https://\<nome da organização do Azure DevOps\>.visualstudio.com/_git/\<nome do projeto\>

![Criar um projeto de Machine Learning que tenha um repositório de Git](media/using-git-ml-project/create_project_with_git_rep.png)

Também é possível criar o projeto usando a ferramenta da linha de comando do Azure (Azure CLI). Você tem a opção de fornecer um token de acesso pessoal. O Machine Learning pode usar esse token para acessar o repositório de Git em vez de usar suas credenciais do AD do Azure:

```
# Create a new project that has a Git repo by using a personal access token.
$ az ml project create -a <Experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <Azure DevOps personal access token>
```

> [!IMPORTANT]
> Se você escolher o modelo de projeto em branco, o repositório Git que você escolher usar já poderá ter uma branch mestre. O Machine Learning simplesmente clona a branch mestre localmente. Ele adiciona a pasta aml_config e outros arquivos de metadados do projeto para a pasta de projeto local. 
>
> Se você escolher qualquer outro modelo de projeto, seu repositório Git *não poderá* ter uma branch mestre. Se isso ocorrer, você verá um erro. A alternativa é usar o comando `az ml project create` para criar o projeto, com uma opção `--force`. Isso exclui os arquivos na branch mestre original e os substitui pelos novos arquivos no modelo que você escolher.

Um novo projeto de Machine Learning é criado com a integração com o repositório de Git remoto habilitada. A pasta do projeto é sempre inicializada em Git como um repositório Git local. O remoto do Git é definido para o repositório Git do Azure DevOps remoto, de forma que as confirmações possam ser enviadas por push para o repositório Git remoto.

### <a name="associate-an-existing-machine-learning-project-with-an-azure-devops-git-repo"></a>Associar um projeto de Machine Learning existente a um repositório Git do Azure DevOps
É possível criar um projeto do Machine Learning sem um repositório Git do Azure DevOps e contar com o repositório Git local para instantâneos de histórico de execuções. Posteriormente, é possível associar um repositório Git do Azure DevOps a este projeto do Machine Learning existente usando o seguinte comando:

```azurecli
# Ensure that you are in the project path so Azure CLI has the context of your current project.
$ az ml project update --repo https://<Azure DevOps organization name>.visualstudio.com/_git/<project name>
```

> [!NOTE] 
> Você pode executar a operação de atualização do repositório somente em um projeto de Machine Learning que não tenha um repositório Git associado a ele. Além disso, depois de associar um repositório de Git com Machine Learning, não será possível removê-lo.

## <a name="step-4-capture-a-project-snapshot-in-the-git-repo"></a>Etapa 4. Capturar um instantâneo do projeto no repositório Git
Realizar algumas execuções de script no projeto e fazer algumas alterações entre as execuções. Você pode fazer isso através do aplicativo da área de trabalho ou da CLI do Azure, usando o comando `az ml experiment submit`. Para obter mais informações, consulte o [tutorial de classificação de Íris](tutorial-classifying-iris-part-1.md). Para cada execução, se houver qualquer alteração feita nos arquivos na pasta do projeto, um instantâneo da pasta inteira do projeto será confirmado e enviado por push para ao repositório Git remoto em um branch chamado AzureMLHistory/\<project GUID\>. Para exibir os branches e as confirmações, incluindo o branch AzureMLHistory/\<GUID do projeto\>, acesse a URL do repositório Git do Azure DevOps. 

> [!NOTE] 
> O instantâneo só será confirmado antes da execução de um script. Atualmente, uma execução de preparação de dados ou uma execução de célula de Notebook não aciona o instantâneo.

![Branch do histórico de execuções](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Será melhor se você não operar no branch de histórico usando comandos do Git. Ele pode interferir com o histórico de execução. Em vez disso, use o branch mestre ou crie outros branches em vez disso para suas próprias operações de Git.

## <a name="step-5-restore-a-previous-project-snapshot"></a>Etapa 5. Restaurar um instantâneo de projeto anterior 
Para restaurar a pasta toda do projeto ao estado de um instantâneo de histórico de execução anterior, no Workbench de Machine Learning:
1. Na barra de atividade (ícone de ampulheta), selecione **Executa**.
2. Na exibição de **Lista de Execuções**, selecione a execução que você deseja restaurar.
3. Na exibição de **Executar detalhes** exibição, selecione **Restaurar**.

![Branch do histórico de execuções](media/using-git-ml-project/restore_project.png)

Opcionalmente, você pode usar os comandos a seguir na janela de CLI do Azure no Workbench de Machine Learning:

```azurecli
# Discover the run I want to restore a snapshot from.
$ az ml history list -o table

# Restore the snapshot from a specific run.
$ az ml project restore --run-id <run ID>
```

Tenha cuidado ao executar esse comando. Executar esse comando substitui toda a pasta do projeto com o instantâneo tirado quando a execução específica foi inicializada. O seu projeto permanece no branch atual. Isso significa que você **perderá todas as alterações** na pasta do projeto atual.  

Você talvez queira usar o Git para confirmar as alterações para o branch atual antes de executar essa operação.

## <a name="step-6-use-the-master-branch"></a>Etapa 6. Usar o branch mestre
Uma maneira de evitar a perda acidental do estado atual do seu projeto é confirmar o projeto ao branch mestre do repositório Git (ou a qualquer branch que você mesmo tenha criado). Você pode usar o Git a partir da linha de comando ou da ferramenta de cliente de Git de sua preferência para operar no branch mestre. Por exemplo: 

```sh
# Check status to make sure you are on the master branch (or branch of your choice).
$ git status

# Stage all changes.
$ git add -A

# Commit all changes locally on the master branch.
$ git commit -m 'these are my updates so far'

# Push changes to the remote Azure DevOps Git repo master branch.
$ git push origin master
```

Agora, você pode restaurar o projeto com segurança a um instantâneo anterior ao concluir a etapa 5. Você pode sempre voltar para a confirmação que você acabou de criar no branch mestre.

## <a name="authentication"></a>Autenticação
Se você confiar apenas nas funções de histórico de execuções do Machine Learning para tirar instantâneos do projeto e restaurá-los, você não precisa se preocupar com a autenticação do repositório de Git. A autenticação é tratada pela camada de serviço de Experimentação de Machine Learning.

No entanto, se você usar suas próprias ferramentas de Git para gerenciar o controle de versão, será necessário lidar com a autenticação no repositório Git remoto no Azure DevOps. No Machine Learning, o repositório Git remoto é adicionado ao repositório local como um Git remoto usando o protocolo HTTPS. Isso significa que, quando você emite comandos de Git para o computador remoto (como enviar por push ou pull), você precisará fornecer o nome de usuário e senha ou um token de acesso pessoal. Para criar um token de acesso pessoal em um repositório Git do Azure DevOps, siga as instruções em [Usar um token de acesso pessoal para autenticar](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

## <a name="next-steps"></a>Próximas etapas
- Saiba como [usar o Processo de ciência de dados de equipe para organizar a estrutura do seu projeto](how-to-use-tdsp-in-azure-ml.md).
