---
title: 'Tutorial: Criar um pipeline de CI/CD para o seu código existente usando o Azure DevOps Projects'
description: O Azure DevOps Projects facilita o uso inicial do Azure. O DevOps Projects ajuda você a usar seu próprio código e o repositório do GitHub para iniciar um aplicativo em um serviço do Azure de sua escolha em algumas etapas rápidas.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 88ee15a3b5cc53542d9e098dee485b8a526bb9a6
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161745"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Tutorial: Criar um pipeline de CI/CD para o seu código existente usando o Azure DevOps Projects

O Azure DevOps Projects apresenta uma experiência simplificada na qual é possível trazer o seu código e repositório Git existentes ou escolher um aplicativo de exemplo para criar um pipeline de CI (integração contínua) e CD (entrega contínua) para o Azure.

Você vai:

> [!div class="checklist"]
> * Use o DevOps Projects para criar um pipeline de CI/CD
> * Configurar o acesso ao seu repositório do GitHub e escolher uma estrutura
> * Configurar o Azure DevOps e uma assinatura do Azure 
> * Confirmar alterações no GitHub e implantá-las automaticamente no Azure
> * Examinar o pipeline de CI/CD do Azure Pipelines
> * Configurar o monitoramento do Application Insights
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Você pode obter uma gratuita por meio do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Acesso ao GitHub ou repositório Git externo que contém o .NET, Java, PHP, Node, Python ou código da Web estático.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

O Azure DevOps Projects cria um pipeline de CI/CD no Azure Pipelines. É possível criar uma nova organização do Azure DevOps ou usar uma existente. O Azure DevOps Projects também cria recursos do Azure na assinatura do Azure de sua escolha.

1. Entre no [Portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **Novo**.

1. Na caixa de pesquisa, digite **DevOps Projects**, depois selecione **Criar**.

    ![O painel do DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

1. Selecione **Traga seu próprio código**, depois selecione **Próxima**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>Configurar o acesso ao seu repositório do GitHub e escolher uma estrutura

1. Selecione o **GitHub** ou um repositório Git externo, depois selecione seu repositório e o branch que contém seu aplicativo.

1. Selecione sua estrutura Web, depois selecione **Avançar**.

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

    A estrutura do aplicativo escolhida anteriormente determina o tipo de destino de implantação do serviço do Azure disponível aqui. 
    
1. Selecione o serviço de destino, depois selecione **Avançar**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma assinatura do Azure 

1. Crie uma nova organização do Azure DevOps ou selecione uma organização existente.

     a. Insira um nome para seu projeto do Azure DevOps. 
    
    b. Selecione a assinatura e o local do Azure, insira um nome para o aplicativo e selecione **Concluído**.

    Após alguns minutos, o painel do DevOps Projects é exibido no portal do Azure. Um aplicativo de exemplo é configurado em um repositório em sua organização do Azure DevOps, é executado um build, e seu aplicativo é implantado no Azure. Esse painel oferece visibilidade no seu repositório de código do GitHub, pipeline de CI/CD e aplicativo no Azure. 
    
1. Selecione **Procurar** para exibir o aplicativo em execução.

    ![Exibição do painel do DevOps Projects](_img/azure-devops-project-github/dashboardnopreview.png) 
    
O Azure DevOps Projects configura automaticamente um build de CI e gatilho de liberação. Seu código permanece no seu repositório GitHub ou em outro repositório externo. 

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Confirmar alterações no GitHub e implantá-las automaticamente no Azure 

Agora você está pronto para colaborar com uma equipe em seu aplicativo usando um processo de CI/CD que implanta automaticamente o trabalho mais recente em seu site. Cada alteração do repositório GitHub inicia um build no Azure DevOps, e um pipeline de CD executa uma implantação no Azure.

1. Faça uma alteração em seu aplicativo, depois confirme a alteração em seu repositório do GitHub.  
    Depois de um tempo, um build inicia no Azure Pipelines. É possível monitorar o status do build no painel do DevOps Projects ou no navegador com sua organização do Azure DevOps.

1. Depois de concluir o build, atualize o aplicativo para verificar as alterações.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Examinar o pipeline de CI/CD do Azure Pipelines

O Azure DevOps Projects configura automaticamente um pipeline de CI/CD no Azure Pipelines. Explore e personalize o pipeline conforme necessário. Para se familiarizar com os pipelines de build e de lançamento, faça o seguinte:

1. Na parte superior do painel do DevOps Projects, selecione **Pipelines de build**.  
    Uma guia do navegador exibe o pipeline de build do seu novo projeto.

1. Aponte para o campo **Status**, depois selecione as reticências (...).  
    Um menu exibe várias opções, como o enfileiramento de um novo build, pausando um build e editando o pipeline de build.

1. Selecione **Editar**.

1. Nesse painel, é possível examinar as diversas tarefas do pipeline de build.  
    A compilação realiza várias tarefas, como efetuar buscas das fontes no repositório Git, restaurar dependências e publicar as saídas usadas para as implantações.

1. Na parte superior do pipeline de build, selecione o nome desse pipeline.

1. Altere o nome do pipeline de build para algo mais descritivo, selecione **Salvar e enfileirar** e selecione **Salvar**.

1. Embaixo do nome do pipeline de build, selecione **Histórico**.  
    Você verá uma trilha de auditoria das alterações recentes do build. O Azure DevOps controla quaisquer alterações feitas no pipeline de build e permite que você compare versões.

1. Selecione **Gatilhos**.  
    O Azure DevOps Projects criou automaticamente um gatilho de CI, e cada confirmação no repositório inicia um novo build. Como opção, é possível incluir ou excluir os branches do processo de CI.

1. Selecione **Retenção**.  
        Dependendo do seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

1. Selecione **Build e lançamento**, depois selecione **Lançamentos**.  
    O Azure DevOps Projects cria um pipeline de lançamento para gerenciar implantações no Azure.

1. Selecione as reticências (...) ao lado do pipeline de lançamento, depois selecione **Editar**.  
    O pipeline de lançamento contém um *pipeline* que define o processo de lançamento. 
    
1. Em **Artefatos**, selecione **Soltar**.  
    O pipeline de build examinado nas etapas anteriores produz a saída usada para o artefato. 

1. Ao lado do ícone **Soltar**, selecione **Gatilho de implantação contínua**.  
    Esse pipeline de lançamento tem um gatilho de CD habilitado, que executa uma implantação sempre que houver um novo artefato de build disponível. Outra opção é desabilitar o gatilho para que suas implantações exijam uma execução manual. 

1. À esquerda, selecione **Tarefas**.  
    Tarefas são as atividades realizadas pelo seu processo de implantação. Neste exemplo, uma tarefa foi criada para ser implantada no serviço do Aplicativo Azure.

1. À direita, selecione **Exibir versões** para exibir um histórico de versões.

1. Selecione as reticências (...) ao lado de uma versão, depois selecione **Abrir**.  
    Há vários menus para explorar, como um resumo da versão, itens de trabalho associados e testes.

1. Selecione **Confirmações**.  
    Essa exibição mostra as confirmações de código associadas à essa implantação. 

1. Selecione **Logs**.  
    Os logs contêm informações úteis sobre o processo de implantação. É possível exibi-los durante e após as implantações.

## <a name="configure-azure-application-insights-monitoring"></a>Configurar o monitoramento do Application Insights

Com o Azure Application Insights, você pode monitorar facilmente o aplicativo quanto à sua disponibilidade e uso. O Azure DevOps Projects configura automaticamente um recurso do Application Insights para seu aplicativo. Além disso você pode configurar vários alertas e recursos de monitoramento conforme necessário.

1. No portal do Azure, vá até o painel do DevOps Projects. 

1. No canto inferior direito, selecione o link **Application Insights** para seu aplicativo.  
    O painel do **Application Insights** é aberto. Essa exibição contém informações de uso, desempenho e monitoramento da disponibilidade do seu aplicativo.

    ![O painel do Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Selecione **Intervalo de tempo**, depois selecione **Última hora**. Para filtrar os resultados, selecione **Atualizar**.  
    Agora você consegue exibir todas as atividades dos últimos 60 minutos. Para sair do intervalo de tempo, selecione **x**.

1. Selecione **Alertas**, depois selecione **Adicionar alerta de métrica**. 

1. Insira um nome para o alerta.

1. Na lista suspensa **Alterar fonte em**, selecione seu **Recursos do Serviço de Aplicativo.** <!-- Please confirm whether this should be "Source Alter on" or "Source Alert on" -->

1. Na lista suspensa de **Métrica**, examine as várias métricas de alerta.  
    O alerta padrão é para um **tempo de resposta do servidor maior do que 1 segundo**. Você pode configurar facilmente vários alertas para melhorar os recursos de monitoramento do seu aplicativo.

1. Selecione a caixa de seleção **Notificar proprietários, colaboradores e leitores por email**.  
    Como opção, você pode executar ações adicionais quando um alerta é exibido executando um aplicativo lógico do Azure.

1. Selecione **OK** para criar o alerta.  
    Em poucos instantes, o alerta será exibido como ativo no painel.
    
1. Saia da área de **Alertas** e volte até o painel do **Application Insights**.

1. Selecione **Disponibilidade**, depois selecione **Adicionar teste**. 

1. Insira um nome de teste, depois selecione **Criar**.  
    Um teste de ping simples é criado para verificar a disponibilidade do seu aplicativo. Depois de alguns minutos, os resultados de teste estão disponíveis e o painel do Application Insights exibe um status de disponibilidade.

## <a name="clean-up-resources"></a>Limpar recursos

Quando eles não forem mais necessários, será possível excluir o serviço do Aplicativo Azure e os recursos relacionados criados neste tutorial. Para isso, use a funcionalidade **Excluir** no painel do DevOps Projects.

## <a name="next-steps"></a>Próximas etapas

Depois de ter configurado seu processo de CI/CD neste tutorial, um pipeline de build e um pipeline de lançamento foram criados automaticamente no Azure DevOps Projects. Você pode modificar esses pipelines de build e de lançamento para atender às necessidades de sua equipe. Você aprendeu como:

> [!div class="checklist"]
> * Use o DevOps Projects para criar um pipeline de CI/CD
> * Configurar o acesso ao seu repositório do GitHub e escolher uma estrutura
> * Configurar o Azure DevOps e uma assinatura do Azure 
> * Confirmar alterações no GitHub e implantá-las automaticamente no Azure
> * Examinar o pipeline de CI/CD do Azure Pipelines
> * Configurar o monitoramento do Application Insights
> * Limpar recursos

Para saber mais sobre o pipeline de CI/CD, consulte:

> [!div class="nextstepaction"]
> [Definir seu pipeline de implantação contínua (CD) de vários estágios](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
