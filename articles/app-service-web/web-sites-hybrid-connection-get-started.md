---
title: "Acessar os recursos locais usando conexões híbridas no Serviço de Aplicativo do Azure"
description: "Criar uma conexão entre um aplicativo Web no Serviço de Aplicativo do Azure e um recurso local que usa uma porta TCP estática"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: a46ed26b-df8e-4fc3-8e05-2d002a6ee508
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: cb656dd952e76ee29e102fff531d34a45e4630e4
ms.lasthandoff: 01/20/2017


---
# <a name="access-on-premises-resources-using-hybrid-connections-in-azure-app-service"></a>Acessar os recursos locais usando conexões híbridas no Serviço de Aplicativo do Azure
Você pode conectar um aplicativo do Serviço de Aplicativo do Azure a qualquer recurso local que utilize uma porta TCP estática, como o SQL Server, MySQL, APIs Web HTTP e a maioria dos serviços Web personalizados. Este artigo mostra como criar uma conexão híbrida entre um Serviço de Aplicativo e um banco de dados do SQL Server local.

> [!NOTE]
> A parte de aplicativos Web do recurso de conexões híbridas está disponível apenas no [Portal do Azure](https://portal.azure.com). Para criar uma conexão nos Serviços BizTalk, consulte [Conexões Híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274). 
> 
> Este conteúdo também se aplica aos Aplicativos Móveis no Serviço de Aplicativo do Azure. 
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure. Para uma assinatura gratuita, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
  
    Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
* Para usar um banco de dados local SQL Server ou SQL Server Express com uma conexão híbrida, o TCP/IP precisa ser habilitado em uma porta estática. É recomendado usar uma instância padrão no SQL Server porque ele usa a porta estática 1433. Para informações sobre a instalação e a configuração do SQL Server Express para uso com conexões híbridas, consulte [Conectar-se a um SQL Server local por meio de um Site do Azure usando Conexões Híbridas](http://go.microsoft.com/fwlink/?LinkID=397979).
* O computador no qual você instala o agente do Hybrid Connection Manager local, descrito posteriormente neste artigo:
  
  * Precisa ser capaz de conectar-se ao Azure pela porta 5671
  * Precisa ser capaz de atingir o *hostname*:*portnumber* do seu recurso local. 

> [!NOTE]
> As etapas neste artigo pressupõem que você está utilizando o navegador a partir do computador que hospedará o agente local de conexão híbrida.
> 
> 

## <a name="create-a-web-app-in-the-azure-portal"></a>Criar um aplicativo Web no Portal do Azure
> [!NOTE]
> Se já criou no Portal do Azure um back-end de Aplicativo Móvel ou aplicativo Web que deseja utilizar para este tutorial, você pode pular para [Criar uma Conexão Híbrida e um Serviço do BizTalk](#CreateHC) e começar de lá.
> 
> 

1. No canto superior esquerdo do [Portal do Azure](https://portal.azure.com), clique em **Novo** > **Web + Móvel** > **Aplicativo Web**.
   
    ![Novo aplicativo Web][NewWebsite]
2. Na folha **Aplicativo Web**, forneça uma URL e clique em **Criar**. 
   
    ![Nome do site][WebsiteCreationBlade]
3. Após alguns momentos, o aplicativo Web é criado e sua folha de aplicativo Web aparece. A lâmina é um painel com rolagem vertical, que permite a você gerenciar o seu site.
   
    ![Website executando][WebSiteRunningBlade]
4. Para verificar se o site está funcionando, você pode clicar no ícone **Procurar** para exibir a página padrão.
   
    ![Clique em Procurar para ver seu aplicativo Web][Browse]
   
    ![Página de aplicativo Web padrão][DefaultWebSitePage]

Em seguida, você criará uma conexão híbrida e um serviço do BizTalk para o aplicativo Web.

<a name="CreateHC"></a>

## <a name="create-a-hybrid-connection-and-a-biztalk-service"></a>Criar uma Conexão Híbrida e um Serviço do BizTalk
1. Na folha do seu aplicativo Web, clique em **Todas as configurações** > **Rede** > **Configurar seus pontos de extremidade de conexão híbrida**.
   
    ![Conexões Híbridas][CreateHCHCIcon]
2. Na lâmina Conexões Híbridas, clique em **Adicionar**.
   
    <!-- ![Add a hybrid connnection][CreateHCAddHC]
   -->
3. A lâmina **Adicionar uma conexão híbrida** se abre.  Como essa é sua primeira conexão híbrida, a opção **Nova Conexão Híbrida** fica pré-selecionada e a folha **Criar Conexão Híbrida** se abre para você.
   
    ![Criar uma Conexão Híbrida][TwinCreateHCBlades]
   
    Na lâmina **Criar Conexão Híbrida**:
   
   * Como **Nome**, forneça um nome para a conexão.
   * Como **Nome de host**, insira o nome do computador local que hospeda seu recurso.
   * Como **Porta**, insira o número da porta que o seu recurso local utiliza (1433 para uma instância padrão de SQL Server).
   * Clique em **Serviço Biz Talk**
4. A folha **Criar serviço BizTalk** será aberta. Insira um nome para o serviço BizTalk, então clique em **OK**.
   
    ![Criar serviço BizTalk][CreateHCCreateBTS]
   
    A folha **Criar Serviço BizTalk** é fechada e você retorna à folha **Criar Conexão Híbrida**.
5. Na lâmina Criar Conexão Híbrida, clique em **OK**. 
   
    ![Clique em OK][CreateBTScomplete]
6. Quando o processo for concluído, a área de notificações no Portal informa a você que a conexão foi criada com sucesso.
   
    <!--- TODO
   
    Tudo falha nesta etapa. I can't create a BizTalk service in the dogfood portal. Volto para o portal clássico (portal completo) e crio o serviço BizTalk, mas ele não parece permitir que você estabeleça a conexão – Ao concluir a etapa Criar conexão híbrida, você obtém o seguinte erro: Falha ao criar conexão híbrida RelecIoudHC. O tipo de recurso não foi encontrado no namespace 'Microsoft.BizTaIkServices para a api versão 2014-06-01'.
   
    The error indicates it couldn't find the type, not the instance.
    ![Notificação de sucesso][CreateHCSuccessNotification]
    -->
7. Na folha do aplicativo Web, o ícone **Conexões híbridas** agora mostra que uma conexão híbrida foi criada.
   
    ![Uma conexão híbrida criada][CreateHCOneConnectionCreated]

Nesse ponto, você concluiu uma parte importante da infraestrutura de conexão híbrida de nuvem. Em seguida, você criará uma parte local correspondente.

<a name="InstallHCM"></a>

## <a name="install-the-on-premises-hybrid-connection-manager-to-complete-the-connection"></a>Instalar o Gerenciador de Conexões Híbridas local para completar a conexão
1. Na folha do aplicativo Web, clique em **Todas as configurações** > **Rede** > **Configurar seus pontos de extremidade de conexão híbrida**. 
   
    ![Ícone Conexões Híbridas][HCIcon]
2. Na folha **Conexões Híbridas** a coluna **Status** para o ponto de extremidade adicionado recentemente exibe **Não conectado**. Clique na conexão para configurá-la.
   
    ![Não conectado][NotConnected]
   
    A lâmina Conexão Híbrida se abre.
   
    ![NotConnectedBlade][NotConnectedBlade]
3. Na lâmina, clique em **Configuração do Ouvinte**.
   
    ![Clique em Configuração do Ouvinte][ClickListenerSetup]
4. A lâmina **Propriedades da Conexão Híbrida** se abre. Em **Gerenciador de Conexão Híbrida Local**, escolha **Clique aqui para instalar**.
   
    ![Clique aqui para instalar][ClickToInstallHCM]
5. Na caixa de diálogo de aviso de segurança Executar Aplicativo, selecione **Executar** para continuar.
   
    ![Selecione Executar para continuar][ApplicationRunWarning]
6. Na caixa de diálogo **Controle de Conta de Usuário**, selecione **Sim**.
   
   ![Selecione Sim][UAC]
7. O Gerenciador de Conexão Híbrida é baixado e instalado para você. 
   
    ![Instalando][HCMInstalling]
8. Quando a instalação é concluída, clique em **Fechar**.
   
    ![Clique em Fechar][HCMInstallComplete]
   
    Na folha **Conexões Híbridas**, a coluna **Status** agora exibe **Conectado**. 
   
    ![Status Conectado][HCStatusConnected]

Agora que a infraestrutura de conexão híbrida está concluída, você criará um aplicativo híbrido que a utilize. 

> [!NOTE]
> As seções a seguir mostram como usar uma conexão híbrida com um projeto de back-end .NET doa Aplicativos Móveis.
> 
> 

## <a name="configure-the-mobile-app-net-backend-project-to-connect-to-the-sql-server-database"></a>Configurar o projeto de back-end .NET de aplicativo móvel para se conectar ao banco de dados do SQL Server
No Serviço de Aplicativo, um projeto de back-end .NET dos Aplicativos Móveis é apenas um aplicativo Web ASP.NET com um SDK de Aplicativos Móveis adicional instalado e inicializado. Para usar seu aplicativo Web como um back-end de Aplicativos Móveis, você deve [baixar e inicializar o SDK de back-end .NET dos Aplicativos Móveis](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#install-sdk).  

Para os Aplicativos Móveis, você também precisa definir uma cadeia de conexão do banco de dados local e modifica o back-end para usar essa conexão. 

1. No Gerenciador de Soluções no Visual Studio, abra o arquivo Web.config para o back-end .NET do Aplicativos Móveis, localize a seção **connectionStrings** e adicione uma nova entrada do SqlClient semelhante à seguinte, que aponta para o banco de dados do SQL Server local:
   
        <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />
   
    Lembre-se de substituir `<**secure_password**>` na cadeia de caracteres pela senha que você criou para *HybridConnectionLogin*.
2. Clique em **Salvar** , no Visual Studio, para salvar o arquivo Web.config.
   
   > [!NOTE]
   > Essa configuração de conexão é usada quando a execução ocorre no computador local. Quando a execução é realizada no Azure, essa configuração é substituída pela configuração de conexão definida no portal.
   > 
   > 
3. Expanda a pasta **Modelos** e abra o arquivo de modelo de dados, que termina em *Context.cs*.
4. Modifique o construtor de instância **DbContext** para passar o valor `OnPremisesDBConnection` para o construtor **DbContext** base, como o trecho de código a seguir:
   
        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }
   
    O serviço usará a nova conexão para o banco de dados do SQL Server.

## <a name="update-the-mobile-app-backend-to-use-the-on-premises-connection-string"></a>Atualize o back-end dos Aplicativos Móveis para usar a cadeia de conexão local
Em seguida, você precisa adicionar uma configuração de aplicativo para essa nova cadeia de conexão para que possa ser usado no Azure.  

1. No [portal do Azure](https://portal.azure.com) no código do back-end de aplicativo Web do seu Aplicativo Móvel, clique em **Todas as configurações** e em **Configurações do aplicativo**.
2. Na folha **Configurações do aplicativo Web**, role para baixo até **Cadeias de conexão** e adicione uma nova cadeia de conexão do **SQL Server** chamada `OnPremisesDBConnection`, com um valor como `Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>`.
   
    Substitua `<**secure_password**>` pela senha segura do seu banco de dados local.
   
    ![Cadeia de conexão para banco de dados local](./media/web-sites-hybrid-connection-get-started/set-sql-server-database-connection.png)
3. Pressione **Salvar** para salvar a conexão híbrida e a cadeia de conexão que acabaram de ser criadas.

Neste ponto, você pode publicar novamente o projeto do servidor e testar a nova conexão com os clientes existentes dos Aplicativos Móveis. Os dados serão lidos e gravados no banco de dados local usando a conexão híbrida.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Próximas etapas
* Para obter informações sobre como criar um aplicativo Web ASP.NET que utiliza uma conexão híbrida, consulte [Conectar-se a um SQL Server local a partir de um Site do Azure usando Conexões Híbridas](http://go.microsoft.com/fwlink/?LinkID=397979). 

### <a name="additional-resources"></a>Recursos adicionais
[Visão geral de Conexões Híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist apresenta conexões híbridas (vídeo do Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Site de Conexões Híbridas](https://azure.microsoft.com/services/biztalk-services/)

[Serviços BizTalk: guias Painel, Monitor, Escala, Configurar e Conexão Híbrida](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)

[Criando uma nuvem híbrida no mundo real com portabilidade perfeita com aplicativo (vídeo no Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Conectar-se a um SQL Server local a partir de serviços móveis do Azure utilizando Conexões Híbridas (vídeo no Channel 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## <a name="whats-changed"></a>O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[New]:./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png

