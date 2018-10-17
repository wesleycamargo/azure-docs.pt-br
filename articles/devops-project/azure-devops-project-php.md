---
title: Criar um pipeline de CI/CD para PHP com o Projeto de DevOps do Azure | Início Rápido
description: O Projeto de DevOps facilita o uso inicial do Azure. Ele ajuda você a iniciar um aplicativo em um serviço do Azure de sua escolha em algumas etapas rápidas.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
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
ms.openlocfilehash: cb0ec777a4ab55897ef898d3c8a77b4ecaf9c5eb
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47409619"
---
# <a name="create-a-cicd-pipeline-for-php-with-the-azure-devops-project"></a>Criar um pipeline de CI/CD para PHP com o Projeto de DevOps do Azure

O Projeto de DevOps do Azure apresenta uma experiência simplificada que cria recursos do Azure e configura um pipeline de integração contínua (CI) e entrega contínua (CD) para seu aplicativo PHP no Azure DevOps Services.  

Caso não tenha uma assinatura do Azure, você pode obter uma gratuita via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

O Azure DevOps Project cria um pipeline de CI/CD no Azure.  Você pode criar uma **nova organização do Azure DevOps Services** gratuita ou usar uma **organização existente**.  O projeto de DevOps também cria **recursos do Azure** na **assinatura do Azure** de sua escolha.

1. Entre no [portal do Microsoft Azure](https://portal.azure.com).

1. Escolha o ícone **Criar um recurso** na barra de navegação à esquerda e procure o **Projeto do DevOps**.  Escolha **Criar**.

    ![Iniciando a configuração de Entrega Contínua](_img/azure-devops-project-php/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selecionar um aplicativo de exemplo e o serviço do Azure

1. Selecione o aplicativo de exemplo do **PHP**.  Os exemplos do PHP incluem uma opção de várias estruturas de aplicativo.

1. A estrutura de exemplo padrão é **Laravel**. Mantenha a configuração padrão e selecione **Avançar**.  

1. **Aplicativo Web para Contêineres** é o destino de implantação padrão.  A estrutura do aplicativo, escolhida nas etapas anteriores, determina o tipo de destino de implantação do serviço do Azure disponível aqui.  Mantenha o serviço padrão e selecione **Avançar**.
 
## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Configurar o Azure DevOps Services e uma assinatura do Azure 

1. Crie uma **nova** organização do Azure DevOps Services ou escolha uma organização **existente**.  Escolha um **nome** para o projeto do Azure DevOps.  Selecione sua **assinatura do Azure**, **local** e escolha um **nome** para seu aplicativo.  Quando terminar, escolha **Concluído**.

1. Em alguns minutos, o **painel do Azure DevOps Project** é carregado no portal do Azure.  Um aplicativo de exemplo é configurado em um repositório em sua organização do Azure DevOps Services, é executado um build e seu aplicativo é implantado no Azure.  Esse painel oferece visibilidade no seu **repositório de código**, **pipeline CI/CD do Azure** e seu **aplicativo no Azure**.  À direita do painel, selecione **Procurar** para exibir o aplicativo em execução.

    ![Exibição Painel](_img/azure-devops-project-php/dashboardnopreview.png) 
    
O Azure DevOps Project configura automaticamente um build de CI e libera o gatilho.  Agora você está pronto para colaborar com uma equipe em um aplicativo PHP com um processo de CI/CD que implanta automaticamente o trabalho mais recente em seu site da Web.

## <a name="commit-code-changes-and-execute-cicd"></a>Confirmar as alterações de código e executar CI/CD

O Azure DevOps Project criou um repositório Git na sua organização do Azure DevOps Services ou na conta do GitHub.  Siga as etapas abaixo para exibir o repositório e fazer alterações de código no seu aplicativo.

1. No lado esquerdo do painel do Projeto de DevOps, selecione o link para seu branch **mestre**.  Esse link abre uma exibição do repositório de Git recém-criado.

1. Para exibir a URL de clone do repositório, selecione **Clone** na parte superior direita do navegador. Você pode clonar seu repositório Git no seu IDE favorito.  Nas próximas etapas, você pode usar o navegador da Web para criar e confirmar as alterações de código diretamente no branch mestre.

1. À esquerda do navegador, navegue até o arquivo **resources/views/welcome.blade.php**.

1. Selecione **Editar** e faça uma alteração de alguma parte do texto.  Por exemplo, altere partes do texto de uma das marcas div.

1. Escolha **Confirmar** e salve as alterações.

1. No seu navegador, navegue até o **Painel do Azure DevOps Project**.  Agora você deve ver uma compilação que está em andamento.  As alterações que você acabou de criar automaticamente são compiladas e implantadas por meio de um pipeline CI/CD do Azure.

## <a name="examine-the-azure-cicd-pipeline"></a>Examinar o pipeline de CI/CD do Azure

O Azure DevOps Project configura automaticamente um pipeline completo de CI/CD do Azure em sua organização do Azure DevOps Services.  Explore e personalize o pipeline conforme necessário.  Siga as etapas abaixo para se familiarizar com os pipelines de build e de lançamento do Azure DevOps Services.

1. Selecione **Pipelines de Build** na **parte superior** do painel do Azure DevOps Project.  Esse link abre uma guia do navegador e abre o pipeline de build do Azure DevOps Services para seu novo projeto.

1. Mova o cursor do mouse para a direita do pipeline de build, ao lado do campo **Status**. Selecione as **reticências** que aparecem.  Essa ação abre um menu no qual você pode iniciar várias atividades, como enfileirar um novo build, pausar um build e editar o pipeline de build.

1. Selecione **Editar**.

1. Nessa exibição, **examine as várias tarefas** para o pipeline de build.  A compilação executa várias tarefas, como efetuar fetch das fontes do repositório Git, restaurar dependências e publicar saídas usadas para implantações.

1. Na parte superior do pipeline de build, selecione o **nome do pipeline de build**.

1. Altere o **nome** do pipeline de build para algo mais descritivo.  Selecione **Salvar e pôr na fila**, depois selecione **Salvar**.

1. Embaixo do nome do pipeline de build, selecione **Histórico**.  Você verá uma trilha de auditoria das alterações recentes do build.  O Azure DevOps Services controla as alterações feitas no pipeline de build e permite que você compare as versões.

1. Selecione **Gatilhos**.  O projeto do Azure DevOps criou automaticamente um gatilho de CI e cada confirmação no repositório inicia um novo build.  Você pode optar por incluir ou excluir os branches do processo de CI.

1. Selecione **Retenção**.  Com base em seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

1. Selecione **Compilação e Versão**, em seguida, escolha **Versões**.  O projeto do Azure DevOps criou um pipeline de lançamento do Azure DevOps Services para gerenciar implantações no Azure.

1. No lado esquerdo do navegador, selecione as **reticências** ao lado do pipeline de lançamento e escolha **Editar**.

1. O pipeline de lançamento contém um **pipeline** que define o processo de lançamento.  Em **Artefatos**, selecione **Soltar**.  O pipeline de build examinado nas etapas anteriores produz a saída usada para o artefato. 

1. No lado direito do ícone **Soltar**, selecione o **Gatilho de implantação contínua**.  Esse pipeline de lançamento tem um gatilho de CD habilitado, que executa uma implantação sempre que houver um novo artefato de build disponível.  Opcionalmente, você pode desabilitar o gatilho para que suas implantações exijam uma execução manual. 

1. À esquerda do navegador, selecione **Tarefas**.  As tarefas são as atividades que seu processo de implantação realiza.  Neste exemplo, uma tarefa foi criada para ser implantada no **serviço de Aplicativo do Azure**.

1. No lado direito do navegador, selecione **Exibir versões**.  Essa exibição mostra um histórico de versões.

1. Selecione as **reticências** ao lado de uma das versões e escolha **Abrir**.  Há vários menus para explorar nessa exibição, como um resumo da versão, itens de trabalho associados e testes.

1. Selecione **Confirmações**.  Essa exibição mostra as confirmações de código associadas à implantação específica. 

1. Selecione **Logs**.  Os logs contêm informações úteis sobre o processo de implantação.  Eles podem ser exibidos durante e após as implantações.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode excluir o serviço de Aplicativo Azure e recursos relacionados criados nesse início rápido usando a funcionalidade **Excluir** no painel do Projeto de DevOps do Azure.

## <a name="next-steps"></a>Próximas etapas

Depois de configurar seu processo de CI/CD neste início rápido, um pipeline de build e um pipeline de lançamento foram criados automaticamente no seu Azure DevOps Project. Você pode modificar esses pipelines de build e de lançamento para atender às necessidades de sua equipe. Para saber mais, veja este tutorial:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
