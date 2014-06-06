<properties linkid="develop-net-tutorials-get-started-vs2013" urlDisplayName="Introdução ao ASP.NET com o Azure" pageTitle="Introdução ao ASP.NET com o Azure" metaKeywords="" description="Este tutorial mostra como criar um projeto Web ASP.NET no Visual Studio 2013 e implantá-lo em um Site do Azure. Em menos de 15 minutos, você terá um aplicativo em funcionamento na nuvem." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Introdução ao Azure e ASP.NET" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />


# Introdução ao Azure e ASP.NET

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/develop/net/tutorials/get-started/" title="Visual Studio 2013" class="current">Visual Studio 2013</a><a href="/pt-br/develop/net/tutorials/get-started-vs2012/" title="Visual Studio 2012">Visual Studio 2012</a></div>

Este tutorial mostra como criar um aplicativo Web ASP.NET e implantá-lo em um site do Azure usando o Visual Studio 2013 ou o Visual Studio 2013 for Web Express. O tutorial pressupõe que você não tem nenhuma experiência anterior com o Azure ou o ASP.NET. Ao concluir o tutorial, você terá um aplicativo Web simples em funcionamento na nuvem.

É possível abrir uma conta do Azure gratuitamente e, se você ainda não tiver o Visual Studio 2013, o SDK instalará automaticamente o Visual Studio 2013 para o Web Express. Portanto, você pode começar a desenvolver para o Azure de maneira totalmente gratuita.
 
Você aprenderá a:

* Como habilitar a máquina para desenvolvimento do Azure instalando o SDK do Azure.
* Como criar um projeto Web Visual Studio ASP.NET e implantá-lo em um Site do Azure.
* Como fazer uma alteração no projeto e a reimplantá-lo.

A ilustração a seguir mostra o aplicativo concluído:

![Home page do site](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

>[WACOM.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Se não tiver uma conta, você poderá <a href="/pt-br/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">ativar os benefícios de assinante do MSDN</a> ou <a href="/pt-br/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">inscrever-se para uma avaliação gratuita</a>.
 
### Segmentos do tutorial

* [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment)
* [Criar um projeto Web ASP.NET no Visual Studio](#create-an-asp.net-web-project)
* [Implantar o aplicativo no Azure](#deploy-the-application-to-azure)
* [Fazer uma alteração e reimplantar](#make-a-change-and-redeploy)
* [Próximas etapas](#next-steps)

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]


## Criar um aplicativo Web ASP.NET

A primeira etapa é criar um projeto de aplicativo Web. O Visual Studio criará automaticamente o Site do Azure que implantará o seu projeto para mais tarde. 

1. Abra o Visual Studio 2013 ou o Visual Studio 2013 Express for Web.

2. No menu **Arquivo**, clique em **Novo Projeto**.

	![Novo projeto no menu Arquivo](./media/web-sites-dotnet-get-started-vs2013/gs13newproj.png)

3. Na caixa de diálogo **Novo Projeto**, expanda **C#** ou **Visual Basic** e selecione **Web** em **Modelos Instalados**. Em seguida, selecione **Aplicativo Web ASP.NET**.

3. Verifique se o **.NET Framework 4.5** está selecionado como a estrutura de destino.

4. Nomeie o aplicativo **MyExample** e clique em **OK**.

	![Caixa de diálogo Novo Projeto](./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png)

5. Na caixa de diálogo **Novo Projeto ASP.NET**, selecione o modelo **MVC** ou o modelo **Web Forms** e, em seguida, clique em **Alterar Autenticação**.

	[MVC e Web Forms](http://www.asp.net/get-started/websites) são estruturas do ASP.NET para criar sites. Se você não tem preferência e planeja fazer outros tutoriais do Azure, o MVC é uma boa opção porque existem mais tutoriais do Azure que usam MVC.

	![Caixa de diálogo Novo Projeto ASP .NET](./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png)

6. Na caixa de diálogo **Alterar Autenticação**, clique em **Sem Autenticação** e clique em **OK**.

	![Sem Autenticação](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)

	O aplicativo de exemplo que você está criando não possui recursos que exijam que os usuários façam logon. A seção [Próximas etapas](#next-steps) no final do tutorial vincula a um tutorial que implementa autenticação e autorização.

5. Na caixa de diálogo em **Azure**, marque a caixa de seleção e a caixa suspensa definida como **Site**. 

	A legenda da caixa de seleção deve ser **Host na nuvem** ou **Criar recursos remotos**. Em ambos os casos, o efeito é o mesmo. 

	Essas configurações especificam que o Visual Studio crie um Site do Azure para seu projeto Web. Você implantará o projeto Web para o site recém-criado. (Como alternativa, você pode alterar a seleção da caixa suspensa para que o Visual Studio crie uma máquina virtual do Azure que seja executado no IIS, mas este tutorial não fornece detalhes das etapas para essa opção.)

5. Na caixa de diálogo **Novo Projeto ASP .NET**, clique em **OK**.

	![Caixa de diálogo Novo Projeto ASP .NET](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)

	A captura de tela mostra o modelo MVC selecionado; se você escolheu **Web Forms**, ele estará selecionado. 

5. Se você ainda não tiver entrado no Azure, o Visual Studio solicitará para fazê-lo. Clique em **Entrar**.

	![Entrar no Azure](./media/web-sites-dotnet-get-started-vs2013/signin.png)

6. Insira a ID e a senha da conta que você usa para gerenciar sua assinatura do Azure.

	![Entrar no Azure](./media/web-sites-dotnet-get-started-vs2013/signindb.png)

	Quando estiver conectado, a caixa de diálogo **Configurar Definições de Site do Azure** perguntará quais recursos você deseja criar.

	![Conectado no Azure](./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png)

3. O Visual Studio fornece um **Nome de site** padrão, que o Azure usará como o prefixo de URL do aplicativo. Se preferir, digite um nome de site diferente.

	A URL completa consistirá no que você digitar aqui mais o domínio que você vê ao lado da caixa de texto. Por exemplo, se o nome do site for `MyExample6442`, a URL será `MyExample6442.azurewebsites.net`. Se outra pessoa já tiver usado a URL digitada, você verá um ponto de exclamação vermelho à direita em vez de uma marca de verificação verde, e você precisará inserir outro valor.

4. Na lista suspensa **Região**, escolha o local mais próximo de você.

	Essa configuração especifica em qual data center do Azure seu site será executado. 

5. Deixe os campos de banco de dados inalterados.

	Para este tutorial, você não está usando um banco de dados. A seção [Próximas etapas](#next-steps) no final do tutorial vincula a um tutorial que mostra como usar um banco de dados.

6. Clique em **OK**.

	Em poucos segundos, o Visual Studio cria o projeto Web na pasta especificada e cria o site na região do Azure que você especificou.  

	A janela **Gerenciador de Soluções** mostra os arquivos e as pastas no novo projeto. (A captura de tela é para um projeto de Web Forms; um projeto de MVC tem arquivos e pastas diferentes.)

	![Gerenciador de Soluções](./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png)

	A **Atividade de Publicação da Web** mostra que o site foi criado.

	![Site criado](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png)


## Implantar o aplicativo no Azure

7. Na janela **Atividade de Publicação da Web**, clique em **Publicar MyExample nesse site agora**.

	![Site criado](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png)

	Em poucos segundos, o assistente de **Publicar Web** é exibido. O assistente cria um novo *publicar perfil* que contém configurações, como a URL do site que o Visual Studio precisa para implantar o seu projeto no Azure. O perfil é salvo automaticamente para que mais tarde, quando fizer alterações no projeto, você possa facilmente reimplantar o projeto no mesmo site.

8. Na guia **Conexão** do assistente **Publicar Web**, clique em **Validar Conexão** para certificar-se de que o Visual Studio pode se conectar ao Azure para implantar o projeto Web.

	![Validar a conexão](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png)

	Quando a conexão tiver sido validada, uma marca de seleção verde será mostrada ao lado do botão **Validar Conexão**. 

9. Clique em **Próximo**.

	![Conexão validada com êxito](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png)

10. Na guia **Configurações**, clique em **Avançar**.

	![Guia Configurações](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)

	Você pode aceitar as configurações padrão nesta guia.  Você está implantando uma compilação de versão e não precisa excluir arquivos no servidor de destino, pré-compilar o aplicativo ou excluir arquivos na pasta App_Data.   A seção [Próximas etapas](#next-steps) no final do tutorial vincula a um tutorial que implanta uma compilação de depuração e mostra como executar o Visual Studio no modo de depuração remotamente.

11. Na guia **Visualização**, clique em **Iniciar Visualização**.

	![Botão StartPreview na guia Visualização](./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png)

	A guia exibe uma lista dos arquivos que serão copiados no servidor. A exibição da visualização não é obrigatória para publicar o aplicativo, mas é uma função reconhecidamente útil.

12. Clique em **Publicar**.

	![Saída do arquivo StartPreview](./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png)

	O Visual Studio inicia o processo de cópia dos arquivos no servidor do Azure.

	As janelas **Saída** e **Atividade de Publicação da Web** mostram as ações de implantação que foram executadas e relata a conclusão bem-sucedida da implantação.

	![Relatório de implantação bem-sucedida na janela de saída](./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png)

	Após a implantação bem-sucedida, o navegador padrão abre automaticamente a URL do site implantado e
	o aplicativo que você criou agora está em execução na nuvem. A URL na barra de endereços do navegador mostra que o site está sendo carregado da Internet.

	![Site em execução no Azure](./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png)

13. Feche o navegador.

## Fazer uma alteração e reimplantar

Nesta seção opcional do tutorial, altere o projeto Web, execute-o localmente no computador de desenvolvimento para verificar a alteração e, em seguida, implante a alteração no Azure.

2. Se você criou um projeto MVC, abra o arquivo *Views/Home/Index.cshtml* ou *. vbhtml* no **Gerenciador de Soluções**, altere o título **h1** de "ASP.NET" para "ASP.NET e Azure" e salve o arquivo. 

	![index.cshtml de MVC](./media/web-sites-dotnet-get-started-vs2013/index.png)

	![Alteração de h1 no MVC](./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png)

1. Se você criou um projeto Web Forms, abra o arquivo *Default.aspx* no **Gerenciador de Soluções**, altere o título **h1** de "ASP.NET" para "ASP.NET e Azure" e salve o arquivo.

	![default. aspx no Web Forms](./media/web-sites-dotnet-get-started-vs2013/default.png)

	![Alteração de h1 no Web Forms](./media/web-sites-dotnet-get-started-vs2013/wfandazure.png)

1. Pressione CTRL + F5 para testar as alterações executando o site no computador local.

	![Site em execução localmente](./media/web-sites-dotnet-get-started-vs2013/localandazure.png)

	A URL `http://localhost` mostra que está sendo executado no computador local. Por padrão, ele é executado no IIS Express, que é uma versão simples do IIS desenvolvida para uso durante o desenvolvimento de aplicativos Web.


1. Feche o navegador.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Publicar**.

	![Selecionar Publicar](./media/web-sites-dotnet-get-started-vs2013/choosepublish.png)

	A guia Visualizar do assistente **Publicar Web** é exibida. Se for necessário alterar quaisquer configurações de publicação, você poderá escolher uma guia diferente, mas agora tudo o que você deseja fazer é reimplantar com as mesmas configurações.

2. No assistente de **Publicar Web**, clique em **Publicar**.

	![Clicar em Publicar](./media/web-sites-dotnet-get-started-vs2013/clickpublish.png)

	O Visual Studio implanta o projeto no Azure e abre o site no navegador padrão.

	![Site implantado alterado](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

Uma forma ainda mais rápida de reimplantar quando você não precisa alterar as configurações de publicação é usar a barra de ferramentas **Publicação Web com Um Clique**.

![Barra de ferramentas Publicação Web com Um Clique](./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png)

A barra de ferramentas está desabilitada por padrão; ative-a no menu **Exibir - Barras de ferramentas**. Você pode usá-lo para selecionar um perfil, clique em um botão para publicar ou clique em um botão para abrir o assistente **Publicar Web**. 

## Próximas etapas

Neste tutorial, você viu como criar um aplicativo Web simples e implantá-lo em um Site do Azure. Estes são alguns tópicos e recursos relacionados para saber mais sobre eles:

* Outras maneiras de implantar um projeto Web
* Como gerenciar, dimensionar e solucionar problemas de um site
* Como adicionar funcionalidade de banco de dados e autorização
* Como escolher entre Sites, Serviços de nuvem e VMs para aplicativos Web

### Outras maneiras de implantar um projeto Web

Neste tutorial, você viu a maneira mais rápida de criar um site e implantá-lo em uma única operação. Para uma visão geral de outras maneiras de implantar, usando o Visual Studio ou por [implantação automática](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) de um [sistema de controle do código-fonte](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), consulte [Como implantar um Site do Azure (a página pode estar em inglês)](/pt-br/documentation/articles/web-sites-deploy/"). 

Uma maneira de automatizar a implantação é fazê-lo usando scripts do Windows PowerShell. O Visual Studio e o Azure simplificam essa tarefa gerando scripts do PowerShell que você pode usar para executar as mesmas operações de implantação que você faz no Visual Studio. Para obter mais informações, consulte [Automatizar tudo (Compilando aplicativos de nuvem do mundo real com o Azure) (a página pode estar em inglês)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

### Como gerenciar um site

O [Portal de Gerenciamento do Azure](pt-br/services/management-portal/) é uma interface da Web que permite a você gerenciar e monitorar seus serviços do Azure, como sites, serviços de nuvem, máquinas virtuais, bancos de dados e muito mais. Para ver o que você pode fazer no portal, acesse [https://manage.windowsazure.com]()e entre com o nome de usuário e a senha de sua conta com direitos de administrador para a sua assinatura do Azure. Para obter mais informações, consulte [Como gerenciar sites (a página pode estar em inglês)](/pt-br/manage/services/web-sites/how-to-manage-websites/).

Você também pode criar algumas funções de gerenciamento de site diretamente no **Gerenciador de Servidores** no Visual Studio. Para obter informações sobre o que você pode fazer no **Gerenciador de Servidores**, consulte [Solucionando problemas de Sites do Azure no Visual Studio (a página pode estar em inglês)](/pt-br/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

### Como dimensionar um site

Quando seu site é público e começa a receber mais tráfego, os tempos de resposta podem se tornar lentos. Para corrigir isso, você pode adicionar facilmente recursos do servidor na guia **Dimensionar** do portal de gerenciamento. Para obter mais informações, consulte [Como dimensionar um site (a página pode estar em inglês)](/pt-br/manage/services/web-sites/how-to-scale-websites/). (A adição de recursos do servidor para dimensionar um site não é gratuita.)

### Como solucionar problemas de um site

Convém examinar a saída de rastreamento ou o log para obter ajuda na solução de problemas. O Visual Studio fornece ferramentas internas para facilitar a exibição dos logs do Azure à medida que são gerados em tempo real. Você também pode executar em modo de depuração remotamente no Azure. Para obter mais informações, consulte [Solucionando problemas de Sites do Azure no Visual Studio (a página pode estar em inglês)](/pt-br/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

### Como adicionar funcionalidade de banco de dados e autorização

A maioria dos sites de produção usam um banco de dados e restringem algumas funções de site a usuários autorizados determinados. Para um tutorial que mostra como começar com o acesso ao banco de dados, autenticação e autorização, consulte [Implantar um aplicativo ASP.NET MVC seguro com associação, OAuth e Banco de Dados SQL a um Site do Azure (a página pode estar em inglês)](/pt-br/develop/net/tutorials/web-site-with-sql-database/).

### Como decidir se o seu aplicativo deve ser executado em um Serviço de Nuvem

No Azure você pode executar aplicativos Web nos Sites, conforme mostrado neste tutorial ou em serviços de nuvem ou máquinas virtuais. Para obter mais informações, consulte [Modelos de execução do Azure (a página pode estar em inglês)](/pt-br/develop/net/fundamentals/compute/) e [Sites, Serviços de Nuvem e VMs do Azure: quando usar o quê? (a página pode estar em inglês)](/pt-br/manage/services/web-sites/choose-web-app-service/).

