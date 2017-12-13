---
title: "Roaming e colaboração no Azure Machine Learning Workbench | Microsoft Docs"
description: "Saiba como configurar o roaming e colaboração no Azure Machine Learning Workbench."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 137608007716452ec6468f1e13f494b095a11cb0
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Roaming e colaboração no Azure Machine Learning Workbench
Este artigo descreve como é possível utilizar o Azure Machine Learning Workbench para configurar projetos para roaming entre computadores e colaborar com os membros da equipe. 

Ao criar um projeto no Azure Machine Learning que possui um link de repositório (repo) Git remoto, os instantâneos e metadados do projeto são armazenados na nuvem. É possível utilizar o link de nuvem para acessar o projeto a partir de um computador diferente (roaming). Também é possível colaborar com os membros da equipe, fornecendo-lhes acesso ao projeto. 

## <a name="prerequisites"></a>Pré-requisitos
1. Instale o aplicativo Machine Learning Workbench. Certifique-se de ter acesso a uma conta de Experimentação do Azure Machine Learning. Para obter mais informações, consulte o [guia de instalação](quickstart-installation.md).

2. Acesse o [Visual Studio Team Services](https://www.visualstudio.com) (Team Services) e, em seguida, crie um repositório para vincular seu projeto. Para obter mais informações, consulte [Usando um reposto Git com um projeto do Machine Learning Workbench](using-git-ml-project.md).

## <a name="create-a-new-machine-learning-project"></a>Crie um novo projeto do Machine Learning
Abra o Machine Learning Workbench e, em seguida, crie um novo projeto (por exemplo, um projeto chamado Íris). Na caixa **URL do Repositório do GIT do Visualstudio.com** insira uma URL válida para um repositório Git do Team Services. 

> [!IMPORTANT]
> Se você escolher o modelo de projeto em branco, o repositório Git que você escolher usar já poderá ter uma branch mestre. O Machine Learning simplesmente clona a branch mestre localmente. Ele adiciona a pasta aml_config e outros arquivos de metadados do projeto para a pasta de projeto local. 
>
> Se você escolher qualquer outro modelo de projeto, seu repositório Git *não poderá* ter uma branch mestre. Se isso ocorre, você verá um erro. A alternativa é usar o comando `az ml project create` para criar o projeto, com uma opção `--force`. Isso exclui os arquivos na branch mestre original e os substitui pelos novos arquivos no modelo que você escolher.

Depois que o projeto for criado, envie algumas execuções em qualquer script que esteja no projeto. Essa ação confirma o estado do projeto para a branch de histórico de execuções do repositório do Git remoto. 

> [!NOTE] 
> Somente execuções do script acionam as confirmações para a branch de histórico de execuções. A execução de preparação de dados e o Notebook não disparam instantâneos de projeto na branch do histórico de execuções.

Se você configurou a autenticação Git, você também poderá operar na branch mestre. Ou, poderá criar uma nova branch. 

Exemplo: 
```
# Check current repo status.
$ git status

# Stage all changes in the current repo.
$ git add -A

# Commit changes.
$ git commit -m "my commit fixes this weird bug!"

# Push to the remote repo.
$ git push origin master
```

## <a name="roaming"></a>Roaming
<a name="roaming"></a>

### <a name="open-machine-learning-workbench-on-a-second-computer"></a>Abra o Machine Learning Workbench em um segundo computador
Depois que repositório Git do Team Services estiver vinculado ao seu projeto, você poderá acessar o projeto Íris de qualquer computador que tenha o Machine Learning Workbench instalado. 

Para acessar o projeto Íris em outro computador, você deverá entrar no aplicativo utilizando as mesmas credenciais que foram utilizadas para criar o projeto. Também será necessário estar na mesma conta de Experimentação do Machine Learning e espaço de trabalho. O projeto Íris está listado alfabeticamente com outros projetos no espaço de trabalho. 

### <a name="download-the-project-on-a-second-computer"></a>Faça o download do projeto em um segundo computador
Quando o espaço de trabalho estiver aberto no segundo computador, o ícone adjacente ao projeto íris será diferente do ícone da pasta típica. O ícone de download indica que o conteúdo do projeto está na nuvem e que o projeto já está pronto para ser baixado para o computador atual. 

![Criar projeto](./media/roaming-and-collaboration/downloadable-project.png)

Selecione o projeto Íris para iniciar um download. Quando o download terminar, o projeto estará pronto para ser acessado no segundo computador. 

No Windows, o projeto está localizado em C:\Users\\<username\>\Documents\AzureML.

No MacOS, o projeto está localizado em /home/\<username\>/Documents/AzureML.

Em uma versão futura, planejamos aprimorar a funcionalidade para que seja possível selecionar uma pasta de destino. 

> [!NOTE]
> Se você tiver uma pasta no diretório do Machine Learning que tenha exatamente o mesmo nome do projeto, o download falhará. Para contornar esse problema, renomeie temporariamente a pasta existente.


### <a name="work-on-the-downloaded-project"></a>Trabalhar no projeto baixado 
O projeto baixado recentemente reflete o estado do projeto na última execução no projeto. Um instantâneo do estado do projeto será automaticamente confirmado com a branch do histórico de execução no repositório Git do Team Services sempre que você enviar uma execução. O instantâneo que está associado à execução mais recente é utilizado para instanciar o projeto no segundo computador. 
 

## <a name="collaboration"></a>Colaboração
Você pode colaborar com os membros da equipe em projetos que estão vinculados a um repositório Git do Team Services. É possível atribuir permissões aos usuários para a conta de Experimentação do Machine Learning, espaço de trabalho e projeto. Atualmente, é possível executar comandos do Azure Resource Manager utilizando a CLI do Azure. Você também pode usar o [portal do Azure](https://portal.azure.com). Para obter mais informações, consulte [Utilizar o Portal do Azure para adicionar usuários ](#portal).    

### <a name="use-the-command-line-to-add-users"></a>Utilizar a linha de comando para adicionar usuários
Por exemplo, Alice é o Proprietário do projeto Íris. Alice quer compartilhar o acesso ao projeto com Roberto. 

Alice seleciona o menu **Arquivo** e, em seguida, seleciona o item de menu **Prompt de Comando**. A janela do prompt de comando é aberta com o projeto Íris. Alice poderá então decidir qual o nível de acesso que ela deseja fornecer a Roberto. Ela concede permissões executando os seguintes comandos:  

```azurecli
# Find the Resource Manager ID of the Experimentation account.
az ml account experimentation show --query "id"

# Add Bob to the Experimentation account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <Experimentation account Resource Manager ID>

# Find the Resource Manager ID of the workspace.
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the workspace by inheritance. Bob can invite or remove other users.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace Resource Manager ID>
```

Após a atribuição de função, diretamente ou por herança, Roberto poderá visualizar o projeto na lista de projetos do Machine Learning Workbench. Talvez Roberto tenha que reiniciar o aplicativo para visualizar o projeto. Roberto poderá então fazer o download do projeto, conforme descrito em [Roaming](#roaming), e começar a colaborar com Alice. 

O histórico de execuções para todos os usuários que colaboram em um projeto é confirmado com o mesmo repositório Git remoto. Quando Alice executar uma execução, Roberto poderá ver a execução na seção de histórico de execuções do projeto no aplicativo do Machine Learning Workbench. Roberto também poderá restaurar o projeto para o estado de qualquer execução, incluindo as execuções iniciadas por Alice. 

Ao compartilhar um repositório Git remoto ao projeto, Alice e Roberto também podem colaborar na branch mestre. Se necessário, eles também poderão criar branches pessoais e utilizar mesclagens e solicitações de pull para colaborar. 

### <a name="use-the-azure-portal-to-add-users"></a>Utilizar o Portal do Azure para adicionar usuários
<a name="portal"></a>

Os projetos, espaços de trabalho e contas de Experimentação do Machine Learning são recursos do Azure Resource Manager. Para atribuir funções, é possível utilizar o link **Controle de Acesso** no [Portal do Azure](https://portal.azure.com). 

Localize o recurso para o qual deseja adicionar usuários, utilizando a exibição **Todos os Recursos**. Selecione o link **IAM (Controle de acesso)** e, em seguida, selecione **Adicionar usuários**. 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>Fluxo de trabalho de colaboração de exemplo
Para ilustrar o fluxo de trabalho da colaboração, vamos seguir um exemplo. Os funcionários da Contoso, Alice e Roberto, querem colaborar em um projeto de ciência de dados, utilizando o Machine Learning Workbench. Suas identidades pertencem ao mesmo locatário do Azure AD (Microsoft Azure Active Directory) da Contoso. A seguir, estão as etapas que Alice e Roberto executam:

1. Alice cria um repositório Git vazio em um projeto do Team Services. O projeto do Team Services deve estar em uma assinatura do Azure criada no locatário do Microsoft Azure Active Directory da Contoso. 

2. Alice cria uma conta de Experimentação do Machine Learning, um espaço de trabalho e um projeto do Machine Learning Workbench em seu computador. Ao criar o projeto, ela entra na URL do repositório Git do Team Services.

3. Alice começa a trabalhar no projeto. Ela cria alguns scripts e executa algumas execuções. Para cada execução, um instantâneo de toda a pasta do projeto é automaticamente enviado por push como uma confirmação para uma branch de histórico de execuções do repositório Git do Team Services que cria o Machine Learning Workbench.

4. Alice está feliz com o trabalho em andamento. Ela quer confirmar suas alterações na branch mestre local e, em seguida, enviá-las por push para a branch mestre do repositório Git do Team Service. Com o projeto aberto, no Machine Learning Workbench, ela abre a janela do Prompt de Comando e, em seguida, insere esses comandos:
    
    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Stage all changes.
    $ git add -A

    # Commit changes with a comment.
    $ git commit -m "this is a good milestone"

    # Push the commit to the master branch of the remote Git repo in Team Services.
    $ git push
    ```

5. Alice adiciona Roberto ao espaço de trabalho como um Colaborador. Ela pode fazer isso no Portal do Azure ou utilizando o comando `az role assignment`, conforme demonstrado anteriormente. Alice também concede permissões de leitura/gravação de Roberto para o repositório Git do Team Services.

6. Roberto inicia sessão no Machine Learning Workbench no seu computador. Ele pode ver o espaço de trabalho que Alice compartilhou com ele. Ele pode ver o projeto Íris listado nesse espaço de trabalho. 

7. Roberto seleciona o nome do projeto. O projeto é baixado para seu computador.
    * Os arquivos de projeto baixados são uma cópia do instantâneo da última execução gravada no histórico de execuções. Eles não são a última confirmação na branch mestre.
    * A pasta do projeto local é configurada para a branch mestre, com as alterações não registradas.

8. Roberto pode procurar as execuções que foram executadas por Alice. Ele pode restaurar instantâneos de qualquer execução anterior.

9. Roberto deseja obter as últimas alterações que Alice enviou por push e, depois, começar a trabalhar em uma branch diferente. No Machine Learning Workbench, Roberto abre uma janela do prompt de comando e executa os seguintes comandos:

    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Get the latest commit in the Team Services Git master branch and overwrite current files.
    $ git pull --force

    # Create a new local branch named "bob" so that Bob's work is done in the "bob" branch
    $ git checkout -b bob
    ```

10. Roberto modifica o projeto e envia novas execuções. As alterações são feitas na branch de Roberto. As execuções de Roberto também se tornam visíveis para Alice.

11. Roberto está pronto para enviar por push suas alterações para o repositório Git remoto. Para evitar conflitos com a branch mestre, onde Alice está trabalhando, Roberto envia por push seu trabalho para uma nova branch remota, que também é chamada de Roberto.

    ```sh
    # Verify that the current branch is "bob," and that it has unstaged changes.
    $ git status
    
    # Stage all changes.
    $ git add -A

    # Commit the changes with a comment.
    $ git commit -m "I found a cool new trick."

    # Create a new branch on the remote Team Services Git repo, and then push the changes.
    $ git push origin bob
    ```

12. Para contar a Alice sobre o novo truque legal em seu código, Roberto cria uma solicitação de pull no repositório Git remoto da branch Roberto para a branch mestre. Alice pode, então, mesclar a solicitação de pull na branch mestre.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [usando um reposto Git com um projeto do Machine Learning Workbench](using-git-ml-project.md).
