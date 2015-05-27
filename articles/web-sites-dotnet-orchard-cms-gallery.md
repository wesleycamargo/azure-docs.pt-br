<properties 
	pageTitle="Criar um aplicativo Web Orchard CMS do Azure Marketplace" 
	description="Um tutorial que ensina a criar um novo aplicativo Web no Azure. Aprenda também como iniciar e gerenciar seu aplicativo Web usando o Portal do Azure" 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tomfitz"/>

# Criar um aplicativo Web Orchard CMS do Azure Marketplace

## Visão geral

O Marketplace disponibiliza uma ampla gama de aplicativos Web populares desenvolvidos pela Microsoft, outras empresas e iniciativas de software livre. Os aplicativos Web criados a partir do Marketplace não exigem a instalação de qualquer software que não seja o navegador usado para se conectar ao [Portal de visualização do Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Para obter mais informações sobre os aplicativos Web no Marketplace, consulte [Azure Marketplace](/marketplace/).

Neste tutorial, você aprenderá:

- Como criar um novo aplicativo Web do Marketplace

- Como iniciar e gerenciar seu aplicativo Web do Portal do Azure
 
Você criará um aplicativo Web Orchard CMS que usa um modelo padrão. O [Orchard](http://www.orchardproject.net/) é um aplicativo CMS gratuito de software livre baseado em NET que permite que você crie aplicativos Web e sites personalizados e orientados por conteúdo. O Orchard CMS inclui uma estrutura de extensibilidade por meio da qual você pode [baixar outros módulos e temas](http://gallery.orchardproject.net/) para personalizar seu aplicativo Web. A ilustração a seguir mostra o aplicativo Web Orchard CMS no [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) que você criará.

![Blog do pomar][13]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Criar um aplicativo Web Orchard do Marketplace

1. Faça logon no [portal de visualização do Azure](http://portal.azure.com).

2. Clique em **Novo** > **Web + Celular** > **Azure Marketplace**.
	
	![Criar Novo][1]

3. Clique em **Aplicativos Web** > **Orchard CMS**. Na próxima folha, clique em **Criar**.
	
	![Criar do Marketplace][2]

4. Configure a URL do aplicativo Web, plano do Serviço de Aplicativo, grupo de recursos e local. Quando tiver concluído, clique em **Criar**.
	
	![Configurar o aplicativo][3]

	Após a criação do aplicativo Web, o botão **Notificações** mostrará a palavra "ÊXITO" em verde e a folha de seu aplicativo da Web será exibida.

## Inicie e gerencie seu aplicativo Web Orchard

1. Na folha de seu aplicativo Web, clique em **Procurar** para abrir a página inicial de seu aplicativo Web.

	![botão Procurar][12]

2. Insira as informações de configuração exigidas pelo Orchard e clique em **Concluir Configuração** para finalizar a configuração e abrir a página inicial do aplicativo Web.

	![login do pomar][7]

	Você terá um novo aplicativo Web Orchard parecido com a captura de tela abaixo.

	![seu aplicativo Web Orchard][13]

3. Siga os detalhes na [Documentação do Orchard](http://docs.orchardproject.net/) para saber mais sobre o Orchard e configurar o novo aplicativo Web.

## Próxima etapa

* [Desenvolver e implantar um aplicativo Web com o Microsoft WebMatrix](web-sites-dotnet-using-webmatrix.md) -- saiba como editar um Aplicativo Web do Serviço de Aplicativo no WebMatrix. 
* [Crie um aplicativo ASP.NET MVC com autenticação e banco de dados SQL e implante o Serviço de Aplicativo do Azure](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)-- saiba como criar um novo aplicativo Web no Serviço de Aplicativo do Azure a partir do Visual Studio.

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, confira: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal para o portal de visualização, confira: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png



<!--HONumber=54-->