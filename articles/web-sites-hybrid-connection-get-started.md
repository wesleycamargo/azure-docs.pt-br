<properties linkid="web-sites-hybrid-connection" title="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" pageTitle="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" description="Create a connection between an Azure website and an on-premises resource that uses a static TCP port" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm"></tags>

# Conectar um site do Azure a um recurso local usando Conexões Híbridas

Você pode conectar um site no Microsoft Azure a qualquer recurso local que utilize uma porta TCP estática, como SQL Server, MySQL, APIs da web HTTP, Serviços Móveis e os serviços Web mais personalizados. Esse artigo mostra como você pode criar uma conexão híbrida entre um site do Azure e um banco de dados local de SQL Server.

> [WACOM.NOTE] A porção Sites do recurso Conexões Híbridas está disponível somente no [portal Visualização do Azure][]. Para criar uma conexão nos Serviços BizTalk, consulte [Conexões Híbridas][].

## Pré-requisitos

-   Uma assinatura do Azure. Para uma assinatura gratuita, consulte [Avaliação Gratuita do Azure][].

-   Para usar um banco de dados local SQL Server ou SQL Server Express com uma conexão híbrida, o TCP/IP precisa ser habilitado em uma porta estática. Usar uma instância padrão no SQL Server é recomendado porque utiliza a porta estática 1433. Para obter informações sobre instalação e configuração do SQL Server Express para uso com conexões híbridas, consulte [Conectar a um SQL Server local a partir de um site do Azure usando Conexões Híbridas][].

-   O computador no qual você instala o agente do Hybrid Connection Manager local, descrito posteriormente neste artigo:

    -   Precisa ser capaz de conectar-se ao Azure pela porta 5671
    -   Precisa ser capaz de atingir o *hostname*:*portnumber* do seu recurso local.

> [WACOM.NOTE] As etapas neste artigo pressupõem que você está utilizando o navegador a partir do computador que hospedará o agente local de conexão híbrida.

## Neste artigo

[Criar um site no Portal de Visualização do Azure][]

[Criar uma Conexão Híbrida e um Serviço do BizTalk][]

[Instalar o Hybrid Connection Manager local para concluir a conexão][]

[Próximas etapas][]

## Criar um site no Portal de Visualização do Azure

> [WACOM.NOTE] Se você já criou um site no Portal de Visualização do Azure que você deseja utilizar para este tutorial, você pode pular para [Criar uma Conexão Híbrida e um Serviço do BizTalk][] e começar a partir dali.

1.  No canto inferior esquerdo do [Portal de Visualização do Azure][portal Visualização do Azure], clique em **Novo**, depois selecione **Site**.

    ![Novo botão][]

    ![Novo site][]

2.  Na lâmina **Site**, forneça um nome para seu site e em seguida clique em **Criar**.

    ![Nome do site][]

3.  Após alguns instantes, o site é criado e sua lâmina de site aparece. A lâmina é um painel com rolagem vertical, que permite a você gerenciar o seu site.

    ![Website executando][]

4.  Para verificar se o site está funcionando, você pode clicar no ícone **Procurar** para exibir a página padrão.

    ![Clique em Procurar para ver seu site][]

    ![Página de site padrão][]

Em seguida, você criará uma conexão híbrida e um serviço do BizTalk para o site.

<a name="CreateHC"></a>

## Criar uma Conexão Híbrida e um Serviço do BizTalk

1.  De volta ao Portal de Visualização, role para baixo pela lâmina para seu site e selecione **Conexões Híbridas**.

    ![Conexões Híbridas][1]

2.  Na lâmina Conexões Híbridas, clique em **Adicionar**.

    ![Adicione uma conexão híbrida][]

3.  A lâmina **Adicionar uma conexão híbrida** se abre. Como essa é sua primeira conexão híbrida, a opção **Nova Conexão Híbrida** fica pré-selecionada e a lâmina **Criar Conexão Híbrida** se abre para você.

    ![Criar uma Conexão Híbrida][]

    Na lâmina **Criar Conexão Híbrida**:

    -   Como **Nome**, forneça um nome para a conexão.
    -   Como **Nome de host**, insira o nome do computador local que hospeda seu recurso.
    -   Como **Porta**, insira o número da porta que o seu recurso local utiliza (1433 para uma instância padrão de SQL Server).
    -   Clique em **Serviço Biz Talk**

4.  A lâmina **Criar Serviço Biz Talk** se abre. Insira um nome para o serviço BizTalk, então clique em **OK**.

    ![Criar Serviço BizTalk][]

    A lâmina **Criar Serviço Biz Talk** é fechada e você retorna à lâmina **Criar Conexão Híbrida**.

5.  Na lâmina Criar Conexão Híbrida, clique em **OK**.

    ![Clique em OK][]

6.  Quando o processo for concluído, a área Notificações no portal informa a você que a conexão foi criada com sucesso.

    ![Notificação de sucesso][]

7.  Na lâmina site, o ícone **Conexões Híbridas** agora mostra que uma conexão híbrida foi criada.

    ![Uma conexão híbrida criada][]

Nesse ponto, você concluiu uma parte importante da infraestrutura de conexão híbrida de nuvem. Em seguida, você criará uma parte local correspondente.

<a name="InstallHCM"></a>

## Instalar o Hybrid Connection Manager local para concluir a conexão

1.  Na lâmina site, clique no ícone Conexões Híbridas.

    ![Ícone Conexões Híbridas][]

2.  Na lâmina **Conexões Híbridas** a coluna **Status** para o ponto de extremidade adicionado recentemente exibe **Não conectado**. Clique na conexão para configurá-la.

    ![Não conectado][]

    A lâmina Conexão Híbrida se abre.

    ![NotConnectedBlade][]

3.  Na lâmina, clique em **Configuração do Ouvinte**.

    ![Clique em Configuração do Ouvinte][]

4.  A lâmina **Propriedades da Conexão Híbrida** se abre. Em **Gerenciador de Conexão Híbrida Local**, escolha **Clique aqui para instalar**.

    ![Clique aqui para instalar][]

5.  Na cauxa de diálogo de aviso de segurança Executar Aplicativo, selecione **Executar** para continuar.

    ![Selecione Executar para continuar][]

6.  Na caixa de diálogo **Controle de Conta de Usuário**, selecione **Sim**.

    ![Selecione Sim][]

7.  O Gerenciador de Conexão Híbrida é baixado e instalado para você.

    ![Instalando][]

8.  Quando a instalação é concluída, clique em **Fechar**.

    ![Clique em Fechar][]

    Na lâmina **Conexões Híbridas**, a coluna **Status** agora exibe **Conectado**.

    ![Status Conectado][]

Agora que a infraestrutura de conexão híbrida está concluída, você criará um aplicativo híbrido que a utilize.

<a name="NextSteps"></a>

## Próximas etapas

-   Para obter informações sobre como criar um aplicativo Web ASP.NET que utiliza uma conexão híbrida, consulte [Conectar-se a um SQL Server local a partir de um Site do Azure usando Conexões Híbridas][Conectar a um SQL Server local a partir de um site do Azure usando Conexões Híbridas].

-   Para obter informações sobre como usar uma conexão híbrida com um serviço móvel, consulte [Conectar-se a um SQL Server local a partir de um serviço móvel do Azure utilizando Conexões Híbridas][].

### Recursos adicionais

[Visão geral de Conexões Híbridas][Conexões Híbridas]

[Josh Twist apresenta conexões híbridas (vídeo no canal 9)][]

[Site de Conexões Híbridas][]

[Serviços do BizTalk: Guias Painel, Monitor, Escala, Configurar e Conexão Híbrida][]

[Criando uma nuvem híbrida no mundo real com portabilidade perfeita com aplicativo (vídeo no canal 9)][]

[Conectar-se a um SQL Server local a partir de serviços móveis do Azure utilizando Conexões Híbridas (vídeo no canal 9)][]

<!-- IMAGES -->

  [portal Visualização do Azure]: https://portal.azure.com
  [Conexões Híbridas]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Avaliação Gratuita do Azure]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Conectar a um SQL Server local a partir de um site do Azure usando Conexões Híbridas]: http://go.microsoft.com/fwlink/?LinkID=397979
  [Criar um site no Portal de Visualização do Azure]: #CreateSite
  [Criar uma Conexão Híbrida e um Serviço do BizTalk]: #CreateHC
  [Instalar o Hybrid Connection Manager local para concluir a conexão]: #InstallHCM
  [Próximas etapas]: #NextSteps
  [Novo botão]: ./media/web-sites-hybrid-connection-get-started/B01New.png
  [Novo site]: ./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
  [Nome do site]: ./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
  [Website executando]: ./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
  [Clique em Procurar para ver seu site]: ./media/web-sites-hybrid-connection-get-started/B05Browse.png
  [Página de site padrão]: ./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
  [1]: ./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
  [Adicione uma conexão híbrida]: ./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
  [Criar uma Conexão Híbrida]: ./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
  [Criar Serviço BizTalk]: ./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
  [Clique em OK]: ./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
  [Notificação de sucesso]: ./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
  [Uma conexão híbrida criada]: ./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
  [Ícone Conexões Híbridas]: ./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
  [Não conectado]: ./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
  [NotConnectedBlade]: ./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
  [Clique em Configuração do Ouvinte]: ./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
  [Clique aqui para instalar]: ./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
  [Selecione Executar para continuar]: ./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
  [Selecione Sim]: ./media/web-sites-hybrid-connection-get-started/D07UAC.png
  [Instalando]: ./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
  [Clique em Fechar]: ./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
  [Status Conectado]: ./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png
  [Conectar-se a um SQL Server local a partir de um serviço móvel do Azure utilizando Conexões Híbridas]: http://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/
  [Josh Twist apresenta conexões híbridas (vídeo no canal 9)]: http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections
  [Site de Conexões Híbridas]: http://azure.microsoft.com/en-us/services/biztalk-services/
  [Serviços do BizTalk: Guias Painel, Monitor, Escala, Configurar e Conexão Híbrida]: http://azure.microsoft.com/en-us/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
  [Criando uma nuvem híbrida no mundo real com portabilidade perfeita com aplicativo (vídeo no canal 9)]: http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=
  [Conectar-se a um SQL Server local a partir de serviços móveis do Azure utilizando Conexões Híbridas (vídeo no canal 9)]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections
