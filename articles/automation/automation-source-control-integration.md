<properties 
    pageTitle="Integração de controle de origem na Automação do Azure | Microsoft Azure"
    description="Este artigo descreve a integração de controle de origem com o GitHub na Automação do Azure."
    services="automation"
    documentationCenter=""
    authors="SnehaGunda"
    manager="stevenka"
    editor="tysonn" />    
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="11/04/2015"
    ms.author="sngun" />

# Integração de controle de origem na Automação do Azure

A integração de controle de origem permite que você associe runbooks e scripts do PowerShell na sua conta da Automação a um repositório de controle de origem do GitHub. Ele fornece o ambiente onde você pode colaborar com facilidade com sua equipe, controlar alterações e reverter para versões anteriores de seus runbooks. Por exemplo, o controle de origem permite sincronizar ramificações diferentes para contas de desenvolvimento, teste ou produção da Automação, ajuda a promover o código testado em seu ambiente de desenvolvimento para o ambiente de produção. Você pode enviar por push código da Automação do Azure para o controle de origem ou efetuar pull de seus runbooks do controle de origem para a Automação do Azure. Este artigo descreve como configurar o controle de origem no seu ambiente da Automação do Azure. Começaremos pela criação de um repositório GitHub e percorreremos a configuração da Automação do azure para que ela acesse seus repositórios do GitHub.


>[AZURE.NOTE]O controle de origem dá suporte a efetuar pull e a enviar por push de [Runbooks de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks), bem como de [Runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks), mas os [runbooks do Gráfico](automation-runbook-types.md#graphical-runbooks) ainda não têm suporte.<br><br>


Há duas etapas simples necessárias para configurar o controle de origem para sua Conta de Automação, e somente uma se você já tiver uma conta do GitHub. Eles são:
## Etapa 1 – Criar um repositório do GitHub

Se você já tiver uma conta do GitHub e um repositório que deseja vincular à Automação do Azure, faça logon em sua conta existente e comece da etapa 2 abaixo. Caso contrário, navegue até o [GitHub](https://github.com/), inscreva-se em uma nova conta e [crie um novo repositório](https://help.github.com/articles/create-a-repo/).


## Etapa 2 – Instalar o controle de origem

* Na folha Conta de Automação do portal de visualização do Azure, clique em **Configurar Controle de Origem.**<br> ![Configurar Controle de Origem](media/automation-source-control-integration/automation_01_SetUpSourceControl.png)
* A folha **Controle de Origem** será aberta e você poderá configurar os detalhes da conta GitHub. A seguir, a lista de parâmetros a serem configurados:<br>

|Parâmetro |Descrição |
|:---|:---| 
|Escolher Origem | Selecione a origem. No momento, apenas **GitHub** tem suporte. |
|Autorização | Clique no botão **Autorizar** para conceder acesso da Automação do Azure a seu repositório do GitHub. Se você já estiver conectado à sua conta do GitHub em uma janela diferente, as credenciais da conta serão usadas. Quando a autorização for bem-sucedida, a folha mostrará o seu nome de usuário do GitHub em **Propriedade de Autorização**. |
|Escolher o repositório | Selecione um repositório do GitHub na lista de repositórios disponíveis. |
|Escolher a ramificação | Selecione uma ramificação na lista de ramificações disponíveis. Somente a ramificação **mestre** será mostrada se você ainda não tiver criado quaisquer ramificações. |
|Caminho da pasta do runbook | O caminho da pasta do runbook especifica o caminho no repositório do GitHub do qual você deseja enviar seu código por push ou por pull. Ele deve ser inserido no formato **/nomedapasta/nomedasubpasta**. Somente os scripts no caminho da pasta do runbook serão sincronizados com sua conta da Automação. Os scripts nas subpastas do caminho da pasta do runbook serão **NÃO** serão sincronizados. Use **/** para sincronizar todos os scripts do PowerShell no repositório. |


Por exemplo, se você tiver um repositório chamado **PowerShellScripts** com uma pasta chamada **RootFolder** que contenha uma pasta chamada **SubFolder**. Se todas as pastas e o repositório contiverem runbooks que você deseja sincronizar, então:

  1. Para sincronizar os runbooks do **repositório**, o caminho da pasta do runbook será */*
  2. Para sincronizar os runbooks de **RootFolder**, o caminho da pasta do runbook será */RootFolder*
  3. Para sincronizar os runbooks de **SubFolder**, o caminho da pasta do runbook será */RootFolder/SubFolder*.<br>

* Depois de configurar os parâmetros, eles serão exibidos na **folha Configurar Controle de Origem.**

![Configurar Folha](media/automation-source-control-integration/automation_02_SourceControlConfigure.png)


* A integração do controle de origem está configurada para a sua conta de automação e deve ser atualizada com as informações do GitHub; agora você pode clicar nessa parte para exibir todo o seu histórico de trabalhos de sincronização de controle de origem. <br> ![Valores de Repositório](media/automation-source-control-integration/automation_03_RepoValues.png)

* Depois de configurar o controle de origem, os seguintes recursos da Automação serão criados na sua conta da Automação:

Dois [ativos de variável](automation-variables.md) são criados.
      

  * A variável **Microsoft.Azure.Automation.SourceControl.Connection** contém os valores da cadeia de conexão, como mostrado abaixo.<br>

|Parâmetro |Valor |
|:---|:---|
| Nome | Microsoft.Azure.Automation.SourceControl.Connection |
| Tipo | Cadeia de caracteres |
| Valor | {"Branch":<*Nome da sua ramificação*>,"RunbookFolderPath":<*Caminho da pasta do runbook*>,"ProviderType":<*tem um valor 1 para o GitHub*>,"Repository":<*Nome do seu repositório*>,"Username":<*O nome do seu usuário no GitHub*>} |


  * A variável **Microsoft.Azure.Automation.SourceControl.OauthToken** contém o valor criptografado seguro do seu OAuthToken. <br>

|Parâmetro |Valor |
|:---|:---|
| Nome | Microsoft.Azure.Automation.SourceControl.OauthToken |
| Tipo | Unknown(Encrypted) |
| Valor | <*OAuthToken Criptografado*> |

![Variáveis](media/automation-source-control-integration/automation_04_Variables.png)

O **Controle de Origem de Automação** é adicionado como um aplicativo autorizado à sua conta do GitHub. Para exibir o aplicativo: na home page do GitHub, navegue até o **perfil** > **Configurações** > **Aplicativos**. Esse aplicativo permite que a Automação do Azure sincronize seu repositório do GitHub para uma conta da Automação.

![Aplicativo do Git](media/automation-source-control-integration/automation_05_GitApplication.png)


## Usando o controle de origem na Automação


### Fazer check-in de um runbook da Automação do Azure no controle de origem

O check-in de runbook permite que você envie por push as alterações feitas em um runbook para o repositório do controle de origem. A seguir, as etapas para fazer check-in de um runbook:

* De sua Conta de Automação, [crie um novo runbook textual](automation-first-runbook-textual.md) ou [edite um runbook textual existente](automation-edit-textual-runbook.md). Esse runbook pode ser um Fluxo de Trabalho do PowerShell ou um runbook de script do PowerShell.  
* Depois de editar o runbook, salve-o e clique em **check-in** na folha **Editar**. ![Botão Check-in](media/automation-source-control-integration/automation_06_CheckinButton.png)


 >[AZURE.NOTE]O check-in da Automação do Azure substituirá o código que existe atualmente no controle de origem. A instrução de linha de comando equivalente do Git para fazer check-in é **git add + git commit + git push**

* Quando você clicar em **check-in**, será exibida uma mensagem, clique em sim para continuar. ![Mensagem de Check-in](media/automation-source-control-integration/automation_07_CheckinMessage.png)
* O check-in inicia o runbook: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Esse runbook se conecta ao GitHub e envia por push alterações da Automação do Azure para seu repositório. Para exibir o histórico de trabalho de check-in, volte para a guia **Integração de Controle de Origem** e clique para abrir a folha Sincronização de Repositório. Essa folha mostra todos os seus trabalhos de controle de origem. Selecione o trabalho que você deseja exibir e clique para exibir os detalhes. ![Fazer Check-in de Runbook](media/automation-source-control-integration/automation_08_CheckinRunbook.png)
* Se houver erros no trabalho de check-in, o status do trabalho deverá ser suspenso e será possível exibir mais detalhes sobre o erro na folha do trabalho. A parte **Todos os Logs** mostrará todos os fluxos do PowerShell associados ao trabalho. Isso fornecerá detalhes para ajudar você a corrigir os problemas.  
* O nome do runbook modificado é enviado como um parâmetro de entrada para o runbook de check-in. Você pode [exibir os detalhes do trabalho](automation-runbook-execution.md#viewing-job-status-using-the-azure-management-portal) expandindo o runbook na folha **Sincronização de Repositórios**. ![Entrada de Check-in](media/automation-source-control-integration/automation_09_CheckinInput.png)
* Atualize seu repositório do GitHub depois que o trabalho for concluído para exibir as alterações. Deve haver uma confirmação em seu repositório com uma mensagem de confirmação: ***Nome do Runbook* Atualizado na Automação do Azure.**  



### Sincronizar runbooks do controle de origem para a Automação do Azure 

O botão de sincronização na folha Sincronização do Runbook permite efetuar pull em todos os runbooks no caminho da pasta do runbook do seu repositório para sua Conta de Automação. O mesmo repositório pode ser sincronizado em mais de uma Conta de Automação. A seguir, as etapas para sincronizar um runbook:

* Na Conta de Automação onde você configura o controle de origem, abra a **folha Integração de Controle de Origem/Sincronização de Repositórios** e clique em **Sincronizar** e será exibida uma mensagem, clique em **Sim** para continuar. ![Botão Sincronizar](media/automation-source-control-integration/automation_10_SyncButtonwithMessage.png)
* A sincronização inicia o runbook: **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Esse runbook se conecta ao GitHub e efetua pull das alterações do seu repositório para a Automação do Azure. Para exibir o runbook, volte para a folha **Integração do Controle de Origem/Sincronização de Repositórios** e a expanda. ![Sincronizar Runbook](media/automation-source-control-integration/automation_11_SyncRunbook.png)

 
>[AZURE.NOTE]Uma sincronização do controle de origem substitui a versão de rascunho dos runbooks que existem atualmente em sua conta da Automação para **TODOS** os runbooks atualmente no controle de origem. A instrução de linha de comando equivalente do Git para sincronizar é **git pull**


## Desconectando o controle de origem

Para se desconectar da sua conta do GitHub, abra a folha Sincronização de Repositórios e clique em **Desconectar**. Depois de se desconectar do controle de origem, os runbooks sincronizados anteriormente ainda permanecerão em sua conta da Automação, mas a folha Sincronização de Repositórios não será habilitada. ![Botão Desconectar](media/automation-source-control-integration/automation_12_Disconnect.png)



## Próximas etapas

Para saber mais sobre a integração de controle de origem, consulte os seguintes recursos:- [Automação do Azure: integração de controle de origem na Automação do Azure](https://azure.microsoft.com/blog/azure-automation-source-control-13/) - [Vote em seu sistema de controle de origem favorito](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d) - [Automação do Azure: integrando o controle de origem de runbook usando o Visual Studio Online](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)

<!---HONumber=Nov15_HO3-->