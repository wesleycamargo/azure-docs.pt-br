<properties title="Create an Umbraco website from the gallery in Microsoft Azure" pageTitle="Create an Umbraco website from the gallery in Microsoft Azure" description="required" metaKeywords="Azure, gallery, Umbraco, web site, website" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm"></tags>

# Criar um site do Umbraco com base na galeria do Microsoft Azure

Umbraco CMS é um sistema de gerenciamento de conteúdo de software livre que pode ser usado para criar vários aplicativos de pequenos a complexos. A galeria Aplicativos dos Sites do Azure oferece uma grande variedade de aplicativos Web populares desenvolvidos pela Microsoft, por empresas terceiras e iniciativas de software livre. A galeria permite criar um site do Umbraco CMS em apenas alguns minutos aplicando os kits de início ou integrando o próprio design.

Este artigo demonstra o novo Portal de Visualização do Azure, que simplifica muito o gerenciamento de recursos. O novo portal do Azure foi projetado para agilizar o processo de entrega do software colocando-se ferramentas de plataforma cruzada, tecnologias e serviços da Microsoft e de seus parceiros em um único espaço de trabalho. Em vez de usar recursos independentes como Sites do Azure, projetos do Visual Studio ou bancos de dados, é possível criar, gerenciar e analisar todo o aplicativo como um grupo de recursos único.

Neste tutorial, você aprenderá:

-   Como criar um novo site por meio da galeria usando o novo Portal de Visualização do Azure
-   Como compilar um site de blog usando o Umbraco CMS

## Criar um site com base na Galeria do portal do Azure

1.  Faça logon no [Portal de Gerenciamento do Microsoft Azure][Portal de Gerenciamento do Microsoft Azure].

2.  Escolha o ícone da **Galeria do Azure**.

    ![Escolher Galeria da Web][Escolher Galeria da Web]

3.  Na **Galeria** , selecione a guia **Web** e selecione **Umbraco CMS**.

    ![Selecionar Umbraco na Galeria da Web][Selecionar Umbraco na Galeria da Web]

4.  Para criar um novo site do Umbraco CMS, clique em **Criar**.

    ![Clicar em Criar][Clicar em Criar]

5.  A próxima etapa é configurar todos os recursos associados ao Umbraco CMS. Nesse caso, os recursos são um site e um banco de dados SQL Server. Primeiro, selecione **Site** para definir as configurações do site, como **URL**, **Plano de Hospedagem na Web**, **Configurações do Aplicativo Web** e **Local**.

    ![Configurar recursos][Configurar recursos]

6.  Agora configure o banco de dados. Selecione **Banco de Dados** e escolha **Criar um novo banco de dados**. Esse exemplo cria um SQL Server para o banco de dados no Azure.

    ![Criar um SQL Server no Azure][Criar um SQL Server no Azure]

7.  Agora que o site e o banco de dados estão configurados, você pode começar a implantar o aplicativo clicando em **Criar** na parte inferior da primeira folha **Umbraco CMS** vista na imagem anterior.

    ![Clicar em Criar][1]

Depois que a implantação for concluída, a placa inicial no portal mostra que o Grupo de Recursos do Umbraco CMS, no caso **UmbracoCMSgroup**, foi criado. Na seção **Resumo**, clique no nome do site (nesse caso, **umbracocmsgroup**) para ver as propriedades do site. Também na seção **Resumo**, é possível selecionar o recurso do banco de dados para ver as propriedades do banco de dados associado.

![][]

## Iniciar e configurar o site do Umbraco CMS

1.  Na faixa de detalhes do site, clique em **Procurar** para procurar o site (nesse caso, umbracocmsgroup.azurewebsites.net.)

    ![Procurar o site][Procurar o site]

2.  Quando você procura o site, o Umbraco CMS inicia o assistente de instalação. Insira as informações solicitadas e clique em **Personalizar**.

    ![Instalar assistente do Umbraco][Instalar assistente do Umbraco]

3.  Insira os detalhes da conexão e da autenticação do banco de dados que o Umbraco usará. Selecione **Microsoft SQL Azure** para o tipo de banco de dados. É possível obter a cadeia de conexão do banco de dados na seção **Configurações do Site**.

    ![Configurar o banco de dados][Configurar o banco de dados]

4.  Se estiver começando a usar o Umbraco CMS, você poderá selecionar um kit de início do site. Do contrário, clique em **Não, obrigado. Não desejo instalar um site de início**.

    ![Instalar um site de início][Instalar um site de início]

5.  O instalador do Umbraco concluirá a instalação do aplicativo. Depois que o aplicativo for configurado, você será redirecionado para o painel administrativo do Umbraco CMS.

    ![Painel do Umbraco CMS][Painel do Umbraco CMS]

6.  Agora você criará uma página de texto de exemplo que publicará. Selecione **Conteúdo**, **Estouro** e **TextPage**.

    ![Criar uma página de texto][Criar uma página de texto]

7.  Insira um título e um conteúdo para a página de texto, conforme mostrado abaixo. Quando terminar, clique em **Salvar e publicar**.

    ![Inserir um título e um conteúdo][Inserir um título e um conteúdo]

8.  Aguarde a publicação da página. Quando a publicação for concluída, você receberá um pequeno alerta no canto inferior direito da tela. Agora é possível procurar conteúdo no site.

    ![Página do site publicada][Página do site publicada]

É isso! Você criou com êxito um site de blog usando o Umbraco CMS em apenas alguns minutos.

## Recursos adicionais

[Documentação do Umbraco][Documentação do Umbraco]

[Tutoriais em vídeo do Umbraco][Tutoriais em vídeo do Umbraco]

[Visão geral do Portal de Visualização do Microsoft Azure][Visão geral do Portal de Visualização do Microsoft Azure]

[Documentação do Portal de Visualização do Microsoft Azure][Documentação do Portal de Visualização do Microsoft Azure]

[Portal de Visualização do Azure (Channel 9)][Portal de Visualização do Azure (Channel 9)]

[Documentação dos Sites do Microsoft Azure][Documentação dos Sites do Microsoft Azure]

<!-- IMAGES -->

  [Portal de Gerenciamento do Microsoft Azure]: https://portal.azure.com/
  [Escolher Galeria da Web]: ./media/web-sites-gallery-umbraco/01Startboard.PNG
  [Selecionar Umbraco na Galeria da Web]: ./media/web-sites-gallery-umbraco/02WebGallery.PNG
  [Clicar em Criar]: ./media/web-sites-gallery-umbraco/03UmbracoCMS.PNG
  [Configurar recursos]: ./media/web-sites-gallery-umbraco/04AppSettings.PNG
  [Criar um SQL Server no Azure]: ./media/web-sites-gallery-umbraco/05NewServer.PNG
  [1]: ./media/web-sites-gallery-umbraco/06UmbracoCMSGroup.PNG
  []: ./media/web-sites-gallery-umbraco/07UmbracoCMSGroupBlade.PNG
  [Procurar o site]: ./media/web-sites-gallery-umbraco/08UmbracoCMSGroupRunning.PNG
  [Instalar assistente do Umbraco]: ./media/web-sites-gallery-umbraco/09InstallUmbraco7.png
  [Configurar o banco de dados]: ./media/web-sites-gallery-umbraco/10ConfigureYourDatabase.png
  [Instalar um site de início]: ./media/web-sites-gallery-umbraco/11InstallAStarterWebsite.png
  [Painel do Umbraco CMS]: ./media/web-sites-gallery-umbraco/14FriendlyCMS.PNG
  [Criar uma página de texto]: ./media/web-sites-gallery-umbraco/15CreateItemUnderOverflow.PNG
  [Inserir um título e um conteúdo]: ./media/web-sites-gallery-umbraco/16EnterAName.PNG
  [Página do site publicada]: ./media/web-sites-gallery-umbraco/17MyPage.PNG
  [Documentação do Umbraco]: http://our.umbraco.org/documentation
  [Tutoriais em vídeo do Umbraco]: https://umbraco.com/help-and-support/video-tutorials.aspx
  [Visão geral do Portal de Visualização do Microsoft Azure]: http://azure.microsoft.com/pt-br/overview/preview-portal/
  [Documentação do Portal de Visualização do Microsoft Azure]: http://azure.microsoft.com/pt-br/documentation/preview-portal/
  [Portal de Visualização do Azure (Channel 9)]: http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal
  [Documentação dos Sites do Microsoft Azure]: http://azure.microsoft.com/pt-br/documentation/services/web-sites/
