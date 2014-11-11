<properties linkid="Deploy_a_Secure_ASP.NET_Web_Forms_App_with_Membership_OAuth_and_SQL_Database_to_an_Azure_Web_Site" pageTitle="Deploy a Secure ASP.NET Web Forms App with Membership, OAuth, and SQL Database to an Azure Website" metaKeywords="Azure WebForms Hello World Tutorial, Azure Getting Started with WebForms Tutorial, Azure C# WebForms Hello World Tutorial, SQL Azure C# WebForms Tutorial" description="This tutorial shows you how to build a secure ASP.NET 4.5 Web Forms web app that incorporates a SQL Database and deploy the application to Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy a secure ASP.NET 4.5 Web Forms app to an Azure Website" authors="erikre"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="erikre" />

# Implantar um aplicativo de Web Forms ASP.NET seguro com Associação, OAuth e banco de dados SQL em um Site do Azure

Este tutorial mostra como criar um aplicativo com Web Forms ASP.NET 4.5 seguro que incorpora um banco de dados SQL e implanta o aplicativo no Azure.

> [WACOM.NOTE]
> Para uma versão MVC deste tutorial, consulte [Implantar um aplicativo ASP.NET MVC 5 seguro com Associação, OAuth e Banco de Dados SQL em um site do Azure][Implantar um aplicativo ASP.NET MVC 5 seguro com Associação, OAuth e Banco de Dados SQL em um site do Azure].

É possível abrir uma conta do Azure gratuitamente e, se você ainda não tiver o Visual Studio 2013, o SDK instalará automaticamente o Visual Studio 2013 para o Web Express. É possível iniciar o desenvolvimento para o Azure de maneira gratuita.

Este tutorial pressupõe que você não tem nenhuma experiência anterior utilizando o Microsoft Azure. Ao concluir este tutorial, você terá um aplicativo Web em funcionamento na nuvem e usando um banco de dados também localizado na nuvem.

Você aprenderá a:

-   Criar um projeto de Web Forms ASP.NET 4.5 seguro e publicá-lo em um Site do Azure.
-   Utilizar OAuth e o banco de dados de associação do ASP.NET para proteger seu aplicativo.
-   Utilizar um único banco de dados tanto para dados do aplicativo quanto para associação.
-   Utilizar o Scaffolding dos Web Forms para criar páginas web que permitem a você modificar dados.
-   Como usar a nova API de associação para adicionar usuários e funções.
-   Como usar um banco de dados SQL para armazenar dados no Azure.

Você criará um aplicativo Web de lista de contatos simples criado no ASP.NET de Web Forms 4.5 e que usa o Entity Framework para acesso ao banco de dados. A imagem a seguir mostra a página de Web Forms que permite acesso de leitura e gravação ao banco de dados:

![Contatos - Editar página][Contatos - Editar página]

> [WACOM.NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Se não tiver uma conta, você poderá [ativar os benefícios de assinante MSDN][ativar os benefícios de assinante MSDN] ou [inscrever-se para uma avaliação gratuita][inscrever-se para uma avaliação gratuita].

Este tutorial contém as seguintes seções:

-   [Configurar o ambiente de desenvolvimento][Configurar o ambiente de desenvolvimento]
-   [Configurar o ambiente do Azure][Configurar o ambiente do Azure]
-   [Criar um aplicativo de Web Forms ASP.NET][Criar um aplicativo de Web Forms ASP.NET]
-   [Adicionar um banco de dados ao aplicativo][Adicionar um banco de dados ao aplicativo]
-   [Habilitar SSL para o projeto][Habilitar SSL para o projeto]
-   [Adicionar um provedor de OAuth 2.0][Adicionar um provedor de OAuth 2.0]
-   [Utilizar a API de associação para restringir o acesso][Utilizar a API de associação para restringir o acesso]
-   [Implantar o aplicativo com o banco de dados no Azure][Implantar o aplicativo com o banco de dados no Azure]
-   [Revisar o banco de dados][Revisar o banco de dados]
-   [Próximas etapas][Próximas etapas]

## Configurar o ambiente de desenvolvimento

Para começar, defina o ambiente de desenvolvimento instalando o Visual Studio 2013 e o SDK do Azure para .NET.

1.  Instale o [Visual Studio 2013][Visual Studio 2013], se ainda não tiver instalado.
2.  Instale o [SDK do Azure para o Visual Studio 2013][SDK do Azure para o Visual Studio 2013]. Este tutorial requer o Visual Studio 2013 antes de se instalar o SDK do Azure para o Visual Studio 2013.

    > [WACOM.NOTE]
    > Dependendo de quantas dependências de SDK já estiverem instaladas no seu computador, a instalação do SDK pode demorar bastante, de vários minutos a meia hora ou mais.

3.  Se for solicitado que você execute ou salve o executável da instalação, clique em **Executar**.
4.  Na janela do **Web Platform Installer**, clique em **Instalar** e prossiga com a instalação.
    ![Web Platform Installer][Web Platform Installer]

    > [WACOM.NOTE]
    > Se você já tem o SDK instalado, não há nenhum item a instalar. O número de itens a instalar aparecerá no canto inferior esquerdo da janela do **Web Platform Installer**.

5.  Se você ainda não tem o **Visual Studio Atualização 2**, baixe e instale o **[Visual Studio 2013 Atualização 2][Visual Studio 2013 Atualização 2]** ou posterior.

    > [WACOM.NOTE]
    > É necessário que você instale o Visual Studio 2013 Atualização 2 ou posterior para poder utilizar o Google OAuth 2.0 e para utilizar SSL localmente, sem advertências. Além disso, você precisa da Atualização 2 para utilizar o Scaffolding dos Web Forms.

Quando a instalação for concluída, você terá todo o necessário para iniciar o desenvolvimento.

## Configurar o ambiente do Azure

Nesta seção você irá configurar o ambiente do Azure criando um Site e um banco de dados SQL no Azure.

### Criar um site e um banco de dados SQL no Azure

Neste tutorial, seu Site do Azure será executado em um ambiente de hospedagem compartilhado, o que significa que ele é executado em MVs (máquinas virtuais) que são compartilhadas com outros sites do Azure. Um ambiente de hospedagem compartilhado é uma maneira de começar na nuvem a um baixo custo. Mais tarde, se seu tráfego da Web aumentar, o aplicativo poderá ser dimensionado para atender à necessidade executando em VMs dedicadas. Se precisar de uma arquitetura mais complexa, você poderá migrar para um Serviço de Nuvem do Azure. Os serviços de nuvem são executados em VMs dedicadas que podem ser configuradas de acordo com suas necessidades.

O Banco de Dados SQL no Azure é um serviço de banco de dados relacional baseado em que é fundamentado em tecnologias do SQL Server. As ferramentas e os aplicativos que funcionam com o SQL Server também funcionam com o Banco de Dados SQL.

1.  No [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Sites** na guia esquerda, em seguida, clique em **Novo**.
    ![Web Platform Installer][1]
2.  Clique em **Site**, então clique em **Criação Personalizada**.
    ![Criação Personalizada][Criação Personalizada]
    O assistente **Novo Site - Criação Personalizada** é aberto.

3.  Na etapa **Criar Site** do assistente, insira uma cadeia de caracteres na caixa **URL** a ser usada como a URL exclusiva de seu aplicativo. A URL completa consistirá no que você digitar aqui mais o sufixo que você vê ao lado da caixa de texto. A ilustração mostra uma URL que provavelmente já é utilizada e, portanto, **você precisa escolher outra**.
    ![Contatos - Criar Novo Site][Contatos - Criar Novo Site]
4.  Na lista suspensa Plano de Hospedagem na Web, escolha a região mais próxima de você. Essa configuração especifica em qual datacenter sua VM executará.
5.  Na lista suspensa **Banco de dados**, escolha **Criar um banco de dados SQL gratuito de 20 MB**.
6.  Na caixa **Nome da Cadeia de Conexão do Banco de Dados**, deixe o valor padrão de *DefaultConnection*.
7.  Clique na seta na parte inferior da caixa de diálogo.
    O assistente avança para a etapa **Especificar configurações de banco de dados**.
8.  Na caixa **Nome**, digite *`ContactDB`*.
    ![Configurações de Banco de Dados][Configurações de Banco de Dados]
9.  Na caixa **Servidor**, selecione o servidor **Novo banco de dados SQL**.
    Alternativamente, se você tiver criado um banco de dados de SQL Server, poderá selecionar esse SQL Server a partir de um controle suspenso.
10. Defina a **Região** como a mesma área na qual você criou o site.
11. Insira um **Nome de logon** e **Senha** de administrador.
    Se selecionou **Novo servidor do Banco de Dados SQL**, você não irá digitar um nome e uma senha existentes aqui; digite um novo nome e uma senha que você está definindo agora para usar mais tarde ao acessar o banco de dados. Se você tiver selecionado um SQL Server criado anteriormente, será solicitada a senha para o nome da conta do SQL Server anterior que você criou. Para este tutorial, não selecionaremos a caixa **Avançado**.
12. Clique na marca de seleção no canto inferior direito da caixa para indicar que você concluiu.

O **Portal de Gerenciamento do Azure** retorna para a página **Sites** e a coluna **Status** mostra que o site está sendo criado. Depois de pouco tempo (normalmente menos de um minuto), a coluna **Status** mostra que o site foi criado com êxito. Na barra de navegação à esquerda, o número de sites presentes na sua conta é exibido próximo ao ícone **Sites**, enquanto o número de bancos de dados aparece ao lado do ícone **Bancos de Dados SQL**.

## Criar um aplicativo de Web Forms ASP.NET

Você criou um Site do Azure, mas ainda não há conteúdo nele. A próxima etapa é criar o aplicativo Web do Visual Studio que será publicado no Azure.

### Criar o projeto

1.  Selecione **Novo Projeto** a partir do menu **Arquivo**, no Visual Studio.
    ![Menu Arquivo - Novo Projeto][Menu Arquivo - Novo Projeto]
2.  Selecione o grupo de modelos **Modelos** -\> **Visual C#** -\> **Web** à esquerda.
3.  Selecione o modelo **Aplicativo Web ASP.NET** na coluna central.
4.  Nomeie seu projeto como *ContactManager* e clique em **OK**.
    ![Caixa de diálogo Novo Projeto][Caixa de diálogo Novo Projeto]

    > [WACOM.NOTE]
    > O nome do projeto nessa série de tutoriais é **ContactManager**. É recomendável que você utilize exatamente esse nome de projeto, de modo que o código fornecido pela série de tutoriais funcione conforme esperado.

5.  Na caixa de diálogo **Novo Projeto ASP.NET**, selecione o modelo **Web Forms**. Desmarque a caixa de seleção **Hospedar na nuvem** se estiver selecionada e clique em **OK**.
    ![Caixa de diálogo Novo Projeto ASP .NET][Caixa de diálogo Novo Projeto ASP .NET]
    O aplicativo de Web Forms será criado.

    ### Atualizar a página mestra

    Nos Web Forms ASP.NET, as páginas mestras permitem que você crie um layout consistente para as páginas em seu aplicativo. Uma única página mestra define a aparência e comportamento padrão que você deseja para todas as páginas (ou um grupo de páginas) em seu aplicativo. Então, você pode criar páginas de conteúdo individuais com o conteúdo que você deseja exibir. Quando os usuários solicitam as páginas de conteúdo, o ASP.NET as combina com a página mestra para produzir um resultado que combine o layout da página mestra com o conteúdo da página de conteúdo.
    O novo site requer o nome do aplicativo e um link atualizados. O link direcionará para a página que exibirá os detalhes de contato. Para fazer essas alterações, você modificará o HTML na página mestra.

6.  No **Gerenciador de Soluções**, encontre a página *Site.Master* e abra-a.
7.  Se a página estiver em modo de exibição de **Design**, alterne para o modo de exibição de **Código-Fonte**.
8.  Atualize a página mestra modificando ou adicionando a marcação destacada em amarelo:

``` prettyprint
<%@ Master Language="C#" AutoEventWireup="true" CodeBehind="Site.master.cs" Inherits="ContactManager.SiteMaster" %>

<!DOCTYPE html>

<html lang="en">
<head runat="server">
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title><%: Page.Title %> - Contact Manager</title>

    <asp:PlaceHolder runat="server">
        <%: Scripts.Render("~/bundles/modernizr") %>
    </asp:PlaceHolder>
    <webopt:bundlereference runat="server" path="~/Content/css" />
    <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />

</head>
<body>
    <form runat="server">
        <asp:ScriptManager runat="server">
            <Scripts>
                <%--Para saber mais sobre empacotamento de script no ScriptManager, consulte http://go.microsoft.com/fwlink/?LinkID=301884 --%>
                <%--Framework Scripts--%>
                <asp:ScriptReference Name="MsAjaxBundle" />
                <asp:ScriptReference Name="jquery" />
                <asp:ScriptReference Name="bootstrap" />
                <asp:ScriptReference Name="respond" />
                <asp:ScriptReference Name="WebForms.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebForms.js" />
                <asp:ScriptReference Name="WebUIValidation.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebUIValidation.js" />
                <asp:ScriptReference Name="MenuStandards.js" Assembly="System.Web" Path="~/Scripts/WebForms/MenuStandards.js" />
                <asp:ScriptReference Name="GridView.js" Assembly="System.Web" Path="~/Scripts/WebForms/GridView.js" />
                <asp:ScriptReference Name="DetailsView.js" Assembly="System.Web" Path="~/Scripts/WebForms/DetailsView.js" />
                <asp:ScriptReference Name="TreeView.js" Assembly="System.Web" Path="~/Scripts/WebForms/TreeView.js" />
                <asp:ScriptReference Name="WebParts.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebParts.js" />
                <asp:ScriptReference Name="Focus.js" Assembly="System.Web" Path="~/Scripts/WebForms/Focus.js" />
                <asp:ScriptReference Name="WebFormsBundle" />
                <%--Scripts do Site--%>
            </Scripts>
        </asp:ScriptManager>

        <div class="navbar navbar-inverse navbar-fixed-top">
            <div class="container">
                <div class="navbar-header">
                    <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                    </button>
                    <a class="navbar-brand" runat="server" id="ContactDemoLink" href="~/Contacts/Default.aspx">Contact Demo</a>
                </div>
                <div class="navbar-collapse collapse">
                    <ul class="nav navbar-nav">
                        <li><a runat="server" href="~/">Home</a></li>
                        <li><a runat="server" href="~/About">About</a></li>
                        <li><a runat="server" href="~/Contact">Contact</a></li>
                    </ul>
                    <asp:LoginView runat="server" ViewStateMode="Disabled">
                        <AnonymousTemplate>
                            <ul class="nav navbar-nav navbar-right">
                                <li><a runat="server" href="~/Account/Register">Register</a></li>
                                <li><a runat="server" href="~/Account/Login">Log in</a></li>
                            </ul>
                        </AnonymousTemplate>
                        <LoggedInTemplate>
                            <ul class="nav navbar-nav navbar-right">
                                <li><a runat="server" href="~/Account/Manage" title="Manage your account">Hello, <%: Context.User.Identity.GetUserName()  %> !</a></li>
                                <li>
                                    <asp:LoginStatus runat="server" LogoutAction="Redirect" LogoutText="Log off" LogoutPageUrl="~/" OnLoggingOut="Unnamed_LoggingOut" />
                                </li>
                            </ul>
                        </LoggedInTemplate>
                    </asp:LoginView>
                </div>
            </div>
        </div>
        <div class="container body-content">
            <asp:ContentPlaceHolder ID="MainContent" runat="server">
            </asp:ContentPlaceHolder>
            <hr />
            <footer>
                <p>© <%: DateTime.Now.Year %> - Contact Manager</p>
            </footer>
        </div>
    </form>
</body>
</html>
```

Mais adiante, neste tutorial, você adicionará o scaffolding para Web Forms. O scaffolding criará a página à qual o link “Contato/Demonstração” acima faz referência.

### Executar o aplicativo localmente

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse na página *Default.aspx* e selecione **Definir como página inicial**.
2.  Em seguida, pressione **CTRL+F5** para executar o aplicativo.
    A página padrão do aplicativo aparece na janela do navegador padrão.
    ![Contatos - Criar Novo Site][2]

Isso é tudo o que você precisa fazer por enquanto para criar o aplicativo que você implantará no Azure. Posteriormente, você adicionará a funcionalidade do banco de dados, além das páginas necessárias para exibir e editar dados de contato.

### Implantar o Aplicativo no Azure

Agora que você criou e executou seu aplicativo localmente, é hora de implantá-lo no Azure.

1.  No Visual Studio, clique com o botão direito no nome do projeto no **Gerenciador de Soluções** e selecione **Publicar** a partir do menu de contexto.
    ![Selecione Publicar][Selecione Publicar]
    A caixa de diálogo **Publicar na Web** é exibida.

2.  Na guia **Perfil** da caixa de diálogo **Publicar na Web**, clique em **Sites do Azure**.
    ![Caixa de diálogo Publicar na Web][Caixa de diálogo Publicar na Web]
3.  Se você ainda não fez sua autenticação, clique no botão **Entrar** na caixa de diálogo **Selecionar site existente**. Após ter concluído a autenticação, selecione o site que você criou na primeira parte deste tutorial. Clique em **OK** para continuar.
    ![Caixa de diálogo Selecionar Site Existente][Caixa de diálogo Selecionar Site Existente]
    O Visual Studio irá baixar suas configurações de publicação.
4.  Na caixa de diálogo **Publicar na Web**, clique em **Publicar**.
    ![Caixa de diálogo Publicar na Web][3]
    Você verá o status geral de publicação na janela **Atividade de Publicação da Web** no Visual Studio:
    ![Atividade de Publicação da Web][Atividade de Publicação da Web]

O aplicativo que você criou agora está em execução na nuvem. Na próxima vez que você implantar o aplicativo a partir do Visual Studio, somente os arquivos alterados (ou novos) serão implantados.
 ![Aplicativo no Navegador][Aplicativo no Navegador]

> [WACOM.NOTE]
> Se você encontrar um erro enquanto publica um site já estabelecido, poderá limpar a localização antes de adicionar seus novos arquivos.
> Publique novamente seu aplicativo mas dessa vez, na caixa de diálogo **Publicar na Web**, selecione a guia **Configurações**. Em seguida, defina as configurações como **Depurar** e selecione a opção **Remover os arquivos adicionais presentes no destino**. Selecione **Publicar** para implantar seu aplicativo novamente.
>  ![Caixa de diálogo Publicar na Web][4]

## Adicionar um banco de dados ao aplicativo

Em seguida, você atualizará o aplicativo Web Forms para adicionar a habilidade de exibir e atualizar contatos, além de armazenar os dados no banco de dados padrão. Quando você criou o projeto Web Forms, o banco de dados também foi criado por padrão. O aplicativo usará o Entity Framework para acessar o banco de dados e ler e atualizar dados nele presentes.

### Adicionar Uma Classe de Modelo de Dados

Você começa criando um modelo de dados simples por meio de código. Este modelo de dados estará contido em uma classe chamada `Contacts`. O nome da classe `Contacts` foi escolhido para evitar um conflito de nome de classe com a classe `Contact` contida no arquivo Contact.aspx.cs criado pelo modelo de Formulários da Web.

1.  No **Gerenciador de Soluções**, clique com o botão direito na pasta *Modelos* e clique em **Adicionar** -\> **Classe**.
    ![Selecione Classe][Selecione Classe]
    A caixa de diálogo **Adicionar Novo Item** é exibida.

2.  Nomeie essa nova classe como *Contacts.cs*.
    ![Caixa de diálogo Adicionar Novo Item][Caixa de diálogo Adicionar Novo Item]
3.  Substitua o código padrão pelo código a seguir:

    ``` prettyprint
    using System.ComponentModel.DataAnnotations;using System.Globalization;
    namespace ContactManager.Models{ public class Contacts { [ScaffoldColumn(false)][Key] public int ContactId { get; set; } public string Name { get; set; } public string Address { get; set; } public string City { get; set; } public string State { get; set; } public string Zip { get; set; } [DataType(DataType.EmailAddress)] public string Email { get; set; } }}
    ```

    </p>

A classe **Contatos** define os dados que você armazenará para cada contato, além de uma chave primária, (`ContactID`) que é necessária para o banco de dados. A classe **Contatos** representa os dados de contato que serão exibidos. Cada instância de um objeto Contatos corresponderá a uma linha dentro de uma tabela de banco de dados relacional, sendo que cada propriedade da classe Contatos será mapeada para uma coluna na tabela de banco de dados relacional. Posteriormente neste tutorial, você revisará os dados de contato contidos no banco de dados.

### Scaffolding de Web Forms

Você criou a classe de modelo **Contatos** acima. Agora, você pode usar o Scaffolder de Web Forms para gerar páginas intituladas como *Listar*, *Inserir*, *Editar* e *Excluir* que serão usadas ao trabalhar com esses dados. O Scaffolder de Web Forms utiliza o Entity Framework, Bootstrap e Dados Dinâmicos. Por padrão, o Scaffolder de Web Forms é instalado como uma extensão de seu projeto, como parte do modelo de projeto ao utilizar o Visual Studio 2013.

As etapas a seguir permitirão que você utilize o Scaffolder de Web Forms.

1.  No Visual Studio, a partir da barra de menus, selecione **Ferramentas** -\> **Extensões e Atualizações**.
    A caixa de diálogo **Extensões e Atualizações** é exibida.
2.  A partir do painel esquerdo da caixa de diálogo, selecione **Online** -\> **Galeria do Visual Studio** -\> **Ferramentas** -\> **Scaffolding**.
3.  Se você não vê 'Scaffolding de Web Forms' na lista, insira 'Scaffolding de Web Forms' na caixa de pesquisa, à direita da caixa de diálogo.
4.  Se o Scaffolder de Web Forms ainda não está instalado, selecione **Download** para baixar e instalar o 'Scaffolding de Web Forms'. Reinicie o Visual Studio se necessário. Certifique-se de salvar suas alterações ao projeto quando solicitado.
    ![Caixa de diálogo Extensões e Atualizações][Caixa de diálogo Extensões e Atualizações]
5.  Compile o projeto (**Ctrl+Shift+B**).
    Você deve compilar o projeto antes de usar o mecanismo de scaffolding.
6.  No **Gerenciador de Soluções**, clique com o botão direito em *Projeto* e então selecione **Adicionar** -\> **Novo Item Scaffold**.
    A caixa de diálogo **Adicionar Scaffold** é exibida.
7.  Selecione **Web Forms** a partir do painel esquerdo e selecione **Páginas Web Forms utilizando Entity Framework** a partir do painel central. Em seguida, clique em **Adicionar**.
    ![Caixa de diálogo Adicionar Scaffold][Caixa de diálogo Adicionar Scaffold]
    A caixa de diálogo **Adicionar Páginas de Web Forms** é exibida.

8.  Na caixa de diálogo **Adicionar Páginas de Web Forms**, defina a **Classe modelo** como `Contact (ContactManager.Models)`. Defina a **Classe de contexto de dados** para `ApplicationDbContext (ContactManager.Models)`. Em seguida, clique em **Adicionar**.
    ![Caixa de diálogo Adicionar Páginas de Web Forms][Caixa de diálogo Adicionar Páginas de Web Forms]

O Scaffolder de Web Forms acrescenta uma nova pasta que contém as páginas *Default.aspx*, *Delete.aspx*, *Edit.aspx* e *Insert.aspx*. O Scaffolder de Web Forms também cria uma pasta *DynamicData* que contém outras pastas, uma nomeada *EntityTemplates* e outra *FieldTemplates*. O `ApplicationDbContext` será utilizado tanto para o banco de dados de associação quanto para os dados de contato.

### Configurar o aplicativo para usar o Modelo de Dados

A próxima tarefa é habilitar o recurso Codificar Primeiras Migrações para criar o banco de dados com base no modelo de dados criado. Além disso, você adicionará dados de exemplo e um inicializador de dados.

1.  No menu **Ferramentas**, selecione **Gerenciador de pacotes NuGet** e, em seguida, selecione **Console do gerenciador de pacotes**.
    ![Caixa de diálogo Adicionar Páginas de Web Forms][5]
2.  Na janela Console do Gerenciador de Pacotes, digite o seguinte comando:

    ``` prettyprint
    enable-migrations
    ```

    O comando enable-migrations cria a pasta *Migrações* e coloca nessa pasta um arquivo *Configuration.cs*, que pode ser editado para propagar o banco de dados e configurar migrações.

3.  Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

    ``` prettyprint
    add-migration Initial
    ```

    O comando `add-migration Initial` gera um arquivo chamado <date_stamp>Inicial na pasta *Migrações* que cria o banco de dados. O primeiro parâmetro (Initial) é arbitrário e é usado para criar o nome do arquivo. Você pode ver os novos arquivos de classe no **Gerenciador de Soluções**. Na classe `Initial`, o método `Up` cria a tabela `Contact`, e o método `Down` (usado quando você deseja retornar ao estado anterior) a descarta.

4.  Abra o arquivo *Migrations\\Configuration.cs*.
5.  Adicione o namespace a seguir:

    ``` prettyprint
    using ContactManager.Models;
    ```

6.  Substitua o método `Seed` pelo seguinte código:

    ``` prettyprint
    protected override void Seed(ContactManager.Models.ApplicationDbContext context){ context.Contacts.AddOrUpdate(p => p.Name, new Contacts { ContactId = 1, Name = "Ivan Irons", Address = "One Microsoft Way", City = "Redmond", State = "WA", Zip = "10999", Email = "ivani@wideworldimporters.com", }, new Contacts { ContactId = 2, Name = "Brent Scholl", Address = "5678 1st Ave W", City = "Redmond", State = "WA", Zip = "10999", Email = "brents@wideworldimporters.com", }, new Contacts { ContactId = 3, Name = "Terrell Bettis", Address = "9012 State St", City = "Redmond", State = "WA", Zip = "10999", Email = "terrellb@wideworldimporters.com", }, new Contacts { ContactId = 4, Name = "Jo Cooper", Address = "3456 Maple St", City = "Redmond", State = "WA", Zip = "10999", Email = "joc@wideworldimporters.com", }, new Contacts { ContactId = 5, Name = "Ines Burnett", Address = "7890 2nd Ave E", City = "Redmond", State = "WA", Zip = "10999", Email = "inesb@wideworldimporters.com", } );}
    ```

    Esse código inicializa (propaga) o banco de dados com as informações de contato. Para obter mais informações sobre a propagação do banco de dados, consulte [Propagando e depurando BDs do Entity Framework (EF)][Propagando e depurando BDs do Entity Framework (EF)].

7.  Em **Console do Gerenciador de Pacotes**, digite o comando:

    ``` prettyprint
    update-database
    ```

    O `update-database` executa a primeira migração que cria o banco de dados. Por padrão, o banco de dados é criado como um banco de dados LocalDB do SQL Server Express.
    ![Console do Gerenciador de Pacotes][Console do Gerenciador de Pacotes]

### Executar o aplicativo localmente e exibir os dados

Execute o aplicativo agora para ver como você pode exibir os contatos.

1.  Primeiro, compile o projeto (**Ctrl+Shift+B**).
2.  Pressione **Ctrl+F5** para executar o aplicativo.
    O navegador abrirá e exibirá a página *Default.aspx*.
3.  Selecione o link **Contato/Demonstração** na parte superior da página, para exibir a página *Lista de Contatos*.
    ![Página Lista de Contatos][Página Lista de Contatos]

## Habilitar SSL para o projeto

SSL é um protocolo que permite que os servidores Web e clientes Web se comuniquem de modo mais seguro, por meio de criptografia. Quando o SSL não é utilizado, os dados enviados entre o cliente e servidor ficam abertos à detecção de pacotes por qualquer um com acesso físico à rede. Adicionalmente, diversos esquemas comuns de autenticação não são seguros por HTTP puro. Em particular, a autenticação Básica e a autenticação de formulários enviam credenciais sem criptografia. Para serem seguros, esses esquemas de autenticação precisam utilizar SSL.

1.  No **Gerenciador de Soluções**, clique no projeto **ContactManager** e então pressione **F4** para exibir a janela **Propriedades**.
2.  Altere **SSL Habilitado** para `true`.
3.  Copie a **URL do SSL**, de modo que você possa utilizá-la mais tarde.
    A URL do SSL será `https://localhost:44300/` a menos que você tenha criados Sites de SSL (como mostrado abaixo).
    ![Propriedades do Projeto][Propriedades do Projeto]
4.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **Gerenciador de Contatos** e clique em **Propriedades**.
5.  Na guia à esquerda, clique em **Web**.
6.  Altere a **Url do projeto** para usar a **URL de SSL** que você salvou anteriormente.
    ![Propriedades da Web do Projeto][Propriedades da Web do Projeto]
7.  Salve a página pressionando **CTRL+S**.
8.  Pressione **Ctrl+F5** para executar o aplicativo.
    O Visual Studio exibirá uma opção para permitir que você evite advertências do SSL.
9.  Clique em **Sim** para confiar no certificado SSL IIS Express e continuar.
    ![Detalhes do certificado SSL IIS Express][Detalhes do certificado SSL IIS Express]
    Um aviso de segurança é exibido.

10. Clique em **Sim** para instalar o certificado em seu localhost.
    ![Caixa de diálogo Aviso de Segurança][Caixa de diálogo Aviso de Segurança]
    A janela do navegador será exibida.

Você pode testar seu aplicativo Web localmente, utilizando o SSL.

## Adicionar um provedor de OAuth 2.0

Os Web Forms ASP.NET oferecem opções aprimoradas de associação e autenticação. Esses aprimoramentos incluem OAuth. O OAuth é um protocolo aberto que permite autorização segura em um método simples e padrão da web, aplicativos móveis e de área de trabalho. O modelo de Internet do ASP.NET MVC usa OAuth para expor o Facebook, Twitter, Google e a Microsoft como provedores de autenticação. Embora este tutorial use apenas o Google como o provedor de autenticação, você pode facilmente modificar o código para usar qualquer um dos provedores. As etapas para implementar outros provedores são muito semelhantes às etapas que você verá neste tutorial.

Além da autenticação, o tutorial também usará funções para implementar a autorização. Somente os usuários que você adicionar à função `canEdit` poderão alterar dados (criar, editar ou excluir contatos).

As etapas a seguir permitirão que você adicione um provedor de autenticação do Google.

1.  Abra o arquivo *App\_Start\\Startup.Auth.cs*.
2.  Remove os caracteres do comentário do método `app.UseGoogleAuthentication()` para que o método pareça-se cm o seguinte:

    ``` prettyprint
     app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions() { ClientId = "", ClientSecret = "" });
    ```

3.  Navegue até o [Console Para Desenvolvedores do Google][Console Para Desenvolvedores do Google]. Você também precisará se autenticar com sua conta de email de desenvolvedor do Google (gmail.com). Se você não tem uma conta do Google, selecione o link **Criar uma conta**.
    Em seguida, você verá o **Console Para Desenvolvedores do Google**.
    ![Console Para Desenvolvedores do Google][6]

4.  Clique no botão **Criar Projeto** e insira um nome e ID de projeto (você pode utilizar os valores padrão). Em seguida, clique na **caixa de seleção Contrato** e no botão **Criar**.
    ![Google - Novo Projeto][Google - Novo Projeto]
    Em alguns segundos, o novo projeto será criado e seu navegador exibirá a página com os novos projetos.
5.  Na guia esquerda, clique em **APIs e autenticação**, então clique em **Credenciais**.
6.  Clique em **Criar Nova ID de Cliente** em **OAuth**.
    A caixa de diálogo **Criar ID de Cliente** será exibida.
    ![Google - Criar ID de Cliente][Google - Criar ID de Cliente]
7.  Na caixa de diálogo **Criar ID de Cliente**, mantenha o **aplicativo Web** padrão para o tipo de aplicativo.
8.  Defina as **Origens de JavaScript Autorizadas** como a URL de SSL que você utilizou anteriormente neste tutorial (**<https://localhost:44300/>** a menos que você tenha criado outros projetos SSL).
    Esta URL é a origem para seu aplicativo. Para este exemplo, você irá inserir apenas a URL de teste do localhost. Todavia, você pode inserir múltiplas URLs na conta, para localhost e produção.

9.  Defina o **URI de Redirecionamento Autorizado** com o seguinte valor:

    ``` prettyprint
    https://localhost:44300/signin-google
    ```

    Este valor é o URI que o OAuth ASP.NET utiliza para comunicar-se com o servidor google OAuth. Lembre-se da URL de SSL que você utilizou acima (**<https://localhost:44300/>** a menos que você tenha criado outros projetos SSL).

10. Clique no botão **Criar ID de Cliente**.
11. No Visual Studio, atualize o método `UseGoogleAuthentication` da página *Startup.Auth.cs* copiando e colando o **AppId** e **App Secret** no método. Os valores da **AppId** e da **App Secret**, mostrados abaixo, são exemplos e não funcionarão.

    ``` prettyprint
    using System;using Microsoft.AspNet.Identity;using Microsoft.AspNet.Identity.EntityFramework;using Microsoft.AspNet.Identity.Owin;using Microsoft.Owin;using Microsoft.Owin.Security.Cookies;using Microsoft.Owin.Security.DataProtection;using Microsoft.Owin.Security.Google;using Owin;using ContactManager.Models;
    namespace ContactManager{ public partial class Startup {
        // For more information on configuring authentication, please visit http://go.microsoft.com/fwlink/?LinkId=301883
        public void ConfigureAuth(IAppBuilder app)
        {
            // Configure the db context and user manager to use a single instance per request
            app.CreatePerOwinContext(ApplicationDbContext.Create);
            app.CreatePerOwinContext<ApplicationUserManager>(ApplicationUserManager.Create);

            // Enable the application to use a cookie to store information for the signed in user
            // and to use a cookie to temporarily store information about a user logging in with a third party login provider
            // Configure the sign in cookie
            app.UseCookieAuthentication(new CookieAuthenticationOptions
            {
                AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
                LoginPath = new PathString("/Account/Login"),
                Provider = new CookieAuthenticationProvider
                {
                    OnValidateIdentity = SecurityStampValidator.OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                        validateInterval: TimeSpan.FromMinutes(20),
                        regenerateIdentity: (manager, user) => user.GenerateUserIdentityAsync(manager))
                }
            });
            // Use a cookie to temporarily store information about a user logging in with a third party login provider
            app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);

            // Uncomment the following lines to enable logging in with third party login providers
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
    ```

    }

    </pre>

12. Pressione **CTRL+F5** para compilar e executar o aplicativo. Clique no link **Logon**.
13. Em **Usar outro serviço para fazer logon**, clique em **Google**.
    ![Logon][Logon]
14. Se você precisa inserir suas credenciais, você será redirecionado ao site do google onde você irá inserir essas credenciais.
    ![Google - Entrar][Google - Entrar]
15. Após inserir suas credenciais, aparecerá uma solicitação para que você dê permissões para o aplicativo Web que acabou de criar:
    ![Conta de Serviço Padrão do Projeto][Conta de Serviço Padrão do Projeto]
16. Clique em **Aceitar**. Você será agora redirecionado de volta à página **Registrar** do aplicativo **ContactManager**, na qual você poderá registrar sua conta do Google.
    ![Registrar com sua Conta do google][Registrar com sua Conta do google]
17. Você tem a opção de alterar o nome de registro de email local utilizado para sua conta do Gmail, mas normalmente você deverá preferir manter o alias de email padrão (ou seja, aquele utilizado por você para a autenticação). Clique em **Logon**.

## Utilizar a API de associação para restringir o acesso

A Identidade ASP.NET é o sistema de associação utilizado para autenticação ao criar um aplicativo Web ASP.NET. Ela torna mais fácil integrar dados de perfil específicos de usuário com dados de aplicativo. Além disso, a Identidade ASP.NET permite que você escolha o modelo de persistência para perfis de usuário em seu aplicativo. Você pode armazenar os dados em um banco de dados do SQL Server ou outro dispositivo de armazenamento de dados, incluindo dispositivos de armazenamento de dados *NoSQL* como Tabelas de Armazenamento do Azure.

Ao utilizar o modelo padrão de Web Forms ASP.NET, você tem funcionalidade integrada de associação que você pode utilizar imediatamente, a partir do momento em que o aplicativo é executado. Você utilizará a Identidade do ASP.NET para acrescentar uma função de administrador e atribuir um usuário a essa função. Em seguida, você aprenderá como restringir o acesso à pasta de administração e às páginas que são utilizadas para modificar os dados de contato.

### Adicionar um Administrator

Usando a Identidade ASP.NET, você pode adicionar uma função de administrador e atribuir um usuário a essa função, por meio de código.

1.  No **Gerenciador de Soluções**, abra o arquivo *Configuration.cs* na pasta *Migrações*.
2.  Adicione as instruções `using` a seguir no namespace `ContactManger.Migrations`:

    ``` prettyprint
    using Microsoft.AspNet.Identity;using Microsoft.AspNet.Identity.EntityFramework;
    ```

3.  Adicione o método `AddUserAndRole` a seguir à classe `Configuration` depois do método `Seed`:

    ``` prettyprint
     public void AddUserAndRole(ContactManager.Models.ApplicationDbContext context) { IdentityResult IdRoleResult; IdentityResult IdUserResult;
        var roleStore = new RoleStore&lt;IdentityRole&gt;(context);
        var roleMgr = new RoleManager&lt;IdentityRole&gt;(roleStore);

        if (!roleMgr.RoleExists(&quot;canEdit&quot;))
        {
            IdRoleResult = roleMgr.Create(new IdentityRole { Name = &quot;canEdit&quot; });
        }

        //var userStore = new UserStore&lt;ApplicationUser&gt;(context);
        //var userMgr = new UserManager&lt;ApplicationUser&gt;(userStore);
        var userMgr = new UserManager&lt;ApplicationUser&gt;(new UserStore&lt;ApplicationUser&gt;(context));

        var appUser = new ApplicationUser
        {
            UserName = &quot;canEditUser@wideworldimporters.com&quot;,
            Email = &quot;canEditUser@wideworldimporters.com&quot;
        };
        IdUserResult = userMgr.Create(appUser, &quot;Pa$$word1&quot;);

        if (!userMgr.IsInRole(userMgr.FindByEmail(&quot;canEditUser@wideworldimporters.com&quot;).Id, &quot;canEdit&quot;))
        {
          //  IdUserResult = userMgr.AddToRole(appUser.Id, &quot;canEdit&quot;);
            IdUserResult = userMgr.AddToRole(userMgr.FindByEmail(&quot;canEditUser@wideworldimporters.com&quot;).Id, &quot;canEdit&quot;);
        }
    }
    ```

    </pre>
4.  Adicione uma chamada para o método `AddUserAndRole` de dentro do início do método `Seed`. Observe que somente o início do método `Seed` está sendo mostrado.

    ``` prettyprint
     protected override void Seed(ContactManager.Models.ApplicationDbContext context) { AddUserAndRole(context);
    ```

5.  Após salvar todas as suas alterações, execute o comando a seguir no **Console do Gerenciador de Pacotes**:

    ``` prettyprint
    Update-Database
    ```

    Esse código cria uma nova função chamada `canEdit`, bem como um novo usuário local com o email <canEditUser@wideworldimporters.com>. Depois, o código adiciona <canEditUser@wideworldimporters.com> à função `canEdit`. Para obter mais informações, consulte a página de recursos de [Identidade do ASP.NET][Identidade do ASP.NET].

    </p>

### Restringir o Acesso à Pasta de Administração

O aplicativo de exemplo **ContactManager** permite tanto que usuários anônimos quanto aqueles que já realizaram logon exibam contatos. Todavia, após você completar essa seção, os usuários que já fizeram logon e aos quais foi atribuída a função "canEdit" serão os únicos usuários capazes de modificar os contatos.

Você criará uma pasta chamada *Admin*, que somente os usuários aos quais for atribuída a função "canEdit" poderão acessar.

1.  No **Gerenciador de Soluções**, adicione uma subpasta à pasta *Contatos* e nomeie a nova subpasta como *Admin*.
2.  Mova os arquivos a seguir da pasta *Contatos* para a pasta *Contatos/Admin*:

    -   *Delete.aspx* e\* Delete.aspx.cs\*
    -   *Edit.aspx* e\* Edit.aspx.cs\*
    -   *Insert.aspx* e\* Insert.aspx.cs\*

3.  Atualize as referências a links em *Contatos/Default.aspx* adicionando "Admin/" antes das referências a páginas que oferecem um link para *Insert.aspx*, *Edit.aspx* e *Delete.aspx*:

    ``` prettyprint
    <%@ Page Title="ContactsList" Language="C#" MasterPageFile="~/Site.Master" CodeBehind="Default.aspx.cs" Inherits="ContactManager.Contacts.Default" ViewStateMode="Disabled" %><%@ Register TagPrefix="FriendlyUrls" Namespace="Microsoft.AspNet.FriendlyUrls" %>
    <asp:Content runat="server" ContentPlaceHolderID="MainContent"> <h2>Contacts List</h2> <p> <asp:HyperLink runat="server" NavigateUrl="Admin/Insert.aspx" Text="Create new" /> </p> <div> <asp:ListView runat="server" DataKeyNames="ContactId" ItemType="ContactManager.Models.Contacts" AutoGenerateColumns="false" AllowPaging="true" AllowSorting="true" SelectMethod="GetData"> <EmptyDataTemplate> There are no entries found for Contacts </EmptyDataTemplate> <LayoutTemplate> <table class="table"> <thead> <tr> <th>Name</th> <th>Address</th> <th>City</th> <th>State</th> <th>Zip</th> <th>Email</th> <th> </th> </tr> </thead> <tbody> <tr runat="server" id="itemPlaceholder" /> </tbody> </table> </LayoutTemplate> <ItemTemplate> <tr> <td> <asp:DynamicControl runat="server" DataField="Name" ID="Name" Mode="ReadOnly" /> </td> <td> <asp:DynamicControl runat="server" DataField="Address" ID="Address" Mode="ReadOnly" /> </td> <td> <asp:DynamicControl runat="server" DataField="City" ID="City" Mode="ReadOnly" /> </td> <td> <asp:DynamicControl runat="server" DataField="State" ID="State" Mode="ReadOnly" /> </td> <td> <asp:DynamicControl runat="server" DataField="Zip" ID="Zip" Mode="ReadOnly" /> </td> <td> <asp:DynamicControl runat="server" DataField="Email" ID="Email" Mode="ReadOnly" /> </td> <td> <a href="Admin/Edit.aspx?ContactId=<%#: Item.ContactId%>">Edit</a> | <a href="Admin/Delete.aspx?ContactId=<%#: Item.ContactId%>">Delete</a> </td> </tr> </ItemTemplate> </asp:ListView> </div></asp:Content>
    ```

4.  Atualize as seis referências do código `Response.Redirect("Default.aspx")` para `Response.Redirect("~/Contacts/Default.aspx")` para os três arquivos a seguir:

    -   *Delete.aspx.cs*
    -   *Edit.aspx.cs*
    -   *Insert.aspx.cs*

    Agora, todos os links funcionarão corretamente quando você exibir e atualizar os dados de contato.

5.  Para restringir o acesso à pasta *Admin*, a partir do **Gerenciador de Soluções**, clique com o botão direito na pasta *Admin* e selecione **Adicionar Novo Item**.
6.  A partir da lista de modelos web do Visual C#, selecione **Arquivo de Configuração Web** a partir da lista do meio, aceite o nome padrão de *Web.config*, então selecione **Adicionar**.
7.  Substitua o conteúdo XML existente no arquivo *Web.config* pelo seguinte:

    ``` prettyprint
    <?xml version="1.0"?><configuration> <system.web> <authorization> <allow roles="canEdit"/> <deny users="*"/> </authorization> </system.web></configuration>
    ```

8.  Salve o arquivo *Web.config*.
    O arquivo *Web.config* especifica que somente usuários aos quais foi atribuída a função "canEdit" podem acessar as páginas contidas na pasta *Admin*.

Quando um usuário que não é parte da função "canEdit" tenta modificar os dados, ele é redirecionado à página de *Logon*.

## Implantar o aplicativo com o banco de dados no Azure

Agora que o aplicativo Web está concluído, você pode publicá-lo no Azure.

### Publicar o Aplicativo

1.  No Visual Studio, compile o projeto (**Ctrl+Shift+B**).
2.  Clique com o botão direito no projeto no **Gerenciador de Soluções** e selecione a opção **Publicar**.
    ![Opção de menu Publicar][Opção de menu Publicar]
    A caixa de diálogo **Publicar na Web** é exibida.
    ![Caixa de diálogo Publicar na Web][7]
3.  A partir da guia **Perfil**, selecione **Sites do Azure** como o destino de publicação, se ainda não estiver selecionado.
    ![Caixa de diálogo Publicar na Web][8]
4.  Clique em **Entrar** se você ainda não tiver se autenticado.
5.  Selecione o site existente, que você criou anteriormente neste tutorial a partir da caixa suspensa **Sites Existentes** e clique no botão **OK**.
    ![Selecione a caixa de diálogo Site Existente][Selecione a caixa de diálogo Site Existente]
    Se for solicitado a você que salve as alterações feitas ao perfil, selecione **Sim**.
6.  Clique na guia **Configurações**.
    ![Selecione a caixa de diálogo Site Existente][9]
7.  Defina a caixa suspensa **Configuração** com a opção **Depurar**.
8.  Clique no ícone com a **seta para baixo** ao lado de **ApplicationDbContext** e defina-o como **ContactDB**.
9.  Marque a caixa de seleção **Executar as Migrações Iniciais do Código**.

    > [WACOM.NOTE]
    > Neste exemplo, você precisa marcar essa caixa de seleção na primeira vez em que publicar o aplicativo. Desse modo, o método *Seed* no arquivo *Configuration.cs* só será chamado uma vez.

10. Em seguida, clique em **Publicar**.
    Seu aplicativo será publicado no Azure.

> [WACOM.NOTE]
> Se você fechou e abriu novamente o Visual Studio depois de criar o perfil de publicação, talvez você não veja a cadeia de conexão na lista suspensa. Nesse caso, em vez de editar o perfil de publicação que você criou anteriormente, crie um novo da mesma maneira como criou anteriormente e siga estas etapas na guia **Configurações**.)

### Revisar o Aplicativo no Azure

1.  No navegador, clique no link **Contato/Demonstração**.
    A Lista de Contatos é exibida.
    ![Contatos listados no Navegador][Contatos listados no Navegador]

2.  Selecione **Criar Novo** na página **Lista de Contatos**.
    ![Contatos Listados no Navegador][Contatos Listados no Navegador]
    Você é redirecionado à página de **Logon** porque você não realizou logon com uma conta com permissão para modificar contatos.
3.  Após você inserir o email e senha abaixo, clique no botão **Logon**.
    **Email**: `canEditUser@wideworldimporters.com`
    **Senha**: `Pa$$word1`
    ![Página Logon][Página Logon]

4.  Insira novos dados para cada campo e então pressione o botão **Inserir**.
    ![Adicionar Página Novo Contato][Adicionar Página Novo Contato]
    A página *EditContactList.aspx* é exibida, com o novo registro.
    ![Adicionar Página Novo Contato][10]

5.  Selecione o link **Logoff**.

### Parar o Aplicativo

Para evitar que outras pessoas se registrem e utilizem seu aplicativo de exemplo, você interromperá o site.

1.  No Visual Studio, a partir do **Menu Exibir**, selecione **Gerenciador de Servidores**.
2.  No **Gerenciador de Servidores**, navegue até **Sites**.
3.  Clique com o botão direito do mouse em cada instância do Site e selecione **Parar Site**.
    ![Item de menu Parar Site][Item de menu Parar Site]

    Como alternativa, no Portal de Gerenciamento do Microsoft Azure, você pode selecionar o site e clicar no ícone **parar** na parte inferior da página.
    ![Adicionar Página Novo Contato][11]

## Revisar o banco de dados

É importante saber como exibir e modificar o banco de dados diretamente. Saber como trabalhar diretamente com o banco de dados permite que você confirme dados presentes no banco de dados e também compreenda como os dados são armazenados em cada tabela.

### Examine o BD do SQL Azure

1.  No Visual Studio, abra **Gerenciador de Servidores** e navegue até **ContactDB**.
2.  Clique com o botão direito do mouse no **ContactDB** e selecione **Abrir no Gerenciador de Objetos do SQL Server**.
    ![Item de menu Abrir no Gerenciador de Objetos do SQL Server][Item de menu Abrir no Gerenciador de Objetos do SQL Server]
3.  Se a caixa de diálogo **Adicionar Regra de Firewall** for exibida, selecione **Adicionar Regra de Firewall**.

    > [WACOM.NOTE]
    > Se você não pode expandir o item **Bancos de Dados SQL** e não pode ver o item **ContactDB** a partir do Visual Studio, você pode seguir as instruções para abrir uma porta de firewall ou um grupo de portas. Para fazer isso, siga as instruções em **Configurar regras de firewall do Azure** perto do final do [Tutorial MVC][Implantar um aplicativo ASP.NET MVC 5 seguro com Associação, OAuth e Banco de Dados SQL em um site do Azure]. Como alternativa, você também pode revisar os dados do banco de dados local compilando, executando o aplicativo e adicionando dados a ele localmente (**CTRL+F5** a partir do Visual Studio).

4.  Se a caixa de diálogo **Conectar-se ao servidor** for exibida, insira a **senha** que você criou no início deste tutorial e pressione o botão **Conectar**.

    > [WACOM.NOTE]
    > Se você não se lembra da senha, você pode encontrá-la em seu arquivo local do projeto. No **Gerenciador de Soluções**, expanda a pasta *Propriedades* e então expanda a pasta *PublishProfiles*. Abra o arquivo *contactmanager.pubxml* (seu arquivo pode ser nomeado de modo diferente). Pesquise no arquivo, buscando sua senha para publicação.

5.  Expanda o banco de dados **contactDB** e então expanda **Tabelas**.
6.  Clique com o botão direito na tabela **dbo.AspNetUsers** e selecione **Exibir Dados**.
    ![Item de menu Exibir Dados][Item de menu Exibir Dados]
    Você pode exibir os dados associados ao usuário <canEditUser@contoso.com>.
    ![Janela ContactManager][Janela ContactManager]

### Adicionar um usuário à função de administrador editando o banco de dados

Anteriormente, no tutorial, você usou código para adicionar usuários à função canEdit. Um método alternativo é manipular diretamente os dados nas tabelas de associação. As etapas a seguir mostram como usar esse método alternativo para adicionar um usuário a uma função.

1.  No **Gerenciador de Objetos do SQL Server**, clique com o botão direito do mouse em **dbo.AspNetUserRoles** e selecione **Exibir Dados**.
    ![Dados de AspNetUserRoles][Dados de AspNetUserRoles]
2.  Copie a *RoleId* e cole-a na linha vazia (nova).
    ![Dados de AspNetUserRoles][12]
3.  Na tabela **dbo.AspNetUsers**, encontre o usuário que você deseja incluir na função, copiando a *Id* do usuário.
4.  Cole a *Id* copiada no campo **UserId** da nova linha na tabela **AspNetUserRoles**.

> [WACOM.NOTE]
> Estamos trabalhando em uma ferramenta que irá facilitar bastante o gerenciamento de usuários e funções.

## Próximas etapas

Para obter mais informações sobre Web Forms ASP.NET, consulte [Saiba sobre os Web Forms ASP.NET][Saiba sobre os Web Forms ASP.NET] no site ASP.NET e [Guias e tutoriais do Microsoft Azure][Guias e tutoriais do Microsoft Azure].

Este tutorial foi baseado no tutorial MVC [Implantar um aplicativo ASP.NET MVC 5 seguro com Associação, OAuth e Banco de Dados SQL em um site do Azure][Implantar um aplicativo ASP.NET MVC 5 seguro com Associação, OAuth e Banco de Dados SQL em um site do Azure] programado por Rick Anderson (Twitter [@RickAndMSFT][@RickAndMSFT]) com assistência de Tom Dykstra e Barry Dorrans (Twitter [@blowdart][@blowdart]).

Deixe comentários sobre o que você gostou e o que você gostaria de ver melhorado, não apenas sobre o próprio tutorial, mas também sobre os produtos que ele demonstra. Seus comentários nos ajudarão a priorizar melhorias. Você também pode solicitar e votar em novos tópicos em [Mostre-me como com código][Mostre-me como com código].

  [Implantar um aplicativo ASP.NET MVC 5 seguro com Associação, OAuth e Banco de Dados SQL em um site do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [Contatos - Editar página]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms00.png
  [ativar os benefícios de assinante MSDN]: /pt-br/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [inscrever-se para uma avaliação gratuita]: /pt-br/pricing/free-trial/?WT.mc_id=A261C142F
  [Configurar o ambiente de desenvolvimento]: #set-up-the-development-environment
  [Configurar o ambiente do Azure]: #Set-up-the-Azure-environment
  [Criar um aplicativo de Web Forms ASP.NET]: #Create-an-ASP.NET-Web-Forms-Application
  [Adicionar um banco de dados ao aplicativo]: #Add-a-Database-to-the-Application
  [Habilitar SSL para o projeto]: #Enable-SSL-for-the-Project
  [Adicionar um provedor de OAuth 2.0]: #Add-an-OAuth-2.0-Provider
  [Utilizar a API de associação para restringir o acesso]: #Use-the-Membership-API-to-Restrict-Access
  [Implantar o aplicativo com o banco de dados no Azure]: #Deploy-the-Application-with-the-Database-to-Azure
  [Revisar o banco de dados]: #Review-the-Database
  [Próximas etapas]: #Next-Steps
  [Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=306566
  [SDK do Azure para o Visual Studio 2013]: http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409
  [Web Platform Installer]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-01.png
  [Visual Studio 2013 Atualização 2]: http://www.microsoft.com/download/details.aspx?id=42666
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [1]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-02.png
  [Criação Personalizada]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-03.png
  [Contatos - Criar Novo Site]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-04.png
  [Configurações de Banco de Dados]: ./media/web-sites-dotnet-web-forms-secure/Intro-SecureWebForms-05.png
  [Menu Arquivo - Novo Projeto]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms01.png
  [Caixa de diálogo Novo Projeto]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms02.png
  [Caixa de diálogo Novo Projeto ASP .NET]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms03.png
  [2]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms04.png
  [Selecione Publicar]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms05.png
  [Caixa de diálogo Publicar na Web]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms06.png
  [Caixa de diálogo Selecionar Site Existente]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms07.png
  [3]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms08.png
  [Atividade de Publicação da Web]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms09.png
  [Aplicativo no Navegador]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms10.png
  [4]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms11.png
  [Selecione Classe]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms12.png
  [Caixa de diálogo Adicionar Novo Item]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13.png
  [Caixa de diálogo Extensões e Atualizações]: ./media/web-sites-dotnet-web-forms-secure/ExtensionsAndUpdatesDB.png
  [Caixa de diálogo Adicionar Scaffold]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13a.png
  [Caixa de diálogo Adicionar Páginas de Web Forms]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13b.png
  [5]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13c.png
  [Console do Gerenciador de Pacotes]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms13d.png
  [Página Lista de Contatos]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms17.png
  [Propriedades do Projeto]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms18.png
  [Propriedades da Web do Projeto]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms19.png
  [Detalhes do certificado SSL IIS Express]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms20.png
  [Caixa de diálogo Aviso de Segurança]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21.png
  [Console Para Desenvolvedores do Google]: https://console.developers.google.com/
  [6]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21a.png
  [Google - Novo Projeto]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21b.png
  [Google - Criar ID de Cliente]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21c.png
  [Logon]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21d.png
  [Google - Entrar]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21e.png
  [Conta de Serviço Padrão do Projeto]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21f.png
  [Registrar com sua Conta do google]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms21g.png
  [Identidade do ASP.NET]: http://www.asp.net/identity
  [Opção de menu Publicar]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms22.png
  [7]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms22a.png
  [8]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms23.png
  [Selecione a caixa de diálogo Site Existente]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms25.png
  [9]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms26.png
  [Contatos listados no Navegador]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms27.png
  [Contatos Listados no Navegador]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms29.png
  [Página Logon]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms28.png
  [Adicionar Página Novo Contato]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms30.png
  [10]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms31.png
  [Item de menu Parar Site]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms26a.png
  [11]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms26b.png
  [Item de menu Abrir no Gerenciador de Objetos do SQL Server]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms32.png
  [Item de menu Exibir Dados]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms34.png
  [Janela ContactManager]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms35.png
  [Dados de AspNetUserRoles]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms36.png
  [12]: ./media/web-sites-dotnet-web-forms-secure/SecureWebForms37.png
  [Saiba sobre os Web Forms ASP.NET]: http://www.asp.net/web-forms
  [Guias e tutoriais do Microsoft Azure]: http://azure.microsoft.com/pt-br/documentation/services/web-sites/#net
  [@RickAndMSFT]: https://twitter.com/RickAndMSFT
  [@blowdart]: https://twitter.com/blowdart
  [Mostre-me como com código]: http://aspnet.uservoice.com/forums/228522-show-me-how-with-code
