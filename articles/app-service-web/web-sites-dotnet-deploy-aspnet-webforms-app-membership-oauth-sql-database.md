<properties 
	pageTitle="Criar e implantar um aplicativo Web Forms do ASP.NET seguro com associação, OAuth e Banco de Dados SQL no Serviço de Aplicativo do Azure" 
	description="Este tutorial mostra como criar um aplicativo com Web Forms ASP.NET 4.5 seguro que incorpora um banco de dados SQL e implanta o aplicativo no Azure." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Erikre" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="erikre"/>


# Criar e implantar um aplicativo Web Forms do ASP.NET seguro com associação, OAuth e Banco de Dados SQL no Serviço de Aplicativo do Azure

##Visão geral
Este tutorial mostra como criar um aplicativo com Web Forms ASP.NET 4.5 seguro que incorpora um banco de dados SQL e implanta o aplicativo no Azure.

>[AZURE.NOTE] 
Para obter uma versão para MVC deste tutorial, consulte [Criar um aplicativo ASP.NET MVC com autenticação e o banco de dados SQL e implantar no Serviço de Aplicativo do Azure](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).

É possível abrir uma conta do Azure gratuitamente e, se você ainda não tiver o Visual Studio 2013, o SDK instalará automaticamente o Visual Studio 2013 para o Web Express. É possível iniciar o desenvolvimento para o Azure de maneira gratuita.

Este tutorial pressupõe que você não tem nenhuma experiência anterior utilizando o Microsoft Azure. Ao concluir este tutorial, você terá um aplicativo Web em funcionamento na nuvem e usando um banco de dados também localizado na nuvem.

Você aprenderá a:

- Criar um projeto de Web Forms ASP.NET 4.5 seguro e publicá-lo no Serviço de Aplicativo do Azure.
- Utilizar OAuth e o banco de dados de associação do ASP.NET para proteger seu aplicativo.
- Utilizar um único banco de dados tanto para dados do aplicativo quanto para associação.
- Utilizar o Scaffolding dos Web Forms para criar páginas web que permitem a você modificar dados.
- Como usar a nova API de associação para adicionar usuários e funções.
- Como usar um banco de dados SQL para armazenar dados no Azure.

Você criará um aplicativo Web de lista de contatos simples criado no ASP.NET de Web Forms 4.5 e que usa o Entity Framework para acesso ao banco de dados. A imagem a seguir mostra a página de Web Forms que permite acesso de leitura e gravação ao banco de dados:

![Contatos - Editar página](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms00.png)

>[AZURE.NOTE] 
Para concluir este tutorial, você precisa de uma conta do Azure. Se não tem uma conta, você pode <a href="/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">ativar os benefícios de assinante MSDN</a> ou <a href="/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">inscrever-se em uma avaliação gratuita</a>. Se você quiser obter uma introdução ao Azure antes de se registrar com uma conta, acesse [Experimentar o Serviço de Aplicativo](https://tryappservice.azure.com/), em que é possível criar imediatamente um site de iniciante ASP.NET de curta duração no Azure gratuitamente. Nenhum cartão de crédito é exigido, sem compromissos.

##Configurar o ambiente de desenvolvimento 
Para começar, defina o ambiente de desenvolvimento instalando o Visual Studio 2013 e o SDK do Azure para .NET.

1. Instale o [Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=306566), se você ainda não o tiver instalado.  
2. Instale o [SDK do Azure para o Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409). Este tutorial requer o Visual Studio 2013 antes de se instalar o SDK do Azure para o Visual Studio 2013. Dependendo de quantas dependências de SDK você já tiver no seu computador, a instalação do SDK pode demorar bastante, de vários minutos a meia hora ou mais.  

3. Se for solicitado que você execute ou salve o executável da instalação, clique em **Executar**.
4. Na janela do **Web Platform Installer**, clique em **Instalar** e prossiga com a instalação.  
	![Web Platform Installer](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-01.png)  

      Se você já tem o SDK instalado, não há nenhum item a instalar. O número de itens a instalar aparecerá no canto inferior esquerdo da janela do **Web Platform Installer**.

5. Se você ainda não tem o **Visual Studio Atualização 2**, baixe e instale o **[Visual Studio 2013 Atualização 2](http://www.microsoft.com/download/details.aspx?id=42666)** ou posterior.

	>[AZURE.NOTE]  
	É necessário que você instale o Visual Studio 2013 Atualização 2 ou posterior para poder utilizar o Google OAuth 2.0 e para utilizar SSL localmente, sem advertências. Além disso, você precisa da Atualização 2 para utilizar o Scaffolding dos Web Forms.

Quando a instalação for concluída, você terá todo o necessário para iniciar o desenvolvimento.

##Configurar o ambiente do Azure
Nesta seção você configurará o ambiente do Azure criando um Azure e um banco de dados SQL no Azure.

###Criar um aplicativo Web e um banco de dados SQL no Azure 
Neste tutorial, seu aplicativo Web do Azure será executado em um ambiente de hospedagem compartilhado, o que significa que ele é executado em máquinas virtuais (VMs) que são compartilhadas com outros aplicativos Web no Serviço de Aplicativo do Azure. Um ambiente de hospedagem compartilhado é uma maneira de começar na nuvem a um baixo custo. Mais tarde, se seu tráfego da Web aumentar, o aplicativo poderá ser dimensionado para atender à necessidade executando em VMs dedicadas. Se precisar de uma arquitetura mais complexa, você poderá migrar para um Serviço de Nuvem do Azure. Os serviços de nuvem são executados em VMs dedicadas que podem ser configuradas de acordo com suas necessidades.

O Banco de Dados SQL no Azure é um serviço de banco de dados relacional baseado em que é fundamentado em tecnologias do SQL Server. As ferramentas e os aplicativos que funcionam com o SQL Server também funcionam com o Banco de Dados SQL.

1. No [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/), clique em **Aplicativos Web** na guia à esquerda e clique em **Novo**.  
	![Web Platform Installer](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-02.png)
2. Clique em **Aplicativo Web** e, em seguida, clique em **Criação Personalizada**.  
	![Criação Personalizada](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-03.png)  
	O assistente **Novo Aplicativo Web - Criação Personalizada** é aberto.  

3. Na etapa **Criar Aplicativo Web** do assistente, digite uma cadeia de caracteres na caixa **URL** para usar como URL exclusiva do seu aplicativo. A URL completa consistirá no que você digitar aqui mais o sufixo que você vê ao lado da caixa de texto. A ilustração mostra uma URL que provavelmente será executada, portanto, **você deve escolher uma URL diferente**.  
	![Contatos - Criar Novo Site](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-04.png)
4. Na lista suspensa Plano de Hospedagem na Web, escolha a região mais próxima de você. Essa configuração especifica em qual datacenter sua VM executará.
5. Na lista suspensa **Banco de dados**, escolha **Criar um banco de dados SQL gratuito de 20 MB**.
6. Na caixa **Nome da Cadeia de Conexão do Banco de Dados**, deixe o valor padrão de *DefaultConnection*.
7. Clique na seta na parte inferior da caixa. O assistente avança para a etapa **Especificar configurações do banco de dados**.
8. Na caixa **Nome**, digite *`ContactDB`*.  
	![Configurações do banco de dados](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-05.png)  
9. Na caixa **Servidor**, selecione **Novo Servidor de Banco de Dados SQL**. Como alternativa, se tiver criado um banco de dados SQL Server, você poderá selecionar esse SQL Server no controle suspenso.
10. Defina a **Região** como a mesma área na qual você criou o aplicativo Web.
11. Insira um **Nome de Logon** e **Senha** de administrador. Se selecionou **Novo servidor do Banco de Dados SQL**, você não irá digitar um nome e uma senha existentes aqui; digite um novo nome e uma senha que você está definindo agora para usar mais tarde ao acessar o banco de dados. Se você tiver selecionado um SQL Server criado anteriormente, será solicitada a senha para o nome da conta do SQL Server anterior que você criou. Para este tutorial, não selecionaremos a caixa **Avançado**.
12. Clique na marca de seleção no canto inferior direito da caixa para indicar que você concluiu.

O **Portal de Gerenciamento do Azure** retorna para a página **Aplicativos Web** e a coluna **Status** mostra que o site está em criação. Depois de pouco tempo (normalmente menos de um minuto), a coluna **Status** mostra que o site foi criado com êxito. Na barra de navegação à esquerda, o número de sites presentes na conta é exibido próximo ao ícone **Aplicativo Web** e o número de bancos de dados aparece ao lado do ícone **Bancos de Dados SQL**.
##Criar um aplicativo de Web Forms ASP.NET 
Você criou um aplicativo Web, mas ainda não há conteúdo nele. A próxima etapa é criar o aplicativo Web do Visual Studio que será publicado no Azure.
###Criar o projeto 
1. Selecione **Novo Projeto** do menu **Arquivo** no Visual Studio.  
	![Menu Arquivo - Novo projeto](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms01.png)  
2. Selecione o grupo de modelos **Modelos** -> **Visual C#** -> **Web** à esquerda. 
3. Selecione o modelo **Aplicativo Web ASP.NET** na coluna central.
4. Nomeie o projeto *ContactManager* e clique em **OK**.  
	![Caixa de diálogo Novo Projeto](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms02.png)  

      O nome do projeto nessa série de tutoriais é **ContactManager**. É recomendável que você utilize exatamente esse nome de projeto, de modo que o código fornecido pela série de tutoriais funcione conforme esperado.

5. Na caixa de diálogo **Novo Projeto ASP.NET**, selecione o modelo **Web Forms**. Desmarque a caixa de seleção **Host na nuvem** se ele estiver selecionado e clique em **OK**.  
	![Caixa de diálogo Novo Projeto ASP .NET](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms03.png)  
	O aplicativo Web Forms será criado.
###Atualizar a página mestra
Nos Web Forms ASP.NET, as páginas mestras permitem que você crie um layout consistente para as páginas em seu aplicativo. Uma única página mestra define a aparência e comportamento padrão que você deseja para todas as páginas (ou um grupo de páginas) em seu aplicativo. Então, você pode criar páginas de conteúdo individuais com o conteúdo que você deseja exibir. Quando os usuários solicitam as páginas de conteúdo, o ASP.NET as combina com a página mestra para produzir um resultado que combine o layout da página mestra com o conteúdo da página de conteúdo. O novo site precisa do nome do aplicativo e de um link atualizado. O link direcionará para a página que exibirá os detalhes de contato. Para fazer essas alterações, você modificará o HTML na página mestra.

1. No **Gerenciador de Soluções**, encontre a página *Site.Master* e abra-a.
2. Se a página estiver em modo de exibição de **Design**, alterne para o modo de exibição de **Código-Fonte**.
3. Atualize a página mestra modificando ou adicionando a marcação destacada em amarelo:

<pre class="prettyprint">
&lt;%@ Master Language="C#" AutoEventWireup="true" CodeBehind="Site.master.cs" Inherits="ContactManager.SiteMaster" %>

&lt;!DOCTYPE html>

&lt;html lang="en">
&lt;head runat="server">
    &lt;meta charset="utf-8" />
    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0" />
    &lt;title>&lt;%: Page.Title %> - <mark>Gerenciador de Contatos</mark>&lt;/title>

    &lt;asp:PlaceHolder runat="server">
        &lt;%: Scripts.Render("~/bundles/modernizr") %>
    &lt;/asp:PlaceHolder>
    &lt;webopt:bundlereference runat="server" path="~/Content/css" />
    &lt;link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />

&lt;/head>
&lt;body>
    &lt;form runat="server">
        &lt;asp:ScriptManager runat="server">
            &lt;Scripts>
                &lt;%--Para saber mais sobre empacotamento de script no ScriptManager, consulte http://go.microsoft.com/fwlink/?LinkID=301884 --%>
                &lt;%--Framework Scripts--%>
                &lt;asp:ScriptReference Name="MsAjaxBundle" />
                &lt;asp:ScriptReference Name="jquery" />
                &lt;asp:ScriptReference Name="bootstrap" />
                &lt;asp:ScriptReference Name="respond" />
                &lt;asp:ScriptReference Name="WebForms.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebForms.js" />
                &lt;asp:ScriptReference Name="WebUIValidation.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebUIValidation.js" />
                &lt;asp:ScriptReference Name="MenuStandards.js" Assembly="System.Web" Path="~/Scripts/WebForms/MenuStandards.js" />
                &lt;asp:ScriptReference Name="GridView.js" Assembly="System.Web" Path="~/Scripts/WebForms/GridView.js" />
                &lt;asp:ScriptReference Name="DetailsView.js" Assembly="System.Web" Path="~/Scripts/WebForms/DetailsView.js" />
                &lt;asp:ScriptReference Name="TreeView.js" Assembly="System.Web" Path="~/Scripts/WebForms/TreeView.js" />
                &lt;asp:ScriptReference Name="WebParts.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebParts.js" />
                &lt;asp:ScriptReference Name="Focus.js" Assembly="System.Web" Path="~/Scripts/WebForms/Focus.js" />
                &lt;asp:ScriptReference Name="WebFormsBundle" />
                &lt;%--Scripts do Site--%>
            &lt;/Scripts>
        &lt;/asp:ScriptManager>

        &lt;div class="navbar navbar-inverse navbar-fixed-top">
            &lt;div class="container">
                &lt;div class="navbar-header">
                    &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                        &lt;span class="icon-bar">&lt;/span>
                        &lt;span class="icon-bar">&lt;/span>
                        &lt;span class="icon-bar">&lt;/span>
                    &lt;/button>
                    &lt;a class="navbar-brand" runat="server" <mark>id="ContactDemoLink"</mark> href="~/<mark>Contacts/Default.aspx</mark>"><mark>Contact Demo</mark>&lt;/a>
                &lt;/div>
                &lt;div class="navbar-collapse collapse">
                    &lt;ul class="nav navbar-nav">
                        &lt;li>&lt;a runat="server" href="~/">Home&lt;/a>&lt;/li>
                        &lt;li>&lt;a runat="server" href="~/About">About&lt;/a>&lt;/li>
                        &lt;li>&lt;a runat="server" href="~/Contact">Contact&lt;/a>&lt;/li>
                    &lt;/ul>
                    &lt;asp:LoginView runat="server" ViewStateMode="Disabled">
                        &lt;AnonymousTemplate>
                            &lt;ul class="nav navbar-nav navbar-right">
                                &lt;li>&lt;a runat="server" href="~/Account/Register">Register&lt;/a>&lt;/li>
                                &lt;li>&lt;a runat="server" href="~/Account/Login">Log in&lt;/a>&lt;/li>
                            &lt;/ul>
                        &lt;/AnonymousTemplate>
                        &lt;LoggedInTemplate>
                            &lt;ul class="nav navbar-nav navbar-right">
                                &lt;li>&lt;a runat="server" href="~/Account/Manage" title="Manage your account">Olá, &lt;%: Context.User.Identity.GetUserName()  %> !&lt;/a>&lt;/li>
                                &lt;li>
                                    &lt;asp:LoginStatus runat="server" LogoutAction="Redirect" LogoutText="Log off" LogoutPageUrl="~/" OnLoggingOut="Unnamed_LoggingOut" />
                                &lt;/li>
                            &lt;/ul>
                        &lt;/LoggedInTemplate>
                    &lt;/asp:LoginView>
                &lt;/div>
            &lt;/div>
        &lt;/div>
        &lt;div class="container body-content">
            &lt;asp:ContentPlaceHolder ID="MainContent" runat="server">
            &lt;/asp:ContentPlaceHolder>
            &lt;hr />
            &lt;footer>
                &lt;p>&amp;copy; &lt;%: DateTime.Now.Year %> - <mark>Gerenciador de Contatos</mark>&lt;/p>
            &lt;/footer>
        &lt;/div>
    &lt;/form>
&lt;/body>
&lt;/html>
</pre>

Mais adiante, neste tutorial, você adicionará o scaffolding para Web Forms. O scaffolding criará a página à qual o link “Contato/Demonstração” acima faz referência.
###Executar o aplicativo localmente 
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na página *Default.aspx* e selecione **Definir como página inicial**. 
2. Em seguida, pressione **CTRL+F5** para executar o aplicativo.  
	A página padrão do aplicativo aparece na janela do navegador padrão.
	![Contatos - Criar Novo Site](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms04.png)  

Isso é tudo o que você precisa fazer por enquanto para criar o aplicativo que você implantará no Azure. Posteriormente, você adicionará a funcionalidade do banco de dados, além das páginas necessárias para exibir e editar dados de contato.
###Implantar o Aplicativo no Azure
Agora que você criou e executou seu aplicativo localmente, é hora de implantá-lo no Azure.

1. No Visual Studio, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.  
	![Selecionar Publicar](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms05.png)  
	A caixa de diálogo **Publicar Web** é exibida.  

2. Na guia **Perfil** da caixa de diálogo **Publicar Web**, clique em **Aplicativo Web do Azure**.
	  
3. Se você ainda não fez sua autenticação, clique no botão **Entrar** na caixa de diálogo **Selecionar aplicativo Web existente**. Depois de entrar, selecione o aplicativo Web que você criou na primeira parte desse tutorial. Clique em **OK** para continuar.  
	![Selecionar a caixa de diálogo Site Existente](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms07.png)  
	O Visual Studio baixará as configurações de publicação.
4. Na caixa de diálogo **Publicar na Web**, clique em **Publicar**.  
	![Caixa de diálogo Publicar na Web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms08.png)  
	Você verá o status geral da publicação na janela **Atividade de Publicação Web** no Visual Studio:  
	![Atividade de Publicação na Web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms09.png)  

O aplicativo que você criou agora está em execução na nuvem. Na próxima vez que você implantar o aplicativo a partir do Visual Studio, somente os arquivos alterados (ou novos) serão implantados.  
	![Aplicativo no Navegador](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms10.png)

>[AZURE.NOTE]  
Se ocorrer um erro durante a publicação em um aplicativo Web já estabelecido, é possível limpar o local antes de adicionar os novos arquivos.  
Publique o aplicativo novamente, porém na caixa de diálogo **Publicar na Web**, selecione a guia **Configurações**. Em seguida, defina as configurações como **Depurar** e selecione a opção **Remover os arquivos adicionais presentes no destino**. Selecione **Publicar** para implantar seu aplicativo novamente.  
	![Caixa de diálogo Publicar na Web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms11.png)

##Adicionar um banco de dados ao aplicativo 
Em seguida, você atualizará o aplicativo Web Forms para adicionar a habilidade de exibir e atualizar contatos, além de armazenar os dados no banco de dados padrão. Quando você criou o projeto Web Forms, o banco de dados também foi criado por padrão. O aplicativo usará o Entity Framework para acessar o banco de dados e ler e atualizar dados nele presentes.
###Adicionar Uma Classe de Modelo de Dados 
Você começa criando um modelo de dados simples por meio de código. Esse modelo de dados estará contido em uma classe chamada `Contacts`. O nome da classe `Contacts` foi escolhido para evitar um conflito de nome de classe com a classe `Contact` contida no arquivo Contact.aspx.cs criado pelo modelo de Web Forms.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Models*, clique em **Adicionar** -> **Classe**.  
	![Selecionar Classe](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms12.png)  
	A caixa de diálogo **Adicionar novo item** é exibida.  

2. Nomeie essa nova classe *Contacts.cs*.  
	![Caixa de diálogo Adicionar Novo Item](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13.png)
3. Substitua o código padrão pelo código a seguir:  
	<pre class="prettyprint">
	using System.ComponentModel.DataAnnotations;
	using System.Globalization;
	
	namespace ContactManager.Models
	{
    public class Contacts
    {
        [ScaffoldColumn(false)]
        [Key]
        public int ContactId { get; set; }
        public string Name { get; set; }
        public string Address { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string Zip { get; set; }
        [DataType(DataType.EmailAddress)]
        public string Email { get; set; }
    }
	}
	</pre>

A classe **Contatos** define os dados que você armazenará para cada contato, além de uma chave primária (`ContactID`) que é necessária para o banco de dados. A classe **Contatos** representa os dados de contato que serão exibidos. Cada instância de um objeto Contatos corresponderá a uma linha dentro de uma tabela de banco de dados relacional, sendo que cada propriedade da classe Contatos será mapeada para uma coluna na tabela de banco de dados relacional. Posteriormente neste tutorial, você revisará os dados de contato contidos no banco de dados.

###Scaffolding de Web Forms 
Você criou a classe de modelo **Contatos** acima. Agora, você pode usar o Scaffolder de Web Forms para gerar páginas intituladas como *Listar*, *Inserir*, *Editar* e *Excluir* que serão usadas ao trabalhar com esses dados. O Scaffolder de Web Forms utiliza o Entity Framework, Bootstrap e Dados Dinâmicos. Por padrão, o Scaffolder de Web Forms é instalado como uma extensão de seu projeto, como parte do modelo de projeto ao utilizar o Visual Studio 2013.

As etapas a seguir permitirão que você utilize o Scaffolder de Web Forms.

1. No Visual Studio, na barra de menus selecione **Ferramentas** -> **Extensões e Atualizações**. A caixa de diálogo **Extensões e Atualizações** é exibida.
2. A partir do painel esquerdo da caixa de diálogo, selecione **Online** -> **Galeria do Visual Studio** -> **Ferramentas** -> **Scaffolding**.
3. Se você não vê 'Scaffolding de Web Forms' na lista, insira 'Scaffolding de Web Forms' na caixa de pesquisa, à direita da caixa de diálogo.  
4. Se o Scaffolder de Web Forms ainda não está instalado, selecione **Download** para baixar e instalar o 'Scaffolding de Web Forms'. Reinicie o Visual Studio se necessário. Certifique-se de salvar as alterações para o projeto quando solicitado.  
	![Caixa de diálogo Extensões e Atualizações](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/ExtensionsAndUpdatesDB.png)  
5. Compile o projeto (Ctrl + Shift + B). Você deve compilar o projeto antes de usar o mecanismo de scaffolding.  
6. No **Gerenciador de Soluções**, clique com botão direito do mouse em *projeto* e, em seguida, selecione **Adicionar** -> **Novo Item de Scaffold**. A caixa de diálogo **Adicionar Scaffold** é exibida.
7. Selecione **Web Forms** a partir do painel esquerdo e selecione **Páginas Web Forms utilizando Entity Framework** a partir do painel central. Clique em **Adicionar**.  
	![Caixa de diálogo Adicionar Scaffold](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13a.png) 
	A caixa de diálogo **Adicionar Páginas de Web Forms** é exibida.  

8. Na caixa de diálogo **Adicionar Páginas de Web Forms**, defina a **Classe modelo** como `Contacts (ContactManager.Models)`. Defina a **Classe de contexto de dados** para `ApplicationDbContext (ContactManager.Models)`. Em seguida, clique em **Adicionar**. 
	![Caixa de diálogo Adicionar Páginas de Web Forms](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13b.png)

O Scaffolder de Web Forms acrescenta uma nova pasta que contém as páginas *Default.aspx*, *Delete.aspx*, *Edit.aspx* e *Insert.aspx*. O Scaffolder de Web Forms também cria uma pasta *DynamicData* que contém outras pastas, uma nomeada *EntityTemplates* e outra *FieldTemplates*. O `ApplicationDbContext` será utilizado tanto para o banco de dados de associações quanto para os dados de contato.

###Configurar o aplicativo para usar o Modelo de Dados 
A próxima tarefa é habilitar o recurso Codificar Primeiras Migrações para criar o banco de dados com base no modelo de dados criado. Além disso, você adicionará dados de exemplo e um inicializador de dados.

1. No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** e, em seguida, **Console do Gerenciador de Pacotes**.  
	![Caixa de diálogo Adicionar Páginas de Web Forms](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13c.png)  
2. Na janela Console do Gerenciador de Pacotes, digite o seguinte comando:  
	<pre class="prettyprint">
	enable-migrations
	</pre>  
	O comando enable-migrations cria a pasta *Migrations* e coloca nessa pasta um arquivo *Configuration.cs*, que pode ser editado para propagar o banco de dados e configurar migrações.  
3. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:  
	<pre class="prettyprint">
	add-migration Initial
	</pre>  
	O comando `add-migration Initial` gera um arquivo chamado <date_stamp>Inicial na pasta *Migrações* que cria o banco de dados. O primeiro parâmetro (Initial) é arbitrário e é usado para criar o nome do arquivo. Você pode ver os novos arquivos de classe no **Gerenciador de Soluções**. Na classe `Initial`, o método `Up` cria a tabela `Contact`, e o método `Down` (usado quando você deseja retornar ao estado anterior) a descarta.  
4. Abra o arquivo *Migrations\\Configuration.cs*. 
5. Adicione o namespace a seguir:  
	<pre class="prettyprint">
	using ContactManager.Models;
	</pre>
6. Substitua o método `Seed` pelo seguinte código:  
	<pre class="prettyprint">
	protected override void Seed(ContactManager.Models.ApplicationDbContext context)
	{
    context.Contacts.AddOrUpdate(p => p.Name,
       new Contacts
       {
           ContactId = 1,
           Name = "Ivan Irons",
           Address = "One Microsoft Way",
           City = "Redmond",
           State = "WA",
           Zip = "10999",
           Email = "ivani@wideworldimporters.com",
       },
       new Contacts
        {
            ContactId = 2,
            Name = "Brent Scholl",
            Address = "5678 1st Ave W",
            City = "Redmond",
            State = "WA",
            Zip = "10999",
            Email = "brents@wideworldimporters.com",
        },
        new Contacts
        {
            ContactId = 3,
            Name = "Terrell Bettis",
            Address = "9012 State St",
            City = "Redmond",
            State = "WA",
            Zip = "10999",
            Email = "terrellb@wideworldimporters.com",
        },
        new Contacts
        {
            ContactId = 4,
            Name = "Jo Cooper",
            Address = "3456 Maple St",
            City = "Redmond",
            State = "WA",
            Zip = "10999",
            Email = "joc@wideworldimporters.com",
        },
        new Contacts
        {
            ContactId = 5,
            Name = "Ines Burnett",
            Address = "7890 2nd Ave E",
            City = "Redmond",
            State = "WA",
            Zip = "10999",
            Email = "inesb@wideworldimporters.com",
        }
        );
	}
	</pre>
Esse código inicializa (propaga) o banco de dados com as informações de contato. Para obter mais informações sobre a propagação do banco de dados, consulte [Propagando e depurando BDs do Entity Framework (EF) ](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).  
7. Em **Console do Gerenciador de Pacotes**, digite o comando:  
	<pre class="prettyprint">
	update-database
	</pre>  
O `update-database` executa a primeira migração que cria o banco de dados. Por padrão, o banco de dados é criado como um banco de dados do SQL Server Express LocalDB.  
	![Console do Gerenciador de Pacotes](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13d.png)

###Executar o aplicativo localmente e exibir os dados 
Execute o aplicativo agora para ver como você pode exibir os contatos.

1. Primeiro, compile o projeto (**Ctrl+Shift+B**).  
2. Pressione **CTRL+F5** para executar o aplicativo. O navegador será aberto e mostrará a página *Default.aspx*.
3. Selecione o link **Contato/Demonstração** na parte superior da página para exibir a página *Contact List*.  
	![Página Lista de Contatos](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms17.png)  

##Habilitar SSL para o projeto 
SSL é um protocolo que permite que os servidores Web e clientes Web se comuniquem de modo mais seguro, por meio de criptografia. Quando o SSL não é utilizado, os dados enviados entre o cliente e servidor ficam abertos à detecção de pacotes por qualquer um com acesso físico à rede. Adicionalmente, diversos esquemas comuns de autenticação não são seguros por HTTP puro. Em particular, a autenticação Básica e a autenticação de formulários enviam credenciais sem criptografia. Para serem seguros, esses esquemas de autenticação precisam utilizar SSL.

1. No **Gerenciador de Soluções**, clique no projeto **ContactManager** e então pressione **F4** para exibir a janela **Propriedades**. 
2. Altere **SSL Habilitado** para `true`. 
3. Copie a **URL do SSL**, de modo que você possa utilizá-la mais tarde.  
	A URL do SSL será `https://localhost:44300/`, a menos que você tenha criado aplicativos Web SSL anteriormente (como é mostrado abaixo).
	![Propriedades do Projeto](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms18.png)  
4. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **Gerenciador de Contatos** e clique em **Propriedades**.
5. Na guia à esquerda, clique em **Web**.
6. Altere a **Url do Projeto** para usar a **URL de SSL** que você salvou anteriormente.  
	![Propriedades da Web do Projeto](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms19.png)  
7. Salve a página pressionando **CTRL+S**.
8. Pressione **CTRL+F5** para executar o aplicativo. O Visual Studio exibirá uma opção para que você possa evitar avisos do SSL.  
9. Clique em **Sim** para confiar no certificado SSL IIS Express e continuar.  
	![Detalhes do certificado SSL do IIS Express](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms20.png)  
	Será exibido um aviso de segurança.  

10. Clique em **Sim** para instalar o certificado em seu localhost.  
	![Caixa de diálogo Aviso de Segurança](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21.png)  
	A janela do navegador será exibida.

Você pode testar seu aplicativo Web localmente, utilizando o SSL.



##Adicionar um provedor de OAuth 2.0 
Os Web Forms ASP.NET oferecem opções aprimoradas de associação e autenticação. Esses aprimoramentos incluem OAuth. O OAuth é um protocolo aberto que permite autorização segura em um método simples e padrão da web, aplicativos móveis e de área de trabalho. O modelo de Internet do ASP.NET MVC usa OAuth para expor o Facebook, Twitter, Google e a Microsoft como provedores de autenticação. Embora este tutorial use apenas o Google como o provedor de autenticação, você pode facilmente modificar o código para usar qualquer um dos provedores. As etapas para implementar outros provedores são muito semelhantes às etapas que você verá neste tutorial.

Além da autenticação, o tutorial também usará funções para implementar a autorização. Somente os usuários que você adicionar à função `canEdit` poderão alterar dados (criar, editar ou excluir contatos).

As etapas a seguir permitirão que você adicione um provedor de autenticação do Google.

1. Abra o arquivo *App_Start\\Startup.Auth.cs*. 
2. Remove os caracteres do comentário do método para que o método `app.UseGoogleAuthentication()` pareça-se cm o seguinte:  
	<pre class="prettyprint">
            app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
            {
                ClientId = "",
                ClientSecret = ""
            });
	</pre>
3. Navegue até o [Console Para Desenvolvedores do Google](https://console.developers.google.com/). Você também precisará se autenticar com sua conta de email de desenvolvedor do Google (gmail.com). Se você não tiver uma conta do Google, selecione o link **Criar uma conta**.  
	Em seguida, você verá o **Console de desenvolvedores do Google**.
	![Console de Desenvolvedor do Google](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21a.png)  

4. Clique no botão **Criar Projeto** e insira um nome e ID de projeto (você pode utilizar os valores padrão). Em seguida, clique na **caixa de seleção do contrato** e no botão **Criar**.  
	![Google - Novo Projeto](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21b.png)  
	Em poucos segundos o novo projeto será criado e o navegador exibirá a nova página de projetos.
5. Na guia esquerda, clique em **APIs e autenticação**, então clique em **Credenciais**.
6. Clique em **Criar nova ID de cliente** em **OAuth**.  
	A caixa de diálogo **Criar ID de Cliente** será exibida.
	![Google - Criar ID de Cliente](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21c.png)  
7. Na caixa de diálogo **Criar ID de Cliente**, mantenha o **Aplicativo Web** padrão para o tipo de aplicativo.  
8. Defina as **Origens de JavaScript autorizados** para a URL do SSL utilizado anteriormente por você neste tutorial (**https://localhost:44300/**, a menos que você tenha criado outros projetos de SSL). Esta URL é a origem para seu aplicativo. Para este exemplo, você irá inserir apenas a URL de teste do localhost. Todavia, você pode inserir múltiplas URLs na conta, para localhost e produção.  

9. Defina o **URI de Redirecionamento Autorizado** com o seguinte valor:
	<pre class="prettyprint">  
	https://localhost:44300/signin-google  
	</pre>  
	Este valor é o URI que o OAuth ASP.NET utiliza para comunicar-se com o servidor google OAuth. Lembre-se da URL de SSL que você utilizou acima (**https://localhost:44300/** a menos que você tenha criado outros projetos SSL).  
10. Clique no botão **Criar ID de Cliente**.
11. No Visual Studio, atualize o método `UseGoogleAuthentication` da página *Startup.Auth.cs* copiando e colando o **AppId** e o **Segredo do Aplicativo** no método. Os valores da **AppId** e do **Segredo do aplicativo**, mostrados abaixo, são exemplos e não funcionarão.  
	<pre class="prettyprint">  
	using System;
	using Microsoft.AspNet.Identity;
	using Microsoft.AspNet.Identity.EntityFramework;
	using Microsoft.AspNet.Identity.Owin;
	using Microsoft.Owin;
	using Microsoft.Owin.Security.Cookies;
	using Microsoft.Owin.Security.DataProtection;
	using Microsoft.Owin.Security.Google;
	using Owin;
	using ContactManager.Models;
	
	namespace ContactManager
	{
    public partial class Startup {

        // Para mais informações sobre como configurar a autenticação, visite http://go.microsoft.com/fwlink/?LinkId=301883
        public void ConfigureAuth(IAppBuilder app)
        {
            // Configura o db context e o gerenciador de usuários para usar uma instância única por pedido
            app.CreatePerOwinContext(ApplicationDbContext.Create);
            app.CreatePerOwinContext<ApplicationUserManager>(ApplicationUserManager.Create);

            // Permite que o aplicativo use um cookie para armazenar as informações para o usuário conectado
            // e para usar um cookie para armazenar temporariamente informação sobre a entrada de um usuário com um provedor de logon de terceiros
            // Configura o cookie de entrada
            app.UseCookieAuthentication(new CookieAuthenticationOptions
            {
                AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
                LoginPath = new PathString("/Account/Login"),
                Provider = new CookieAuthenticationProvider
                {
                    OnValidateIdentity = SecurityStampValidator.OnValidateIdentity&lt;ApplicationUserManager, ApplicationUser>(
                        validateInterval: TimeSpan.FromMinutes(20),
                        regenerateIdentity: (manager, user) => user.GenerateUserIdentityAsync(manager))
                }
            });
            // Use um cookie para armazenar temporariamente as informações sobre a conexão de um usuário a um provedor de logon de terceiros
            app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);

            // Retire o comentário das linhas seguidos para habilitar a entrada com provedores de logon de terceiros
            //app.UseMicrosoftAccountAuthentication(
            //    clientId: "",
            //    clientSecret: "");

            //app.UseTwitterAuthentication(
            //   consumerKey: "",
            //   consumerSecret: "");

            //app.UseFacebookAuthentication(
            //   appId: "",
            //   appSecret: "");

            app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
            {
                ClientId = "<mark>000000000000.apps.googleusercontent.com</mark>",
                ClientSecret = "<mark>00000000000</mark>"
            });
        }
    }
}
</pre>
12. Pressione **CTRL+F5** para compilar e executar o aplicativo. Clique no link **Logon**.
13. Em **Utilizar outro serviço para fazer logon**, clique em **Google**.  
	![Fazer Logon](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21d.png)  
14. Se é necessário inserir suas credenciais, você será redirecionado ao site do Google, onde vai inserir essas credenciais.  
	![Google - Entrar](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21e.png)  
15. Após inserir suas credenciais, aparecerá uma solicitação para que você dê permissões para o aplicativo Web que acabou de criar:  
	![Conta de Serviço do Projeto Padrão](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21f.png)  
16. Clique em **Aceitar**. Você será agora redirecionado de volta à página **Registrar** do aplicativo **ContactManager**, na qual será possível registrar sua conta do Google.
	![Registre-se com sua Conta do Google](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21g.png)  
17. Você tem a opção de alterar o nome de registro de email local utilizado para sua conta do Gmail, mas normalmente você deverá preferir manter o alias de email padrão (ou seja, aquele utilizado por você para a autenticação). Clique em **Logon**.

##Utilizar a API de associação para restringir o acesso 
A Identidade ASP.NET é o sistema de associação utilizado para autenticação ao criar um aplicativo Web ASP.NET. Ela torna mais fácil integrar dados de perfil específicos de usuário com dados de aplicativo. Além disso, a Identidade ASP.NET permite que você escolha o modelo de persistência para perfis de usuário em seu aplicativo. Você pode armazenar os dados em um banco de dados do SQL Server ou outro dispositivo de armazenamento de dados, incluindo dispositivos de armazenamento de dados *NoSQL* como Tabelas de Armazenamento do Azure.

Ao utilizar o modelo padrão de Web Forms ASP.NET, você tem funcionalidade integrada de associação que você pode utilizar imediatamente, a partir do momento em que o aplicativo é executado. Você utilizará a Identidade do ASP.NET para acrescentar uma função de administrador e atribuir um usuário a essa função. Em seguida, você aprenderá como restringir o acesso à pasta de administração e às páginas que são utilizadas para modificar os dados de contato.

###Adicionar um Administrator 
Usando a Identidade ASP.NET, você pode adicionar uma função de administrador e atribuir um usuário a essa função, por meio de código.

1. No **Gerenciador de Soluções**, abra o arquivo *Configuration.cs* na pasta *Migrações*.
2. Adicione as instruções `using` a seguir no namespace `ContactManger.Migrations`:  
	<pre class="prettyprint">
	using Microsoft.AspNet.Identity;
	using Microsoft.AspNet.Identity.EntityFramework;
	</pre>
3. Adicione o método `AddUserAndRole` a seguir à classe `Configuration` depois do método `Seed`:  
	<pre class="prettyprint">
    public void AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
    {
        IdentityResult IdRoleResult;
        IdentityResult IdUserResult;

        var roleStore = new RoleStore&lt;IdentityRole>(context);
        var roleMgr = new RoleManager&lt;IdentityRole>(roleStore);

        if (!roleMgr.RoleExists("canEdit"))
        {
            IdRoleResult = roleMgr.Create(new IdentityRole { Name = "canEdit" });
        }

        //var userStore = new UserStore&lt;ApplicationUser>(context);
        //var userMgr = new UserManager&lt;ApplicationUser>(userStore);
        var userMgr = new UserManager&lt;ApplicationUser>(new UserStore&lt;ApplicationUser>(context));

        var appUser = new ApplicationUser
        {
            UserName = "canEditUser@wideworldimporters.com",
            Email = "canEditUser@wideworldimporters.com"
        };
        IdUserResult = userMgr.Create(appUser, "Pa$$word1");

        if (!userMgr.IsInRole(userMgr.FindByEmail("canEditUser@wideworldimporters.com").Id, "canEdit"))
        {
          //  IdUserResult = userMgr.AddToRole(appUser.Id, "canEdit");
            IdUserResult = userMgr.AddToRole(userMgr.FindByEmail("canEditUser@wideworldimporters.com").Id, "canEdit");
        }
    }
</pre>
4. Adicione uma chamada para o método `AddUserAndRole` de dentro do início do método `Seed`. Observe que somente o início do método `Seed` está sendo mostrado.  
	<pre class="prettyprint">
    protected override void Seed(ContactManager.Models.ApplicationDbContext context)
    {
        <mark>AddUserAndRole(context);</mark>
</pre>
5. Após salvar todas as suas alterações, execute o comando a seguir no **Console do Gerenciador de Pacotes**:  
	<pre class="prettyprint">
Update-Database
</pre>Esse código cria uma nova função chamada `canEdit` e cria um novo usuário local com o email de canEditUser@wideworldimporters.com. Em seguida, o código adiciona canEditUser@wideworldimporters.com à função `canEdit`. Para obter mais informações, consulte a página de recursos de [Identidade do ASP.NET](http://www.asp.net/identity).

###Restringir o Acesso à Pasta de Administração 
O aplicativo de exemplo **ContactManager** permite tanto que usuários anônimos quanto aqueles que já realizaram logon exibam contatos. Todavia, após você completar essa seção, os usuários que já fizeram logon e aos quais foi atribuída a função "canEdit" serão os únicos usuários capazes de modificar os contatos.

Você criará uma pasta chamada *Admin*, que somente os usuários aos quais for atribuída a função "canEdit" poderão acessar.

1. No **Gerenciador de Soluções**, adicione uma subpasta à pasta *Contatos* e nomeie a nova subpasta como *Admin*.
2. Mova os arquivos a seguir da pasta *Contatos* para a pasta *Contatos/Admin*:  
	- *Delete.aspx *e* Delete.aspx.cs*
	- *Edit.aspx *e* Edit.aspx.cs*
	- *Insert.aspx *e* Insert.aspx.cs*
3. Atualize as referências a links em *Contacts/Default.aspx* adicionando "Admin/" antes das referências a páginas que oferecem um link para *Insert.aspx*, *Edit.aspx* e *Delete.aspx*:  
	<pre class="prettyprint">
	&lt;%@ Page Title=&quot;ContactsList&quot; Language=&quot;C#&quot; MasterPageFile=&quot;~/Site.Master&quot; CodeBehind=&quot;Default.aspx.cs&quot; Inherits=&quot;ContactManager.Contacts.Default&quot; ViewStateMode=&quot;Disabled&quot; %&gt;
	&lt;%@ Register TagPrefix=&quot;FriendlyUrls&quot; Namespace=&quot;Microsoft.AspNet.FriendlyUrls&quot; %&gt;
	
	&lt;asp:Content runat=&quot;server&quot; ContentPlaceHolderID=&quot;MainContent&quot;&gt;
	    &lt;h2&gt;Contacts List&lt;/h2&gt;
	    &lt;p&gt;
	        &lt;asp:HyperLink runat=&quot;server&quot; NavigateUrl=&quot;<mark>Admin/</mark>Insert.aspx&quot; Text=&quot;Create new&quot; /&gt;
	    &lt;/p&gt;
	    &lt;div&gt;
	        &lt;asp:ListView runat=&quot;server&quot;
	            DataKeyNames=&quot;ContactId&quot; ItemType=&quot;ContactManager.Models.Contacts&quot;
	            AutoGenerateColumns=&quot;false&quot;
	            AllowPaging=&quot;true&quot; AllowSorting=&quot;true&quot;
	            SelectMethod=&quot;GetData&quot;&gt;
	            &lt;EmptyDataTemplate&gt;
	                There are no entries found for Contacts
	            &lt;/EmptyDataTemplate&gt;
	            &lt;LayoutTemplate&gt;
	                &lt;table class=&quot;table&quot;&gt;
	                    &lt;thead&gt;
	                        &lt;tr&gt;
	                            &lt;th&gt;Name&lt;/th&gt;
	                            &lt;th&gt;Address&lt;/th&gt;
	                            &lt;th&gt;City&lt;/th&gt;
	                            &lt;th&gt;State&lt;/th&gt;
	                            &lt;th&gt;Zip&lt;/th&gt;
	                            &lt;th&gt;Email&lt;/th&gt;
	                            &lt;th&gt;&amp;nbsp;&lt;/th&gt;
	                        &lt;/tr&gt;
	                    &lt;/thead&gt;
	                    &lt;tbody&gt;
	                        &lt;tr runat=&quot;server&quot; id=&quot;itemPlaceholder&quot; /&gt;
	                    &lt;/tbody&gt;
	                &lt;/table&gt;
	            &lt;/LayoutTemplate&gt;
	            &lt;ItemTemplate&gt;
	                &lt;tr&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Name&quot; ID=&quot;Name&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Address&quot; ID=&quot;Address&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;City&quot; ID=&quot;City&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;State&quot; ID=&quot;State&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Zip&quot; ID=&quot;Zip&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;asp:DynamicControl runat=&quot;server&quot; DataField=&quot;Email&quot; ID=&quot;Email&quot; Mode=&quot;ReadOnly&quot; /&gt;
	                    &lt;/td&gt;
	                    &lt;td&gt;
	                        &lt;a href=&quot;<mark>Admin/</mark>Edit.aspx?ContactId=&lt;%#: Item.ContactId%&gt;&quot;&gt;Edit&lt;/a&gt; | 
	                        &lt;a href=&quot;<mark>Admin/</mark>Delete.aspx?ContactId=&lt;%#: Item.ContactId%&gt;&quot;&gt;Delete&lt;/a&gt;
	                    &lt;/td&gt;
	                &lt;/tr&gt;
	            &lt;/ItemTemplate&gt;
	        &lt;/asp:ListView&gt;
	    &lt;/div&gt;
	&lt;/asp:Content&gt;
	</pre>
4. Atualize as seis referências do código `Response.Redirect("Default.aspx")` para `Response.Redirect("~/Contacts/Default.aspx")` nos três arquivos a seguir:  
	- *Delete.aspx.cs*
	- *Edit.aspx.cs*
	- *Insert.aspx.cs*  

	Agora, todos os links funcionarão corretamente quando você exibir e atualizar os dados de contato.
5. Para restringir o acesso à pasta *Admin*, a partir do **Gerenciador de Soluções**, clique com o botão direito na pasta *Admin* e selecione **Adicionar Novo Item**.
6. A partir da lista de modelos web do Visual C#, selecione **Arquivo de Configuração Web** a partir da lista do meio, aceite o nome padrão de *Web.config*, então selecione **Adicionar**.
7. Substitua o conteúdo XML existente no arquivo *Web.config* pelo seguinte:
	<pre class="prettyprint">
&lt;?xml version="1.0"?>
&lt;configuration>
  &lt;system.web>
    &lt;authorization>
      &lt;allow roles="canEdit"/>
      &lt;deny users="*"/>
    &lt;/authorization>
  &lt;/system.web>
&lt;/configuration>
</pre>
8. Salve o arquivo *Web.config*. O arquivo *Web.config* especifica que somente os usuários atribuídos à função "canEdit" podem acessar as páginas contidas na pasta *Admin*. 

Quando um usuário que não é parte da função "canEdit" tenta modificar os dados, ele é redirecionado à página de *Logon*.

##Implantar o aplicativo com o banco de dados no Azure 
Agora que o aplicativo Web está concluído, você pode publicá-lo no Azure.

###Publicar o Aplicativo 
1. No Visual Studio, compile o projeto (**Ctrl+Shift+B**).
2. No Visual Studio, clique com o botão direito no nome do projeto no **Gerenciador de Soluções** e selecione **Publicar**.  
	![Opção do menu Publicar](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms22.png)  
	A caixa de diálogo **Publicar Web** é exibida.  
	![Caixa de diálogo Publicar na Web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms22a.png)  
3. Na guia **Perfil**, selecione **Aplicativo Web do Azure** como o destino de publicação, caso essa opção não esteja selecionada.  
	![Caixa de diálogo Publicar na Web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms23.png)  
4. Clique em **Entrar** se você ainda não tiver se autenticado.
5. Selecione o aplicativo Web existente que você criou anteriormente neste tutorial na caixa suspensa de **Aplicativos Web Existentes** e clique no botão **OK**.  
	![Selecionar a caixa de diálogo Site Existente](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms25.png)  
	Se você for solicitado a salvar alterações feitas no perfil, selecione **Sim**.
6. Clique na guia **Configurações**.  
	![Selecionar a caixa de diálogo Site Existente](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms26.png)  
7. Defina a caixa suspensa **Configuração** com a opção **Depurar**.
8. Clique no ícone com a **seta para baixo** ao lado de **ApplicationDbContext** e defina-o como **ContactDB**.
9. Marque a caixa de seleção **Executar as Migrações Iniciais do Código**.  

	>[AZURE.NOTE]  
	Neste exemplo, você precisa marcar essa caixa de seleção na primeira vez em que publicar o aplicativo. Desse modo, o método *Seed* no arquivo *Configuration.cs* só será chamado uma vez.

10. Em seguida, clique em **Publicar**.  
	Seu aplicativo será publicado no Azure.

>[AZURE.NOTE]  
Se você fechou e abriu novamente o Visual Studio depois de criar o perfil de publicação, talvez você não veja a cadeia de conexão na lista suspensa. Nesse caso, em vez de editar o perfil de publicação que você criou anteriormente, crie um novo da mesma maneira como criou anteriormente e siga estas etapas na guia **Configurações**.)

###Revisar o Aplicativo no Azure 
1. No navegador, clique no link **Demonstração de Contato**.  
	A Lista de Contatos é exibida.
	![Contatos listados em Navegador](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms27.png)  

2. Selecione **Criar novo** na página **Lista de Contatos**.  
	![Contatos listados em Navegador](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms29.png)  
	Você será redirecionado à página de **Logon** porque você não realizou logon com uma conta com permissão para modificar contatos.
3. Após inserir o email e senha abaixo, clique no botão **Fazer logon**.  
	**Email**: `canEditUser@wideworldimporters.com`  
	**Senha**: `Pa$$word1`  
	![Página Logon](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms28.png)  

4. Insira novos dados para cada campo e pressione o botão **Inserir**.  
	![Página Adicionar Novo Contato](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms30.png)  
	A página *EditContactList.aspx* é exibida com o novo registro.  
	![Página Adicionar Novo Contato](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms31.png)

5. Selecione o link **Logoff**.

###Parar o Aplicativo 
Para evitar que outras pessoas se registrem e utilizem seu aplicativo de exemplo, você interromperá o aplicativo Web.

1. No Visual Studio, a partir do **Menu Exibir**, selecione **Gerenciador de Servidores**. 
2. No **Gerenciador de Servidores**, navegue até **Aplicativo Web**.
3. Clique com o botão direito do mouse em cada instância do aplicativo Web e selecione **Parar Aplicativo Web**.  
	![Item de menu Parar Site](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms26a.png)  

	Como alternativa, no Portal de Gerenciamento do Microsoft Azure, você pode selecionar o aplicativo Web e clicar no ícone **parar** na parte inferior da página.  
	![Página Adicionar Novo Contato](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms26b.png)

##Revisar o banco de dados 
É importante saber como exibir e modificar o banco de dados diretamente. Saber como trabalhar diretamente com o banco de dados permite que você confirme dados presentes no banco de dados e também compreenda como os dados são armazenados em cada tabela.

###Examine o BD do SQL Azure 
1. No Visual Studio, abra **Gerenciador de Servidores** e navegue até **ContactDB**.
2. Clique com o botão direito do mouse em **ContactDB** e selecione **Abrir no Pesquisador de Objetos do SQL Server**.  
	![Item de menu Abrir no Pesquisador de Objetos do SQL Server](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms32.png)  
3. Se a caixa de diálogo **Adicionar Regra de Firewall** for exibida, selecione **Adicionar Regra de Firewall**.  
      Se não for possível expandir o item **Bancos de Dados SQL** nem ver o item **ContactDB** pelo Visual Studio, você pode seguir as instruções para abrir uma porta de firewall ou um grupo de portas. Para fazer isso, siga as instruções em **Configurar regras de firewall do Azure** perto do final do [Tutorial MVC](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Como alternativa, você também pode revisar os dados do banco de dados local compilando, executando o aplicativo e adicionando dados a ele localmente (**CTRL+F5** pelo Visual Studio).  

4. Se a caixa de diálogo **Conectar-se ao servidor** for exibida, insira a **senha** que você criou no início deste tutorial e pressione o botão **Conectar**.  
      Se você não se lembra da senha, você pode encontrá-la em seu arquivo local do projeto. No **Gerenciador de Soluções**, expanda a pasta *Propriedades* e então expanda a pasta *PublishProfiles*. Abra o arquivo *contactmanager.pubxml* (seu arquivo pode ser nomeado de modo diferente). Pesquise no arquivo, buscando sua senha para publicação.

5. Expanda o banco de dados **contactDB** e então expanda **Tabelas**.
6. Clique com o botão direito do mouse na guia **dbo.AspNetUsers** e selecione **Exibir Dados**.  
	![Item de menu Exibir Dados](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms34.png)  
	Você poderá exibir os dados associados ao usuário canEditUser@contoso.com.  
	![Janela ContactManager](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms35.png)  

###Adicionar um usuário à função de administrador editando o banco de dados 
Anteriormente, no tutorial, você usou código para adicionar usuários à função canEdit. Um método alternativo é manipular diretamente os dados nas tabelas de associação. As etapas a seguir mostram como usar esse método alternativo para adicionar um usuário a uma função.

1. No **Pesquisador de Objetos do SQL Server**, clique com o botão direito do mouse em **dbo.AspNetUserRoles** e selecione **Exibir Dados**.
	![Dados de AspNetUserRoles](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms36.png)  
2. Copie a *RoleId* e cole-a na linha vazia (nova).  
	![Dados de AspNetUserRoles](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms37.png)  
3. Na tabela **dbo.AspNetUsers**, encontre o usuário que você deseja incluir na função, copiando a *Id* do usuário.
4. Cole a *Id* copiada no campo **UserId** da nova linha na tabela **AspNetUserRoles**.  

>[AZURE.NOTE]  
Estamos trabalhando em uma ferramenta que irá facilitar bastante o gerenciamento de usuários e funções.

##Próximas etapas
Para saber mais sobre Web Forms ASP.NET, consulte [Saiba sobre os Web Forms ASP.NET](http://www.asp.net/web-forms) no aplicativo Web ASP.NET e [Guias e tutoriais do Microsoft Azure](http://azure.microsoft.com/documentation/services/web-sites/#net).

Este tutorial é baseado no tutorial para MVC [Criar um aplicativo MVC ASP.NET com autorização e banco de dados do SQL DB e implantá-lo no Serviço de Aplicativo do Azure](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md), escrito por Rick Anderson (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) com ajuda de Tom Dykstra e Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)).

Deixe comentários sobre o que você gostou e o que você gostaria de ver melhorado, não apenas sobre o próprio tutorial, mas também sobre os produtos que ele demonstra. Seus comentários nos ajudarão a priorizar melhorias. Você também pode solicitar e votar em novos tópicos em [Mostre-me como com código](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

 

<!----HONumber=July15_HO4-->