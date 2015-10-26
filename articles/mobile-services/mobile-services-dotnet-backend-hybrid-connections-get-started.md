<properties 
	pageTitle="Conectar-se a um SQL Server local de um serviço móvel de back-end .NET usando Conexões Híbridas | Serviços Móveis do Azure" 
	description="Saiba como se conectar a um SQL Server local de um serviço móvel de back-end .NET usando Conexões Híbridas do Azure" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/15/2015" 
	ms.author="glenga"/>

  
# Conectar-se dos Serviços Móveis do Azure a um SQL Server local usando Conexões Híbridas 

Quando sua empresa muda para nuvem, talvez não seja possível migrar todos os seus ativos para Azure imediatamente. As Conexões Híbridas permitem que os Serviços Móveis do Azure se conectem com segurança aos ativos locais. Dessa forma, você pode disponibilizar seus dados locais para os clientes móveis usando o Azure. Os ativos com suporte incluem qualquer recurso que seja executado em uma porta TCP estática, incluindo Microsoft SQL Server, MySQL, APIs da web HTTP e os serviços Web mais personalizados. As Conexões Híbridas utilizam a autorização de assinatura de acesso compartilhado (SAS) para proteger as conexões do seu serviço móvel e o Gerenciador de Conexões Híbridas local para a conexão híbrida. Para obter mais informações, consulte [Visão geral de Conexões Híbridas](../integration-hybrid-connection-overview.md).

Neste tutorial, você aprenderá a modificar um serviço móvel de back-end do .NET para usar um banco de dados SQL Server local em vez do Banco de Dados SQL Azure padrão provisionado com seu serviço. As Conexões Híbridas também têm suporte para um serviço móvel de back-end do JavaScript, conforme descrito [neste artigo](http://blogs.msdn.com/b/azuremobile/archive/2014/05/12/connecting-to-an-external-database-with-node-js-backend-in-azure-mobile-services.aspx).

##Pré-requisitos##

Este tutorial exige que você tenha o seguinte:

- **Um serviço móvel de back-end .NET existente** <br/>Siga o tutorial [Introdução aos Serviços Móveis] para criar e baixar um novo serviço móvel de back-end .NET no [Portal de Gerenciamento do Azure].

[AZURE.INCLUDE [hybrid-connections-prerequisites](../../includes/hybrid-connections-prerequisites.md)]

## Instalar o SQL Server Express, habilitar TCP/IP e criar um banco de dados SQL Server local

[AZURE.INCLUDE [hybrid-connections-create-on-premises-database](../../includes/hybrid-connections-create-on-premises-database.md)]

## Criar uma Conexão Híbrida

[AZURE.INCLUDE [hybrid-connections-create-new](../../includes/hybrid-connections-create-new.md)]

## Instalar o Gerenciador de Conexões Híbridas local para completar a conexão

[AZURE.INCLUDE [hybrid-connections-install-connection-manager](../../includes/hybrid-connections-install-connection-manager.md)]

## Configurar o projeto de serviço móvel para se conectar ao banco de dados do SQL Server

Nesta etapa, você define uma cadeia de conexão para o banco de dados local e modifica o serviço móvel para usar essa conexão.

1. No Visual Studio 2013, abra o projeto que define seu serviço móvel de back-end .NET. 

	Para aprender sobre como baixar seu projeto de back-end do .NET, consulte [Introdução aos Serviços Móveis](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md).

2. No Gerenciador de Soluções, abra o arquivo Web.config, localize a seção **connectionStrings** e adicione uma nova entrada do SqlClient semelhante à seguinte, que aponta para o banco de dados do SQL Server local:
	
	    <add name="OnPremisesDBConnection" 
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />

	Lembre-se de substituir `<**secure_password**>` nessa cadeia de caracteres pela senha que você criou para *HbyridConnectionLogin*.
	
3. Clique em **Salvar**, no Visual Studio, para salvar o arquivo Web.config.

	> [AZURE.NOTE]Essa configuração de conexão é usada quando a execução ocorre no computador local. Quando a execução é realizada no Azure, essa configuração é substituída pela configuração de conexão definida no portal.

4. Expanda a pasta **Modelos** e abra o arquivo de modelo de dados, que termina em *Context.cs*.

6. Modifique o construtor de instância **DbContext** para passar o valor `OnPremisesDBConnection` para o construtor **DbContext** base, como o trecho de código a seguir:

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }

	O serviço usará a nova conexão para o banco de dados do SQL Server.
 
##Testar a conexão de banco de dados no local

Antes de publicar no Azure e usar a conexão híbrida, é uma boa ideia verificar se a conexão de banco de dados funciona quando executada localmente. Dessa forma, você pode diagnosticar e corrigir problemas de conexão com mais facilidade antes de publicar novamente e começar a usar a conexão híbrida.

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]

## Atualize o Azure para usar a cadeia de conexão local

Agora que você verificou a conexão de banco de dados, é necessário adicionar uma configuração de aplicativo para essa nova cadeia de conexão, de modo que ela possa ser usada no Azure, e publicar o serviço móvel no Azure.

1. No [Portal de Gerenciamento do Azure], procure seu serviço móvel.
  
1. Clique na guia **Configurar** e localize a seção **Cadeias de Conexão**.

	![Cadeia de conexão para banco de dados local](./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png)

2. Adicione uma nova cadeia de conexão **SQL Server** denominada `OnPremisesDBConnection` com um valor como o seguinte:

		Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>


	Substitua `<**secure_password**>` pela senha segura para *HybridConnectionLogin*.

2. Pressione **Salvar** para salvar a conexão híbrida e a cadeia de conexão que acabaram de ser criadas.

3. Usando o Visual Studio, publique seu projeto de serviço móvel atualizado no Azure.

	A página inicial do serviço é exibida.

4. Usando o botão **Experimente agora** na página inicial ou usando um aplicativo cliente conectado ao seu serviço móvel, invoque algumas operações que geram alterações de banco de dados.

	>[AZURE.NOTE]Quando você usar o botão **Experimente agora** para abrir as páginas de API de Ajuda, lembre-se de fornecer a chave de aplicativo como a senha (com um nome de usuário em branco).

4. No SQL Server Management Studio, conecte-se à instância do SQL Server, abra o Pesquisador de Objetos, expanda o banco de dados **OnPremisesDB** e expanda **Tabelas**.

5. Clique com o botão direito na tabela **hybridService1.TodoItems** e escolha **Selecionar as 1000 linhas principais** para exibir os resultados.

	Observe que as alterações geradas no seu aplicativo foram salvas pelo serviço móvel no banco de dados local usando a conexão híbrida.

##Consulte também##
 
+ [Site de Conexões Híbridas](../../services/biztalk-services/)
+ [Visão geral de Conexões Híbridas](../integration-hybrid-connection-overview.md)
+ [Serviços BizTalk: guias Painel, Monitor, Escala, Configurar e Conexão Híbrida](../biztalk-dashboard-monitor-scale-tabs.md)
+ [Como fazer alterações no modelo de dados para um serviço móvel de back-end do .NET](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)

<!-- IMAGES -->

<!-- Links -->
[Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
[Introdução aos Serviços Móveis]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

<!---HONumber=Oct15_HO3-->