---
title: "Roaming e colaboração no Azure Machine Learning Workbench | Microsoft Docs"
description: "Lista de problemas conhecidos e um guia para ajudar a solucioná-los"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 50f48fb096cb907e050769a8a4159689eb25418c
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Roaming e colaboração no Azure Machine Learning Workbench
Este documento mostra como o Azure Machine Learning Workbench pode ajudar a usar perfis móveis em projetos nos computadores, bem como habilitar a colaboração com sua equipe. 

Quando você cria um projeto do Azure Machine Learning com um link do repositório Git (repositório) remoto, os metadados do projeto e os instantâneos são armazenados na nuvem. O link de nuvem permite que você acesse o projeto de um computador diferente (roaming). Você também pode conceder acesso aos seus colegas de trabalho, permitindo a colaboração. 

## <a name="prerequisites"></a>Pré-requisitos
Primeiramente, instale o Azure Machine Learning Workbench com acesso a uma conta de Experimentação. Siga o [guia de instalação](quickstart-installation.md) para obter mais detalhes.

Em seguida, acesse o [Visual Studio Team System](https://www.visualstudio.com) e crie um repositório para vincular ao projeto. Para obter informações detalhadas sobre o Git, consulte o artigo [Uso do repositório Git com um projeto do Azure Machine Learning Workbench](using-git-ml-project.md).

## <a name="create-a-new-azure-machine-learning-project"></a>Crie um novo projeto do Azure Machine Learning
Inicie o Azure Machine Learning Workbench e crie um novo projeto (por exemplo, _iris_). Preencha a caixa de texto **URL do repositório GIT Visualstudio.com** com uma URL de repositório Git do VSTS válida. 

> [!IMPORTANT]
> Se você escolher o modelo de projeto em branco, não haverá problemas se o repositório do Git escolhido já tiver um branch _mestre_. O Azure ML simplesmente clona a branch _mestre_ localmente e adiciona a pasta `aml_config` e outros arquivos de metadados do projeto para a pasta de projeto local. Mas, se você escolher qualquer outro modelo de projeto, seu repositório de Git não poderá já ter uma branch _mestre_ ou você verá um erro. A alternativa é usar a ferramenta de linha de comando `az ml project create` para criar o projeto e fornecer uma opção de `--force`. Isso excluirá os arquivos na branch mestre original e os substituirá por novos arquivos no modelo que você escolher.

Quando o projeto for criado, envie algumas execuções em qualquer script dentro do projeto. Essa ação confirma o estado do projeto na branch de histórico de execução do repositório Git remoto. 

> [!NOTE] 
> Somente execuções do script acionam as confirmações para a branch de histórico de execuções. Execuções do Notebook ou execução de preparo de dados não acionam instantâneos de projeto na branch de histórico de execuções.

Se você tem que configurar a autenticação de Git, é possível operar explicitamente na branch mestre ou criar uma nova branch. 

Por exemplo: 
```
# check current repo status
$ git status

# stage all changes in the current repo
$ git add -A

# commit changes
$ git commit -m "my commit fixes this weird bug!"

# push to remote repo.
$ git push origin master
```

## <a name="roaming"></a>Roaming
<a name="roaming"></a>

### <a name="open-azure-machine-learning-workbench-on-second-machine"></a>Abra o Azure Machine Learning Workbench no segundo computador
Depois que o repositório Git do VSTS é vinculado ao seu projeto, você pode acessar o projeto _iris_ de qualquer computador no qual você instalou o Azure Machine Learning Workbench. 

Para acessar o projeto iris em outro computador, você precisa fazer logon no aplicativo com as mesmas credenciais usadas durante a criação do projeto. Além disso, você precisará navegar até a mesma conta de Experimentação e espaço de trabalho. O projeto _iris_ será listado em ordem alfabética com outros projetos dentro do espaço de trabalho. 

### <a name="download-project-on-second-machine"></a>Baixe o projeto no segundo computador
Quando o espaço de trabalho é aberto no segundo computador, o ícone adjacente ao projeto _iris_ é diferente do ícone da pasta comum. O ícone de download indica que o conteúdo do projeto está na nuvem e precisa ser baixado no computador atual. 

![criar projeto](./media/roaming-and-collaboration/downloadable-project.png)

Clicar no projeto _iris_ inicia uma ação de download. Após um tempo, quando o download for concluído, o projeto estará pronto para ser acessado no segundo computador. 

No Windows, é `C:\Users\<username>\Documents\AzureML`

Em macOS, ele fica em:`/home/<username>/Documents/AzureML`

Em uma versão futura, planejamos aprimorar a funcionalidade para permitir que você selecione uma pasta de destino. 

> [!NOTE]
> Se você tiver uma pasta no diretório do Azure ML com o mesmo nome do projeto, o download falhará. No momento, você precisa renomear a pasta existente para contornar esse problema.


### <a name="work-on-the-downloaded-project"></a>Trabalhar no projeto baixado 
O projeto baixado recentemente reflete o estado do projeto a partir da última execução do projeto. Um instantâneo do estado do projeto é confirmado automaticamente para a branch do histórico de execuções no repositório Git do VSTS toda vez que você envia uma execução. Usamos o instantâneo associado à execução mais recente ao instanciar o projeto no segundo computador. 
 

## <a name="collaboration"></a>Colaboração
Você pode colaborar com sua equipe em projetos vinculados a um repositório Git do VSTS. Você pode atribuir permissões a usuários na conta de Experimentação, espaço de trabalho e projeto. Neste momento, você pode executar os comandos do Azure Resource Manager usando a CLI do Azure. Você também pode usar o [portal do Azure](https://portal.azure.com). Veja a [seção a seguir](#portal).    

### <a name="using-command-line-to-add-users"></a>Uso da linha de comando para adicionar usuários
Vamos usar um exemplo. Digamos que Alice é a proprietária do projeto e_Iris_ e deseja compartilhar o acesso com Bob. 

Alice clica no menu **Arquivo** e seleciona o item de menu **Prompt de comando** para iniciar o prompt de comando configurado para o projeto _iris_. Então, Alice decide qual nível de acesso deseja conceder a Bob executando os comandos a seguir.  

```azurecli
# Find ARM ID of the experimnetation account
az ml account experimentation show --query "id"

# Add Bob to the Experimentation Account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the Workspace by inheritance. And he can invite or remove others.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace ARM ID>
```

Após a atribuição de função, diretamente ou por herança, Bob pode ver o projeto na lista de projetos do Workbench. O aplicativo pode precisar de uma reinicialização para ver o projeto. Bob pode baixar o projeto conforme descrito na [seção Roaming](#roaming) e colaborar com Alice. 

O histórico de execuções de todos os usuários que colaboram em um projeto é confirmado para o mesmo repositório Git remoto. Assim, quando Alice executa uma execução, Bob pode ver a execução na seção de histórico de execuções do projeto no aplicativo do Workbench. Bob também pode restaurar o projeto para o estado de qualquer execução incluindo execuções iniciadas por Alice. 

Compartilhar um repositório Git remoto para o projeto permite que Alice e Bob também colaborem na branch mestre. Se necessário, eles também podem criar branches pessoais e usar solicitações pull do Git e mesclagens para colaborar. 

### <a name="using-azure-portal-to-add-users"></a>Uso do portal do Azure para adicionar usuários
<a name="portal"></a>

As contas de Experimentação do Azure Machine Learning, espaços de trabalho e projetos são recursos do Azure Resource Manager. Você pode usar o link de controle de acesso no [Portal do Azure](https://portal.azure.com) para atribuir funções. 

Localize o recurso que você está procurando para adicionar usuários da exibição Todos os recursos. Clique no link de Controle de Acesso (IAM) na página. Adicionar usuários 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>Fluxo de trabalho de colaboração de exemplo
Para ilustrar o fluxo de colaboração, vamos examinar um exemplo. Os funcionários da Contoso Alice e Bob desejam colaborar em um projeto de ciência de dados usando o Azure ML Workbench. Suas identidades pertencem ao mesmo locatário do Azure AD Contoso.

1. Primeiro, Alice cria um repositório Git vazio em um projeto do VSTS. Este projeto VSTS deve residir em uma assinatura do Azure criada no locatário Contoso AAD. 

2. Alice então cria uma conta do Azure ML de experimentação, um espaço de trabalho e um projeto do Azure ML Workbench em seu computador. Ela fornece o URL do repositório Git ao criar o projeto.

3. Alice começa a trabalhar no projeto. Ela cria alguns scripts e executa algumas execuções. Para cada execução, um instantâneo da pasta do projeto inteira é automaticamente enviado para uma branch do histórico de execuções do repositório Git do VSTS criada pelo Workbench como uma confirmação.

4. Alice está satisfeita com o trabalho em andamento. Ela deseja confirmar a alteração na branch _mestre_ local e a envia por push para a branch _mestre_ do repositório Git do VSTS. Para fazer isso, com o projeto aberto, ela abre a janela de prompt de comando do Azure ML Workbench e emite os seguintes comandos:
    
    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # stage all changes
    $ git add -A

    # commit changes with a comment
    $ git commit -m "this is a good milestone"

    # push the commit to the master branch of the remote Git repo in VSTS
    $ git push
    ```

5. Alice então adiciona Bob no espaço de trabalho como um colaborador. Ela pode fazer isso no portal do Azure ou usando o comando `az role assignment` ilustrado acima. Ela também concede a Bob o acesso de leitura/gravação para o repositório Git do VSTS.

6. Bob agora se registra no Azure ML Workbench em seu computador. Ele pode ver o espaço de trabalho que Alice compartilhou com ele e o projeto listado nesse espaço de trabalho. 

7. Bob clica no nome do projeto e o projeto é baixado em seu computador.
    
    a. Os arquivos de projeto baixados são clones do instantâneo da última execução registrada no histórico de execuções. Eles não são a última confirmação na branch mestre.
    
    b. A pasta do projeto local é definida na branch _mestre_ com alterações sem etapas.

8. Bob agora pode procurar por execuções realizadas por Alice e restaurar o instantâneo de todas as execuções anteriores.

9. Bob deseja obter as últimas alterações enviada por push por Alice e começar a trabalhar em uma branch diferente. Portanto, ele abre a janela de prompt de comando do Azure ML Workbench e executa os comandos a seguir:

    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # get the latest commit in VSTS Git master branch and overwrite current files
    $ git pull --force

    # create a new local branch named "bob" so Bob's work is done on the "bob" branch
    $ git checkout -b bob
    ```

10. Bob agora modifica o projeto e envia novas execuções. As alterações são feitas na branch _bob_. E as execuções de Bob também se tornarão visíveis para Alice.

11. Bob agora está pronto para enviar por push as alterações para o repositório Git remoto. Para evitar conflitos com a branch _mestre_ em que Alice está trabalhando, ele decide enviar por push o seu trabalho para uma nova branch remota também denominada _bob_.

    ```sh
    # verify that the current branch is "bob" and it has unstaged changes
    $ git status
    
    # stage all changes
    $ git add -A

    # commit them with a comment
    $ git commit -m "I found a cool new trick."

    # create a new branch on the remote VSTS Git repo, and push changes
    $ git push origin bob
    ```

12. Bob, em seguida, pode contar a Alice sobre o novo truque no seu código e cria uma solicitação de pull no repositório Git remoto a da branch _bob_ para a branch _mestre_. E Alice pode então mesclar a solicitação de pull na branch _mestre_.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o uso de Git com o Azure ML Workbench: [Usar o repositório Git com um projeto do Azure Machine Learning Workbench](using-git-ml-project.md)