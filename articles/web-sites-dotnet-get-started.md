<properties 
	pageTitle="Introdução aos sites da Web do Azure para ASP.NET" 
	description="Este tutorial mostra como criar um projeto Web ASP.NET no Visual Studio 2013 e implantá-lo em um site da Web do Azure. Em menos de 15 minutos, você terá um aplicativo em funcionamento na nuvem." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="11/12/2014" 
	ms.author="tdykstra"/>

# Introdução aos Sites do Azure e ASP.NET

Este tutorial mostra como criar um aplicativo Web ASP.NET e implantá-lo em um site do Azure usando o Visual Studio 2013 ou o Visual Studio 2013 for Web Express. O tutorial pressupõe que você não tem nenhuma experiência anterior com o Azure ou o ASP.NET. Ao concluir o tutorial, você terá um aplicativo Web simples em funcionamento na nuvem.

Você aprenderá a:

* Como habilitar seu computador para desenvolvimento do Azure ao instalar o SDK do Azure.
* Como criar um projeto Web Visual Studio ASP.NET e implantá-lo em um Site do Azure.
* Como fazer uma alteração no projeto Web e a reimplantá-lo.
* Como usar o Portal de Gerenciamento do Azure para monitorar e gerenciar seu site.

Para concluir este tutorial, você precisa de uma conta do Azure:

* Você pode [abrir uma conta do Azure gratuitamente](/pt-br/pricing/free-trial/?WT.mc_id=A261C142F) - você obtém créditos que podem ser usados para experimentar serviços pagos do Azure e, mesmo após eles serem utilizados, você pode manter a conta e utilizar os serviços gratuitos do Azure, como os sites.
* Você pode [ativar benefícios para assinantes do MSDN](/pt-br/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - todos os meses, sua assinatura do MSDN oferece créditos que podem ser usados para serviços pagos do Azure.</li>

> [AZURE.NOTE] Se você quiser iniciar com Websites do Azure antes de assinar uma conta, acesse <a href="https://trywebsites.azurewebsites.net">https://trywebsites.azurewebsites.net</a>, em que é possível criar imediatamente um site de iniciante ASP.NET de vida curta nos Websites do Azure gratuitamente.

A ilustração a seguir mostra o aplicativo concluído:

![Web site home page](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

### Segmentos do tutorial

* [Inscrever-se para o Microsoft Azure (Vídeo)](#video)
* [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment)
* [Criar um aplicativo Web ASP.NET no Visual Studio](#create-an-aspnet-web-application)
* [Implantar o aplicativo no Azure](#deploy-the-application-to-azure)
* [Fazer uma alteração e reimplantar](#make-a-change-and-redeploy)
* [Monitore e gerencie o site no Portal de Gerenciamento](#monitor-and-manage-the-site-in-the-management-portal)
* [Próximas etapas](#next-steps)

##<a name="video"></a>Inscrever-se para o Microsoft Azure (Vídeo)

Neste vídeo, Scott Hanselman apresenta como é fácil inscrever-se para uma avaliação gratuita do Microsoft Azure. (Duração: 1:58)

> [AZURE.VIDEO sign-up-for-microsoft-azure]

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Criar um aplicativo Web ASP.NET

A primeira etapa é criar um projeto de aplicativo Web. O Visual Studio criará automaticamente o Site do Azure que implantará o seu projeto para mais tarde. O diagrama a seguir ilustra o que está acontecendo nessas duas etapas.

![Diagram showing project creation and deployment steps](./media/web-sites-dotnet-get-started-vs2013/createdeploydiagram.png)

1. Abra o Visual Studio 2013 ou o Visual Studio 2013 Express for Web.

2. No menu **Arquivo**, clique em **Novo Projeto**.

3. Na caixa de diálogo **Novo projeto**, clique em **C#** > **Web** > **Aplicativo Web ASP.NET**. Se preferir, você pode escolher **Visual Basic**.

3. Verifique se o **.NET Framework 4.5** está selecionado como a estrutura de destino.

4. Nomeie o aplicativo **MyExample** e clique em **OK**.

	![New Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png)

5. Na caixa de diálogo **Novo Projeto ASP.NET**, selecione o modelo **MVC**. Se prefere trabalhar com Web Forms ASP.NET, você pode selecionar o modelo **Web Forms**. 

	[MVC e Web Forms](http://www.asp.net/get-started/websites) são estruturas do ASP.NET para criar sites. Para este tutorial, você pode escolher qualquer uma das duas opções; porém, se você escolher Web Forms, você precisará editar  *Default.aspx* posteriormente nos pontos em que o tutorial instrui você a editar  *Index.cshtml*.

7. Clique em **Alterar Autenticação**. 

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png)

6. Na caixa de diálogo **Alterar Autenticação**, clique em **Sem Autenticação** e clique em **OK**.

	![No Authentication](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)

	O aplicativo de exemplo que você está criando não permitirá aos usuários fazer logon. A seção [Próximas etapas](#next-steps) possui um link para um tutorial que implanta autenticação e autorização.

5. Na caixa de diálogo **Novo Projeto ASP.NET**, deixe as configurações sob **Azure** inalteradas e, por último, clique em **OK**. 

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)

	As configurações padrão especificam que o Visual Studio crie um Site do Azure para seu projeto Web. Na próxima seção do tutorial, você implantará o projeto Web para o site recém-criado.

	(A legenda da caixa de seleção deve ser **Host na nuvem** ou **Criar recursos remotos**. Em ambos os casos, o efeito é o mesmo.)
	
5. Se você ainda não tiver entrado no Azure, o Visual Studio solicitará a você que o faça. Clique em **Entrar**.

	![Sign in to Azure](./media/web-sites-dotnet-get-started-vs2013/signin.png)

6. Na caixa de diálogo **Entrar no Azure**, insira a ID e a senha da conta que você usa para gerenciar sua assinatura do Azure.
	
	Quando estiver conectado, a caixa de diálogo **Configurar Definições de Site do Azure** perguntará quais recursos você deseja criar.

	![Signed in to Azure](./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png)

3. O Visual Studio fornece um **Nome de site** padrão, que o Azure usará como o prefixo de URL do aplicativo. Se preferir, digite um nome de site diferente.

	A URL completa consistirá no que você digitar aqui mais o *.azurewebsites.net* (conforme exibido ao lado da caixa de texto **Nome do Site**). Por exemplo, se o nome do site é `MyExample6442`, the URL will be `MyExample6442.azurewebsites.net`. A URL precisa ser exclusiva. Se outra pessoa já tiver usado o valor digitado por você, você verá um ponto de exclamação vermelho à direita em vez de uma marca de verificação verde, e você precisará inserir outro nome de site.

4. Na lista suspensa **Região**, escolha o local mais próximo de você.

	Essa configuração especifica em qual datacenter do Azure o seu site será executado. Para este tutorial, você pode selecionar qualquer região e isso não resultará em nenhuma diferença perceptível, mas para um site de produção, é melhor que seu servidor Web esteja tão próximo quanto possível dos navegadores acessando seu site, para reduzir a [latência](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Deixe os campos de banco de dados inalterados.

	Para este tutorial, você não está usando um banco de dados. A seção [Próximas etapas](#next-steps) no final do tutorial vincula a um tutorial que mostra como usar um banco de dados.

6. Clique em **OK**.

	Em poucos segundos, o Visual Studio cria o projeto Web na pasta especificada e cria o site na região do Azure que você especificou.  

	A janela **Gerenciador de Soluções** mostra os arquivos e as pastas no novo projeto.

	![Solution Explorer](./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png)

	A **Atividade de Publicação da Web** mostra que o site foi criado.

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png)

	E você pode ver o site no Gerenciador de Servidores.

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/siteinse.png)



## Implantar o aplicativo no Azure

7. Na janela **Atividade de Publicação da Web**, clique em **Publicar MyExample nesse site agora**.

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png)

	Em poucos segundos, o assistente de **Publicar Web** é exibido. 

	As configurações das quais o Visual Studio precisa para implantar seu projeto no Azure foram salvas em um  *publish profile*. O assistente permite que você revise e altere essas mudanças.

8. Na guia **Conexão** do assistente **Publicar Web**, clique em **Validar Conexão** para certificar-se de que o Visual Studio pode se conectar ao Azure para implantar o projeto Web.

	![Validate connection](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png)

	Quando a conexão tiver sido validada, uma marca de seleção verde será mostrada ao lado do botão **Validar Conexão**. 

9. Clique em **Próximo**.

	![Successfully validated connection](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png)

10. Na guia **Configurações**, clique em **Avançar**.

	![Settings tab](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)

	Você pode aceitar os valores padrão para **Configuração** e **Opções de Publicação de Arquivos**.

	O menu suspenso **Configuração** permite que você implante uma compilação de Depuração para depuração remota. A seção [Próximas etapas](#next-steps) vincula a um tutorial que mostra como executar o Visual Studio no modo de depuração remotamente.

	Se você expandir **Opções de Publicação de Arquivos**, verá diversas configurações que permitem a você gerenciar cenários que não se aplicam a este tutorial:
 
	* Remova os arquivos adicionais presentes no destino.
	  
		Exclua quaisquer arquivos do servidor que não estejam em seu projeto. Você pode precisar disso se estiver implantando um projeto em um site para o qual você já implantou um projeto diferente, anteriormente.

	* Faça a pré-compilação durante a publicação. 
	 
		Isso pode reduzir os tempos de aquecimento para primeira solicitação em sites grandes.

	* Exclua os arquivos da pasta App_Data. 
	 
		Para teste, algumas vezes você tem um arquivo de banco de dados de SQL Server na pasta App_Data que você não deseja implantar na produção.
	
11. Na guia **visualização**, clique em **Iniciar Visualização**.

	![StartPreview button in the Preview tab](./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png)

	A guia exibe uma lista dos arquivos que serão copiados no servidor. A exibição da visualização não é obrigatória para publicar o aplicativo, mas é uma função reconhecidamente útil.

12. Clique em **Publicar**.

	![StartPreview file output](./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png)

	O Visual Studio inicia o processo de cópia dos arquivos no servidor do Azure.

	As janelas **Saída** e **Atividade de Publicação da Web** mostram as ações de implantação que foram executadas e relata a conclusão bem-sucedida da implantação.

	![Output window reporting successful deployment](./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png)

	Após a implantação bem-sucedida, o navegador padrão abre automaticamente a URL do site implantado, e
	o aplicativo que você criou agora está em execução na nuvem. A URL na barra de endereços do navegador mostra que o site está sendo carregado da Internet.

	![Web site running in Azure](./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png)

13. Feche o navegador.

## Fazer uma alteração e reimplantar

Nesta seção do tutorial, você altera o título **h1** da home page, executa o projeto localmente no seu computador de desenvolvimento para verificar a alteração e, em seguida, implanta a alteração no Azure.

2. Abra o arquivo *Views/Home/Index.cshtml* ou *.vbhtml*no **Explorador de Soluções**, altere o cabeçalho **h1** a partir do "ASP.NET" para "ASP.NET e Azure" e salve o arquivo. 

	![MVC index.cshtml](./media/web-sites-dotnet-get-started-vs2013/index.png)

	![MVC h1 change](./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png)

1. Pressione CTRL + F5 para ver o título atualizado executando o site no seu computador local.

	![Web site running locally](./media/web-sites-dotnet-get-started-vs2013/localandazure.png)

	A URL  `http://localhost` mostra que ele está sendo executado no computador local. Por padrão, ele é executado no IIS Express, que é uma versão simples do IIS desenvolvida para uso durante o desenvolvimento de aplicativo Web.


1. Feche o navegador.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Publicar**.

	![Chooose Publish](./media/web-sites-dotnet-get-started-vs2013/choosepublish.png)

	A guia Visualizar do assistente **Publicar Web** é exibida. Se for necessário alterar quaisquer configurações de publicação, você poderá escolher uma guia diferente, mas agora tudo o que você deseja fazer é reimplantar com as mesmas configurações.

2. No assistente de **Publicar Web**, clique em **Publicar**.

	![Click Publish](./media/web-sites-dotnet-get-started-vs2013/clickpublish.png)

	O Visual Studio implanta o projeto no Azure e abre o site no navegador padrão.

	![Changed site deployed](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

**Dica:** você pode habilitar a barra de ferramentas do **Web One Click Publish** para a implantação ainda mais rápida. Clique em **Exibição** > **Barras de ferramentas**e, em seguida, selecione **Web One Click Publish**. A barra de ferramentas permite que você selecione um perfil, clique em um botão para publicar ou clique em um botão para abrir o assistente **Publicar na Web**.

![Web One Click Publish Toolbar](./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png)

## Monitore e gerencie o site no Portal de Gerenciamento

O [Portal de Gerenciamento do Azure](/pt-br/services/management-portal/) é uma interface da Web que permite a você gerenciar e monitorar seus serviços do Azure, como o site que você acabou de criar. Nesta seção do tutorial, você descobre um pouco do que pode fazer no portal.

1. Em seu navegador, vá para [http://manage.windowsazure.com](),e entre com suas credenciais do Azure.

	O portal exibe uma lista de seus serviços do Azure.

2. Clique no nome do seu site.

	![Portal home page with new web site called out](./media/web-sites-dotnet-get-started-vs2013/portalhome.png)
  
3. Clique na guia **Painel**.

	A guia **Painel** exibe uma visão geral das estatísticas de utilização e link para uma série de funções de gerenciamento de site comumente utilizadas. Em **Visão Rápida**, você também encontrará um link para a home page de seu aplicativo.

	![Portal web site dashboard tab](./media/web-sites-dotnet-get-started-vs2013/portaldashboard.png)
  
	Nesse período, seu site não teve muito tráfego e talvez não exiba nada no gráfico. Se você navegar até seu aplicativo, atualize a página algumas vezes, então atualize o portal da página **Painel**; você verá algumas estatísticas serem exibidas. Você pode clicar na guia **Monitorar** para obter mais detalhes.

4. Clique na guia **Configurar**.

	A guia [Configurar](/pt-br/documentation/articles/web-sites-configure//) permite que você controle a versão do .NET utilizada no site, habilita recursos como [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) e [registro em log de diagnóstico](web-sites-enable-diagnostic-log.md), define [connection string values](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)e mais. 

	![Portal web site configure tab](./media/web-sites-dotnet-get-started-vs2013/portalconfigure.png)
  
5. Clique na guia **Escala**.

	Para as camadas pagas do serviço de Sites, a guia [Escala](web-sites-scale.md) permite que você controle o tamanho e número das máquinas que atendem seu aplicativo Web para gerenciar variações no tráfego.

	Você pode dimensionar manualmente, ou então configurar critérios ou agendamentos para dimensionamento automático.

	![Portal website scale tab](./media/web-sites-dotnet-get-started-vs2013/portalscale.png)

Esses são apenas alguns dos recursos do Portal de Gerenciamento. Você também pode criar novos sites, excluir sites existentes, parar e reiniciar sites e gerenciar outros tipos de serviços do Azure, como bancos de dados e máquinas virtuais.  

**Dica:** há um novo portal de gerenciamento no modo de visualização que substituirá aquele que você estava usando. Para obter mais informações, consulte [Portal de Visualização do Azure](/pt-br/overview/preview-portal/).

## Próximas etapas

Neste tutorial, você viu como criar um aplicativo Web simples e implantá-lo em um Site do Azure. Estes são alguns tópicos e recursos relacionados para saber mais sobre eles.

* Outras maneiras de implantar um projeto Web

	Neste tutorial, você viu a maneira mais rápida de criar um site e implantá-lo em uma única operação. Para uma visão geral de outras maneiras de implantar, usando o Visual Studio ou por [implantação automática](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) de um [sistema de controle do código-fonte](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), consulte [Como implantar um Site do Azure](/pt-br/documentation/articles/web-sites-deploy/"). 

	O Visual Studio também pode gerar scripts do Windows PowerShell, que permitem a você automatizar a implantação. Para obter mais informações, consulte [Automatizar tudo (Compilando aplicativos de nuvem do mundo real com o Azure) ](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* Como gerenciar um site no Visual Studio

	Para obter informações sobre o que você pode fazer no **Gerenciador de Servidores**, consulte [Solucionando problemas de Sites do Azure no Visual Studio](/pt-br/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

* Como solucionar problemas de um site

	O Visual Studio fornece ferramentas que facilitam a exibição dos logs do Azure à medida que são gerados em tempo real. Você também pode executar em modo de depuração remotamente no Azure. Para obter mais informações, consulte [Solucionando problemas de Sites do Azure no Visual Studio](/pt-br/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

* Como adicionar funcionalidade de banco de dados e autorização

	Para um tutorial que mostra como acessar um banco de dados e restringe algumas funções do site para usuários autorizados, consulte [implantar um aplicativo ASP.NET MVC seguro com associação, OAuth e banco de dados SQL em um site do Azure](/pt-br/develop/net/tutorials/web-site-with-sql-database/).

* Como adicionar um nome de domínio personalizado e SSL

	Para obter informações sobre como usar SSL e seu próprio domínio (por exemplo www.contoso.comem vez de contoso.azurewebsites.net), consulte os seguintes recursos:

	* [Configurando um nome de domínio personalizado para um Site do Azure](web-sites-custom-domain-name.md). 
	* [Habilitar HTTPS para um site do Azure](http://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/)

* Como evitar tempo de espera para voltar ao funcionamento após intervalos em ociosidade 

	Por padrão, os sites serão descarregados se estiverem ociosos durante um determinado período de tempo. A primeira solicitação após isso acontecer precisa aguardar que o site seja carregado novamente. Para evitar esse tempo de espera, você pode habilitar o recurso AlwaysOn. Para obter mais informações, consulte as opções de configuração em [Como configurar sites](http://azure.microsoft.com/documentation/articles/web-sites-configure/).

* Como adicionar recursos em tempo real como chat

	Se o seu site for incluir recursos em tempo real (como um serviço de chat, um jogo, um indicador de ações, entre outros), você poderá obter o melhor desempenho utilizando o [ASP.NET SignalR](http://www.asp.net/signalr) com o método de transporte [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/). Para obter mais informações, consulte [Usando SignalR com sites do Microsoft Azure](http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites). 

* Como escolher entre Sites, Serviços de nuvem e VMs do Azure para aplicativos Web

	No Azure você pode executar aplicativos Web nos Sites, conforme mostrado neste tutorial ou em serviços de nuvem ou máquinas virtuais. Para obter mais informações, consulte [Modelos de execução do Azure](/pt-br/develop/net/fundamentals/compute/) e [Sites, Serviços de Nuvem e VMs do Azure: Quando usar qual opção??](/pt-br/manage/services/web-sites/choose-web-app-service/).



<!--HONumber=42-->
