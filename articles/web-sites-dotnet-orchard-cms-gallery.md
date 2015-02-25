<properties 
	pageTitle="Criar um site do Orchard CMS da galeria no Azure" 
	description="Um tutorial que ensina a criar um novo site no Azure. Saiba também como iniciar e gerenciar seu site usando o Portal de gerenciamento." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/21/2014" 
	ms.author="tomfitz"/>

# Criar um site do Orchard CMS da galeria no Azure

A galeria disponibiliza uma ampla gama de aplicativos Web populares desenvolvidos pela Microsoft, por outras empresas e através de iniciativas de software livre. Os aplicativos da Web criados a partir da galeria não exigem a instalação de qualquer software que não seja o navegador usado para se conectar ao [Portal de Gerenciamento do Azure](http://manage.windowsazure.com). Para obter mais informações sobre os aplicativos Web na galeria, consulte [Galeria de aplicativos Web do Windows](http://www.microsoft.com/web/gallery/categories.aspx).

Neste tutorial, você aprenderá:

- Como criar um novo site da galeria

- Como iniciar e gerenciar seu site do Portal de Gerenciamento
 
Você criará um site do Orchard CMS que utiliza um modelo padrão. [Orchard](http://www.orchardproject.net/) é um gratuito, de código aberto.Aplicativo baseado em NET do CMS que permite que você crie sites personalizados e orientados por conteúdo. O Orchard CMS inclui uma estrutura de extensibilidade por meio da qual você pode [fazer o download de outros módulos e temas](http://gallery.orchardproject.net/) para personalizar seu site. A ilustração a seguir mostra o site pomar CMS que você criará.

![Orchard blog][13]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

<h2>Criar um site do Orchard da galeria</h2>

1. Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).

2. Clique no ícone **Novo** na parte inferior esquerda do portal.
	
	![Create New][1]

3. Clique no ícone **Site** e clique em **Da Galeria**.
	
	![Create From Gallery][2]

4. Localize e clique no **pomar CMS** ícone na lista e, em seguida, clique na seta para continuar.
	
	![Orchard from list][3]

5. Na página **Configurar seu aplicativo**, insira ou selecione valores para todos os campos:
	
- Insira um nome de URL de sua escolha.	
- Selecione a região mais próxima de seus usuários. (Isso lhe dará o melhor desempenho.)

	![configure your app][4]

6. Clique na marca de seleção no canto inferior direito da caixa para iniciar a implantação do novo site CMS Orchard.

Azure iniciará a construção e implantação das operações. Enquanto o site estiver sendo criado e implantado, o status dessas operações será exibido na parte inferior do Portal de Gerenciamento de Sites. Depois que todas as operações forem realizadas, será exibida uma mensagem que indicará que seu site foi criado.

<h2>Inicie e gerencie seu site do Orchard</h2>

1. Clique no nome do seu novo site na página **Sites** e, em seguida, clique em **Procurar** na parte inferior do portal para abrir a página de boas-vindas do site.

	![launch dashboard][5]

	![browse button][12]

2. Insira as informações de configuração apropriadas exigidas pelo Orchard e clique em **Concluir Configuração** para finalizar a configuração e abrir a home page do site.

	![login to Orchard][7]

	Você terá um novo site Orchard parecido com a captura de tela a seguir.  

	![your Orchard site][13]

3. Siga os detalhes na [Documentação do Orchard](http://docs.orchardproject.net/) para saber mais sobre o Orchard e configurar o novo site.

<h2>Próxima etapa</h2>
* [Desenvolver e implantar um site da web com o Microsoft WebMatrix](/pt-br/develop/net/tutorials/website-with-webmatrix/) - Saiba como editar um site Azure no WebMatrix.
* [Implantar um aplicativo de segurança ASP.NET MVC com associação, OAuth e banco de dados SQL para um Site do Azure](/pt-br/develop/net/tutorials/web-site-with-sql-database/)- Saiba como criar um novo site do Visual Studio.

[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png




<!--HONumber=42-->
