<properties linkid="develop-dotnet-website-from-gallery" urlDisplayName="Website from Gallery" pageTitle="Create an Orchard CMS website from the gallery in Azure" metaKeywords="Azure build website, manage website Azure" description="A tutorial that teaches you how to create a new website in Azure. Also learn how to launch and manage your site using the Management Portal." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create an Orchard CMS website from the gallery in Azure" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Criar um site do Pomar CMS da galeria no Azure

A galeria disponibiliza uma ampla gama de aplicativos populares da Web desenvolvidos pela Microsoft, outras empresas e iniciativas de software de código aberto. Os aplicativos da Web criados a partir da galeria não exigem a instalação de qualquer software que não seja o navegador usado para se conectar ao [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure]. Para obter mais informações sobre os aplicativos da web na galeria, consulte [Windows Web App galeria][Windows Web App galeria]

Neste tutorial, você aprenderá:

-   Como criar um novo site da galeria

-   Como iniciar e gerenciar seu site do Portal de Gerenciamento

Você criará site Pomar CMS que usa um modelo padrão. [Pomar][Pomar] é um gratuito, de código aberto.Aplicativo baseado em NET do CMS que permite que você crie sites personalizados e orientados por conteúdo. Pomar CMS inclui uma estrutura de extensibilidade por meio da qual você pode [fazer o download de outros módulos e temas][fazer o download de outros módulos e temas] para personalizar seu site. A ilustração a seguir mostra o site pomar CMS que você criará.

![Blog do pomar][Blog do pomar]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Criar um site do Pomar da galeria

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

2.  Clique no ícone **Novo** na parte inferior esquerda do portal.

    ![Criar Novo][Criar Novo]

3.  Clique no ícone **Site**, então clique em **Da Galeria**.

    ![Criar Da Galeria][Criar Da Galeria]

4.  Localize e clique no **pomar CMS** ícone na lista e, em seguida, clique na seta para continuar.

    ![Pomar da lista][Pomar da lista]

5.  Na página **Configurar seu aplicativo**, insira ou selecione valores para todos os campos:

-   Insira um nome de URL de sua escolha
-   Selecione a região mais próxima de seus usuários. (Isso lhe dará o melhor desempenho).

    ![configurar o aplicativo][configurar o aplicativo]

1.  Clique na marca de seleção no canto inferior direito da caixa para iniciar a implantação do novo site CMS pomar.

Azure iniciam construir e implantar as operações. Enquanto o site estiver sendo criado e implantado, o status dessas operações será exibido na parte inferior do Portal de Gerenciamento de Sites. Depois que todas as operações forem realizadas, será exibida uma mensagem que indicará que seu site foi criado.

## Inicie e gerencie seu site do Pomar

1.  Clique no nome do seu novo site sobre o **Sites da Web** página e, em seguida, clique em **procurar** na parte inferior do portal para abrir a página de boas-vindas do site.

    ![iniciar painel][iniciar painel]

    ![botão Procurar][botão Procurar]

2.  Insira as informações de configuração apropriadas exigidas pelo Pomar e clique em **Concluir Configuração** para finalizar a configuração e abrir a página de logon do site.

    ![login do pomar][login do pomar]

    Você terá um novo site do Pomar, parecido com a captura de tela a seguir.

    ![o site do pomar][Blog do pomar]

3.  Siga os detalhes no [pomar documentação][pomar documentação] para saber mais sobre pomar e configurar o novo site.

## <span class="short-header">Próximas etapas</span>Próxima etapa

-   [Desenvolver e implantar um site da web com o Microsoft WebMatrix][Desenvolver e implantar um site da web com o Microsoft WebMatrix]– Saiba como editar um site Azure no WebMatrix.
-   [Implantar um aplicativo de segurança ASP.NET MVC com associação, OAuth e Banco de Dados SQL para um Site do Azure][Implantar um aplicativo de segurança ASP.NET MVC com associação, OAuth e Banco de Dados SQL para um Site do Azure]– Saiba como criar um novo site da web do Visual Studio.

  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Windows Web App galeria]: http://www.microsoft.com/web/gallery/categories.aspx
  [Pomar]: http://www.orchardproject.net/
  [fazer o download de outros módulos e temas]: http://gallery.orchardproject.net/
  [Blog do pomar]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Criar Novo]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
  [Criar Da Galeria]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
  [Pomar da lista]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
  [configurar o aplicativo]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
  [iniciar painel]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
  [botão Procurar]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
  [login do pomar]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
  [pomar documentação]: http://docs.orchardproject.net/
  [Desenvolver e implantar um site da web com o Microsoft WebMatrix]: /pt-br/develop/net/tutorials/website-with-webmatrix/
  [Implantar um aplicativo de segurança ASP.NET MVC com associação, OAuth e Banco de Dados SQL para um Site do Azure]: /pt-br/develop/net/tutorials/web-site-with-sql-database/
