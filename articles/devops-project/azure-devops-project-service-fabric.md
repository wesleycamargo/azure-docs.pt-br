---
title: Implantar seu aplicativo ASP.NET Core no Azure Service Fabric com o projeto de DevOps do Azure | Tutorial do VSTS
description: O projeto de DevOps facilita o uso inicial do Azure. O projeto de DevOps do Azure torna fácil implantar seu aplicativo ASP.NET Core com o Azure Service Fabric em algumas etapas rápidas.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 2fd1fc968eb6b61d7378dbc0efa48f2f7eb2aa54
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967293"
---
# <a name="tutorial--deploy-your-aspnet-core-app-to-azure-service-fabric-with-the-azure-devops-project"></a>Tutorial: Implantar seu aplicativo ASP.NET Core no Azure Service Fabric com o projeto de DevOps do Azure

O projeto de DevOps do Azure apresenta uma experiência simplificada na qual você traz o seu código existente e o repositório Git ou escolhe um dos aplicativos de exemplo para criar um pipeline de CI (integração contínua) e CD (entrega contínua) para o Azure.  O projeto de DevOps cria automaticamente recursos do Azure, como o Azure Service Fabric, cria e configura um pipeline de versão no VSTS que inclui uma definição de versão de CI, configura uma definição de versão de CD e cria um recurso do Azure Application Insights para monitoramento.

Você vai:

> [!div class="checklist"]
> * Criar um projeto de DevOps do Azure para um aplicativo ASP.NET Core e o Service Fabric
> * Configurar o VSTS e uma assinatura do Azure 
> * Examinar a definição de build da CI do VSTS
> * Examinar a definição do Release Management da CD do VSTS
> * Confirmar alterações no VSTS e implantar automaticamente no Azure
> * Limpar recursos

## <a name="prerequisites"></a>pré-requisitos

* Uma assinatura do Azure. Você pode obter uma gratuita por meio do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-core-app-and-service-fabric"></a>Criar um projeto de DevOps do Azure para um aplicativo ASP.NET Core e o Service Fabric

O projeto de DevOps do Azure cria um pipeline de CI/CD no VSTS.  Você pode criar uma **nova conta do VSTS** ou usar uma **conta existente**.  O projeto de DevOps do Azure também cria **recursos do Azure** como um cluster do Service Fabric na **assinatura do Azure** de sua escolha.

1. Entre no [Portal do Microsoft Azure](https://portal.azure.com).

1. Escolha o ícone **Criar um recurso** na barra de navegação à esquerda, depois procure o **projeto de DevOps**.  Escolha **Criar**.

    ![Iniciando a entrega contínua](_img/azure-devops-project-service-fabric/fullbrowser.png)

1. Selecione **.NET** e, em seguida, escolha **Avançar**.

1. Para **Escolher uma estrutura de aplicativo**, selecione **ASP.NET Core**, depois selecione **Avançar**.

1. Selecione **Cluster do Service Fabric**, depois escolha **Avançar**.  

## <a name="configure-vsts-and-an-azure-subscription"></a>Configurar o VSTS e uma assinatura do Azure

1. Crie uma **nova** conta do VSTS gratuita ou use uma conta **existente**.  Escolha um **nome** para seu projeto do VSTS.  

1. Selecione sua **assinatura do Azure**.

1. Selecione o link **Alterar** para ver definições adicionais de configuração do Azure e identificar o **tamanho da máquina virtual do nó** e o **sistema operacional** do **cluster do Service Fabric**.  Há várias opções aqui para configurar o tipo e o local dos serviços do Azure.
 
1. Saia da área de configuração do Azure e escolha **Concluído**.

1. Esse processo demora alguns minutos para ser concluído.  Um aplicativo de exemplo ASP.NET Core é configurado em um repositório em sua conta do VSTS, um cluster do Service Fabric é criado, um build e uma versão são executados e seu aplicativo é implantado no Azure.  

    Depois de concluído, o **painel do projeto** do Azure DevOps é carregado no portal do Azure.  Você também pode navegar até o **Painel de projeto de DevOps do Azure** diretamente de **Todos os recursos** no **portal do Azure**.  

    Esse painel oferece visibilidade no **repositório de código** do VSTS, **pipeline CI/CD do VSTS** e **cluster do Service Fabric**.  Você pode configurar opções adicionais no VSTS.  No lado direito do painel, selecione **Procurar** para exibir seu aplicativo em execução.

## <a name="examine-the-vsts-ci-build-definition"></a>Examinar a definição de build da CI do VSTS

O projeto de DevOps do Azure configura automaticamente um pipeline de CI/CD do VSTS completo em sua conta do VSTS.  É possível explorar e personalizar o pipeline.  Siga as etapas abaixo para se familiarizar com a definição de build do VSTS.

1. Navegue até o **painel do projeto de DevOps do Azure**.

1. Selecione **Pipelines de Build** na **parte superior** do **painel do projeto de DevOps do Azure**.  Esse link abre uma guia do navegador e abre a definição de build do VSTS para seu novo projeto.

1. Mova o cursor do mouse para a direita da definição de compilação, ao lado do campo **Status**. Selecione as **reticências** que aparecem.  Essa ação abre um menu no qual você pode iniciar várias atividades, como **pôr um novo build na fila**, **pausar um build** e **editar a definição de build**.

1. Selecione **Editar**.

1. Nessa exibição, **examine as várias tarefas** para sua definição de build.  A compilação executa várias tarefas, como efetuar fetch das fontes no repositório Git do VSTS, restaurar dependências e publicar as saídas usadas para as implantações.

1. Na parte superior da definição de compilação, selecione o **nome da definição de compilação**.

1. Altere o **nome** da sua definição de compilação para algo mais descritivo.  Selecione **Salvar e pôr na fila**, depois selecione **Salvar**.

1. No nome da definição de compilação, selecione **Histórico**.  Você verá uma trilha de auditoria das alterações recentes do build.  O VSTS controla todas as alterações feitas na definição de build e permite comparar as versões.

1. Selecione **Gatilhos**.  O projeto de DevOps do Azure criou automaticamente um gatilho de CI e cada confirmação no repositório inicia uma nova compilação.  Você pode optar por incluir ou excluir os branches do processo de CI.

1. Selecione **Retenção**.  Com base em seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

## <a name="examine-the-vsts-cd-release-management-definition"></a>Examinar a definição do Release Management da CD do VSTS

O projeto de DevOps do Azure cria e configura automaticamente as etapas necessárias para implantar sua conta do VSTS na sua assinatura do Azure.  Essas etapas incluem a configuração de uma conexão de serviço do Azure para autenticar o VSTS para sua assinatura do Azure.  A automação também cria uma definição de versão do VSTS, a qual fornece a CD para o Azure.  Siga as etapas abaixo para examinar mais sobre a definição de versão do VSTS.

1. Selecione **Compilação e Versão**, em seguida, escolha **Versões**.  O projeto de DevOps do Azure criou uma definição da versão do VSTS para gerenciar implantações no Azure.

1. No lado esquerdo do navegador, selecione as **reticências** ao lado de sua definição de versão, em seguida, escolha **Editar**.

1. A definição de versão contém um **pipeline**, que define o processo de lançamento.  Em **Artefatos**, selecione **Soltar**.  A definição da compilação examinada nas etapas anteriores produz a saída usada para o artefato. 

1. À direita do ícone **Soltar**, selecione o **ícone** **Gatilho de implantação contínua** (o qual aparece no formato de raio).  Essa definição da versão tem um gatilho de CD habilitado.  O gatilho inicia uma implantação sempre que houver um novo artefato de build.  Opcionalmente, você pode desabilitar o gatilho para que suas implantações exijam uma execução manual. 

1. No lado direito do navegador, selecione **Exibir versões**.  Essa exibição mostra um histórico de versões.

1. Selecione as **reticências** ao lado de uma das versões e escolha **Abrir**.  Há vários menus para explorar nessa exibição, como um **resumo da versão**, **itens de trabalho associados** e **Testes**.

1. Selecione **Confirmações**.  Essa exibição mostra as confirmações de código associadas à implantação específica. Você pode comparar versões para exibir as diferenças de confirmação entre implantações.

1. Selecione **Logs**.  Os logs contêm informações úteis sobre o processo de implantação.  Eles podem ser exibidos durante e após as implantações.

## <a name="commit-changes-to-vsts-and-automatically-deploy-to-azure"></a>Confirmar alterações no VSTS e implantar automaticamente no Azure 

 > [!NOTE]
 > As etapas a seguir testam o pipeline de CI/CD com uma alteração de texto simples ao seu aplicativo Web.

Agora você está pronto para colaborar com uma equipe em um aplicativo com um processo de CI/CD que implanta automaticamente o trabalho mais recente em seu site da Web.  Cada alteração do repositório git do VSTS inicia um build no VSTS, e uma definição de Release Management do VSTS implanta as alterações no Azure.  Siga as etapas abaixo, ou use outras técnicas para confirmar as alterações no repositório.  Por exemplo, você pode **clonar** o repositório Git em sua ferramenta favorita ou IDE e, em seguida, efetuar push nas alterações para este repositório.

1. Selecione **Código** e depois **Arquivos** no menu do VSTS e navegue até seu repositório.
1. Navegue até o diretório **Views\Home**, depois selecione as **reticências** ao lado do arquivo **Index.cshtml** e escolha **Editar**.

1. Faça uma alteração no arquivo, como algum texto dentro de uma das **marcas div**.  No canto superior direito, selecione **Confirmar**.  Selecione **Confirmar** novamente para efetuar push da sua alteração. 

1. Em alguns instantes, um **build é iniciado no VSTS** e uma versão é executada para implantar as alterações.  Você pode monitorar o **status do build** com o painel do projeto de DevOps ou no navegador com sua conta do VSTS.

1. Quando a versão for concluída, **atualize seu aplicativo** no navegador para verificar se você vê suas alterações.

## <a name="clean-up-resources"></a>Limpar recursos

 > [!NOTE]
 > As etapas a seguir excluirão os recursos permanentemente.  Só use essa funcionalidade depois de ler os prompts com cuidado.

Caso esteja fazendo testes, é possível limpar os recursos para evitar o acúmulo de encargos de cobrança.  Quando não for mais necessário, você pode excluir o cluster do Azure Service Fabric e os recursos relacionados criados nesse tutorial usando a funcionalidade **Excluir** no painel do projeto de DevOps do Azure.  **Tenha cuidado**, como a exclusão de funcionalidade destrói os dados criados pelo projeto de DevOps do Azure no Azure e no VSTS e você não poderá recuperá-los depois disso.

1. No **portal do Azure**, navegue até o **projeto de DevOps do Azure**.
2. No lado **superior direito** do painel, selecione **Excluir**.  Depois de ler o prompt, selecione **Sim** à **excluir permanentemente** os recursos.

## <a name="next-steps"></a>Próximas etapas

Como opção, você pode modificar essas definições de build e de versão para atender às necessidades de sua equipe. Você também pode usar esse padrão de CI/CD como um modelo para seus outros projetos.  Você aprendeu como:

> [!div class="checklist"]
> * Criar um projeto de DevOps do Azure para um aplicativo ASP.NET Core e o Service Fabric
> * Configurar o VSTS e uma assinatura do Azure 
> * Examinar a definição de build da CI do VSTS
> * Examinar a definição do Release Management da CD do VSTS
> * Confirmar alterações no VSTS e implantar automaticamente no Azure
> * Limpar recursos

Para saber mais sobre o Service Fabric e os microsserviços, veja a seguir:

> [!div class="nextstepaction"]
> [Use uma abordagem de microsserviço para compilar aplicativos](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
