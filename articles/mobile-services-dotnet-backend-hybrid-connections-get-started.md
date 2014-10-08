<properties linkid="mobile-services-dotnet-backend-hybrid-connections-get-started" urlDisplayName="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" pageTitle="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections - Azure Mobile Services" metaKeywords="" description="Learn how to connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" metaCanonical="" services="" documentationCenter="Mobile" title="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg"></tags>

# Conectar-se a um SQL Server no local por meio de um serviço móvel do Azure utilizando Conexões Híbridas

Quando uma empresa muda para a nuvem, geralmente é necessário deixar alguns ativos locais, seja por motivos técnicos, conformidade ou segurança. Os Serviços Móveis permitem criar facilmente uma camada de mobilidade hospedada na nuvem sobre esses ativos, ao conectar a eles novamente com segurança em seus locais usando Conexões Híbridas. Os ativos com suporte incluem qualquer recurso que seja executado em uma porta TCP estática, incluindo Microsoft SQL Server, MySQL, APIs da web HTTP e os serviços Web mais personalizados.

Neste tutorial, você aprenderá a modificar um serviço móvel de back-end .NET para usar um banco de dados SQL Server local em vez do Banco de Dados SQL Azure padrão provisionado com seu serviço. Embora Conexões Híbridas tenham suporte para serviços móveis de back-end JavaScript, este tópico só abrange serviços móveis de back-end .NET.

Esse tópico explica estas etapas básicas:

1.  [Pré-requisitos][]
2.  [Instalar o SQL Server Express, habilitar TCP/IP e criar um banco de dados SQL Server local][]
3.  [Criar uma Conexão Híbrida][]
4.  [Instalar o Hybrid Connection Manager local para concluir a conexão][]
5.  [Modificar um Serviço Móvel para usar a conexão][]

<a name="Prerequisites"></a>

## Pré-requisitos

Para concluir este tutorial, você precisará dos produtos a seguir. Todos estão disponíveis em versões gratuitas, portanto, é possível começar a desenvolver para o Azure de maneira totalmente gratuita.

-   **Visual Studio 2013** - Para baixar uma versão de avaliação gratuita do Visual Studio 2013, consulte [Downloads do Visual Studio][]. Instale isso antes de continuar.

-   **SQL Server 2014 Express with Tools** - baixe o Microsoft SQL Server Express gratuitamente na [página do Banco de dados da plataforma da web da Microsoft][]. Escolha a versão **Express** (não LocalDB). A versão **Express with Tools** inclui o SQL Server Management Studio, que você usará neste tutorial.

Você também precisará de um computador local que conectará ao Azure usando Conexões Híbridas. Esse computador deve atender aos seguintes critérios:

-   Ser capaz de conectar ao Azure pela porta 5671
-   Ser capaz de atingir o *hostname*:*portnumber* do recurso local que deseja conectar. O recurso pode ou não ser hospedado no mesmo computador.

<a name="InstallSQL"></a>

## Instalar o SQL Server Express, habilitar TCP/IP e criar um banco de dados SQL Server local

Para usar um banco de dados local SQL Server ou SQL Server Express com uma conexão híbrida, o TCP/IP precisa ser habilitado em uma porta estática. As instâncias padrão no SQL Server usam a porta estática 1433, ao passo que instâncias nomeadas não.

Para obter instruções detalhadas sobre como configurar o SQL Server para que atenda às condições descritas acima, consulte [Instalar o SQL Server Express, habilitar TCP/IP e criar um banco de dados SQL Server local][1]. Se você já tiver o SQL Server instalado em uma configuração e em um ambiente que atenda às condições descritas acima, é possível avançar e iniciar com [Criar um banco de dados SQL Server local][].

Para os fins deste tutorial, presumiremos que o nome do banco de dados seja **OnPremisesDB**, ele está em execução na porta **1433** e que o nome do host do computador seja **onPremisesServer**.

<a name="CreateHC"></a>

## Criar uma Conexão Híbrida

1.  No computador local, faça logon no [Portal de Gerenciamento do Azure][].

2.  Na parte inferior do painel de navegação, selecione **+NOVO** e selecione **Serviços de Aplicativos**, **Serviço BizTalk** e em seguida **Criação Personalizada**

    ![Criar Serviço BizTalk][]

3.  Forneça um **Nome de Serviço BizTalk** e selecione uma **Edição**.

    ![Configure o novo Serviço BizTalk][]

    Este tutorial usa **mobile1**. Você precisará fornecer um nome exclusivo para seu novo Serviço BizTalk.

4.  Uma vez que o Serviço BizTalk foi criado, selecione a guia **Conexões Híbridas** e clique em **Incluir**.

    ![Incluir Conexão Híbrida][]

    Isso cria uma nova conexão híbrida.

5.  Forneça um **Nome** e **Nome de Host** para sua conexão híbrida e configure a **Porta** para `1433`.

    ![Configurar a Conexão Híbrida][]

    O nome do host é o nome do servidor local. Isso configura a conexão híbrida para acessar o SQL Server em execução na porta 1433.

6.  Uma vez que a nova conexão estiver criada, ela aparecerá na tabela a seguir.

    ![Conexão Híbrida criada com sucesso][]

    O status da nova conexão mostra **Configuração local incompleta**.

Agora precisamos instalar o Hybrid Connection Manager no computador local.

<a name="InstallHCM"></a>

## Instalar o Hybrid Connection Manager local para concluir a conexão

O Hybrid Connection Manager permite que seu computador local se conecte ao Azure e retransmita o tráfego TCP. Você deve instalar este software no computador local que está tentando acessar a partir do Azure.

1.  Selecione a conexão que acabou de criar e na barra inferior clique em **Configuração Local**.

    ![Configuração Local][]

2.  Clique em **Instalar e Configurar**.

    ![Instalar o Hybrid Connection Manager][]

    Isso instala uma instância personalizada do Gerenciador de Conexão, que já está pré-configurado para trabalhar com a conexão híbrida que você acabou de criar.

3.  Conclua o restante das etapas de configuração para o Gerenciador de Conexão.

    ![Configuração do Gerenciador de Conexão Híbrida][]

    Quando a instalação estiver concluída, o status de conexão híbrida será alterado para **1 Instância Conectada**. Pode ser necessário atualizar o navegador e aguardar alguns minutos. A configuração local agora está concluída.

    ![Conexão Híbrida conectada][]

<a name="CreateService"></a>

## Modificar um Serviço Móvel para usar a conexão

### Associar uma conexão híbrida ao serviço

1.  Na guia **Serviços Móveis** do portal, selecione um serviço móvel existente ou crie um novo.

    > [WACOM.NOTE]Assegure-se de selecionar um serviço que foi criado usando o back-end do .NET ou crie um novo serviço móvel de back-end do .NET. Para aprender a criar um novo serviço móvel de back-end do .NET, consulte [Introdução aos Serviços Móveis][]

2.  Na guia **Configurar** para seu serviço móvel, localize a seção **Conexões Híbridas** e selecione **Incluir Conexão Híbrida**.

    ![Associar Conexão Híbrida][]

3.  Selecione a conexão híbrida que acabou de criar na guia Serviços BizTalk e pressione **OK**.

    ![Escolher Conexão Híbrida associada][]

O serviço móvel agora está associado à nova conexão híbrida.

### Atualize o serviço para usar a cadeia de conexão local

Por fim, precisamos criar uma configuração de aplicativo para armazenar o valor da cadeia de conexão em nosso SQL Server local. Precisamos modificar o serviço móvel para usar a nova cadeia de conexão.

1.  Na guia **Configurar** em **Configurações do Aplicativo**, inclua uma nova configuração do aplicativo chamada `onPremisesDatabase` com valor igual a `Server=onPremisesServer,1433;Database=OnPremisesDB;User ID=sa;Password={password}`.

    ![Configuração de aplicativo para cadeia de conexão][]

    Substitua `{password}` pela senha segura para seu banco de dados local.

2.  Pressione **Salvar** para salvar a conexão híbrida e a configuração do aplicativo que acabamos de criar.

3.  No Visual Studio 2013, abra o projeto que define seu serviço móvel baseado em .NET.

    Para aprender sobre como baixar seu projeto de back-end do .NET, consulte [Introdução aos Serviços Móveis][].

4.  No Gerenciador de Soluções, expanda a pasta **Modelos** e abra o arquivo de modelo de dados, que termina em *Context.cs*.

5.  Modifique o construtor de instância **DbContext** para torná-lo semelhante ao trecho a seguir:

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base(ConfigurationManager.AppSettings["onPremisesDatabase"])
            {
            }

            // snipped
        }

    O serviço agora usará a nova cadeia de conexão híbrida definida nas configurações do aplicativo do Azure.

6.  Publique suas mudanças e use um aplicativo do cliente conectado ao seu serviço móvel para invocar algumas operações a fim de gerar mudanças do banco de dados.

7.  Abra o SQL Management Studio no computador local em que o SQL Server está em execução, em seguida, no Gerenciador de Objetos, expanda o banco de dados **OnPremisesDB** e expanda **Tabelas**.

8.  Clique com o botão direito na tabela **hybridService1.TodoItems** e escolha **Selecionar as 1000 linhas principais** para exibir os resultados.

    ![SQL Management Studio][]

As mudanças geradas em seu aplicativo foram extraídas do seu serviço móvel para seu banco de dados local.

## Consulte também

-   [Site de Conexões Híbridas][]
-   [Visão geral de Conexões Híbridas][]
-   [Serviços do BizTalk: Guias Painel, Monitor, Escala, Configurar e Conexão Híbrida][]

<!-- IMAGES -->

  [Pré-requisitos]: #Prerequisites
  [Instalar o SQL Server Express, habilitar TCP/IP e criar um banco de dados SQL Server local]: #InstallSQL
  [Criar uma Conexão Híbrida]: #CreateHC
  [Instalar o Hybrid Connection Manager local para concluir a conexão]: #InstallHCM
  [Modificar um Serviço Móvel para usar a conexão]: #CreateService
  [Downloads do Visual Studio]: http://www.visualstudio.com/downloads/download-visual-studio-vs
  [página do Banco de dados da plataforma da web da Microsoft]: http://www.microsoft.com/web/platform/database.aspx
  [1]: /en-us/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#InstallSQL
  [Criar um banco de dados SQL Server local]: /en-us/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#CreateSQLDB
  [Portal de Gerenciamento do Azure]: http://go.microsoft.com/fwlink/p/?linkid=213885&clcid=0x409
  [Criar Serviço BizTalk]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/1.png
  [Configure o novo Serviço BizTalk]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/2.png
  [Incluir Conexão Híbrida]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/3.png
  [Configurar a Conexão Híbrida]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/4.png
  [Conexão Híbrida criada com sucesso]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5.png
  [Configuração Local]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5-1.png
  [Instalar o Hybrid Connection Manager]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/6.png
  [Configuração do Gerenciador de Conexão Híbrida]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/7.png
  [Conexão Híbrida conectada]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/8.png
  [Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Associar Conexão Híbrida]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/9.png
  [Escolher Conexão Híbrida associada]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/10.png
  [Configuração de aplicativo para cadeia de conexão]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png
  [SQL Management Studio]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/12.png
  [Site de Conexões Híbridas]: http://azure.microsoft.com/en-us/services/biztalk-services/
  [Visão geral de Conexões Híbridas]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Serviços do BizTalk: Guias Painel, Monitor, Escala, Configurar e Conexão Híbrida]: http://azure.microsoft.com/en-us/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
