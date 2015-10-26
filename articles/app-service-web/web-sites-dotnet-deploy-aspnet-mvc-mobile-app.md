<properties 
	pageTitle="Implantar um aplicativo Web móvel ASP.NET MVC 5 no Serviço de Aplicativo do Azure" 
	description="Um tutorial que ensina como implantar um aplicativo Web no Serviço de Aplicativo do Azure usando recursos móveis no aplicativo Web ASP.NET MVC 5." 
	services="app-service" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/16/2015" 
	ms.author="cephalin;riande"/>


# Implantar um aplicativo Web móvel ASP.NET MVC 5 no Serviço de Aplicativo do Azure

Este tutorial ensinará você o básico sobre como compilar um aplicativo Web do ASP.NET MVC 5 adaptado para dispositivos móveis e implantá-lo no Serviço de Aplicativo do Azure. Para este Tutorial, você precisará do [Visual Studio Express 2013 para Web][Visual Studio Express 2013] ou do Visual Studio Professional Edition, se já o possuir. Você pode usar o [Visual Studio de 2015] mas as capturas de tela serão diferentes e você deverá usar os modelos do ASP.NET 4. x.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## O que você vai construir

Neste tutorial, você adicionará recursos móveis ao aplicativo de listagem de conferência simples que é fornecido no [projeto inicial][StarterProject]. A captura de tela a seguir mostra as sessões do ASP.NET no aplicativo concluído, como visto no emulador do navegador nas ferramentas de desenvolvedor do Internet Explorer 11 F12.

![][FixedSessionsByTag]

É possível utilizar as ferramentas de desenvolvedor do Internet Explorer 11 F12 e a [ferramenta Fiddler][Fiddler] para ajudar a depurar o seu aplicativo.

## Qualificações que você aprenderá

Eis o que você vai aprender:

-	Como usar o Visual Studio 2013 para publicar seu aplicativo Web diretamente em um aplicativo Web no Serviço de Aplicativo do Azure.
-   Como os modelos do ASP.NET MVC 5 utilizam o framework de CSS Bootstrap para melhorar a exibição em dispositivos móveis
-   Como criar modos de exibição para dispositivos móveis voltados para navegadores de dispositivos móveis específicos, tais como iPhone e Android
-   Como criar modos de exibição dinâmicos (que respondam a navegadores diferentes em todos os dispositivos)

## Configurar o ambiente de desenvolvimento

Configure o ambiente de desenvolvimento instalando o SDK do Azure para .NET 2.5.1 ou posterior.

1. Para instalar o SDK do Azure para .NET, clique no link abaixo. Se você ainda não tiver o Visual Studio 2013 instalado, ele será instalado pelo link. Este tutorial requer o Visual Studio 2013. [SDK do Azure para o Visual Studio 2013][AzureSDKVs2013]
1. Na janela do Web Platform Installer, clique em **Instalar** e prossiga com a instalação.

Também será necessário um emulador do navegador móvel. Qualquer uma das opções a seguir funcionará:

-   Emulador do navegador nas [ferramentas de desenvolvedor do Internet Explorer 11 F12][EmulatorIE11] (usado em todas as capturas de tela do navegador móvel). Ele possui predefinições de cadeia de caracteres de agente de usuário para Windows Phone 8, Windows Phone 7 e Apple iPad.
-	Emulador de navegador nas [DevTools do Google Chrome][EmulatorChrome]. Contém predefinições para vários dispositivos Android, e também para Apple iPhone, Apple iPad e Amazon Kindle Fire. Ele também emula eventos de toque.
-   [Emulador do Opera para dispositivos móveis][EmulatorOpera]

Os projetos do Visual Studio com o código-fonte em C# estão disponíveis para acompanhar este tópico:

-   [Download do projeto inicial][StarterProject]
-   [Download do projeto concluído][CompletedProject]

##<a name="bkmk_DeployStarterProject"></a>Implantar o projeto inicial em um aplicativo Web do Azure

1.	Baixe o [projeto inicial][StarterProject] do aplicativo de listagem de conferência.

2. 	Em seguida, no Windows Explorer, clique com o botão direito no arquivo Mvc5Mobile.zip e escolha *Propriedades*.

3. 	Na caixa de diálogo **Propriedades de Mvc5Mobile.zip**, escolha o botão **Desbloquear**. (Desbloquear impede um aviso de segurança que ocorre quando você tenta usar um arquivo *.zip* que você baixou da Web).

4.	Clique com o botão direito do mouse no arquivo *Mvc5Mobile.zip* e selecione **Extrair Tudo** para descompactar o arquivo.

5. 	No Visual Studio, abra o arquivo *Mvc5Mobile.sln*.

6.  No Gerenciador de Soluções, clique com o botão direito no projeto e clique em **Publicar**.

	![][DeployClickPublish]

7.	Em Publicar Web, clique em **Aplicativos Web do Microsoft Azure**.

	![][DeployClickWebSites]

8.	Clique em **Entrar**.

	![][DeploySignIn]

9.	Siga os prompts para fazer logon na sua conta do Azure.

11. A caixa de diálogo Selecionar aplicativo Web existente deve mostrar agora que você entrou. Clique em **Novo**.

	![][DeployNewWebsite]

12. No campo **Nome do aplicativo Web**, especifique um prefixo único para o nome do aplicativo. O nome totalmente qualificado do aplicativo Web será *&lt;prefixo>*.azurewebsites.net. Além disso, configure os campos **Plano do Serviço de Aplicativo**, **Grupo de recursos** e **Região**. Em seguida, clique em **Criar**.

	![][DeploySiteSettings]

13.	A caixa de diálogo Publicar Web será preenchida com as configurações para seu novo aplicativo Web. Clique em **Publicar**.

	![][DeployPublishSite]

	Depois que o Visual Studio terminar de publicar o projeto inicial para o aplicativo Web do Azure, o navegador da área de trabalho se abre para exibir o aplicativo Web online.

14.	Inicie o emulador de navegador móvel, copie a URL para o aplicativo de conferência (*<prefix>*.azurewebsites.net) do emulador e depois clique no botão da parte superior direita e selecione **Procurar por marca**. Se você estiver usando o Internet Explorer 11 como navegador padrão, basta digitar `F12`, depois `Ctrl+8` e, em seguida, alterar o perfil do navegador para **Windows Phone**. A imagem abaixo mostra o modo de exibição *AllTags* em modo retrato (ao escolher **Procurar por marca**).

	![][AllTags]

>[AZURE.NOTE]Enquanto depura o aplicativo MVC 5 no Visual Studio, você pode publicar seu aplicativo Web no Azure novamente para verificar o aplicativo Web online, diretamente no navegador móvel ou um emulador de navegador.

A exibição é muito legível em um dispositivo móvel. Você também já pode ver alguns dos efeitos visuais aplicados pelo framework de Bootstrap CSS. Clique no link **ASP.NET**.

![][SessionsByTagASP.NET]

O modo de exibição de marca do ASP.NET é ajustado à tela, o que é feito automaticamente pelo Bootstrap para você. No entanto, é possível aprimorar esse modo de exibição para melhor se adequar ao navegador móvel. Por exemplo, a coluna **Data** é muito difícil de ler. Mais adiante no tutorial, você vai alterar o modo de exibição *AllTags* para torná-lo acessível a dispositivos móveis.

##<a name="bkmk_bootstrap"></a> Framework de CSS Bootstrap

Uma novidade do modelo MVC 5 é o suporte interno ao Bootstrap. Você já viu como ele aprimora imediatamente os diferentes modos de exibição em seu aplicativo. Por exemplo, a barra de navegação na parte superior é recolhida automaticamente quando a largura do navegador é menor. No navegador da área de trabalho, tente redimensionar a janela e veja como a barra de navegação muda de aparência e estilo. O responsável é o design Web dinâmico interno do Bootstrap.

Para ver como ficaria a aparência do aplicativo Web sem o Bootstrap, abra *App\\_Start\\\BundleConfig.cs* e transforme em comentário as linhas que contêm *bootstrap.js* e *bootstrap.css*. O código a seguir mostra as duas últimas instruções do método `RegisterBundles` após a alteração:

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

Pressione `Ctrl+F5` para executar o aplicativo.

Observe que a barra de navegação recolhível é agora apenas uma lista simples e desordenada. Clique em **Procurar por marca** novamente, clique em **ASP.NET**. No modo de exibição do emulador do navegador móvel, é possível ver que ele já não se ajusta à tela, e você precisa rolar para os lados para conseguir vero lado direito da tabela.

![][SessionsByTagASP.NETNoBootstrap]

Desfaça as alterações e atualize o navegador móvel para verificar se a exibição acessível para dispositivo móvel foi restaurada.

O Bootstrap não é específico para o ASP.NET MVC 5, e você pode aproveitar esses recursos em qualquer aplicativo Web. Ele é, porém, um recurso interno do modelo de projeto MVC 5 do ASP.NET, de forma que o seu aplicativo Web MVC 5 pode, por padrão, aproveitar o Bootstrap.

Para saber mais sobre o Bootstrap, visite o site [Bootstrap][BootstrapSite].

Na próxima seção, você verá como fornecer modos de exibição específicos para navegadores móveis.

##<a name="bkmk_overrideviews"></a>Substituir os modos de exibição, os layouts e as exibições parciais

Você pode substituir qualquer modo de exibição (inclusive layouts e modos de exibição parciais) para navegadores de dispositivos móveis em geral, para um navegador móvel individual ou para qualquer navegador específico. Para fornecer uma exibição específica para dispositivos móveis, você pode copiar um arquivo de modo de exibição e adicionar *.Mobile* ao nome do arquivo. Por exemplo, para criar uma exibição *Index* de móvel, você pode copiar *Views\\Home\\Index.cshtml* para *Views\\Home\\Index.Mobile.cshtml*.

Nesta seção, você criará um arquivo de layout específico para dispositivos móveis.

Para começar, copie *Views\\Shared\\_Layout.cshtml* para *Views\\Shared\\_Layout.Mobile.cshtml*. Abra *\_Layout.Mobile.cshtml* e altere o título de **Aplicativo MVC5** para **Aplicativo MVC5(Móvel)**.

Em cada chamada `Html.ActionLink` à barra de navegação, remova "Procurar por" em cada link *ActionLink*. O código a seguir mostra a marca `<ul class="nav navbar-nav">` completa do arquivo de layout para dispositivos móveis.

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

Copie o arquivo *Views\\Home\\AllTags.cshtml* para *Views\\Home\\AllTags.Mobile.cshtml*. Abra o novo arquivo e altere o elemento `<h2>` das "Marcas" para "marcas (M)":

    <h2>Tags (M)</h2>

Navegue até a página de marcas utilizando um navegador de desktop e o emulador do navegador móvel. O emulador do navegador móvel mostra as duas alterações que você fez (o título de *\\_Layout.Mobile.cshtml* e de *AllTags.Mobile.cshtml*).

![][AllTagsMobile_LayoutMobile]

Por outro lado, a exibição do navegador da área de trabalho não foi alterada (com títulos de *\_Layout.cshtml* e *AllTags.cshtml*).

![][AllTagsMobile_LayoutMobileDesktop]

##<a name="bkmk_browserviews"></a> Criar modos de exibição para um navegador específico

Além dos modos de exibição específicos para navegadores móveis e de desktop, você pode criar modos de exibição para um único navegador. Por exemplo, é possível criar modos de exibição específicos para o navegador do iPhone ou do Android. Nesta seção, você criará um layout para o navegador do iPhone e uma versão para iPhone do modo de exibição *AllTags*.

Abra o arquivo *Global.asax* e adicione o seguinte código à parte inferior do método `Application_Start`.

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

Este código define um novo modo de exibição denominado “iPhone”, que será comparado a todas as solicitações de entrada. Se a solicitação de entrada corresponder à condição que você definiu (isto é, se o agente do usuário contiver a cadeia de caracteres “iPhone”), o ASP.NET MVC vai procurar por modos de exibição cujo nome contenha o sufixo “iPhone”.

>[AZURE.NOTE]Ao adicionar modos de exibição específicos para determinado navegador móvel, como para iPhone e Android, configure o primeiro argumento como `0` (inserido no topo da lista) para garantir que o modo específico para um navegador tenha precedência sobre o modelo para dispositivos móveis (*.Mobile.cshtml). Se o modelo para dispositivos móveis estiver no topo da lista, ele será selecionado no lugar do modo de exibição que você pretendia usar (a primeira correspondência predomina, e o modelo para dispositivos móveis corresponde a todos os navegadores móveis).

No código, clique com botão direito em `DefaultDisplayMode`, escolha **Resolver** e, em seguida, escolha `using System.Web.WebPages;`. Isso inclui uma referência ao namespace `System.Web.WebPages`, que é onde os tipos de `DisplayModeProvider` e `DefaultDisplayMode` são definidos.

![][ResolveDefaultDisplayMode]

Como alternativa, basta adicionar manualmente a seguinte linha na seção `using` do arquivo.

    using System.Web.WebPages;

Salve as alterações. Copie o arquivo *Views\\Shared\\_Layout.Mobile.cshtml* para *Views\\Shared\\_Layout.iPhone.cshtml*. Abra o novo arquivo e altere o título de `MVC5 Application (Mobile)` para `MVC5 Application (iPhone)`.

Copie o arquivo *Views\\Home\\AllTags.Mobile.cshtml* para *Views\\Home\\AllTags.iPhone.cshtml*. Abra o novo arquivo, altere o elemento `<h2>` de "Tags (M)" para "Tags (iPhone)".

Execute o aplicativo. Execute o emulador de navegador móvel com o agente do usuário definido como “iPhone” e navegue até o modo de exibição *AllTags*. Se você estiver usando o emulador nas ferramentas de desenvolvedor do Internet Explorer 11 F12, configure a emulação para fazer o seguinte:

-   Perfil do navegador = **Windows Phone**
-   Cadeia de caracteres de agente de usuário = **Personalizado**
-   Cadeia de caracteres personalizada = **Apple-iPhone5C1/1001,525**

A captura de tela a seguir mostra o modo de exibição *AllTags* renderizado no emulador das ferramentas de desenvolvedor do Internet Explorer 11 F12 com a cadeia de caracteres de agente do usuário personalizada (trata-se de uma cadeia de caracteres de agente do usuário do iPhone 5C).

![][AllTagsIPhone_LayoutIPhone]

No navegador móvel, selecione o link **Alto-falantes**. Porque não há um modo de exibição móvel (*AllSpeakers.Mobile.cshtml*), a exibição padrão de alto-falantes (*AllSpeakers.cshtml*) é renderizada utilizando o modo de exibição de layout para dispositivos móveis (*\_Layout.Mobile.cshtml*). Como mostrado abaixo, o título **MVC5 Application (Móvel)** é definido em *\_Layout.Mobile.cshtml*.

![][AllSpeakers_LayoutMobile]

Você pode desativar globalmente a renderização do modo de exibição padrão (não móvel) dentro de um layout móvel ao configurar `RequireConsistentDisplayMode` como `true` no arquivo *Views\\_ViewStart.cshtml*, desta forma:

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

Quando o `RequireConsistentDisplayMode` estiver definido como `true`, o layout móvel (*\_Layout.Mobile.cshtml*) será usado apenas para modos de exibição móveis (ou seja, quando o arquivo de exibição tiver o formato ****ViewName**.Mobile.cshtml*). Talvez você queira definir `RequireConsistentDisplayMode` como `true` se o layout voltado para dispositivos móveis não funcionar bem com seus modos de exibição não voltados para dispositivos móveis. A captura de tela abaixo mostra como a página *Alto-falantes* é renderizada quando `RequireConsistentDisplayMode` é definido como `true` (sem a cadeia de caracteres "(Móvel)" na barra de navegação na parte superior).

![][AllSpeakers_LayoutMobileOverridden]

Você pode desativar o modo de exibição consistente em um modo específico definindo o `RequireConsistentDisplayMode` como `false` no arquivo do modo de exibição. A marcação a seguir no arquivo *Views\\Home\\AllSpeakers.cshtml* define o `RequireConsistentDisplayMode` como `false`:

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

Nesta seção, você viu como criar layouts e exibições móveis e como criar layouts e exibições para dispositivos específicos, como o iPhone. No entanto, a principal vantagem do framework de Bootstrap CSS é o layout responsivo, o que significa que uma única folha de estilos pode ser aplicada em área de trabalho, telefone e navegadores de tablet para criar uma aparência consistente. Na próxima seção, você verá como aproveitar o Bootstrap para criar modos de exibição para dispositivos móveis.

##<a name="bkmk_Improvespeakerslist"></a> Melhorar a lista de alto-falantes

Como você acabou de ver, o modo de exibição *Alto-falantes* é legível, mas os links são pequenos e difíceis de tocar em um dispositivo móvel. Nesta seção, você tornará o modo de exibição *AllSpeakers* acessível para dispositivos móveis, que exibe links grandes, fáceis de tocar e contém uma caixa de pesquisa para localizar alto-falantes rapidamente.

É possível utilizar o estilo do [grupo de listas vinculadas][] do Bootstrap para aprimorar o modo de exibição de *Speakers*. Em *Views\\Home\\AllSpeakers.cshtml*, substitua o conteúdo do arquivo Razor com o código abaixo.

     @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker }, new { @class = "list-group-item" })
        }
    </div>

O atributo `class="list-group"` na marca `<div>` aplica o estilo da lista do Bootstrap, e o atributo `class="input-group-item"` aplica o estilo do item da lista do Bootstrap a todos os links.

Atualize o navegador móvel. O modo de exibição atualizado tem esta aparência:

![][AllSpeakersFixed]

O estilo do [grupo de listas vinculadas][] do Bootstrap torna clicável a caixa inteira de todos os links, proporcionando uma experiência de usuário muito melhor. Alterne para o modo de exibição de desktop e observe a consistência na aparência e no estilo.

![][AllSpeakersFixedDesktop]

Embora o modo de exibição do navegador móvel tenha sido aprimorado, é difícil navegar pela longa lista de alto-falantes. O Bootstrap não oferece uma funcionalidade de filtro de pesquisa pronta para uso, mas você pode adicionar uma utilizando poucas linhas de código. Primeiro, adicione uma caixa de pesquisa à exibição, em seguida, vincule ao código do JavaScript para a função de filtro. Em *Views\\Home\\AllSpeakers.cshtml*, adicione uma marca <form> logo após a marca <h2>, como mostrado abaixo:

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <form class="input-group">
        <span class="input-group-addon"><span class="glyphicon glyphicon-search"></span></span>
        <input type="text" class="form-control" placeholder="Search speaker">
    </form>
    <br />
    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class = "list-group-item" })
        }
    </div>

Observe que os estilos Bootstrap são aplicados às marcas `<form>` e `<input>`. O elemento `<span>` adiciona um ícone [glyphicon][] do Bootstrap à caixa de pesquisa.

Na pasta *Scripts*, adicione um Arquivo JavaScript chamado *filter.js*. Abra o arquivo e cole o seguinte código nele:

    $(function () {

        // reset the search form when the page loads
        $("form").each(function () {
            this.reset();
        });

        // wire up the events to the <input> element for search/filter
        $("input").bind("keyup change", function () {
            var searchtxt = this.value.toLowerCase();
            var items = $(".list-group-item");

            // show all speakers that begin with the typed text and hide others
            for (var i = 0; i < items.length; i++) {
                var val = items[i].text.toLowerCase();
                val = val.substring(0, searchtxt.length);
                if (val == searchtxt) {
                    $(items[i]).show();
                }
                else {
                    $(items[i]).hide();
                }
            }
        });
    });

Também será preciso incluir o filter.js em seus pacotes registrados. Abra *App\_Start\\BundleConfig.cs* e altere os primeiros pacotes. Altere a primeira instrução `bundles.Add` (para o pacote **jquery**) para incluir *Scripts\\filter.js*, da seguinte maneira:

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

O pacote **jquery** já está renderizado pelo modo de exibição padrão *\_Layout*. Mais tarde, você pode utilizar o mesmo código JavaScript para aplicar a funcionalidade de filtro a outros modos de exibição de lista.

Atualize o navegador móvel e vá para o modo de exibição *AllSpeakers*. Na caixa de pesquisa, digite “sc”. Agora, a lista de alto-falantes deve ser filtrada de acordo com a cadeia de caracteres de busca.

![][AllSpeakersFixedSearchBySC]

##<a name="bkmk_improvetags"></a> Melhorar a lista de marcas

Como acontece com o modo de exibição *Alto-falantes*, o modo de exibição *Marcas* é legível, mas os links são pequenos e difíceis de tocar em um dispositivo móvel. É possível consertar o modo de exibição de *Marcas* da mesma forma como você consertou o modo de exibição *Alto-falantes*, basta usar as alterações de código descritas anteriormente, com a seguinte sintaxe de método `Html.ActionLink` em *Views\\Home\\AllTags.cshtml*:

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

O navegador de desktop atualizado terá a seguinte aparência:

![][AllTagsFixedDesktop]

E o navegador móvel atualizado terá a seguinte aparência:

![][AllTagsFixed]

>[AZURE.NOTE]Se você perceber que o formato de lista original ainda permanece no navegador móvel e ficar imaginando o que aconteceu com o seu belo estilo do Bootstrap, isso é consequência de alguma alteração indesejada feita durante o processo para criar modos de exibição específicos para dispositivos móveis. No entanto, agora que você está usando o Framework de CSS Bootstrap para criar um design Web dinâmico, vá em frente e exclua os modos de exibição e os layouts específicos para dispositivos móveis. Assim que tiver feito isso, o navegador móvel atualizado vai exibir o estilo do Bootstrap.

##<a name="bkmk_improvedates"></a> Melhorar a lista de datas

É possível melhorar o modo de exibição de *Datas* da mesma forma como você melhorou o modo de exibição de*Alto-falantes* e *Marcas*, basta usar as alterações de código descritas anteriormente, com a seguinte sintaxe de método `Html.ActionLink` em *Views\\Home\\AllDates.cshtml*:

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

A exibição atualizada do navegador móvel será como esta:

![][AllDatesFixed]

Você pode aprimorar ainda mais o modo de exibição de *Datas* organizando os valores data-hora por data. Isso pode ser feito com o estilo de [painéis][] do Bootstrap. Substitua o conteúdo do arquivo *Views\\Home\\AllDates.cshtml* pelo código a seguir:

    @model IEnumerable<DateTime>

    @{
        ViewBag.Title = "All Dates";
    }

    <h2>Dates</h2>

    @foreach (var dategroup in Model.GroupBy(x=>x.Date))
    {
        <div class="panel panel-primary">
            <div class="panel-heading">
                @dategroup.Key.ToString("ddd, MMM dd")
            </div>
            <div class="panel-body list-group">
                @foreach (var date in dategroup)
                {
                    @Html.ActionLink(date.ToString("h:mm tt"), 
                                     "SessionsByDate", 
                                     new { date }, 
                                     new { @class = "list-group-item" })
                }
            </div>
        </div>
    }

Esse código cria uma marca `<div class="panel panel-primary">` separada para cada data da lista e usa o [grupo de listas vinculadas][] para os respectivos links, como antes. Esta é a aparência do navegador móvel quando esse código é executado:

![][AllDatesFixed2]

Alterne para o navegador de desktop. Novamente, repare na consistência de aparência.

![][AllDatesFixed2Desktop]

##<a name="bkmk_improvesessionstable"></a> Melhorar o modo de exibição da tabela de sessões

Nesta seção, você tornará o modo de exibição *SessionsTable* mais acessível a dispositivos móveis. Esta mudança é mais extensa que as anteriores.

No navegador móvel, toque o botão **Marca**, depois insira `asp` na caixa de pesquisa.

![][AllTagsFixedSearchByASP]

Toque o link **ASP.NET**.

![][SessionsTableTagASP.NET]

Como você pode ver, a exibição está formatada como uma tabela, que foi desenhada para ser exibida em um navegador de desktop. No entanto, é um pouco difícil de ler em um navegador móvel. Para corrigir isso, abra *Views\\Home\\SessionsTable.cshtml* e, em seguida, substitua o conteúdo do arquivo com o seguinte código:

    @model IEnumerable<Mvc5Mobile.Models.Session>

    <h2>@ViewBag.Title</h2>

    <div class="container">
        <div class="row">
            @foreach (var session in Model)
            {
                <div class="col-md-4">
                    <div class="list-group">
                        @Html.ActionLink(session.Title, 
                                         "SessionByCode", 
                                         new { session.Code }, 
                                         new { @class="list-group-item active" })
                        <div class="list-group-item">
                            <div class="list-group-item-text">
                                @Html.Partial("_SpeakersLinks", session)
                            </div>
                            <div class="list-group-item-info">
                                @session.DateText
                            </div>
                            <div class="list-group-item-info small hidden-xs">
                                @Html.Partial("_TagsLinks", session)
                            </div>
                        </div>
                    </div>
                </div>
            }
        </div>
    </div>

O código faz 3 coisas:

-   utiliza o [grupo de listas vinculadas personalizado][] do Bootstrap para formatar as informações da sessão verticalmente, de modo que elas sejam legíveis em um navegador móvel (utilizando classes como list-group-item-text)
-   aplica o [sistema de grades][] ao layout, de modo que os itens da sessão flutuem horizontalmente no navegador da área de trabalho e verticalmente no navegador móvel (utilizando a classe col-md-4)
-   utiliza os [utilitários dinâmicos][] para ocultar as marcas de sessão, quando exibidas em um navegador móvel (utilizando a classe hidden-xs)

Você também pode tocar no link de um título para ir à respectiva sessão. A imagem abaixo reflete as alterações de código.

![][FixedSessionsByTag]

O sistema de grades do Bootstrap organiza as sessões verticalmente, de maneira automática, no navegador móvel. Perceba, também, que as marcas não são exibidas. Alterne para o navegador de desktop.

![][SessionsTableFixedTagASP.NETDesktop]

Perceba que, neste navegador, as marcas são exibidas. Você também pode notar que o sistema de grades do Bootstrap organiza os itens da sessão em duas colunas. Se ampliar o navegador, você perceberá que o arranjo muda para três colunas.

##<a name="bkmk_improvesessionbycode"></a> Melhorar o modo de exibição SessionByCode

Finalmente, você consertará o modo de exibição *SessionByCode*, para torná-lo acessível a dispositivos móveis.

No navegador móvel, toque o botão **Marca**, depois insira `asp` na caixa de pesquisa.

![][AllTagsFixedSearchByASP]

Toque o link **ASP.NET**. As sessões para a marca ASP.NET são exibidas.

![][FixedSessionsByTag]

Escolha o link **Construindo um aplicativo de página única com o ASP.NET e AngularJS**.

![][SessionByCode3-644]

A exibição padrão para navegador de desktop é boa, mas você pode aprimorar a aparência facilmente usando alguns componentes GUI do Bootstrap.

Abra *Views\\Home\\SessionByCode.cshtml* e substitua o conteúdo com a seguinte marcação:

    @model Mvc5Mobile.Models.Session

    @{
        ViewBag.Title = "Session details";
    }
    <h3>@Model.Title (@Model.Code)</h3>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Speakers
        </div>
        @foreach (var speaker in Model.Speakers)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class="panel-body" })
        }
    </div>

    <p>@Model.Abstract</p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Tags
        </div>
        @foreach (var tag in Model.Tags)
        {
            @Html.ActionLink(tag, 
                             "SessionsByTag", 
                             new { tag }, 
                             new { @class = "panel-body" })
        }
    </div>

A nova marcação usa o estilo de painéis do Bootstrap para melhorar o modo de exibição no dispositivo móvel.

Atualize o navegador móvel. A imagem a seguir reflete as alterações de código que você acabou de criar:

![][SessionByCodeFixed3-644]

## Conclusão e revisão

Este tutorial mostrou como usar o ASP.NET MVC 5 para desenvolver aplicativos Web com opção para dispositivos móveis. Estão incluídos:

-	Implantar um aplicativo ASP.NET MVC 5 em um aplicativo Web do Serviço de Aplicativo
-   Usar o Bootstrap para criar layouts Web dinâmicos em seu aplicativo MVC 5
-   Substituir layouts, modos de exibição e exibições parciais globalmente ou para um modo de exibição individual
-   Controlar a imposição de layout e de substituição parcial usando a propriedade `RequireConsistentDisplayMode`
-   Criar modos de exibição voltados para navegadores específicos, como o do iPhone
-   Aplicar estilos do Bootstrap no código Razor

## Consulte também

-   [9 princípios básicos do design da Web responsivo](http://blog.froont.com/9-basic-principles-of-responsive-web-design/)
-   [Bootstrap][BootstrapSite]
-   [Blog oficial do Bootstrap][]
-   [Tutorial do Bootstrap no Twitter, feito pela Tutorial Republic][]
-   [The Bootstrap Playground][]
-   [Práticas recomendadas pela W3C para aplicativos móveis para a Web][]
-   [Recomendação Candidata do W3C para consultas de mídia][]

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, confira: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- Internal Links -->
[Deploy the starter project to an Azure web app]: #bkmk_DeployStarterProject
[Bootstrap CSS Framework]: #bkmk_bootstrap
[Override the Views, Layouts, and Partial Views]: #bkmk_overrideviews
[Create Browser-Specific Views]: #bkmk_browserviews
[Improve the Speakers List]: #bkmk_Improvespeakerslist
[Improve the Tags List]: #bkmk_improvetags
[Improve the Dates List]: #bkmk_improvedates
[Improve the SessionsTable View]: #bkmk_improvesessionstable
[Improve the SessionByCode View]: #bkmk_improvesessionbycode

<!-- External Links -->
[Visual Studio Express 2013]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
[Visual Studio de 2015]: https://www.visualstudio.com/downloads/download-visual-studio-vs
[AzureSDKVs2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
[Fiddler]: http://www.fiddler2.com/fiddler2/
[EmulatorIE11]: http://msdn.microsoft.com/library/ie/dn255001.aspx
[EmulatorChrome]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
[EmulatorOpera]: http://www.opera.com/developer/tools/mobile/
[StarterProject]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409
[CompletedProject]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409
[BootstrapSite]: http://getbootstrap.com/
[WebPIAzureSdk23NetVS13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png
[grupo de listas vinculadas]: http://getbootstrap.com/components/#list-group-linked
[glyphicon]: http://getbootstrap.com/components/#glyphicons
[painéis]: http://getbootstrap.com/components/#panels
[grupo de listas vinculadas personalizado]: http://getbootstrap.com/components/#list-group-custom-content
[sistema de grades]: http://getbootstrap.com/css/#grid
[utilitários dinâmicos]: http://getbootstrap.com/css/#responsive-utilities
[Blog oficial do Bootstrap]: http://blog.getbootstrap.com/
[Tutorial do Bootstrap no Twitter, feito pela Tutorial Republic]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
[The Bootstrap Playground]: http://www.bootply.com/
[Práticas recomendadas pela W3C para aplicativos móveis para a Web]: http://www.w3.org/TR/mwabp/
[Recomendação Candidata do W3C para consultas de mídia]: http://www.w3.org/TR/css3-mediaqueries/

<!-- Images -->
[DeployClickPublish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-1.png
[DeployClickWebSites]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-2.png
[DeploySignIn]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-3.png
[DeployUsername]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-4.png
[DeployPassword]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-5.png
[DeployNewWebsite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-6.png
[DeploySiteSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7.png
[DeployPublishSite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-8.png
[MobileHomePage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/mobile-home-page.png
[FixedSessionsByTag]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-Fixed.png
[AllTags]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags.png
[SessionsByTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET.png
[SessionsByTagASP.NETNoBootstrap]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-NoBootstrap.png
[AllTagsMobile_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile.png
[AllTagsMobile_LayoutMobileDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile-Desktop.png
[ResolveDefaultDisplayMode]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Resolve-DefaultDisplayMode.png
[AllTagsIPhone_LayoutIPhone]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsIPhone-_LayoutIPhone.png
[AllSpeakers_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile.png
[AllSpeakers_LayoutMobileOverridden]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile-Overridden.png
[AllSpeakersFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed.png
[AllSpeakersFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-Desktop.png
[AllSpeakersFixedSearchBySC]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-SearchBySC.png
[AllTagsFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-Desktop.png
[AllTagsFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed.png
[AllDatesFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed.png
[AllDatesFixed2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2.png
[AllDatesFixed2Desktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2-Desktop.png
[AllTagsFixedSearchByASP]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-SearchByASP.png
[SessionsTableTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Tag-ASP.NET.png
[SessionsTableFixedTagASP.NETDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Fixed-Tag-ASP.NET-Desktop.png
[SessionByCode3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-3-644.png
[SessionByCodeFixed3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-Fixed-3-644.png
 

<!---HONumber=Oct15_HO3-->