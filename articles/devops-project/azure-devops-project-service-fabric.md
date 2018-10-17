---
title: Implantar seu aplicativo ASP.NET Core no Azure Service Fabric com o Projeto do Azure DevOps | Tutorial do Azure DevOps Services
description: O Projeto de DevOps facilita o uso inicial do Azure. O Projeto do Azure DevOps facilita a implantação do aplicativo ASP.NET Core com o Azure Service Fabric em algumas etapas rápidas.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 6a0539b2213b99e09021a4f90d914172eac62560
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44300386"
---
# <a name="tutorial--deploy-your-aspnet-core-app-to-azure-service-fabric-with-the-azure-devops-project"></a>Tutorial: Implantar seu aplicativo ASP.NET Core no Azure Service Fabric com o projeto de DevOps do Azure

O projeto de DevOps do Azure apresenta uma experiência simplificada na qual você traz o seu código existente e o repositório Git ou escolhe um dos aplicativos de exemplo para criar um pipeline de CI (integração contínua) e CD (entrega contínua) para o Azure.  O Projeto do DevOps cria automaticamente recursos do Azure, como o Azure Service Fabric, cria e configura um pipeline de lançamento no Azure DevOps que configura um pipeline de CI/CD e cria um recurso do Azure Application Insights para monitoramento.

Você vai:

> [!div class="checklist"]
> * Criar um projeto do Azure DevOps para um aplicativo ASP.NET Core e o Service Fabric
> * Configurar o Azure DevOps Services e uma assinatura do Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações no Git e implantar automaticamente no Azure
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Você pode obter uma gratuita por meio do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-core-app-and-service-fabric"></a>Criar um projeto do Azure DevOps para um aplicativo ASP.NET Core e o Service Fabric

O Projeto do Azure DevOps cria um pipeline de CI/CD.  Você pode criar uma nova **organização do Azure DevOps** ou usar uma **organização existente**.  O projeto de DevOps do Azure também cria **recursos do Azure** como um cluster do Service Fabric na **assinatura do Azure** de sua escolha.

1. Entre no [portal do Microsoft Azure](https://portal.azure.com).

1. Escolha o ícone **Criar um recurso** na barra de navegação à esquerda e procure o **Projeto do DevOps**.  Escolha **Criar**.

    ![Iniciando a Entrega Contínua](_img/azure-devops-project-service-fabric/fullbrowser.png)

1. Selecione **.NET** e, em seguida, escolha **Avançar**.

1. Para **Escolher uma estrutura de aplicativo**, selecione **ASP.NET Core**, depois selecione **Avançar**.

1. Selecione **Cluster do Service Fabric**, depois escolha **Avançar**.  

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Configurar o Azure DevOps Services e uma assinatura do Azure

1. Crie uma **nova** organização do Azure DevOps Services ou escolha uma organização **existente**.  Escolha um **nome** para o projeto do Azure DevOps.  

1. Selecione sua **assinatura do Azure**.

1. Selecione o link **Alterar** para ver definições adicionais de configuração do Azure e identificar o **tamanho da máquina virtual do nó** e o **sistema operacional** do **cluster do Service Fabric**.  Há várias opções aqui para configurar o tipo e o local dos serviços do Azure.
 
1. Saia da área de configuração do Azure e escolha **Concluído**.

1. Esse processo demora alguns minutos para ser concluído.  Um aplicativo de exemplo ASP.NET Core é configurado em um repositório em sua organização do Azure DevOps Services, um cluster do Service Fabric é criado, um pipeline de CI/CD é executado e seu aplicativo é implantado no Azure.  

    Depois de concluído, o **painel do Projeto** do Azure DevOps é carregado no portal do Azure.  Você também pode navegar até o **Painel de projeto de DevOps do Azure** diretamente de **Todos os recursos** no **portal do Azure**.  

    Esse painel oferece visibilidade para o **repositório de código** do Azure DevOps Services, o **pipeline de CI/CD do Azure DevOps Services** e o **cluster do Service Fabric**.  Você pode também configurar opções adicionais no Azure DevOps Services.  No lado direito do painel, selecione **Procurar** para exibir seu aplicativo em execução.

## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Examinar o pipeline de CI do Azure DevOps Services

O Projeto do Azure DevOps configura automaticamente um pipeline de CI/CD do Azure DevOps Services em sua organização do Azure DevOps Services.  É possível explorar e personalizar o pipeline.  Siga as etapas abaixo para se familiarizar com o pipeline de build do Azure DevOps Services.

1. Navegue até o **painel do projeto de DevOps do Azure**.

1. Selecione **Pipelines de Build** na **parte superior** do **painel do Projeto do Azure DevOps**.  Esse link abre uma guia do navegador e abre o pipeline de build do Azure DevOps Services para seu novo projeto.

1. Mova o cursor do mouse para a direita do pipeline de build, ao lado do campo **Status**. Selecione as **reticências** que aparecem.  Essa ação abre um menu no qual você pode iniciar várias atividades, como **enfileirar um novo build**, **pausar um build** e **editar o pipeline de build**.

1. Selecione **Editar**.

1. Nessa exibição, **examine as várias tarefas** para o pipeline de build.  O build executa várias tarefas, como buscar fontes no repositório Git do Azure DevOps Services, restaurar dependências e publicar as saídas usadas nas implantações.

1. Na parte superior do pipeline de build, selecione o **nome do pipeline de build**. 

1. Embaixo do nome do pipeline de build, selecione **Histórico**.  Você verá uma trilha de auditoria das alterações recentes do build.  O Azure DevOps Services controla as alterações feitas no pipeline de build e permite que você compare as versões.

1. Selecione **Gatilhos**.  O projeto do Azure DevOps criou automaticamente um gatilho de CI e cada confirmação no repositório inicia um novo build.  Você pode optar por incluir ou excluir os branches do processo de CI.

1. Selecione **Retenção**.  Com base em seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

## <a name="examine-the-azure-devops-services-cd-pipeline"></a>Examinar o pipeline de CD do Azure DevOps Services

O Projeto do Azure DevOps cria e configura automaticamente as etapas necessárias para implantar a organização do Azure DevOps Services na sua assinatura do Azure.  Essas etapas incluem a configuração de uma conexão de serviço do Azure para autenticar o Azure DevOps Services em sua assinatura do Azure.  A automação também cria um pipeline de lançamento do Azure DevOps Services, e essa versão fornece a CD para o Azure.  Siga as etapas abaixo para saber mais sobre o pipeline de lançamento do Azure DevOps Services.

1. Selecione **Compilação e Versão**, em seguida, escolha **Versões**.  O projeto do Azure DevOps criou um pipeline de lançamento do Azure DevOps Services para gerenciar implantações no Azure.

1. No lado esquerdo do navegador, selecione as **reticências** ao lado do pipeline de lançamento e escolha **Editar**.

1. O pipeline de lançamento contém um **pipeline** que define o processo de lançamento.  Em **Artefatos**, selecione **Soltar**.  O pipeline de build examinado nas etapas anteriores produz a saída usada para o artefato. 

1. À direita do ícone **Soltar**, selecione o **ícone** **Gatilho de implantação contínua** (o qual aparece no formato de raio).  Esse pipeline de lançamento tem um gatilho de CD habilitado.  O gatilho inicia uma implantação sempre que houver um novo artefato de build.  Opcionalmente, você pode desabilitar o gatilho para que suas implantações exijam uma execução manual. 

1. No lado direito do navegador, selecione **Exibir versões**.  Essa exibição mostra um histórico de versões.

1. Selecione as **reticências** ao lado de uma das versões e escolha **Abrir**.  Há vários menus para explorar nessa exibição, como um **resumo da versão**, **itens de trabalho associados** e **Testes**.

1. Selecione **Confirmações**.  Essa exibição mostra as confirmações de código associadas à implantação específica. Você pode comparar versões para exibir as diferenças de confirmação entre implantações.

1. Selecione **Logs**.  Os logs contêm informações úteis sobre o processo de implantação.  Eles podem ser exibidos durante e após as implantações.

## <a name="commit-changes-to-git-and-automatically-deploy-to-azure"></a>Confirmar alterações no Git e implantar automaticamente no Azure 

 > [!NOTE]
 > As etapas a seguir testam o pipeline de CI/CD com uma alteração de texto simples ao seu aplicativo Web.

Agora você está pronto para colaborar com uma equipe em um aplicativo com um processo de CI/CD que implanta automaticamente o trabalho mais recente em seu site da Web.  Cada alteração no repositório Git inicia um build, e uma versão implanta as alterações no Azure.  Siga as etapas abaixo, ou use outras técnicas para confirmar as alterações no repositório.  Por exemplo, você pode **clonar** o repositório Git em sua ferramenta favorita ou IDE e, em seguida, efetuar push nas alterações para este repositório.

1. Selecione **Código**, **Arquivos** no menu do Azure DevOps Services e navegue até seu repositório.
1. Navegue até o diretório **Views\Home**, depois selecione as **reticências** ao lado do arquivo **Index.cshtml** e escolha **Editar**.

1. Faça uma alteração no arquivo, como algum texto dentro de uma das **marcas div**.  No canto superior direito, selecione **Confirmar**.  Selecione **Confirmar** novamente para efetuar push da sua alteração. 

1. Em alguns instantes, um **build é iniciado** e uma versão é executada para implantar as alterações.  Você pode monitorar o **status do build** com o painel do Projeto do DevOps ou no navegador com o registro em log em tempo real do Azure DevOps Services.

1. Quando a versão for concluída, **atualize seu aplicativo** no navegador para verificar se você vê suas alterações.

## <a name="clean-up-resources"></a>Limpar recursos

 > [!NOTE]
 > As etapas a seguir excluirão os recursos permanentemente.  Só use essa funcionalidade depois de ler os prompts com cuidado.

Caso esteja fazendo testes, é possível limpar os recursos para evitar o acúmulo de encargos de cobrança.  Quando não for mais necessário, você pode excluir o cluster do Azure Service Fabric e os recursos relacionados criados nesse tutorial usando a funcionalidade **Excluir** no painel do projeto de DevOps do Azure.  **Tenha cuidado**, pois a funcionalidade de exclusão destrói os dados criados pelo Projeto do Azure DevOps no Azure e no Azure DevOps Services e você não poderá recuperá-los depois disso.

1. No **portal do Azure**, navegue até o **projeto de DevOps do Azure**.
2. No lado **superior direito** do painel, selecione **Excluir**.  Depois de ler o prompt, selecione **Sim** à **excluir permanentemente** os recursos.

## <a name="next-steps"></a>Próximas etapas

Opcionalmente, você pode modificar o pipeline de CI/CD do Azure para atender às necessidades da sua equipe. Você também pode usar esse padrão de CI/CD como um modelo para seus outros projetos.  Você aprendeu como:

> [!div class="checklist"]
> * Criar um projeto do Azure DevOps para um aplicativo ASP.NET Core e o Service Fabric
> * Configurar o Azure DevOps Services e uma assinatura do Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações no Git e implantar automaticamente no Azure
> * Limpar recursos

Para saber mais sobre o Service Fabric e os microsserviços, veja a seguir:

> [!div class="nextstepaction"]
> [Use uma abordagem de microsserviço para compilar aplicativos](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
