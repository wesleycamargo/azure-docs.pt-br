<properties title="Create an Umbraco website from the gallery in Microsoft Azure" pageTitle="Criar um site do Umbraco com base na galeria do Microsoft Azure" description="obrigatório" metaKeywords="Azure, gallery, Umbraco, web site, website" services="web-sites" solutions="web" documentationCenter="" authors="tomfitz" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="10/21/2014" ms.author="tomfitz" />

#Criar um site do Umbraco com base na galeria do Microsoft Azure#

Umbraco CMS é um sistema de gerenciamento de conteúdo de software livre que pode ser usado para criar vários aplicativos de pequenos a complexos. A galeria Aplicativos dos Sites do Azure oferece uma grande variedade de aplicativos Web populares desenvolvidos pela Microsoft, por empresas terceiras e iniciativas de software livre. A galeria permite criar um site do Umbraco CMS em apenas alguns minutos aplicando os kits de início ou integrando o próprio design. 

Este artigo demonstra o novo Portal de Visualização do Azure, que simplifica muito o gerenciamento de recursos. O novo portal do Azure foi projetado para agilizar o processo de entrega do software colocando-se ferramentas de plataforma cruzada, tecnologias e serviços da Microsoft e de seus parceiros em um único espaço de trabalho. Em vez de usar recursos independentes como Sites do Azure, projetos do Visual Studio ou bancos de dados, é possível criar, gerenciar e analisar todo o aplicativo como um grupo de recursos único. 

Neste tutorial, você aprenderá:

- Como criar um novo site por meio da galeria usando o novo Portal de Visualização do Azure
- Como compilar um site de blog usando o Umbraco CMS 

##Criar um site com base na Galeria do portal do Azure

1. Faça logon no [Portal de Gerenciamento do Microsoft Azure](https://portal.azure.com/).

2. Escolha o ícone **Galeria do Azure**.
	
	![Choose Web Gallery][01Startboard]
	
3. Na **Galeria**, selecione a guia **Web** e selecione **Umbraco CMS**.
	
	![Select Umbraco in the Web Gallery][02WebGallery]
	
4. Para criar um novo site do Umbraco CMS, clique em **Criar**.
	
	![Click Create][03UmbracoCMS]
	
5. A próxima etapa é configurar todos os recursos associados ao Umbraco CMS. Nesse caso, os recursos são um site e um banco de dados SQL Server. Primeiro, selecione **Site** para definir as configurações do site, como **URL**, **Plano de Hospedagem na Web**, **Configurações do Aplicativo Web** e **Local**. 
	
	![Configure resources][04AppSettings]
	
6. Agora configure o banco de dados. Selecione **Banco de Dados** e escolha **Criar um novo banco de dados**. Esse exemplo cria um SQL Server para o banco de dados no Azure.
	
	![Create a SQL Server on Azure][05NewServer]
	
7. Agora que o site e o banco de dados estão configurados, você pode começar a implantar o aplicativo clicando em **Criar** na parte inferior da primeira folha **Umbraco CMS** vista na imagem anterior.
	
	![Click Create][06UmbracoCMSGroup]
	
Depois que a implantação for concluída, a placa inicial no portal mostra que o Grupo de Recursos do Umbraco CMS, no caso **UmbracoCMSgroup**, foi criado. Na seção **Resumo**, clique no nome do site (nesse caso, **umbracocmsgroup**) para ver as propriedades do site. Também na seção **Resumo**, é possível selecionar o recurso do banco de dados para ver as propriedades do banco de dados associado.
	
![][07UmbracoCMSGroupBlade]

## Iniciar e configurar o site do Umbraco CMS ##

1. Na faixa de detalhes do site, clique em **Procurar** para procurar o site (nesse caso, umbracocmsgroup.azurewebsites.net.)
	
	![Browse to your site][08UmbracoCMSGroupRunning]
	
2. Quando você procura o site, o Umbraco CMS inicia o assistente de instalação. Insira as informações solicitadas e clique em **Personalizar**.
	
	![Install Umbraco wizard][09InstallUmbraco7]
	
3. Insira os detalhes da conexão e da autenticação do banco de dados que o Umbraco usará. Selecione **Microsoft SQL Azure** para o tipo de banco de dados.  É possível obter a cadeia de conexão do banco de dados na seção **Configurações do Site**.
	
	![Configure your database][10ConfigureYourDatabase] 
	
4. Se estiver começando a usar o Umbraco CMS, você poderá selecionar um kit de início do site. Do contrário, clique em **Não, obrigado. Não desejo instalar um site de início**.
	
	![Install a starter website][11InstallAStarterWebsite]
	
5. O instalador do Umbraco concluirá a instalação do aplicativo. Depois que o aplicativo for configurado, você será redirecionado para o painel administrativo do Umbraco CMS.
	
	![Umbraco CMS dashboard][14FriendlyCMS]
	
6. Agora você criará uma página de texto de exemplo que publicará. Selecione **Conteúdo**, **Estouro** e **TextPage**.
	
	![Create a text page][15CreateItemUnderOverflow]
	
7. Insira um título e conteúdo para a página de texto, conforme mostrado abaixo. Quando terminar, clique em **Salvar e publicar**.
	
	![Enter a title and some content][16EnterAName]
	
8. Aguarde a publicação da página. Quando a publicação for concluída, você receberá um pequeno alerta no canto inferior direito da tela. Agora é possível procurar conteúdo no site. 
	
	![Published web site page][17MyPage]
	

É isso! Você criou com êxito um site de blog usando o Umbraco CMS em apenas alguns minutos. 

##Recursos adicionais

[Documentação do Umbraco](http://our.umbraco.org/documentation)

[Tutoriais em vídeo do Umbraco](https://umbraco.com/help-and-support/video-tutorials.aspx)

[Visão geral do Portal de Visualização do Microsoft Azure](http://azure.microsoft.com/pt-br/overview/preview-portal/)

[Documentação do Portal de Visualização do Microsoft Azure](http://azure.microsoft.com/pt-br/documentation/preview-portal/)

[Portal de Visualização do Azure (Channel 9)](http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal) 

[Documentação dos Sites do Microsoft Azure](http://azure.microsoft.com/pt-br/documentation/services/web-sites/)


<!-- IMAGES -->
[01Startboard]: ./media/web-sites-gallery-umbraco/01Startboard.PNG
[02WebGallery]: ./media/web-sites-gallery-umbraco/02WebGallery.PNG
[03UmbracoCMS]: ./media/web-sites-gallery-umbraco/03UmbracoCMS.PNG
[04AppSettings]: ./media/web-sites-gallery-umbraco/04AppSettings.PNG
[05NewServer]: ./media/web-sites-gallery-umbraco/05NewServer.PNG
[06UmbracoCMSGroup]: ./media/web-sites-gallery-umbraco/06UmbracoCMSGroup.PNG
[07UmbracoCMSGroupBlade]: ./media/web-sites-gallery-umbraco/07UmbracoCMSGroupBlade.PNG
[08UmbracoCMSGroupRunning]: ./media/web-sites-gallery-umbraco/08UmbracoCMSGroupRunning.PNG
[09InstallUmbraco7]: ./media/web-sites-gallery-umbraco/09InstallUmbraco7.png
[10ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/10ConfigureYourDatabase.png
[11InstallAStarterWebsite]: ./media/web-sites-gallery-umbraco/11InstallAStarterWebsite.png
[12ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/12ConfigureYourDatabase.png
[14FriendlyCMS]: ./media/web-sites-gallery-umbraco/14FriendlyCMS.PNG
[15CreateItemUnderOverflow]: ./media/web-sites-gallery-umbraco/15CreateItemUnderOverflow.PNG
[16EnterAName]: ./media/web-sites-gallery-umbraco/16EnterAName.PNG
[17MyPage]: ./media/web-sites-gallery-umbraco/17MyPage.PNG
