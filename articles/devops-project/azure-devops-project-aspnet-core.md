---
title: 'Início Rápido: Criar um pipeline de CI/CD para .NET com o Azure DevOps Projects'
description: O Azure DevOps Projects facilita o uso inicial do Azure. Ele ajuda você a iniciar um aplicativo .NET em um serviço do Azure de sua escolha com algumas etapas rápidas.
ms.prod: devops
ms.technology: devops-cicd
services: azure-devops-project
documentationcenter: vs-devops-build
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 5fabe9ba03c9516f5df41645fc6ab1b7a0cb2050
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52262169"
---
# <a name="create-a-cicd-pipeline-for-net-with-azure-devops-projects"></a>Criar um pipeline de CI/CD para .NET com o Azure DevOps Projects

Configure a integração contínua (CI) e a entrega contínua (CD) para seu aplicativo .NET Core ou ASP.NET com o DevOps Projects. O DevOps Projects do Azure simplifica a configuração inicial de um pipeline de build e de lançamento no Azure Pipelines.

Caso não tenha uma assinatura do Azure, você pode obter uma gratuita via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

O DevOps Projects cria um pipeline de CI/CD no Azure DevOps. É possível criar uma nova organização do Azure DevOps ou usar uma existente. O DevOps Projects também cria recursos do Azure na assinatura do Azure de sua escolha.

1. Entre no [Portal do Microsoft Azure](https://portal.azure.com).

1. No painel esquerdo, selecione o ícone **Criar um recurso** na barra de navegação à esquerda, depois procure o **DevOps Projects**.  

3.  Selecione **Criar**.

    ![Iniciando a Entrega Contínua](_img/azure-devops-project-aspnet-core/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selecionar um aplicativo de exemplo e o serviço do Azure

1. Selecione o aplicativo de exemplo do .NET. Os exemplos do .NET incluem uma opção de estrutura do ASP.NET de fonte aberta ou estrutura do .NET Core de plataforma cruzada.

    ![.NET Framework](_img/azure-devops-project-aspnet-core/chooselanguagedotnet.png)

1. Selecione a estrutura de aplicativo do .NET Core.  
    Este exemplo é um aplicativo ASP.NET Core MVC.
    
2. Selecione **Avançar**.  
    O aplicativo Web no Windows é o destino de implantação padrão.  Opcionalmente, você pode escolher Aplicativo Web no Linux ou Aplicativo Web para Contêineres.  A estrutura do aplicativo escolhida anteriormente determina o tipo de destino de implantação do serviço do Azure disponível aqui.  
    
3. Mantenha o serviço padrão e selecione **Avançar**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma assinatura do Azure 

1. Crie uma nova organização do Azure DevOps gratuita ou escolha uma existente.

     a. Escolha um nome para o projeto. 

    b. Selecione a assinatura e o local do Azure, escolha um nome para o aplicativo e selecione **Concluído**.  
    Após alguns minutos, o painel do DevOps Projects é exibido no portal do Azure. Um aplicativo de exemplo é configurado em um repositório em sua organização do Azure DevOps, é executado um build, e seu aplicativo é implantado no Azure. Esse painel oferece visibilidade ao seu repositório de código, pipeline de CI/CD e seu aplicativo no Azure.
    

2. À direita do painel, selecione **Procurar** para exibir seu aplicativo em execução.

    ![Exibição Painel](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Confirmar as alterações de código e executar CI/CD

 O DevOps Projects criou um repositório Git no Azure Repos ou GitHub. Para exibir o repositório e fazer alterações de código no seu aplicativo, faça o seguinte:

1. À esquerda do painel do DevOps Projects, selecione o link para seu branch **mestre**.  
Esse link abre uma exibição do repositório de Git recém-criado.

1. Para exibir a URL de clone do repositório, selecione **Clone** na parte superior direita do navegador.  
Você pode clonar seu repositório Git no seu IDE favorito.  Nas próximas etapas, você pode usar o navegador da Web para criar e confirmar as alterações de código diretamente no branch mestre.

1. À esquerda do navegador, vá até o arquivo **Views/Home/index.cshtml**.

1. Selecione **Editar**, depois faça uma alteração no cabeçalho h2. Por exemplo, digite **Começar agora mesmo com o Azure DevOps Projects** ou faça outra alteração.

    ![Edições de código](_img/azure-devops-project-aspnet-core/codechange.png)

1. Selecione **Confirmar**, depois salve as alterações.

1. No seu navegador, vá até o painel do Azure DevOps Project.  Agora você deve ver uma compilação que está em andamento. As alterações feitas são automaticamente compiladas e implantadas via pipeline de CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Examinar o pipeline de CI/CD

Na etapa anterior, o Azure DevOps Projects configurou automaticamente um pipeline de CI/CD completo. Explore e personalize o pipeline conforme necessário. Siga as etapas a seguir para se familiarizar com os pipelines de build e de lançamento do Azure DevOps.

1. Na parte superior do painel do DevOps Projects, selecione **Pipelines de Build**.  
Esse link abre uma guia de navegador e o pipeline de build do Azure DevOps para seu novo projeto.

1. Selecione as reticências (...).  Essa ação abre um menu no qual você pode iniciar várias atividades, como enfileirar um novo build, pausar um build e editar o pipeline de build.

1. Selecione **Editar**.

    ![Pipeline de build](_img/azure-devops-project-aspnet-core/builddef.png)

1. Nesse painel, é possível examinar as diversas tarefas do pipeline de build.  
 A compilação realiza várias tarefas, como efetuar buscas das fontes no repositório Git, restaurar dependências e publicar as saídas usadas para as implantações.

1. Na parte superior do pipeline de build, selecione o nome desse pipeline.

1. Altere o nome do pipeline de build para algo mais descritivo, selecione **Salvar e enfileirar** e selecione **Salvar**.

1. Embaixo do nome do pipeline de build, selecione **Histórico**.   
No painel **Histórico**, é exibida uma trilha de auditoria das alterações recentes do build.  O Azure Pipelines controla quaisquer alterações feitas no pipeline de build e permite que você compare versões.

1. Selecione **Gatilhos**.  
O DevOps Projects criou automaticamente um gatilho de CI e cada confirmação no repositório inicia um novo build.  Você pode optar por incluir ou excluir os branches do processo de CI.

1. Selecione **Retenção**.  
Dependendo do seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

1. Selecione **Build e lançamento**, depois selecione **Lançamentos**.  
O DevOps Projects cria um pipeline de lançamento para gerenciar implantações no Azure.

1.  À esquerda, selecione as reticências (...) ao lado do pipeline de lançamento, depois selecione **Editar**.  
O pipeline de lançamento contém um pipeline, o qual define o processo de lançamento.  

1. Em **Artefatos**, selecione **Soltar**.  O pipeline de build examinado nas etapas anteriores produz a saída usada para o artefato. 

1. Ao lado do ícone **Soltar**, selecione o **Gatilho de implantação contínua**.  
Esse pipeline de lançamento tem um gatilho de CD habilitado, o qual executa uma implantação sempre que houver um novo artefato de build disponível. Outra opção é desabilitar o gatilho para que suas implantações exijam uma execução manual.  

1. À esquerda, selecione **Tarefas**.   
As tarefas são as atividades realizadas pelo seu processo de implantação. Neste exemplo, uma tarefa foi criada para ser implantada no Serviço de Aplicativo do Azure.

1. À direita, selecione **Exibir versões**. Essa exibição mostra um histórico de versões.

1. Selecione as reticências (...) ao lado de uma das versões, depois selecione **Abrir**.  
Há vários menus para explorar, como um resumo da versão, itens de trabalho associados e testes.


1. Selecione **Confirmações**.   
Essa exibição mostra as confirmações de código associadas à implantação específica. 

1. Selecione **Logs**.  
Os logs contêm informações úteis sobre o processo de implantação. Eles podem ser exibidos durante e após as implantações.


## <a name="clean-up-resources"></a>Limpar recursos

É possível excluir o Serviço de Aplicativo do Azure e outros recursos criados quando eles não forem mais precisos. Use a funcionalidade **Excluir** no painel do DevOps Projects.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como modificar os pipelines de build e de lançamento para atender às necessidades de sua equipe, consulte este tutorial:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>vídeos

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
