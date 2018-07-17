---
title: Criar um pipeline de CI/CD para o seu código existente com o projeto de DevOps do Azure | Tutorial do VSTS
description: O Projeto de DevOps facilita o uso inicial do Azure. Ele ajuda você a usar seu próprio código e o repositório do GitHub para iniciar um aplicativo em um serviço do Azure de sua escolha em algumas etapas rápidas.
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
ms.openlocfilehash: 8c92b45cd3949e56515286c963b035e3c449835b
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967303"
---
# <a name="create-a-cicd-pipeline-for-your-existing-code-with-the-azure-devops-project"></a>Criar um pipeline de CI/CD para o seu código existente com o projeto de DevOps do Azure

O projeto de DevOps do Azure apresenta uma experiência simplificada na qual você traz o seu código existente e o repositório Git ou escolhe um dos aplicativos de exemplo para criar um pipeline de integração contínua (CI) e entrega contínua (CD) para o Azure.

Você vai:

> [!div class="checklist"]
> * Criar um projeto de DevOps do Azure
> * Configurar o acesso ao seu repositório do GitHub e escolher uma estrutura
> * Configurar o VSTS e uma assinatura do Azure 
> * Confirmar alterações no GitHub e implantar automaticamente no Azure
> * Examinar o pipeline de CI/CD do VSTS
> * Configurar o monitoramento do Application Insights

## <a name="prerequisites"></a>pré-requisitos

* Uma assinatura do Azure. Você pode obter uma gratuita por meio do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Acesso ao GitHub ou repositório Git externo que contém o .NET, Java, PHP, nó, Python ou código da Web estático.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

O Projeto de DevOps do Azure cria um pipeline de CI/CD no VSTS.  Você pode criar uma **nova conta do VSTS** ou usar uma **conta existente**.  O Projeto de DevOps do Azure também cria **recursos do Azure** na **assinatura do Azure** de sua escolha.

1. Entre no [portal do Microsoft Azure](https://portal.azure.com).

1. Escolha o ícone **+ Novo** na barra de navegação à esquerda, depois procure o **Projeto de DevOps**.  Escolha **Criar**.

    ![Iniciando a Entrega Contínua](_img/azure-devops-project-github/fullbrowser.png)

1. Selecione **Traga o seu próprio código**.  Quando terminar, escolha **Avançar**.

## <a name="configure-access-to-your-github-repository-and-choose-a-framework"></a>Configurar o acesso ao seu repositório do GitHub e escolher uma estrutura

1. Selecione **GitHub**.  Opcionalmente, você pode escolher uma **repositório de Git externo**.  Escolha sua **repositório** e **branch** que contém seu aplicativo.

1. Selecione sua **estrutura Web**.  Quando terminar, escolha **Avançar**.

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

1. A estrutura do aplicativo, escolhida nas etapas anteriores, determina o tipo de destino de implantação do serviço do Azure disponível aqui.  Selecione o **serviço de destino** de sua escolha.  Quando terminar, escolha **Avançar**.

## <a name="configure-vsts-and-an-azure-subscription"></a>Configurar o VSTS e uma assinatura do Azure 

1. Crie uma **nova** conta do VSTS gratuita ou use uma conta **existente**.  Escolha um **nome** para seu projeto do VSTS.  Selecione sua **assinatura do Azure**, **local** e escolha um **nome** para seu aplicativo.  Quando terminar, escolha **Concluído**.

    ![Inserir informações do VSTS](_img/azure-devops-project-github/vstsazureinfo.png)

1. Em alguns minutos, o **painel do projeto** é carregado no portal do Azure.  Um aplicativo de exemplo é configurado em um repositório em sua conta do VSTS, é executada uma compilação e seu aplicativo é implantado no Azure.  Esse painel oferece visibilidade no **repositório de código** do GitHub, **pipeline CI/CD do VSTS** e **aplicativo no Azure**.  À direita do painel, selecione **Procurar** para exibir o aplicativo em execução.

    ![Exibição Painel](_img/azure-devops-project-github/dashboardnopreview.png) 
    
O projeto de DevOps do Azure configura automaticamente um build de CI e libera o acionador.  Seu código permanecerá no seu GitHub ou em outro repositório externo.  

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Confirmar alterações no GitHub e implantar automaticamente no Azure 

Agora você está pronto para colaborar com uma equipe em um aplicativo com um processo de CI/CD que implanta automaticamente o trabalho mais recente em seu site da Web.  Cada alteração do repositório GitHub inicia um build no VSTS, e uma definição de Release Management do VSTS executa uma implantação no Azure.

1.  Faça uma alteração em seu aplicativo, e **confirme** a alteração em seu repositório do GitHub.
2.  Em poucos instantes, um build será iniciado no VSTS.  Você pode monitorar o status do build com o painel do projeto de DevOps ou no navegador com sua conta do VSTS.
3.  Quando o build for concluído, **atualize seu aplicativo** no navegador para verificar se você vê suas alterações.

## <a name="examine-the-vsts-cicd-pipeline"></a>Examinar o pipeline de CI/CD do VSTS

O projeto de DevOps do Azure configurou automaticamente um pipeline de CI/CD do VSTS completo em sua conta do VSTS.  Explore e personalize o pipeline conforme necessário.  Siga as etapas abaixo para se familiarizar com a compilação do VSTS e as definições da versão.

1. Selecione **Pipelines de Build** na **parte superior** do painel do projeto de DevOps do Azure.  Esse link abre uma guia do navegador e abre a definição de build do VSTS para seu novo projeto.

1. Mova o cursor do mouse para a direita da definição de compilação, ao lado do campo **Status**. Selecione as **reticências** que aparecem.  Essa ação abre um menu no qual você pode iniciar várias atividades, como pôr uma nova compilação na fila, pausar uma compilação e editar a definição de compilação.

1. Selecione **Editar**.

1. Nessa exibição, **examine as várias tarefas** para sua definição de build.  A compilação realiza várias tarefas, como efetuar buscas das fontes no repositório Git, restaurar dependências e publicar as saídas usadas para as implantações.

1. Na parte superior da definição de compilação, selecione o **nome da definição de compilação**.

1. Altere o **nome** da sua definição de compilação para algo mais descritivo.  Selecione **Salvar e pôr na fila**, depois selecione **Salvar**.

1. No nome da definição de compilação, selecione **Histórico**.  Você verá uma trilha de auditoria das alterações recentes do build.  O VSTS controla todas as alterações feitas na definição de compilação e permite comparar as versões.

1. Selecione **Gatilhos**.  O projeto de DevOps do Azure criou automaticamente um gatilho de CI e cada confirmação no repositório inicia uma nova compilação.  Você pode optar por incluir ou excluir os branches do processo de CI.

1. Selecione **Retenção**.  Com base em seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

1. Selecione **Compilação e Versão**, em seguida, escolha **Versões**.  O projeto de DevOps do Azure criou uma definição da versão do VSTS para gerenciar implantações no Azure.

1. No lado esquerdo do navegador, selecione as **reticências** ao lado de sua definição de versão, em seguida, escolha **Editar**.

1. A definição de versão contém um **pipeline**, que define o processo de lançamento.  Em **Artefatos**, selecione **Soltar**.  A definição da compilação examinada nas etapas anteriores produz a saída usada para o artefato. 

1. No lado direito do ícone **Soltar**, selecione o **Gatilho de implantação contínua**.  Essa definição de versão tem um gatilho de CD habilitado, que executa uma implantação sempre que houver um novo artefato de compilação disponível.  Opcionalmente, você pode desabilitar o gatilho para que suas implantações exijam uma execução manual. 

1. À esquerda do navegador, selecione **Tarefas**.  As tarefas são as atividades que seu processo de implantação realiza.  Neste exemplo, uma tarefa foi criada para ser implantada no **serviço de Aplicativo do Azure**.

1. No lado direito do navegador, selecione **Exibir versões**.  Essa exibição mostra um histórico de versões.

1. Selecione as **reticências** ao lado de uma das versões e escolha **Abrir**.  Há vários menus para explorar nessa exibição, como um resumo da versão, itens de trabalho associados e testes.

1. Selecione **Confirmações**.  Essa exibição mostra as confirmações de código associadas à implantação específica. 

1. Selecione **Logs**.  Os logs contêm informações úteis sobre o processo de implantação.  Eles podem ser exibidos durante e após as implantações.

## <a name="configure-azure-application-insights-monitoring"></a>Configurar o monitoramento do Application Insights

Com o Azure Application Insights, você pode monitorar facilmente o aplicativo quanto à sua disponibilidade e uso.  O projeto de DevOps do Azure configurou automaticamente um recurso do Application Insights para seu aplicativo.  Além disso você pode configurar vários alertas e recursos de monitoramento conforme necessário.

1. Navegue até o painel **Projeto de DevOps do Azure** no portal do Azure.  Na parte inferior direita do painel, escolha o link do **Application Insights** para seu aplicativo.

1. A folha do **Application Insights** abre no portal do Azure.  Essa exibição contém informações de uso, desempenho e monitoramento da disponibilidade do seu aplicativo.

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Selecione **Intervalo de tempo** e, em seguida, escolha **última hora**.  Selecione **Atualizar** para filtrar os resultados.  Agora você verá todas as atividades dos últimos 60 minutos.  Selecione o **x** para sair do intervalo de tempo.

1. Selecione **Alertas**, em seguida, selecione **+ Adicionar alerta de métrica**.  

1. Insira um **Nome** para o alerta.

1. Selecione o menu suspenso para **Alterar destino ativado**.  Escolha seu **recursos do Serviço de Aplicativo.**

1. O alerta padrão é para um **tempo de resposta do servidor maior do que 1 segundo**.  Selecione o menu suspenso **Métrica** para examinar as várias métricas de alerta.  Você pode configurar facilmente vários alertas para melhorar os recursos de monitoramento do seu aplicativo.

1. Marque a caixa de seleção para **Notificar por email proprietários, colaboradores e leitores**.  Opcionalmente, você pode executar ações adicionais quando um alerta é disparado, executando um aplicativo lógico do Azure.

1. Selecione **Ok** para criar o alerta.  Em poucos instantes, o alerta será exibido como ativo no painel.  **Saia** da área de Alertas e navegue de volta até a **folha do Application Insights**.

1. Selecione **Disponibilidade**, em seguida, selecione **+ Adicionar teste**. 

1. Insira um **Nome do teste**, em seguida, escolha **Criar**.  Um teste de ping simples é criado para verificar a disponibilidade do seu aplicativo.  Depois de alguns minutos, os resultados de teste estão disponíveis e o painel do Application Insights exibe um status de disponibilidade.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode excluir o serviço de Aplicativo Azure e recursos relacionados criados nesse início rápido usando a funcionalidade **Excluir** no painel do Projeto de DevOps do Azure.

## <a name="next-steps"></a>Próximas etapas

Depois de configurar seu processo de CI/CD nesse tutorial, uma definição de compilação e versão foram criadas automaticamente em seu projeto do VSTS. Você pode modificar essas definições de build e versão para atender às necessidades da sua equipe. Você aprendeu como:

> [!div class="checklist"]
> * Criar um projeto de DevOps do Azure
> * Configurar o acesso ao seu repositório do GitHub e escolher uma estrutura
> * Configurar o VSTS e uma assinatura do Azure 
> * Confirmar alterações no GitHub e implantar automaticamente no Azure
> * Examinar o pipeline de CI/CD do VSTS
> * Configurar o monitoramento do Application Insights

Para saber mais sobre o pipeline do VSTS, consulte este tutorial:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
