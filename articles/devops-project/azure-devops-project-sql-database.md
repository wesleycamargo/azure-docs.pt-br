---
title: Implantar seu aplicativo ASP.NET Core e o Banco de Dados SQL do Azure com o projeto de Azure DevOps | Tutorial do Azure DevOps Services
description: O Projeto de DevOps facilita o uso inicial do Azure. O Azure DevOps Project facilita a implantação de seu ASP.NET com o Banco de Dados SQL do Azure em algumas etapas rápidas.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: d9c7c94e344daee5af87ce40ddf4dcb686696ded
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297326"
---
# <a name="tutorial--deploy-your-aspnet-app-and-azure-sql-database-with-the-azure-devops-project"></a>Tutorial: Implantar seu aplicativo ASP.NET e o Banco de Dados SQL do Azure com o projeto de DevOps do Azure

O projeto de DevOps do Azure apresenta uma experiência simplificada na qual você traz o seu código existente e o repositório Git ou escolhe um dos aplicativos de exemplo para criar um pipeline de CI (integração contínua) e CD (entrega contínua) para o Azure.  O projeto de DevOps cria automaticamente recursos do Azure, como um Banco de dados SQL do Azure, cria e configura um pipeline de lançamento no Azure DevOps que inclui um pipeline de build de CI, configura um pipeline de lançamento para CD e cria um recurso do Azure Application Insights para monitoramento.

Você vai:

> [!div class="checklist"]
> * Criar um Azure DevOps Project para um aplicativo ASP.NET e o Banco de Dados SQL do Azure
> * Configurar o Azure DevOps Services e uma assinatura do Azure 
> * Examinar o pipeline de CI do Azure DevOps Services
> * Examinar o pipeline de CD do Azure DevOps Services
> * Confirmar alterações no Azure DevOps Services e implantar automaticamente no Azure
> * Conectar com o banco de dados do SQL Server do Azure 
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Você pode obter uma gratuita por meio do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-app-and-azure-sql-database"></a>Criar um Azure DevOps Project para um aplicativo ASP.NET e o Banco de Dados SQL do Azure

O Azure DevOps Project cria um pipeline de CI/CD no Azure.  Você pode criar uma **nova organização Azure DevOps Services** ou usar uma **organização existente**.  O projeto de DevOps do Azure também cria **recursos do Azure** como um Banco de Dados SQL do Azure na **assinatura do Azure** de sua escolha.

1. Entre no [portal do Microsoft Azure](https://portal.azure.com).

1. Escolha o ícone **Criar um recurso** na barra de navegação à esquerda e procure o **Projeto do DevOps**.  Escolha **Criar**.

    ![Iniciando a Entrega Contínua](_img/azure-devops-project-github/fullbrowser.png)

1. Selecione **.NET** e, em seguida, escolha **Avançar**.

1. Para **Escolher uma estrutura de aplicativo**, selecione **ASP.NET**.

1. Selecione **Adicionar um banco de dados**, depois escolha **Avançar**.  

1. A estrutura do aplicativo, escolhida nas etapas anteriores, determina o tipo de destino de implantação do serviço do Azure disponível aqui.  Selecione **Avançar**.

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Configurar o Azure DevOps Services e uma assinatura do Azure

1. Crie uma **nova** organização do Azure DevOps Services ou escolha uma organização **existente**.  Escolha um **nome** para o projeto do Azure DevOps.  

1. Selecione sua **assinatura do Azure**.

1. Opcionalmente, selecione o link **Alterar** para ver definições adicionais de configuração do Azure e identificar o **nome de usuário** na seção **Detalhes de logon do servidor de banco de dados**.  **Armazene** o **nome de usuário** para etapas futuras deste tutorial.
 
1. Caso tenha seguido a etapa acima, saia da área de configuração do Azure e escolha **Concluído**.  Caso contrário, basta selecionar **Concluído**.

1. Esse processo demora alguns minutos para ser concluído.  Depois de concluído, o **painel do Projeto** do Azure DevOps é carregado no portal do Azure.  Você também pode navegar até o **Painel de projeto de DevOps do Azure** diretamente de **Todos os recursos** no **portal do Azure**.  À direita do painel, selecione **Procurar** para exibir o aplicativo em execução.
    
## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Examinar o pipeline de CI do Azure DevOps Services

O Azure DevOps Project configura automaticamente um pipeline completo de CI/CD do Azure em sua organização do Azure DevOps Services.  É possível explorar e personalizar o pipeline.  Siga as etapas abaixo para se familiarizar com o pipeline de build do Azure DevOps Services.

1. Navegue até o **painel do projeto de DevOps do Azure**.

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

O Azure DevOps Project cria e configura automaticamente as etapas necessárias para implantar a organização do Azure DevOps Services na sua assinatura do Azure.  Essas etapas incluem a configuração de uma conexão de serviço do Azure para autenticar o Azure DevOps Services em sua assinatura do Azure.  A automação também cria uma Definição da Versão do Azure DevOps Services, e essa versão fornece a CD para o Azure.  Siga as etapas abaixo para saber mais sobre o Definição da Versão do Azure DevOps Services.

1. Selecione **Compilação e Versão**, em seguida, escolha **Versões**.  O projeto do Azure DevOps criou um pipeline de lançamento do Azure DevOps Services para gerenciar implantações no Azure.

1. No lado esquerdo do navegador, selecione as **reticências** ao lado do pipeline de lançamento e escolha **Editar**.

1. O pipeline de lançamento contém um **pipeline** que define o processo de lançamento.  Em **Artefatos**, selecione **Soltar**.  O pipeline de build examinado nas etapas anteriores produz a saída usada para o artefato. 

1. À direita do ícone **Soltar**, selecione o **ícone** **Gatilho de implantação contínua** (o qual aparece no formato de raio).  Esse pipeline de lançamento tem um gatilho de CD habilitado.  O gatilho inicia uma implantação sempre que houver um novo artefato de build.  Opcionalmente, você pode desabilitar o gatilho para que suas implantações exijam uma execução manual. 

1. O projeto do Azure DevOps configurou uma senha aleatória do SQL e usou essa senha para o pipeline de lançamento.  À esquerda do navegador, selecione **Variáveis**. 

1. **Só execute essa etapa se tiver alterado a senha do SQL Server.**  Há uma única **Senha** variável.  À direita da caixa de texto **Valor**, selecione o ícone de **cadeado**.  **Insira** a nova senha e depois selecione **Salvar**.

1. No lado esquerdo do navegador, selecione **Tarefas** e depois escolha seu **ambiente**.  

1. As tarefas são as atividades que seu processo de implantação realiza e são agrupadas em **Fases**.  Há uma única fase para esse pipeline de lançamento.  A fase contém uma tarefa **Implantar o Serviço de Aplicativo do Azure** e **Implantação de Banco de Dados SQL do Azure**.

1. Selecione a tarefa **Executar SQL do Azure** e examine as várias propriedades usadas para a implantação do SQL.  Em **Pacote de Implantação**, observe que a tarefa usa um **arquivo DACPAC SQL**.

1. No lado direito do navegador, selecione **Exibir versões**.  Essa exibição mostra um histórico de versões.

1. Selecione as **reticências** ao lado de uma das versões e escolha **Abrir**.  Há vários menus para explorar nessa exibição, como um **resumo da versão**, **itens de trabalho associados** e **Testes**.

1. Selecione **Confirmações**.  Essa exibição mostra as confirmações de código associadas à implantação específica. Você pode comparar versões para exibir as diferenças de confirmação entre implantações.

1. Selecione **Logs**.  Os logs contêm informações úteis sobre o processo de implantação.  Eles podem ser exibidos durante e após as implantações.

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Confirmar alterações no Azure DevOps Services e implantar automaticamente no Azure 

 > [!NOTE]
 > As etapas a seguir testam o pipeline de CI/CD com uma alteração de texto simples.  Outra opção é fazer uma alteração no Esquema do SQL Server na tabela para testar o processo de implantação do SQL.

Agora você está pronto para colaborar com uma equipe em um aplicativo com um processo de CI/CD que implanta automaticamente o trabalho mais recente em seu site da Web.  Cada alteração do repositório Git do Azure DevOps Services inicia um build no Azure DevOps Services e um pipeline de CD do Azure DevOps Services executa uma implantação no Azure.  Siga as etapas abaixo, ou use outras técnicas para confirmar as alterações no repositório.  As alterações de código iniciam o processo de CI/CD e implantam automaticamente suas novas alterações para o Azure.

1. Selecione **Código**, no menu do Azure DevOps Services e navegue até o seu repositório.

1. Navegue até o diretório **SampleWebApplication\Views\Home**, depois selecione as **reticências** ao lado do arquivo **Index.cshtml** e escolha **Editar**.

1. Faça uma alteração no arquivo, como algum texto dentro de uma das **marcas div**.  No canto superior direito, selecione **Confirmar**.  Selecione **Confirmar** novamente para efetuar push da sua alteração. 

1. Em alguns instantes, um **build é iniciado no Azure DevOps Services** e uma versão é executada para implantar as alterações.  Você pode monitorar o **status do build** com o painel do Projeto do DevOps ou no navegador com sua organização do Azure DevOps Services.

1. Quando a versão for concluída, **atualize seu aplicativo** no navegador para verificar se você vê suas alterações.

## <a name="connect-to-the-azure-sql-server-database"></a>Conectar com o banco de dados do SQL Server do Azure

Você precisa de permissões apropriadas para se conectar ao banco de dados SQL do Azure.

1. No painel do projeto de DevOps do Azure, selecione o **Banco de Dados SQL** para navegar até a página de gerenciamento do BD SQL.
   
1. Selecione **Definir firewall do servidor**, depois selecione **+ Adicionar IP do cliente**.  

1. Clique em **Salvar**.  Seu IP do cliente agora tem permissão de acesso ao **recursos do SQL Server Azure**.

1. Navegue de volta para a folha do **Banco de dados SQL**. 

1. No lado direito da tela, selecione o **Nome do servidor** para navegar até a página de configuração para o **SQL Server**.

1. Selecione **Redefinir senha**, insira uma senha para o **logon de administrador do SQL Server** e depois selecione **Salvar**.  **Guarde** essa senha para etapas futuras deste tutorial.

1. Como opção, agora você pode usar as ferramentas de cliente, como **SQL Server Management Studio** ou **Visual Studio** para se conectar ao SQL Server e banco de dados do Azure.  Use a propriedade **Nome do servidor** para se conectar.

   Se você não alterou o nome de usuário do banco de dados ao configurar o projeto de DevOps inicialmente, seu nome de usuário é a parte local do endereço de email.  Por exemplo, se seu endereço de email for johndoe@microsoft.com, seu nome de usuário é johndoe.

 > [!NOTE]
 > Se você alterar sua senha do logon no SQL, precisará alterar a senha na variável de pipeline de lançamento dos Serviços do Azure DevOps, conforme descrito na seção **Examinar o pipeline de CD do Azure DevOps Services**

## <a name="clean-up-resources"></a>Limpar recursos

 > [!NOTE]
 > As etapas a seguir excluirão os recursos permanentemente.  Só use essa funcionalidade depois de ler os prompts com cuidado.

Caso esteja fazendo testes, é possível limpar os recursos para evitar o acúmulo de encargos de cobrança.  Quando não for mais necessário, você pode excluir o Banco de Dados SQL do Azure e os recursos relacionados criados nesse tutorial usando a funcionalidade **Excluir** no painel do projeto de DevOps do Azure.  **Tenha cuidado**, pois a funcionalidade de exclusão destrói os dados criados pelo Azure DevOps Project no Azure e no Azure DevOps Services e você não poderá recuperá-los depois disso.

1. No **portal do Azure**, navegue até o **projeto de DevOps do Azure**.
2. No lado **superior direito** do painel, selecione **Excluir**.  Depois de ler o prompt, selecione **Sim** à **excluir permanentemente** os recursos.

## <a name="next-steps"></a>Próximas etapas

Como opção, você pode modificar esses pipelines de build e de lançamento para atender às necessidades de sua equipe. Você também pode usar esse padrão de CI/CD como um modelo para seus outros projetos.  Você aprendeu como:

> [!div class="checklist"]
> * Criar um Azure DevOps Project para um aplicativo ASP.NET e o Banco de Dados SQL do Azure
> * Configurar o Azure DevOps Services e uma assinatura do Azure 
> * Examinar o pipeline de CI do Azure DevOps Services
> * Examinar o pipeline de CD do Azure DevOps Services
> * Confirmar alterações no Azure DevOps Services e implantar automaticamente no Azure
> * Conectar com o banco de dados do SQL Server do Azure 
> * Limpar recursos

Para saber mais sobre o pipeline do Azure, consulte este tutorial:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>vídeos

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]