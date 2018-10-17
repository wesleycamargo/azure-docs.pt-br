---
title: Implantar seu aplicativo ASP.NET nas Máquinas Virtuais do Azure com o Projeto de DevOps do Azure | Tutorial do Azure DevOps Services
description: O Projeto de DevOps facilita o uso inicial do Azure. O projeto do Azure DevOps facilita a implantação do seu ASP.NET nas Máquinas Virtuais do Azure em algumas etapas rápidas.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b05e2c2c46aa9bfa8c92d3d3c5c83d018c547b9f
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299127"
---
# <a name="tutorial--deploy-your-aspnet-app-to-azure-virtual-machines-with-the-azure-devops-project"></a>Tutorial: Implantar seu aplicativo ASP.NET nas Máquinas Virtuais do Azure com o Projeto de DevOps do Azure

O projeto de DevOps do Azure apresenta uma experiência simplificada na qual você traz o seu código existente e o repositório Git ou escolhe um dos aplicativos de exemplo para criar um pipeline de CI (integração contínua) e CD (entrega contínua) para o Azure.  O projeto de DevOps cria automaticamente recursos do Azure, como uma nova máquina virtual do Azure, cria e configura um pipeline de lançamento no Azure DevOps que inclui um pipeline de build de CI, configura um pipeline de lançamento para CD e cria um recurso do Azure Application Insights para monitoramento.

Você vai:

> [!div class="checklist"]
> * Criar um projeto do Azure DevOps para um aplicativo ASP.NET
> * Configurar o Azure DevOps Services e uma assinatura do Azure 
> * Examinar o pipeline de CI do Azure DevOps Services
> * Examinar o pipeline de CD do Azure DevOps Services
> * Confirmar alterações no Azure DevOps Services e implantar automaticamente no Azure
> * Configurar o monitoramento do Application Insights
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Você pode obter uma gratuita por meio do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-app"></a>Criar um projeto do Azure DevOps para um aplicativo ASP.NET

O Azure DevOps Project cria um pipeline de CI/CD no Azure.  Você pode criar uma **nova organização Azure DevOps Services** ou usar uma **organização existente**.  O Projeto de DevOps do Azure também cria **recursos do Azure** como máquinas virtuais na **assinatura do Azure** de sua escolha.

1. Entre no [portal do Microsoft Azure](https://portal.azure.com).

1. Escolha o ícone **+ Novo** na barra de navegação à esquerda, depois procure o **Projeto de DevOps**.  Escolha **Criar**.

    ![Iniciando a Entrega Contínua](_img/azure-devops-project-github/fullbrowser.png)

1. Selecione **.NET** e, em seguida, escolha **Avançar**.

1. Para **Escolher uma estrutura de aplicativo**, selecione **ASP.NET**, depois selecione **Avançar**. 

1. A estrutura do aplicativo, escolhida nas etapas anteriores, determina o tipo de destino de implantação do serviço do Azure disponível aqui.  Selecione a **Máquina Virtual**e, em seguida, escolha **Avançar**.

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Configurar o Azure DevOps Services e uma assinatura do Azure

1. Crie uma **nova** organização do Azure DevOps Services ou escolha uma organização **existente**.  Escolha um **nome** para o projeto do Azure DevOps.  

1. Selecione seus **Serviços de assinatura do Azure**.  Opcionalmente, você pode selecionar o link **Alterar** e, em seguida, inserir mais detalhes de configuração, como alterar a localização dos recursos do Azure.
 
1. Insira um **Nome da máquina Virtual**, **Nome de usuário**, e **Senha** para o novo recurso de máquina virtual do Azure e, em seguida, escolha **Concluído**.

1. Levará vários minutos para que a máquina virtual do Azure fique pronta.  Um aplicativo de exemplo ASP.NET é configurado em um repositório em sua organização do Azure DevOps Services, um build e uma versão são executados e seu aplicativo é implantado na VM do Azure recém-criada.  

    Depois de concluído, o **painel do projeto** do Azure DevOps é carregado no portal do Azure.  Você também pode navegar até o **Painel de projeto de DevOps do Azure** diretamente de **Todos os recursos** no **portal do Azure**.  

    Esse painel oferece visibilidade no seu **repositório de código** do Azure DevOps Services, **pipeline de CI/CD do Azure** e no seu **aplicativo no Azure** em execução.    

    ![Exibição Painel](_img/azure-devops-project-vms/dashboardnopreview.png)

1. O projeto do Azure DevOps configura automaticamente um build de CI e dispara um gatilho que implanta automaticamente qualquer alteração de código no seu repositório.  Você pode também configurar opções adicionais no Azure DevOps.  À direita do painel, selecione **Procurar** para exibir o aplicativo em execução.
    
## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Examinar o pipeline de CI do Azure DevOps Services
 
O Azure DevOps Project configura automaticamente um pipeline completo de CI/CD do Azure em sua organização do Azure DevOps Services.  É possível explorar e personalizar o pipeline.  Siga as etapas abaixo para se familiarizar com o pipeline de build do Azure DevOps Services.

1. Selecione **Pipelines de Build** na **parte superior** do **painel Azure DevOps Project**.  Esse link abre uma guia do navegador e abre o pipeline de build do Azure DevOps Services para seu novo projeto.

1. Mova o cursor do mouse para a direita do pipeline de build, ao lado do campo **Status**. Selecione as **reticências** que aparecem.  Essa ação abre um menu no qual você pode executar várias atividades, como **enfileirar um novo build**, **pausar um build** e **editar o pipeline de build**.

1. Selecione **Editar**.

1. Nessa exibição, **examine as várias tarefas** para o pipeline de build.  O build executa várias tarefas, como buscar fontes no repositório Git do Azure DevOps Services, restaurar dependências e publicar as saídas usadas nas implantações.

1. Na parte superior do pipeline de build, selecione o **nome do pipeline de build**.

1. Altere o **nome** do pipeline de build para algo mais descritivo.  Selecione **Salvar e pôr na fila**, depois selecione **Salvar**.

1. Embaixo do nome do pipeline de build, selecione **Histórico**.  Você verá uma trilha de auditoria das alterações recentes do build.  O Azure DevOps Services controla as alterações feitas no pipeline de build e permite que você compare as versões.

1. Selecione **Gatilhos**.  O projeto do Azure DevOps criou automaticamente um gatilho de CI e cada confirmação no repositório inicia um novo build.  Você pode optar por incluir ou excluir os branches do processo de CI.

1. Selecione **Retenção**.  Com base em seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

## <a name="examine-the-azure-devops-services-cd-pipeline"></a>Examinar o pipeline de CD do Azure DevOps Services

O Azure DevOps Project cria e configura automaticamente as etapas necessárias para implantar a organização do Azure DevOps Services na sua assinatura do Azure.  Essas etapas incluem a configuração de uma conexão de serviço do Azure para autenticar o Azure DevOps Services em sua assinatura do Azure.  A automação também cria um pipeline de CD do Azure DevOps Services, e isso fornece a CD para a máquina virtual do Azure.  Siga as etapas abaixo para saber mais sobre o pipeline de CD do Azure DevOps Services.

1. Selecione **Compilação e Versão**, em seguida, escolha **Versões**.  O projeto do Azure DevOps criou um pipeline de lançamento do Azure DevOps Services para gerenciar implantações no Azure.

1. No lado esquerdo do navegador, selecione as **reticências** ao lado do pipeline de lançamento e escolha **Editar**.

1. O pipeline de lançamento contém um **pipeline** que define o processo de lançamento.  Em **Artefatos**, selecione **Soltar**.  O pipeline de build examinado nas etapas anteriores produz a saída usada para o artefato. 

1. À direita do ícone **Soltar**, selecione o **ícone** **Gatilho de implantação contínua** (o qual aparece no formato de raio).  Esse pipeline de lançamento tem um gatilho de CD habilitado.  O gatilho inicia uma implantação sempre que houver um novo artefato de build.  Opcionalmente, você pode desabilitar o gatilho para que suas implantações exijam uma execução manual. 

1. No lado esquerdo do navegador, selecione **Tarefas** e depois escolha seu **ambiente**.  

1. As tarefas são as atividades que seu processo de implantação executa e são agrupadas em **Fases**.  Há duas **Fases** para esse pipeline de lançamento.  A primeira fase contém uma tarefa de **Implantação de grupo de recursos do Azure** que configura VM para implantação e adiciona a nova VM a um **Grupo de implantação do Azure DevOps**.  O grupo de implantação de VM no Azure DevOps gerencia grupos lógicos de computadores de **destino de implantação**.

1. Nesta segunda fase, uma tarefa **Gerenciar o aplicativo Web IIS** foi criada para criar um site do IIS na VM.  Uma segunda tarefa **Implantar o aplicativo Web IIS** foi criada para implantar o site.

1. No lado direito do navegador, selecione **Exibir versões**.  Essa exibição mostra um histórico de versões.

1. Selecione as **reticências** ao lado de uma das versões e escolha **Abrir**.  Há vários menus para explorar nessa exibição, como um **resumo da versão**, **itens de trabalho associados** e **Testes**.

1. Selecione **Confirmações**.  Essa exibição mostra as confirmações de código associadas à implantação específica. Compare as versões para exibir as diferenças de confirmação entre implantações.

1. Selecione **Logs**.  Os logs contêm informações úteis sobre o processo de implantação.  Eles podem ser exibidos durante e após as implantações.

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Confirmar alterações no Azure DevOps Services e implantar automaticamente no Azure 

Agora você está pronto para colaborar com uma equipe em um aplicativo com um processo de CI/CD que implanta automaticamente o trabalho mais recente em seu site da Web.  Cada alteração do repositório Git do Azure DevOps Services inicia um build no Azure DevOps Services e um pipeline de CD do Azure DevOps Services executa uma implantação para a sua VM do Azure.  Siga as etapas abaixo, ou use outras técnicas para confirmar as alterações no repositório.  As alterações de código iniciam o processo de CI/CD e implantam automaticamente suas novas alterações no site do IIS na VM do Azure.

1. Selecione **Código**, no menu do Azure DevOps Services e navegue até o seu repositório.

1. Navegue até o diretório **Views\Home**, depois selecione as **reticências** ao lado do arquivo **Index.cshtml** e escolha **Editar**.

1. Faça uma alteração no arquivo, como algum texto dentro de uma das **marcas div**.  No canto superior direito, selecione **Confirmar**.  Selecione **Confirmar** novamente para efetuar push da sua alteração. 

1. Em alguns instantes, um **build é iniciado no Azure DevOps Services** e uma versão é executada para implantar as alterações.  Monitore o **status do build** com o painel do Projeto do DevOps ou no navegador com sua organização do Azure DevOps Services.

1. Quando a versão for concluída, **atualize seu aplicativo** no navegador para verificar se você vê suas alterações.

## <a name="configure-azure-application-insights-monitoring"></a>Configurar o monitoramento do Application Insights

Com o Azure Application Insights, você pode monitorar facilmente o aplicativo quanto à sua disponibilidade e uso.  O projeto do Azure DevOps configurou automaticamente um recurso do Application Insights para seu aplicativo.  Além disso você pode configurar vários alertas e recursos de monitoramento conforme necessário.

1. Navegue até o painel **Projeto de DevOps do Azure** no **portal do Azure**.  Na parte inferior direita do painel, escolha o link do **Application Insights** para seu aplicativo.

1. A folha do **Application Insights** abre no portal do Azure.  Essa exibição contém informações de uso, desempenho e monitoramento da disponibilidade do seu aplicativo.

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Selecione **Intervalo de tempo** e, em seguida, escolha **última hora**.  Selecione **Atualizar** para filtrar os resultados.  Agora você verá todas as atividades dos últimos 60 minutos.  Selecione o **x** para sair do intervalo de tempo.

1. Você pode encontrar **Alertas** e vários outros links úteis na parte superior do painel.  Selecione **Alertas**, em seguida, selecione **+ Adicionar alerta de métrica**.

1. Insira um **Nome** para o alerta.

1. O alerta padrão é para um **tempo de resposta do servidor maior do que 1 segundo**.  Selecione o menu suspenso **Métrica** para examinar as várias métricas de alerta.  Por exemplo, você pode configurar o **tempo de execução de solicitação do ASP.NET** para um aplicativo ASP.NET.  Você pode configurar facilmente vários alertas para melhorar os recursos de monitoramento do seu aplicativo.

1. Marque a caixa de seleção para **Notificar por email proprietários, colaboradores e leitores**.  Opcionalmente, você pode executar ações adicionais quando um alerta é disparado, executando um aplicativo lógico do Azure.

1. Selecione **Ok** para criar o alerta.  Em poucos instantes, o alerta será exibido como ativo no painel.  **Saia** da área de Alertas e navegue de volta até a **folha do Application Insights**.

1. Selecione **Disponibilidade**, em seguida, selecione **+ Adicionar teste**. 

1. Insira um **Nome do teste**, em seguida, escolha **Criar**.  Isso criará um teste de ping simples para verificar a disponibilidade do seu aplicativo.  Depois de alguns minutos, os resultados de teste estão disponíveis e o painel do Application Insights exibe um status de disponibilidade.

## <a name="clean-up-resources"></a>Limpar recursos

 > [!NOTE]
 > As etapas a seguir excluirão os recursos permanentemente.  Só use essa funcionalidade depois de ler os prompts com cuidado.

Caso esteja fazendo testes, é possível limpar os recursos para evitar o acúmulo de encargos de cobrança.  Quando não for mais necessário, você pode excluir a máquina virtual do Azure e os recursos relacionados criados nesse tutorial usando a funcionalidade **Excluir** no painel do Projeto de DevOps do Azure.  **Tenha cuidado**, já que a exclusão de funcionalidade destrói os dados criados pelo projeto de DevOps do Azure no Azure e no Azure DevOps e você não poderá recuperá-los depois disso.

1. No **portal do Azure**, navegue até o **projeto de DevOps do Azure**.
2. No lado **superior direito** do painel, selecione **Excluir**.  Depois de ler o prompt, selecione **Sim** à **excluir permanentemente** os recursos.

## <a name="next-steps"></a>Próximas etapas

Como opção, você pode modificar esses pipelines de build e de lançamento para atender às necessidades de sua equipe. Você também pode usar esse padrão de CI/CD como um modelo para seus outros projetos.  Você aprendeu como:

> [!div class="checklist"]
> * Criar um projeto do Azure DevOps para um aplicativo ASP.NET
> * Configurar o Azure DevOps Services e uma assinatura do Azure 
> * Examinar o pipeline de CI do Azure DevOps Services
> * Examinar o pipeline de CD do Azure DevOps Services
> * Confirmar alterações no Azure DevOps Services e implantar automaticamente no Azure
> * Configurar o monitoramento do Application Insights
> * Limpar recursos

Para saber mais sobre o pipeline de CI/CD do Azure, consulte este tutorial:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
