---
title: Criar um pipeline de CI/CD para Ruby on Rails com o Projeto de DevOps do Azure | Início Rápido
description: O Projeto de DevOps facilita o uso inicial do Azure. Ele ajuda a iniciar um aplicativo Web Ruby em um serviço do Azure em algumas etapas rápidas.
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
ms.openlocfilehash: 773e77e0965dfb059460faf564d8cc3d80cef3ea
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37966861"
---
# <a name="create-a-cicd-pipeline-for-ruby-on-rails-with-the-azure-devops-project"></a>Criar um pipeline de CI/CD para Ruby on Rails com o Projeto de DevOps do Azure

Configure a integração contínua (CI) e a entrega contínua (CD) para seu aplicativo Ruby on Rails com o **Projeto de DevOps do Azure**.  O projeto de DevOps do Azure simplifica a configuração inicial de uma compilação do VSTS e o pipeline de lançamento.

Caso não tenha uma assinatura do Azure, você pode obter uma gratuita via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

O Projeto de DevOps do Azure cria um pipeline de CI/CD no VSTS.  Você pode criar uma **nova conta do VSTS** ou usar uma **conta existente**.  O Projeto de DevOps do Azure também cria **recursos do Azure** na **assinatura do Azure** de sua escolha.

1. Entre no [portal do Microsoft Azure](https://portal.azure.com).

1. Escolha o ícone **Criar um recurso** na barra de navegação à esquerda, depois procure o **Projeto de DevOps**.  Escolha **Criar**.

    ![Iniciando a Entrega Contínua](_img/azure-devops-project-ruby/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selecionar um aplicativo de exemplo e o serviço do Azure

1. Selecione o aplicativo de exemplo do **Ruby**.

1. Selecione a estrutura do aplicativo **Ruby on Rails**.  Quando terminar, escolha **Avançar**.

1. **Aplicativo Web no Linux** é o destino de implantação padrão.  Opcionalmente, você pode escolher Aplicativo Web para Contêineres.  A estrutura do aplicativo, escolhida nas etapas anteriores, determina o tipo de destino de implantação do serviço do Azure disponível aqui.  Selecione o **serviço de destino** de sua escolha.  Quando terminar, escolha **Avançar**.

## <a name="configure-vsts-and-an-azure-subscription"></a>Configurar o VSTS e uma assinatura do Azure 

1. Crie uma **nova** conta do VSTS gratuita ou escolha uma conta **existente**.  Escolha um **nome** para seu projeto do VSTS.  Selecione sua **assinatura do Azure**, **local** e escolha um **nome** para seu aplicativo.  Quando terminar, escolha **Concluído**.

    ![Inserir informações do VSTS](_img/azure-devops-project-ruby/vstsazureinfo.png)

1. Em alguns minutos, o **painel do projeto** é carregado no portal do Azure.  Um aplicativo de exemplo é configurado em um repositório em sua conta do VSTS, é executada uma compilação e seu aplicativo é implantado no Azure.  Esse painel oferece visibilidade no seu **repositório de código**, **pipeline CI/CD do VSTS** e seu **aplicativo no Azure**.  À direita do painel, selecione **Procurar** para exibir o aplicativo em execução.

    ![Exibição Painel](_img/azure-devops-project-ruby/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Confirmar as alterações de código e executar CI/CD

O projeto de DevOps do Azure criou um repositório Git na sua conta do VSTS ou do GitHub.  Siga as etapas abaixo para exibir o repositório e fazer alterações de código no seu aplicativo.

1. No lado esquerdo do painel do projeto de DevOps, selecione o link para seu branch **mestre**.  Esse link abre uma exibição do repositório de Git recém-criado.

1. Para exibir a URL de clone do repositório, selecione **Clone** na parte superior direita do navegador. Você pode clonar seu repositório Git no seu IDE favorito.  Nas próximas etapas, você pode usar o navegador da Web para criar e confirmar as alterações de código diretamente no branch mestre.

1. À esquerda do navegador, vá para o arquivo **app/views/pages/home.html.erb**.

1. Selecione **Editar** e faça uma alteração em alguma parte do texto.  Por exemplo, modifique o texto dentro de uma das marcas div.

1. Escolha **Confirmar** e salve as alterações.

1. No seu navegador, navegue até o **Painel do projeto de DevOps do Azure**.  Agora você deve ver uma compilação que está em andamento.  As alterações que você acabou de criar automaticamente são compiladas e implantadas por meio de um pipeline CI/CD do VSTS.

## <a name="examine-the-vsts-cicd-pipeline"></a>Examinar o pipeline de CI/CD do VSTS

O projeto de DevOps do Azure configurou automaticamente um pipeline de CI/CD do VSTS completo em sua conta do VSTS.  Explore e personalize o pipeline conforme necessário.  Siga as etapas abaixo para se familiarizar com a compilação do VSTS e as definições da versão.

1. Selecione **Pipelines de Build** na **parte superior** do painel do projeto de DevOps do Azure.  Esse link abre uma guia do navegador e abre a definição de build do VSTS para seu novo projeto.

1. Selecione as **reticências**.  Essa ação abre um menu no qual você pode iniciar várias atividades, como pôr uma nova compilação na fila, pausar uma compilação e editar a definição de compilação.

1. Selecione **Editar**.

1. Nessa exibição, **examine as várias tarefas** para sua definição de build.  A compilação executa várias tarefas, como efetuar fetch das fontes do repositório Git, restaurar dependências e publicar saídas usadas para implantações.

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

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode excluir o serviço de Aplicativo do Azure e os recursos relacionados criados nesse início rápido usando a funcionalidade **Excluir** no painel do projeto de DevOps do Azure.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como modificar as definições de compilação e versão para atender às necessidades de sua equipe, consulte este tutorial:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
