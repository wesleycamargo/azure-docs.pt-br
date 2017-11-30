---
title: "Usando um repositório Git com um projeto do Azure Machine Learning Workbench | Microsoft Docs"
description: "Este artigo explica como usar um repositório Git em conjunto com um projeto do Azure Machine Learning Workbench."
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ms.openlocfilehash: 0cd447a52964578dd2348a786dd57a45ea87516e
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2017
---
# <a name="using-git-repository-with-an-azure-machine-learning-workbench-project"></a>Usando um repositório Git com um projeto do Azure Machine Learning Workbench
Este documento fornece informações sobre como o Azure Machine Learning Workbench usa o Git para fornecer o controle de versão e garantir reprodutibilidade em sua experiência de ciência de dados. Também são fornecidas instruções sobre como associar seu projeto com um repositório Git de nuvem.

## <a name="introduction"></a>Introdução
O Azure Machine Learning Workbench é projetado com integração com o Git desde o princípio. Ao criar um novo projeto, a pasta do projeto é automaticamente "Inicializada por Git" como um repositório Git local (repositório). Enquanto isso, um segundo repositório Git local oculto também é criado com uma branch chamada _AzureMLHistory/< project_GUID >_ para manter o controle das alterações de pasta de projeto para cada execução. 

A associação do projeto do Azure ML a um repositório Git hospedado em um projeto do VSTS (Visual Studio Team Service) habilita o controle de versão automático localmente e remotamente. Essa associação permite que qualquer pessoa com acesso ao repositório remoto baixe o código-fonte mais recente em outro computador (uso de perfil móvel).  

> [!NOTE]
> O VSTS tem sua própria lista de controle de acesso que é independente do serviço de Experimentação do Azure Machine Learning. O acesso de usuário poderá variar entre um repositório Git e um espaço de trabalho do Azure ML ou projeto e precisará ser gerenciado. Portanto, se você deseja compartilhar seu projeto do Azure ML com um membro da equipe, incluindo acesso de nível de código, além de apenas compartilhar o espaço de trabalho, é necessário conceder explicitamente acesso apropriado a essa pessoa ao repositório Git do VSTS. 

Com o Git, também é possível gerenciar o controle de versão explicitamente usando o branch _mestre_ ou através da criação de outros branches no repositório. Você pode usar apenas o repositório Git local e também pode enviar por push ao repositório Git remoto, se provisionado.

Este diagrama ilustra a relação entre um repositório Git do VSTS e um projeto do Azure ML:

![Git do AML](media/using-git-ml-project/aml_git.png)

Para começar a usar um repositório Git remoto, siga estas instruções básicas.

> [!NOTE]
> Atualmente, o Azure Machine Learning só dá suporte a repositórios Git em contas do VSTS. O suporte a repositórios Git em geral (como GitHub e etc.) está planejado para o futuro.

## <a name="step-1-create-an-azure-ml-experimentation-account"></a>Etapa 1. Criar uma conta de Experimentação do Azure ML
Se ainda não tiver feito, crie uma conta de Experimentação do Azure ML e instale o aplicativo do Azure ML Workbench. Veja mais detalhes no [Guia de início rápido de instalação e criação](quickstart-installation.md).

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>Etapa 2. Criar um projeto de equipe ou usar um projeto de equipe existente
No [Portal do Azure](https://portal.azure.com/), crie um novo **Projeto de Equipe**.
1. Clique em **+**
2. Pesquise por **"Projeto de Equipe"**
3. Insira as informações necessárias.
    - Nome: um nome de equipe.
    - Controle de versão: **Git**
    - Assinatura: a que tem uma conta de Experimentação do Azure Machine Learning.
    - Local: idealmente que esteja em uma região próxima dos seus recursos de Experimentação do Azure Machine Learning.
4. Clique em **Criar**. 

![Criar um projeto de equipe no Portal do Azure](media/using-git-ml-project/create_vsts_team.png)

Certifique-se de entrar com sua conta do AAD (Azure Active Directory) usada para acessar o Azure Machine Learning Workbench. Caso contrário, o sistema não pode acessá-lo usando suas credenciais do AAD, a menos que você use a linha de comando para criar o projeto do Azure ML e forneça um token de acesso pessoal para acessar o repositório Git. Mais informações sobre isso mais tarde.

Depois de criar o Projeto de equipe, você estará pronto para prosseguir para a próxima etapa.

Para navegar diretamente até o Projeto de equipe recém criado, a URL é `https://<team_project_name>.visualstudio.com`.

## <a name="step-3-create-a-new-azure-ml-project-with-a-remote-git-repo"></a>Etapa 3. Criar um novo projeto do Azure ML com um repositório Git remoto
Inicie o Azure ML Workbench e crie um novo projeto. Preencha a caixa de texto do repositório Git com a URL do repositório Git do VSTS que você obteve na etapa 2. Normalmente, ele é semelhante a isto: `http://<vsts_account_name>.visualstudio.com/_git/<project_name>`

![Criar projeto do Azure ML com repositório Git](media/using-git-ml-project/create_project_with_git_rep.png)

Você também pode criar o projeto usando a ferramenta de linha de comando. Você tem a opção de fornecer um token de acesso pessoal. O Azure ML pode usar esse token para acessar o repositório Git em seu nome, em vez de usar suas credenciais do AAD:

```
# create a new project with a Git repo and personal access token.
$ az ml project create -a <experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <VSTS personal access token>
```
> [!IMPORTANT]
> Se você escolher o modelo de projeto em branco, não haverá problemas se o repositório do Git escolhido já tiver um branch _mestre_. O Azure ML simplesmente clona a branch _mestre_ localmente e adiciona a pasta `aml_config` e outros arquivos de metadados do projeto para a pasta de projeto local. Mas, se você escolher qualquer outro modelo de projeto, seu repositório de Git não poderá já ter uma branch _mestre_ ou você verá um erro. A alternativa é usar a ferramenta de linha de comando `az ml project create` para criar o projeto e fornecer uma opção de `--force`. Isso excluirá os arquivos na branch mestre original e os substituirá por novos arquivos no modelo que você escolher.

Agora um novo projeto do Azure ML é criado com a integração com repositório Git remoto habilitada e pronta para usar. A pasta do projeto é sempre inicializada em Git como um repositório Git local. E o _remoto_ do Git é definido como o repositório Git do VSTS remoto, de forma que as confirmações possam ser enviados por push para o repositório Git remoto.

## <a name="step-3a-associate-an-existing-azure-ml-project-with-a-vsts-git-repo"></a>Etapa 3a. Associe um projeto existente do Azure ML com um repositório Git do VSTS
Opcionalmente, você também pode criar um projeto do Azure ML sem um repositório Git do VSTS e contar somente com o repositório Git local para instantâneos de histórico de execuções. E você pode associar um repositório Git do VSTS mais tarde com este projeto do Azure ML existente usando o seguinte comando:

```azurecli
# make sure you are in the project path so CLI has context of your current project
$ az ml project update --repo http://<vsts_account_name>.visualstudio.com/_git/<project_name>
```

> [!NOTE] 
> Só é possível executar a operação de atualização do repositório em um projeto do Azure ML que não tem um repositório Git associado a ele. E, depois que o repositório Git estiver associado, ele não pode ser removido.

## <a name="step-4-capture-project-snapshot-in-git-repo"></a>Etapa 4. Capturar instantâneo do projeto no repositório Git
Agora você pode realizar algumas execuções de script no projeto e fazer algumas alterações entre as execuções. Você pode fazer isso através do aplicativo da área de trabalho ou da CLI, usando o comando `az ml experiment submit`. Para obter mais detalhes, você pode seguir o [Tutorial Classificando a íris](tutorial-classifying-iris-part-1.md). Para cada execução, se houver qualquer alteração feita nos arquivos na pasta do projeto, um instantâneo da pasta do projeto inteira será confirmado e enviado por push para ao repositório Git remoto em um branch chamado `AzureMLHistory/<Project_GUID>`. Você pode exibir os branches e as confirmações navegando até o URL do repositório Git do VSTS e encontrar esse branch. 

> [!NOTE] 
> O instantâneo só será confirmado antes da execução de um script. Atualmente, uma execução de preparação de dados ou uma execução de célula de Notebook não aciona o instantâneo.

![branch do histórico de execuções](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Será melhor se você não operar no branch de histórico usando comandos do Git. Isso pode confundir o histórico de execução. Use o branch mestre ou crie outros branches em vez disso para suas próprias operações de Git.

## <a name="step-5-restore-a-previous-project-snapshot"></a>Etapa 5. Restaurar um instantâneo de projeto anterior 
Para restaurar a pasta do projeto inteira ao estado de um instantâneo de estado de projeto de histórico de execução anterior, no Azure ML Workbench:
1. Clique em **Execuções** na barra de atividades (ícone de relógio).
2. Na exibição de **Lista de Execuções**, clique na execução você deseja restaurar.
3. Na exibição **Detalhes da Execução**, clique em **Restaurar**.

![branch do histórico de execuções](media/using-git-ml-project/restore_project.png)

Como alternativa, você pode usar o seguinte comando na janela de CLI do Azure ML Workbench.

```azurecli
# discover the run I want to restore snapshot from:
$ az ml history list -o table

# restore the snapshot from a particular run
$ az ml project restore --run-id <run_id>
```

Ao executar esse comando, substituiremos toda a pasta do projeto pelo instantâneo tirado quando a execução específica foi inicializada. Mas o projeto permanece no branch atual. Isso significa que você **perderá todas as alterações** na pasta do projeto atual. Portanto, tome muito cuidado ao executar esse comando. Você talvez queira realizar o Git para confirmar as alterações para o branch atual antes de executar a operação acima. Consulte antes para obter mais detalhes.

## <a name="step-6-use-the-master-branch"></a>Etapa 6. Usar o branch mestre
Uma maneira de evitar a perda acidental do estado atual do seu projeto é confirmar o projeto ao branch mestre (ou qualquer branch que você mesmo criou) do repositório Git. Você pode usar o Git diretamente através da linha de comando (ou outra ferramenta cliente de Git de sua preferência) para operar no branch mestre. Por exemplo:

```sh
# check status to make sure you are on the master branch (or branch of your choice)
$ git status

# stage all changes
$ git add -A

# commit all changes locally on the master branch
$ git commit -m 'these are my updates so far'

# push changes into the remote VSTS Git repo master branch.
$ git push origin master
```

Agora você pode restaurar o projeto com segurança para um instantâneo anterior, seguindo a etapa 5, sabendo que sempre poderá voltar à confirmação que acabou de fazer no branch mestre.

## <a name="authentication"></a>Autenticação
Se você confiar apenas nas funções de histórico de execuções do Azure ML para tirar instantâneos de projeto e restaurá-los, não precisará se preocupar com a autenticação do repositório Git. Ela será resolvida pela camada do serviço Experimentação.

No entanto, se usar suas próprias ferramentas de Git para gerenciar o controle de versão, você terá que lidar adequadamente com a autenticação no repositório Git remoto no VSTS. No Azure ML, o repositório Git remoto é adicionado ao repositório local como um Git remoto usando o protocolo HTTPS. Isso significa que, quando você emite comandos de Git para o computador remoto (como enviar por push ou pull), precisará fornecer o nome de usuário e senha ou token de acesso pessoal. Execute [estas instruções](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate) para criar o token de acesso pessoal em um repositório Git do VSTS.

## <a name="next-steps"></a>Próximas etapas
Saiba como usar o Processo de ciência de dados de equipe para organizar a estrutura do seu projeto. Consulte [Estruturar um projeto com o TDSP](how-to-use-tdsp-in-azure-ml.md)
