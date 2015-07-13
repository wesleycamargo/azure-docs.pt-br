<properties 
	pageTitle="Criar um aplicativo Web do Umbraco a partir do Marketplace do Microsoft Azure" 
	description="Criar um sistema de gerenciamento de conteúdo Umbraco e implantar no Aplicativos Web do Serviço de Aplicativo do Azure." 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="tomfitz"/>

#Criar um aplicativo Web do Umbraco a partir do Marketplace do Microsoft Azure#

Umbraco CMS é um sistema de gerenciamento de conteúdo de software livre que pode ser usado para criar vários aplicativos de pequenos a complexos. O Azure Marketplace oferece uma ampla gama de aplicativos Web populares desenvolvidos pela Microsoft, por outras empresas e por iniciativas de software livre. A galeria permite criar um aplicativo do Umbraco CMS em apenas alguns minutos aplicando os kits de início ou integrando o próprio design.

Este artigo demonstra o Portal de Visualização do Azure, que simplifica muito o gerenciamento de recursos. O Portal de Visualização do Azure foi projetado para agilizar o processo de entrega do software colocando-se ferramentas de plataforma cruzada, tecnologias e serviços da Microsoft e de seus parceiros em um único espaço de trabalho. Em vez de usar recursos independentes, como Aplicativos Web do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714), projetos do Visual Studio ou bancos de dados, é possível criar, gerenciar e analisar todo o aplicativo como um grupo de recursos único.

Neste tutorial, você aprenderá:

- Como criar um novo aplicativo Web por meio do Marketplace usando o Portal de Visualização do Azure
- Como compilar um site de blog usando o Umbraco CMS 

##Criar um aplicativo Web a partir do Marketplace no Portal de Visualização do Azure

1. Faça logon no [portal de visualização do Azure](https://portal.azure.com/).

2. Escolha o ícone do **Marketplace**.
	
	![Escolher Galeria da Web][01Startboard]
	
3. No **Marketplace**, selecione a guia **Aplicativos Web** e selecione **Umbraco CMS**.
	
	![Selecionar Umbraco na Galeria da Web][02WebGallery]
	
4. Para criar um novo aplicativo Web do Umbraco CMS, clique em **Criar**.
	
	![Clicar em Criar][03UmbracoCMS]
	
5. A próxima etapa é configurar todos os recursos associados ao Umbraco CMS. Nesse caso, os recursos são um aplicativo Web e um banco de dados SQL Server. Primeiro, selecione **Aplicativo Web** para definir as configurações do aplicativo Web, como **URL**, **Plano de Serviço de Aplicativo**, **Configurações do Aplicativo Web** e **Local**.
	
	![Configurar recursos][04AppSettings]
	
6. Agora configure o banco de dados. Selecione **Banco de Dados** e, em seguida, escolha **Servidor**. Esse exemplo cria um SQL Server para o banco de dados no Azure.
	
	![Criar um SQL Server no Azure][05NewServer]
	
7. Agora que o aplicativo Web e o banco de dados estão configurados, você pode começar a implantar o aplicativo clicando em **Criar** na parte inferior da primeira folha **Umbraco CMS** vista na imagem anterior.
	
	![Clicar em Criar][06UmbracoCMSGroup]
	
Após a implantação, o portal exibirá a folha do grupo de recursos do aplicativo Web do Umbraco CMS. Na seção **Resumo**, clique no nome do aplicativo Web para ver suas propriedades. Também na seção **Resumo**, é possível selecionar o recurso do banco de dados para ver as propriedades do banco de dados associado.
	
![][07UmbracoCMSGroupBlade]

## Iniciar e configurar o aplicativo Web do Umbraco CMS ##

1. Na folha de detalhes do aplicativo Web, clique em **Procurar** para procurar o aplicativo Web.
	
	![Procurar o site][08UmbracoCMSGroupRunning]
	
2. Quando você procura o aplicativo Web, o Umbraco CMS inicia o assistente de instalação. Insira as informações solicitadas e clique em **Personalizar**.
	
	![Instalar assistente do Umbraco][09InstallUmbraco7]
	
3. Insira os detalhes da conexão e da autenticação do banco de dados que o Umbraco usará. Selecione **Microsoft SQL Azure** para o tipo de banco de dados. É possível obter a cadeia de conexão do banco de dados na seção **Configurações do Site** do aplicativo Web.
	
	![Configurar o banco de dados][10ConfigureYourDatabase]
	
4. Se estiver começando a usar o Umbraco CMS, você poderá selecionar um kit de início do site. Do contrário, clique em **Não, obrigado. Não desejo instalar um site de início**.
	
	![Instalar um site de início][11InstallAStarterWebsite]
	
5. O instalador do Umbraco concluirá a instalação do aplicativo. Depois que o aplicativo for configurado, você será redirecionado para o painel administrativo do Umbraco CMS.
	
	![Painel do Umbraco CMS][14FriendlyCMS]
	
6. Agora você criará uma página de texto de exemplo que publicará. Selecione **Conteúdo**, **Estouro** e **TextPage**.
	
	![Criar uma página de texto][15CreateItemUnderOverflow]
	
7. Insira um título e um conteúdo para a página de texto, conforme mostrado abaixo. Quando terminar, clique em **Salvar e publicar**.
	
	![Inserir um título e um conteúdo][16EnterAName]
	
8. Aguarde a publicação da página. Quando a publicação for concluída, você receberá um pequeno alerta no canto inferior direito da tela. Agora é possível procurar novo conteúdo no aplicativo Web.
	
	![Página do site publicada][17MyPage]
	

É isso! Você criou com êxito um aplicativo Web de blog usando o Umbraco CMS em apenas alguns minutos.

##Recursos adicionais

[Documentação do Umbraco](http://our.umbraco.org/documentation)

[Tutoriais em vídeo do Umbraco](https://umbraco.com/help-and-support/video-tutorials.aspx)

[Documentação do Portal do Azure](../preview-portal.md)

[Portal do Azure (Channel 9)](http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal)

[Documentação do Aplicativos Web do Serviço de Aplicativo do Azure](/documentation/services/websites/)

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, confira: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal para o portal de visualização, confira: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.


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
 

<!---HONumber=62-->