<properties linkid="develop-dotnet-aspnet-mvc-4-mobile-website" urlDisplayName="Site de celular do ASP.NET MVC 4" pageTitle="Site de celular do .NET ASP.NET MVC 4 - tutoriais do Azure" metaKeywords="Tutorial do Azure, tutorial do aplicativo de web do Azure, aplicativo móvel do Azure, ASP.NET MVC 4,,ASP.NET MVC do Azure" description="Um tutorial que ensina como implantar um aplicativo web para um site do Azure usando recursos móveis no aplicativo web de ASP.NET MVC 4." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Implantar um aplicativo de Web móvel do ASP.NET MVC nos Sites do Azure" authors="tdykstra" solutions="" manager="" editor="" />





# Implante um aplicativo Web móvel ASP.NET MVC em sites do Azure

***Por [Rick Anderson](https://twitter.com/RickAndMSFT) atualizado em 26 de junho de 2013.***

Este tutorial ensinará você o básico sobre como implantar um aplicativo Web em um site do Azure. Para os fins deste tutorial, trabalharemos com recursos móveis em um aplicativo web ASP.NET MVC 4. Para executar as etapas neste tutorial, você pode usar o Microsoft Visual Studio 2012. Você também pode usar o [Visual Studio Express 2012][], que são versões gratuitas do Microsoft Visual Studio. 

<h2>Você aprenderá:</h2>

- Como os modelos do ASP.NET MVC 4 usam o atributo de visor do HTML5 e o processamento adaptável para melhorar a exibição em dispositivos móveis.
- Como criar exibições específicas para dispositivos móveis.
- Como criar um interruptor de exibição que permite aos usuários alternar entre uma exibição móvel e uma exibição da área de trabalho do aplicativo.
- Como implantar o aplicativo web no Azure.

Neste tutorial, você adicionará recursos móveis para o aplicativo de listagem de conferência simples que é fornecido no projeto inicial. A captura de tela a seguir mostra a página principal do aplicativo concluído como visto no emulador de telefone do Windows 7.

![Página principal do aplicativo de conferência MVC4.][AppMainPage]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

<h2>Configurar o ambiente de desenvolvimento</h2>

Defina o ambiente de desenvolvimento instalando o SDK do Azure para o .NET Framework. 

1. Para instalar o SDK do Azure para .NET, clique no link abaixo. Se você ainda não tiver o Visual Studio 2012 instalado, ele será instalado pelo link. Este tutorial requer o Visual Studio 2012. 
[SDK do Azure do Visual Studio 2012]( http://go.microsoft.com/fwlink/?LinkId=254364)
1. Quando for solicitado a executar ou salvar a instalação executável, clique em **Executar**.
1. Na janela do Instalador de Plataforma Web, clique em **Instalar** e prossiga com a instalação.

![Instalador de Plataforma Web - SDK do Azure para .NET][WebPIAzureSdk20NetVS12]

Também será necessário um emulador do navegador móvel. Qualquer uma das opções a seguir funcionará:

- [Emulador do telefone Windows 7][Win7PhoneEmulator]. (Esse é o emulador que é usado na maioria das capturas de tela neste tutorial).
- Altere a sequência de agente do usuário para emular um iPhone. Consulte [esta entrada de blog][setuseragent] sobre a instalação Geek.
- [Operar o emulador móvel][OperaMobileEmulator].
- [Apple Safari][AppleSafari] com o agente de usuário definido para iPhone. Para obter instruções sobre como configurar o agente do usuário no Safari para "iPhone", consulte [Como permitir que o Safari finja que é IE][HowToSafari] no blog de David Alison.
- [FireFox][FireFox] com o [Alternador de agente do usuário do FireFox][FireFoxUserAgentSwitcher].

Este tutorial mostra o código em C#. No entanto, o projeto inicial e o projeto concluído estarão disponíveis no Visual Basic. Os projetos do Visual Studio com o código-fonte do Visual Basic e C# estão disponíveis para acompanhar este tópico:

- [Download do projeto inicial][MVC4StarterProject]
- [Concluído o download do projeto][FinishedProject]

<h2>Etapas neste tutorial:</h2>

- [Criar um site do Azure](#bkmk_CreateWebSite)
- [Configurar o projeto inicial](#bkmk_setupstarterproject)
- [Substitua os modos de exibição, Layouts e exibições parciais][]
- [Usar o jQuery Mobile para definir a interface de navegador móvel][]
- [Melhorar a lista de alto-falantes][]
- [Criar um modo de exibição móvel de alto-falantes][]
- [Melhorar a lista de guias][]
- [Melhorar a lista de datas][]
- [Melhorar o modo de exibição da tabela de sessões][]
- [Melhorar o modo de exibição SessionByCode][]
- [Implantar o aplicativo no site do Azure][]

<h3><a name="bkmk_CreateWebSite"></a>Criar um site no Azure</h3>

O Site do Azure será executado em um ambiente de hospedagem compartilhado, o que significa que ele é executado em MVs (máquinas virtuais) que são compartilhadas com outros clientes do Azure. Um ambiente de hospedagem compartilhado é uma maneira de começar na nuvem a um baixo custo. Mais tarde, se seu tráfego da Web aumentar, o aplicativo poderá ser dimensionado para atender a necessidade executando em VMs dedicadas. Se precisar de uma arquitetura mais complexa, você poderá migrar para um serviço de nuvem do Azure. Os serviços de nuvem são executados em VMs dedicadas que podem ser configuradas de acordo com suas necessidades.

1.	Faça logon no [Portal de Gerenciamento do Azure][managementportal]. No Portal de Gerenciamento, clique em **Novo**.

	![][CreateWebSite1]

2.	Clique em **Site** e, em seguida, clique em **Criação rápida**.

	![][CreateWebSite2]

3.	Em **Criar um novo site**, insira uma cadeia de caracteres na caixa **URL** a ser usada como a URL exclusiva de seu aplicativo.

	![][CreateWebSite3]

	A URL completa consistirá no que você digitar aqui mais o sufixo que você vê abaixo da caixa de texto. A ilustração mostra "MyMobileMVC4WebSite", mas se alguém já tiver usado essa URL, será necessário escolher uma diferente. Selecione a **REGIÃO** na qual você está localizado.

4. Clique na marca de seleção na parte inferior da caixa para indicar que você concluiu.

O Portal de Gerenciamento retorna para a página Sites da Web e a coluna Status mostra que o site está sendo criado. Depois de um tempo (normalmente menos de um minuto), a coluna Status mostra que o site foi criado com êxito. Na barra de navegação à esquerda, o número de sites presentes na conta é exibido no ícone Sites da Web, e o número de bancos de dados aparece no ícone Bancos de Dados SQL.

![][CreateWebSite4]

<h3><a name="bkmk_setupstarterproject"></a>Configurar o projeto inicial.</h3>

1.	Baixe o [projeto inicial do aplicativo de listagem de conferência][MVC4StarterProject].

2. 	Em seguida, no Windows Explorer, clique com o botão direito no arquivo MvcMobileStarterBeta.zip e escolha *Propriedades*.

3. 	Na caixa de diálogo Propriedades de MvcMobileRTMStarter.zip, escolha o botão Desbloquear. (Desbloquear impede um aviso de segurança que ocorre quando você tenta usar um arquivo .zip que você baixou da web.)

	![Caixa de diálogo Propriedades.][PropertiesPopup]

4.	Clique com o botão direito no arquivo MvcMobile.zip e selecione Extrair tudo para descompactar o arquivo.

5. 	No Visual Studio, abra o arquivo MvcMobile.sln.

<h3>Para executar o projeto inicial</h3>

1.	Pressione CTRL+F5 para executar o aplicativo, que será exibido no navegador da área de trabalho.
2.	Inicie o emulador de navegador móvel, copie a URL para o aplicativo de conferência para o emulador e clique em Procurar pelo link da guia.
	- Se você estiver usando o emulador do Windows Phone, clique na barra de URL e pressione a tecla Pause para obter acesso ao teclado. A imagem abaixo mostra o modo de exibição de AllTags (escolhendo Procurar por guia).

	![Procure pela página da guia.][BrowseByTagWithCallout]

A exibição é muito legível em um dispositivo móvel. Escolha o link do ASP.NET.

![Procure sessões marcadas como ASP.NET.][ASPNetPage]

O modo de exibição de marca do ASP.NET é muito desorganizado. Por exemplo, a coluna Data é muito difícil de ler. Mais adiante no tutorial, você criará uma versão do modo de exibição AllTags que é especificamente para navegadores de dispositivos móveis e que tornará a exibição legível.

<h2><a name="bkmk_overrideviews"></a>Substitua os modos de exibição, Layouts e exibições parciais</h2>

Nesta seção, você criará um arquivo de layout específico para dispositivos móveis.

Um novo recurso significativo no ASP.NET MVC 4 é um mecanismo que permite que você substitua qualquer modo de exibição (inclusive layouts e modos de exibição parciais) para navegadores de dispositivos móveis em geral, para um navegador móvel individual ou para qualquer navegador específico. Para fornecer uma exibição específica para dispositivos móveis, você pode copiar um arquivo de modo de exibição e adicionar .Móvel ao nome do arquivo. Por exemplo, para criar uma exibição do Índice móvel, copie *Views\Home\Index.cshtml* para *Views\Home\Index.Mobile.cshtml*.

Para iniciar, copie *Views\Shared\\_Layout.cshtml* para *Views\Shared\\_Layout.Mobile.cshtml*. Abra *_Layout.Mobile.cshtml* e altere o título da **Conferência MVC4** para **Conferência (Celular)**.

Em cada chamada **Html.ActionLink**, remova "Procurar por" em cada link ActionLink. O código a seguir mostra a seção de corpo completo do arquivo layout para dispositivos móveis.

     <body>
        <div class="page">
            <div id="header">
                <div id="logindisplay"></div>
                <div id="title">
                    <h1> Conferência (Celular)</h1>
                </div>
                <div id="menucontainer">
                    <ul id="menu">
                        <li>@Html.ActionLink("Home", "Index", "Home")</li>
                        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
                        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
                        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
                    </ul>
                </div>
            </div>
            <div id="main">
                @RenderBody()
            </div>
            <div id="footer">
            </div>
        </div>
    </body>

Copie o arquivo *Views\Home\AllTags.cshtml* para *Views\Home\AllTags.Mobile.cshtml*. Abra o novo arquivo e altere o elemento &lt;h2&gt; das "Marcas" para "marcas (M)":

     <h2>Marcas (M)</h2>

Navegue até a página de marcas usando um navegador de desktop e o emulador do navegador móvel. O emulador do navegador móvel mostra as duas alterações que você fez.

![Mostrar alterações à página de marcas][Overrideviews1]

Por outro lado, a exibição da área de trabalho não foi alterada.

![Mostrar modo de exibição de marcas da área de trabalho][Overrideviews2]

<h2><a name="bkmk_usejquerymobile"></a>Usar o jQuery Mobile para definir a interface de navegador móvel</h2>

Nesta seção, você instalará o pacote jQuery.Mobile.MVC NuGet, que instala o jQuery Mobile e um widget do alternador de modo de exibição.

A biblioteca [jQuery Mobile][jquerydocs] fornece uma estrutura de interface do usuário que funciona em todos os principais navegadores para dispositivos móveis. O jQuery Mobile aplica aprimoramento progressivo para navegadores de dispositivos móveis que oferecem suporte a CSS e JavaScript. O aprimoramento progressivo permite que todos os navegadores exibam o conteúdo básico de uma página da web, permitindo navegadores e dispositivos mais poderosos com uma exibição mais rica. Os arquivos JavaScript e CSS que estão incluídos com o jQuery Mobile estilizam muitos elementos de acordo com navegadores de dispositivos móveis sem fazer quaisquer alterações de marcação.

1. Excluir o arquivo *Shared\\_Layout.Mobile.cshtml* que você criou anteriormente.

2. Renomeie o *Views\Home\AllTags.Mobile.cshtml* para *Views\Home\AllTags.Mobile.cshtml.hide* (você usará este arquivo novamente mais tarde.) Como o arquivo não tem uma extensão .cshtml, ele não será usado pelo runtime do ASP.NET MVC para processar a exibição *AllTags*.

3. Instale o pacote jQuery.Mobile.MVC NuGet fazendo isso:

	a. No menu **Ferramentas**, selecione o Console do **Gerenciador de Pacotes** e depois selecione **Gerenciador de Pacotes da Biblioteca**.

		![Gerenciador de pacote de biblioteca][jquery1]
	
	b. No **Console do Gerenciador de Pacotes**, digite *Install-Package jQuery.Mobile.MVC -versão 1.0.0*

		![Console do Gerenciador de Pacotes][jquery2]

O pacote jQuery.Mobile.MVC NuGet instala o seguinte:

-  O arquivo *App_Start\BundleMobileConfig.cs*, que é necessário para fazer referência ao jQuery JavaScript e arquivos CSS adicionados. Você deve seguir as instruções abaixo e referenciar o pacote móvel definido neste arquivo.
- Arquivos do jQuery Mobile CSS.
- Um elemento controlador ViewSwitcher (*Controllers\ViewSwitcherController.cs)*. 
- Arquivos do jQuery Mobile JavaScript.
- Um arquivo de layout de estilo móvel jQuery (*Views\Shared\_Layout.Mobile.cshtml*). 
- Uma visualização parcial do alternador de vista (*MvcMobile\Views\Shared\_ViewSwitcher.cshtml*) que fornece um link no topo de cada página para mudar de visualização do desktop para exibição móvel e vice-versa.
- Arquivos de imagem .png e .gif na pasta Content\images. 

Abra o arquivo *Global.asax* e adicione o código a seguir como a última linha do método Application_Start.

 	BundleMobileConfig.RegisterBundles(BundleTable.Bundles);

O seguinte código mostra o arquivo Global.asax completo.

	using System; 
	using System.Web.Http; 
	using System.Web.Mvc; 
	using System.Web.Optimization; 
	using System.Web.Routing; 
	using System.Web.WebPages; 
	 
	namespace MvcMobile 
	{ 
	 
	    public class MvcApplication : System.Web.HttpApplication 
	    { 
	        protected void Application_Start() 
	        { 
	            DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone") 
	            { 
	                ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf 
	                    ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0) 
	            }); 
	            AreaRegistration.RegisterAllAreas(); 
	 
	            WebApiConfig.Register(GlobalConfiguration.Configuration); 
	            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters); 
	            RouteConfig.RegisterRoutes(RouteTable.Routes); 
	            BundleConfig.RegisterBundles(BundleTable.Bundles); 
	            BundleMobileConfig.RegisterBundles(BundleTable.Bundles); 
	        } 
	    } 
	}

Abra o arquivo *MvcMobile\Views\Shared\\_Layout.Mobile.cshtml* arquivo e adicione a seguinte marcação logo após a chamada *Html.Partial*:

	<div data-role="header" align="center">
	    @Html.ActionLink("Home", "Index", "Home")
	    @Html.ActionLink("Date", "AllDates")
	    @Html.ActionLink("Speaker", "AllSpeakers")
	    @Html.ActionLink("Tag", "AllTags")
	</div>

A seção de corpo completo tem esta aparência:

	<body>
	    <div data-role="page" data-theme="a">
	        @Html.Partial("_ViewSwitcher")
	        <div data-role="header" align="center">
	            @Html.ActionLink("Home", "Index", "Home")
	            @Html.ActionLink("Date", "AllDates")
	            @Html.ActionLink("Speaker", "AllSpeakers")
	            @Html.ActionLink("Tag", "AllTags")
	        </div>
	        <div data-role="header">
	            <h1>@ViewBag.Title</h1>
	        </div>
	        <div data-role="content">
	            @RenderSection("featured", false)
	            @RenderBody()
	        </div>
	    </div>
	</body>

Crie o aplicativo e no seu emulador de navegador móvel, vá para o modo de exibição de AllTags. Você verá o seguinte:

![Após instalar o jquery por meio do nuget.][jquery3]

<div class="dev-callout"> 
<b>Observação</b> 
<p>Você pode depurar o código específico móvel definindo a sequência de agente do usuário para o IE ou Chrome para iPhone e, em seguida, usando as ferramentas de desenvolvedor do F-12.  Se o navegador do celular não exibe os links de <strong>Início</strong>, <strong>Alto-falante</strong>, <strong>Marca</strong> e <strong>Data</strong> como botões, as referências a arquivos CSS e scripts jQuery Mobile provavelmente não estão corretas.</p> 
</div>

Além das alterações de estilo, você verá **Exibir o modo de exibição móvel** e um link que permite que você alterne do modo de exibição móvel para o modo de exibição da área de trabalho. Escolha o **Link de exibição da área de trabalho** e a exibição da área de trabalho é exibida.

<!--![Display desktop view][jquery4]-->

O modo de exibição da área de trabalho não oferece uma maneira de navegar diretamente para o modo de exibição móvel. Você corrigirá isso agora. Abra o arquivo *Views\Shared\\_Layout.cshtml*. Abaixo do elemento &lt;corpo>, adicione o seguinte código, que renderiza o elemento do alternador de exibição:

    @Html.Partial("_ViewSwitcher")

Aqui está o código completo:

	<body>
	    @Html.Partial("_ViewSwitcher")
	
	    <div id="title">
	        <h1> Conferência de MVC4 </h1>
	    </div>

		@*Itens removidos para fins de esclarecimento.*@
	</body>

Atualize o modo de exibição **AllTags** no navegador móvel. Agora você pode navegar entre os modos de exibição móveis e desktop.

![Navegue para modos de exibição móveis.][jquery5]

<div class="dev-callout">
<b>Note</b>
<p>Você pode adicionar o seguinte código ao final do Views\Shared\_ViewSwitcher.cshtml para ajudar as visualizações de depuração ao utilizar um navegador que a sequência do agente do usuário definiu para um dispositivo móvel.</p> 

<pre>
	else 
	{ 
	     @:Not Mobile/Get 
	} 
</pre>
<p>e adicionando o seguinte título ao arquivo Views\Shared\_Layout.cshtml.</p> 
<pre>
	&lt;h1>Conferência de MVC4 de Layout não móveis&lt;/h1>
</pre>
</div>

Navegue até a página AllTags em um navegador desktop. O elemento do alternador do modo de exibição não é exibido em um navegador de desktop porque ele é adicionado somente à página de layout para dispositivos móveis. Mais adiante no tutorial, você verá como é possível adicionar o elemento do alternador do modo de exibição para o modo de exibição da área de trabalho.

![Exibir a experiência do desktop.][jquery6]

<h2><a name="bkmk_Improvespeakerslist"></a> Melhorar a lista de alto-falantes</h2>

No navegador do celular e selecione o link de **Alto-falantes**. Como não há nenhum modo de exibição móvel (*AllSpeakers.Mobile.cshtml*), os alto-falantes padrão exibem (*AllSpeakers.cshtml*) é renderizam utilizando o modo de exibição de layout para dispositivos móveis (*_Layout.Mobile.cshtml*).

![Exiba a lista de alto-falantes móveis.][SpeakerList1]

Você pode desativar globalmente a exibição padrão (não móvel) de renderizar dentro de um layout móvel, definindo o RequireConsistentDisplayMode para verdadeiro no arquivo *Views\_ViewStart.cshtml*, como este:

![][SpeakerList2]

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModes.RequireConsistentDisplayMode = true;
    }
Quando *RequireConsistentDisplayMode* for definido como verdadeiro, o (*_Layout.Mobile.cshtml*) do layout móvel será usado somente para visualizações móveis. (Ou seja, o arquivo de exibição é do formulário ViewName.Mobile.cshtml.) Talvez você queira definir *RequireConsistentDisplayMode* como verdadeiro se o layout móvel não funcionar bem com as exibições não móveis. A captura de tela abaixo mostra como a página de alto-falantes processa quando *RequireConsistentDisplayMode* é definido como verdadeiro.

![][SpeakerList4]

Você pode desativar o modo de exibição consistente em um modo de exibição, definindo *RequireConsistentDisplayMode* para falso no arquivo de exibição. A marcação a seguir no arquivo *Views\Home\AllSpeakers.cshtml* define *RequireConsistentDisplayMode* como falso:

    @model IEnumerable<string>
    @{
        ViewBag.Title = "All speakers";
        DisplayModes.RequireConsistentDisplayMode = false;
    }
<h2><a name="bkmk_mobilespeakersview"></a>Criar um modo de exibição móvel de alto-falantes</h2>

Como você acabou de ver, o modo de exibição de alto-falantes está legível, mas os links são pequenos e difíceis de tocar em um dispositivo móvel. Nesta seção, você criará uma exibição de alto-falantes específica para dispositivos móveis que se parece com um aplicativo para dispositivos móveis moderno - ela exibe links grandes, fáceis de tocar e contém uma caixa de pesquisa para localizar os alto-falantes rapidamente.

1. Copie *AllSpeakers.cshtml* para *AllSpeakers.Mobile.cshtml.* Abra o arquivo *AllSpeakers.Mobile.cshtml* e remova o elemento do título &lt;h2&gt;.
2. Na tag **&lt;ul&gt;**, adicione o atributo da função de dados e defina seu valor como *listview*. Como outros atributos de *dados**, a *função de dados="listview"* torna mais fácil de tocar os itens de lista grande. Esta é a aparência de marcação concluída:

	    @model IEnumerable<string>
	    @{
	        ViewBag.Title = "Todos os alto-falantes";
	    }
	    <ul data-role="listview">
	        @foreach(var speaker in Model) {
	            <li>@Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker })</li>
	        }
	    </ul>

3.	Atualize o navegador móvel. O modo de exibição atualizado tem esta aparência:

	![][MobileSpeakersView1]

4.	Na guia **&lt;ul&gt;**, adicione o atributo da função de dados e defina seu valor como verdadeiro. O código a seguir mostra a marcação ul.

		<ul data-role="listview" data-filter="true">

A imagem a seguir mostra a caixa de filtro de pesquisa na parte superior da página que resulta do atributo de filtro de dados.

![][MobileSpeakersView2]

Ao digitar cada letra na caixa de pesquisa, o jQuery Mobile filtra a lista exibida conforme mostrado na imagem abaixo.

![][MobileSpeakersView3]

<h2><a name="bkmk_improvetags"></a> Melhorar a lista de guias</h2>

Como o modo de exibição padrão de alto-falantes, o modo de exibição das Marcas é legível, mas os links são pequenos e difíceis de tocar em um dispositivo móvel. Nesta seção, você corrigirá o modo de exibição de marcas da mesma maneira que você fixa o modo de exibição de alto-falantes.

1. Renomeie o arquivo *Views\Home\AllTags.Mobile.cshtml.hide* para o *Views\Home\AllTags.Mobile.cshtml*. Abra o arquivo renomeado e remova o elemento **&lt;h2&gt;**.

2. Adicione os atributos de função dados e filtro de dados para a marca **&lt;ul&gt;**, conforme mostrado aqui:

		<ul data-role="listview" data-filter="true">
A imagem a seguir mostra a página de marcas filtrando a letra J.

![][TagsList1]

<h2><a name="bkmk_improvedates"></a> Melhorar a lista de datas</h2>

Você pode melhorar a exibição de datas como você melhorou os modos de exibição de **Alto-falantes** e **Marcas**, para que seja mais fácil de usar em um dispositivo móvel.

1. Copie o arquivo *Views\Home\AllDates.Mobile.cshtml* para *Views\Home\AllDates.Mobile.cshtml*.
2. Abra o novo arquivo e remova o elemento **&lt;h2&gt;**.
3. Adicione *função de dados="listview"* para a marca &lt;ul&gt;, como esta:

		<ul data-role="listview">

A imagem abaixo mostra o que a página **Data** parece com o atributo de função de dados no local.

![][DatesList1]

Substitua o conteúdo do arquivo *Views\Home\AllDates.Mobile.cshtml* pelo código a seguir:

    @model IEnumerable<DateTime>
    @{
        ViewBag.Title = "All dates";
        DateTime lastDay = default(DateTime);
    }
    <ul data-role="listview">
        @foreach(var date in Model) {
            if (date.Date != lastDay) {
                lastDay = date.Date;
                <li data-role="list-divider">@date.Date.ToString("ddd, MMM dd")</li>
            }
            <li>@Html.ActionLink(date.ToString("h:mm tt"), "SessionsByDate", new { date })</li>
        }
    </ul>
Este código agrupa todas as sessões por dias. Ele cria um divisor de lista para cada novo dia, e lista todas as sessões para cada dia em uma linha divisória. Aqui está o que se parece quando esse código é executado:

![][DatesList2]

<h2><a name="bkmk_improvesessionstable"></a> Melhorar o modo de exibição da tabela de sessões</h2>

Nesta seção, você criará uma exibição móvel específica de sessões. As alterações feitas serão mais extensivas do que em outros modos que criamos.

No navegador móvel, toque o botão **Alto-falante** e digite Sc na caixa de pesquisa.

![][SessionView1]

Toque o link **Scott Hanselman**.

![][SessionView2]

Como você pode ver, é difícil de ler a exibição em um navegador móvel. A coluna Data é difícil de ler e a coluna Marcas está fora do modo de exibição. Para corrigir isso, copie Views\*Home\SessionsTable.cshtml* para *Views\Home\SessionsTable.Mobile.cshtml* e, em seguida, substitua o conteúdo do arquivo com o seguinte código:

    @using MvcMobile.Models
    @model IEnumerable<Session>
	
    <ul data-role="listview">
        @foreach(var session in Model) {
            <li>
                <a href="@Url.Action("SessionByCode", new { session.Code })">
                    <h3>@session.Title</h3>
                    <p><strong>@string.Join(", ", session.Speakers)</strong></p>
                    <p>@session.DateText</p>
                </a>
            </li>
        }
    </ul>
O código remove as colunas sala e marcas e formata o título, alto-falante e a data verticalmente, de modo que toda esta informação seja legível em um navegador móvel. A imagem abaixo reflete as alterações de código.

![][SessionView3]

<h2><a name="bkmk_improvesessionbycode"></a> Melhorar o modo de exibição SessionByCode</h2>

Finalmente, você criará uma exibição móvel específica da exibição **SessionByCode**. No navegador móvel, toque o botão **Alto-falante** e digite Sc na caixa de pesquisa.

![][SessionByCode1]

Toque o link **Scott Hanselman**. As sessões do Scott Hanselman são exibidas.

![][SessionByCode2]

Escolha o link **Uma visão geral do MS Web Stack of Love**.

![][SessionByCode3]

A exibição da área de trabalho padrão é boa, mas você pode aprimorá-la.

Copie *Views\Home\SessionByCode.cshtml* para *Views\Home\SessionByCode.Mobile.cshtml* e substitua o conteúdo do arquivo *Views\Home\SessionByCode.Mobile.cshtml* pela seguinte marcação:


    @model MvcMobile.Models.Session
	
    @{
        ViewBag.Title = "Detalhes da sessão";
    }

   <h2>@Model.Title</h2>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>
	
    <ul data-role="listview" data-inset="true">
        <li data-role="list-divider">Speakers</li>
        @foreach (var speaker in Model.Speakers) {
            <li>@Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker })</li>
        }
    </ul>
	
    <p>@Model.Description</p>
    <h4>Code: @Model.Code</h4>

    <ul data-role="listview" data-inset="true">
        <li data-role="list-divider">Tags</li>
        @foreach (var tag in Model.Tags) {
            <li>@Html.ActionLink(tag, "SessionsByTag", new { tag })</li>
        }
    </ul>
A nova marcação usa o atributo de **função de dados** para melhorar o layout da visualização.

Atualize o navegador móvel. A imagem a seguir reflete as alterações de código que você acabou de criar:

![][SessionByCode4]

<h2><a name="bkmk_deployapplciation"></a> Implantar o aplicativo no site do Azure</h2>

1. No Visual Studio, clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

	![Publicar no menu de contexto do projeto][PublishVSSolution]

	O assistente de **Publicar Web** é aberto.
2. Na guia **Perfil** do assistente de **Publicar Web**, clique em **Importar**.

	![Importar configurações de publicação][ImportPublishSettings]

	A caixa de diálogo **Importar Perfil de Publicação** é aberta.

3. Se você ainda não adicionou a assinatura do Azure no Visual Studio, execute as etapas a seguir. Nestas etapas você adiciona sua assinatura de forma que a lista suspensa em **Importar de um Site do Azure** incluirá o seu site.
	1. Na caixa de diálogo **Perfil de Publicação de Importação**, clique em **Adicionar assinatura do Azure**. 

	![adicionar ass do win az](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzAddWAsub.png)

	1. Na caixa de diálogo **Importar Assinaturas do Azure**, clique em **Baixar arquivo de assinatura**.
    
	![baixar a ass](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDownLoad.png)
    
	1. Na janela do navegador, salve o arquivo *.publishsettings*.
    
	![download o arquivo pub](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDown2.png)
    
	[WACOM.INCLUDE [publishsettingsfilewarningchunk](../includes/publishsettingsfilewarningchunk.md)]
</br>
    
	1. Na caixa de diálogo **Importar Assinaturas do Azure**, clique em **Procurar** e navegue até o arquivo *.publishsettings*.
    
	![baixar a ass](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDownLoad.png)
    
	1. Clique em **Importar**.
    
	![import](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzImp.png)

	7. Na caixa de diálogo **Importar Perfil de Publicação**, selecione **Importar de um site do Azure**, selecione seu site na lista suspensa e clique em **OK**.

	![Importar Perfil de Publicação][ImportPublishProfile]


	8. Na guia **Conexão**, clique em **Validar Conexão** para garantir que as configurações estão corretas.

	![Validar a conexão][ValidateConnection]

	9. Quando a conexão tiver sido validada, uma marca de seleção verde será mostrada ao lado do botão **Validar Conexão**.
	![ícone de conexão bem-sucedida e o botão Avançar na guia Conexão][firsdeploy007]

	10. Você pode aceitar todas as configurações padrão nesta página.  Você está implantando uma configuração de compilação da versão e não precisa excluir arquivos no servidor de destino. A entrada **UsersContext (DefaultConnection)** em **Bancos de Dados** é proveniente da classe *UsersContext:DbContext* que usa a cadeia de caracteres DefaultConnection. 
Clique em **Próximo**.

	![ícone de conexão bem-sucedida e o botão Avançar na guia Conexão][rxPWS]

	12. Na guia **Visualização**, clique em **Iniciar Visualização**.
A guia exibe uma lista dos arquivos que serão copiados no servidor. A exibição da visualização não é obrigatória para publicar o aplicativo, mas é uma função reconhecidamente útil. Nesse caso, você não precisa fazer nada com a lista de arquivos exibida. Na próxima vez que você publicar, apenas os arquivos que foram alterados estarão na lista de visualização.

	![Botão StartPreview na guia Visualização][firsdeploy009]

	12. Clique em **Publicar**.

	O Visual Studio inicia o processo de cópia dos arquivos no servidor do Azure. A janela **Saída** mostra quais ações de implantação foram executadas e os relatórios da conclusão com êxito da implantação.

	15. O navegador padrão é aberto automaticamente para a URL do site implantado. O aplicativo que você criou agora está em execução na nuvem.

	![][DeployApplication10]

Você pode testar seu site da web ao vivo usando o emulador do telefone, navegando até a URL do site no navegador móvel.

<!-- Internal Links -->
[Criar um site do Azure]: #bkmk_createaccount
[Configurar o projeto inicial]: #bbkmk_setupstarterproject
[Substitua os modos de exibição, Layouts e exibições parciais]: #bkmk_overrideviews
[Usar o jQuery Mobile para definir a interface de navegador móvel]: #bkmk_usejquerymobile
[Melhorar a lista de alto-falantes]: #bkmk_Improvespeakerslis
[Criar um modo de exibição móvel de alto-falantes]: #bkmk_mobilespeakersview
[Melhorar a lista de guias]: #bkmk_improvetags
[Melhorar a lista de datas]: #bkmk_improvedates
[Melhorar o modo de exibição da tabela de sessões]: #bkmk_improvesessionstable
[Melhorar o modo de exibição SessionByCode]: #bkmk_improvesessionbycode
[Implantar o aplicativo no site do Azure]: #bkmk_deployapplciation

<!-- Images -->
[CreateWebSite1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_1.png
[CreateWebSite2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_2.png
[CreateWebSite3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_3.png
[CreateWebSite4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_4.png
[AppMainPage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/FinishedAPPMainScreen.png
[PropertiesPopup]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/propertiespopup.png
[BrowseByTagWithCallout]:./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/BrowseByTagWithCallout.png
[ASPNetPage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ASPNetPage.png
[Overrideviews1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p2m_layouttags_mobile_thumb.png
[Overrideviews2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p2_layoutTagsDesktop_thumb.png
[jquery1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-mobile-open-packagmanager.png
[jquery2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-mobile-open-install-jquey.png
[jquery3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_afternuget_thumb.png
[jquery4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_desktopviewwithmobilelink_thumb.png
[jquery5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_desktopviewwithmobilelink_thumb.png
[jquery6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p3_desktopBrowser_thumb.png
[SpeakerList1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_speakersdesktop_thumb.png
[SpeakerList2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_speakersconsistent_thumb.png
[SpeakerList3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_updatedspeakerview1_thumb.png
[SpeakerList4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_thumb.png
[MobileSpeakersView1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_updatedspeakerview1_thumb.png
[MobileSpeakersView2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_thumb.png
[MobileSpeakersView3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb.png
[TagsList1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_tags_j_thumb.png
[DatesList1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_dates1_thumb.png
[DatesList2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_dates2_thumb.png
[SessionView1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb_1.png
[SessionView2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_scottha_thumb.png
[SessionView3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_sessionsbyscottha_thumb.png
[SessionByCode1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb_2.png
[SessionByCode2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_scottha_thumb.png
[SessionByCode3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_love_thumb.png
[SessionByCode4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_love2_thumb.png
[DeployApplication1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_5.png
[DeployApplication2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_6.png
[DeployApplication3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_7.png
[DeployApplication4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_8.png
[DeployApplication5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_9.png
[DeployApplication6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_10.png
[DeployApplication7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_12.png
[DeployApplication8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_13.png
[DeployApplication9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_14.png
[DeployApplication10]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_15.png

<!-- External Links -->
[MVC4DeveloperPreview]: http://www.asp.net/mvc/mvc4
[WebDeployUpdate]: http://www.windowsazure.com/pt-br/develop/net/
[Visual Studio Express 2012]: http://www.microsoft.com/visualstudio/eng/products/visual-studio-express-products
[MVC4StarterProject]: http://go.microsoft.com/fwlink/?LinkId=228307
[FinishedProject]: http://go.microsoft.com/fwlink/?LinkId=228306
[Win7PhoneEmulator]: http://msdn.microsoft.com/pt-br/library/ff402530(VS.92).aspx
[OperaMobileEmulator]: http://www.opera.com/developer/tools/mobile/
[AppleSafari]: http://www.apple.com/safari/download/
[HowToSafari]: http://www.davidalison.com/2008/05/how-to-let-safari-pretend-its-ie.html
[FireFox]: http://www.bing.com/search?q=firefox+download
[FireFoxUserAgentSwitcher]: https://addons.mozilla.org/pt-br/firefox/addon/user-agent-switcher/
[CSSMediaQuries]: http://www.w3.org/TR/css3-mediaqueries/
[jquerydocs]: http://jquerymobile.com/demos/1.0b3/#/demos/1.0b3/docs/about/intro.html
[setuseragent]: http://www.howtogeek.com/113439/how-to-change-your-browsers-user-agent-without-installing-any-extensions/
[managementportal]: https://manage.windowsazure.com
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk20NetVS12.png
[Add XSRF Protection]: #xsrf
[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ImportPublishSettings.png
[ImportPublishProfile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ImportPublishProfile.png
[PublishVSSolution]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/PublishVSSolution.png
[ValidateConnection]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ValidateConnection.png
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk20NetVS12.png
[firsdeploy007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/dntutmobile-deploy1-publish-005.png
[firsdeploy009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/dntutmobile-deploy1-publish-007.png
[rxPWS]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rxPWS.png


