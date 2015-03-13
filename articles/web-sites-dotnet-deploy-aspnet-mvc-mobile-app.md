<properties 
	pageTitle="Site móvel do .NET ASP.NET MVC 5 - Tutoriais do Azure" 
	description="Um tutorial que ensina como implantar um aplicativo Web em um site do Azure usando recursos móveis no aplicativo Web ASP.NET MVC 5." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/19/2014" 
	ms.author="cephalin,riande"/>


# Implantar um aplicativo Web Móvel do ASP.NET MVC 5 em Sites do Azure

Este tutorial ensina as noções básicas de como criar um ASP.NET MVC 5
Aplicativo Web que é acessível com dispositivo móvel e implantá-o no Microsoft Azure. Para este tutorial, você precisará do 
[Visual Studio Express 2013 para Web][Visual Studio Express 2013] ou a edição professional do Visual Studio se ela já está instalada.

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

### O que você vai construir

Neste tutorial, você adicionará recursos móveis para o aplicativo de listagem de conferência simples que é fornecido no [projeto inicial][StarterProject]. A captura de tela a seguir mostra as sessões do ASP.NET no aplicativo concluído, como visto no emulador do navegador nas ferramentas de desenvolvedor do Internet Explorer 11 F12.

![][FixedSessionsByTag]

Você pode usar as ferramentas de desenvolvedor do Internet Explorer 11 F12 e a [ferramenta Fiddler][Fiddler] para ajudar a depurar o seu aplicativo. 

### Qualificações que você aprenderá

Eis o que você vai aprender:

-	Como usar o Visual Studio 2013 para publicar seu aplicativo Web diretamente em um Site do Microsoft Azure.
-   Como os modelos do ASP.NET MVC 5 usam o framework de CSS Bootstrap para melhorar a exibição em dispositivos móveis
-   Como criar modos de exibição para dispositivos móveis voltados para navegadores de dispositivos móveis específicos, tais como iPhone e Android
-   Como criar modos de exibição dinâmicos (que respondam a navegadores diferentes em todos os dispositivos)

<h2>Configurar o ambiente de desenvolvimento</h2>

Defina o ambiente de desenvolvimento instalando o SDK do Azure para o .NET Framework. 

1. Para instalar o SDK do Azure para .NET, clique no link abaixo. Se você ainda não tiver o Visual Studio 2013 instalado, ele será instalado pelo link. Este tutorial requer o Visual Studio 2013. [SDK do Azure para o Visual Studio 2013][AzureSDKVs2013]
1. Na janela do Web Platform Installer, clique em **Instalar** e prossiga com a instalação.

	![Web Platform Installer - SDK do Azure para .NET][WebPIAzureSdk23NetVS13]

Também será necessário um emulador de navegador móvel. Qualquer uma das opções a seguir:funcionará

-   Emulador do navegador nas [ferramentas de desenvolvedor do Internet Explorer 11 F12][EmulatorIE11] (usado em todas as capturas de tela do navegador móvel). Ele possui predefinições de cadeia de caracteres de agente do usuário para Windows Phone 8, Windows Phone 7 e Apple iPad.
-	Emulador de navegador nas [DevTools do Google Chrome][EmulatorChrome]. Contém predefinições para vários dispositivos Android, e também para Apple iPhone, Apple iPad e Amazon Kindle Fire. Ele também emula eventos de toque.
-   [Operar o emulador móvel][EmulatorOpera]

Os projetos do Visual Studio com código-fonte C# estão disponíveis para acompanhar este tópico:

-   [Download do projeto inicial][StarterProject]
-   [Download do projeto concluído][CompletedProject]

<h2>Etapas neste tutorial</h2>

- [Implantar o projeto inicial em um Site do Microsoft Azure][]
- [Framework de CSS Bootstrap][]
- [Substitua os modos de exibição, layouts e exibições parciais][]
- [Melhorar a lista de alto-falantes][]
- [Melhorar a lista de marcas][]
- [Melhorar a lista de datas][]
- [Melhorar o modo de exibição da tabela de sessões][]
- [Melhorar o modo de exibição SessionByCode][]

<h3><a name="bkmk_DeployStarterProject"></a>Implantar o projeto inicial em um Site do Microsoft Azure</h3>

1.	Baixe o [projeto inicial][StarterProject] do aplicativo de listagem de conferência.

2. 	Em seguida, no Windows Explorer, clique com o botão direito no arquivo Mvc5Mobile.zip e escolha *Properties*.

3. 	Na caixa de diálogo **Propriedades de Mvc5Mobile.zip**, escolha o botão **Desbloquear**. (O desbloqueio impede o aparecimento de um aviso de segurançaquando você tenta usar um arquivo .zip baixadoda web.)

4.	Clique com o botão direito do mouse no arquivo *Mvc5Mobile.zip* e selecione **Extrair tudo** para descompactar o arquivo. 

5. 	No Visual Studio, abra o arquivo *Mvc5Mobile.sln*.

6.  No Gerenciador de Soluções, clique com o botão direito no projeto e clique em **Publicar**.

	![][DeployClickPublish]

7.	Em Publicar Web, clique em **Sites do Microsoft Azure**.

	![][DeployClickWebSites]

8.	Clique em **Entrar**.

	![][DeploySignIn]

9.	Digite seu nome de usuário no Microsoft Azure e clique em **Continuar**.

	![][DeployUsername]

10.	Digite sua senha e clique em **Entrar**.

	![][DeployPassword]

11. A caixa de diálogo Selecionar site existente deve mostrar agora que você entrou. Clique em **Novo**.

	![][DeployNewWebsite]  

12. No campo **Nome do site**, especifique um prefixo único para o nome do site. O nomedo seu site totalmente qualificado site será *&lt;prefixo>*. azurewebsites.net. Ainda, no campo **Região**, selecione uma região. Em seguida, clique em **Criar**.

	![][DeploySiteSettings]

13.	A caixa de diálogo Publicar Web será preenchida com as configurações de site para o seu novo Site. Clique em **Publicar**.

	![][DeployPublishSite]

	Assim que o Visual Studio terminar de publicar o projeto inicial no Site do Microsoft Azure, o navegador será aberto para exibir o site ao vivo.

14.	Inicie o emulador de navegador móvel, copie a URL parao aplicativo de conferência (*<prefix>*.azurewebsites.net) do emulador e depois clique nobotão da parte superior direita e selecione **Procurar por marca**. Se você estiver usando o Internet Explorer 11 como o navegador padrão, basta digitar `F12`, em seguida, `Crtl+8`e, em seguida, alterar o perfil de navegador para **Windows Phone**. A imagem abaixo mostra a exibição *AllTags* no modo retrato (da seleção **Procurar por marca**).

	![][AllTags]

>[AZURE.NOTE] Enquanto depura o aplicativo MVC 5 no Visual Studio, você pode publicar seu site no Microsoft Azure novamente para verificar o site ao vivo direto no navegador móvel ou emulador do navegador.

A exibição é muito legível em um dispositivo móvel. Você também já pode ver alguns dos efeitos visuais aplicados pelo Framework de CSS Bootstrap.
Clique no link **ASP.NET**.

![][SessionsByTagASP.NET]

O modo de exibição de marca do ASP.NET é ajustado à tela, o que é feito automaticamente pelo Bootstrap para você. No entanto, é possível aprimorar esse modo de exibição para melhor se adequar ao navegador móvel. Por exemplo, a coluna **Data** é muito difícil de ler. Posteriormente no tutorial você alterará a exibição do *AllTags*para dispositivos móveis.

<h2><a name="bkmk_bootstrap"></a>Framework de CSS Bootstrap</h2>

Uma novidade do modelo MVC 5 é o suporte interno ao Bootstrap. Você já viu como ele aprimora imediatamente os diferentes modos de exibição em seu aplicativo. Por exemplo, a barra de navegação na parte superior é recolhida automaticamente quando a largura do navegador é menor. No navegador de desktop, tente redimensionar a janela e veja como a barra de navegação muda de aparência e estilo. O responsável é o design da Web dinâmico interno da Inicialização.
Inicializar.

Para ver qual seria a aparência do aplicativo Web sem inicialização, abra *App\_Start\\BundleConfig.cs* e comente as linhas que contêm *bootstrap.js* e *bootstrap.css*. O código a seguir mostra asduas últimas instruções do método `RegisterBundles` após a alteração:

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

Pressione `CTRL+F5` para executar o aplicativo.

Observe que a barra de navegação recolhível é agora apenas uma lista simples e desordenada. Clique em **Procurar por marca** novamente, então clique em **ASP.NET**. No modo de exibição do emulador do navegador móvel, é possível ver que ele já não se ajusta à tela, e você precisa rolar para os lados para conseguir vero lado direito da tabela.

![][SessionsByTagASP.NETNoBootstrap]

Desfaça as alterações e atualize o navegador móvel para verificar se o modo de exibição adaptado para dispositivo móvel foi restaurado.

O Bootstrap não é específico para o ASP.NET MVC 5, e você pode aproveitar esses recursos em qualquer aplicativo Web. Mas ele agora está incorporado ao modelo de projeto do ASP.NET MVC 5, para que seu aplicativo Web do MVC 5 possa tirar proveito da inicialização por padrão.

Para obter mais informações sobre inicialização, vá para o site [Bootstrap][BootstrapSite].

Na próxima seção, você verá como fornecer modos de exibição específicos para navegadores móveis.

<h2><a name="bkmk_overrideviews"></a>Substitua os modos de exibição, layouts e exibições parciais</h2>

Você pode substituir qualquer modo de exibição (inclusive layouts e modos de exibição parciais) para navegadores de dispositivos móveis em geral, para um navegador móvel individual ou para qualquer navegador específico. Para fornecer um modo de exibição específico para dispositivos móveis, você pode copiar um arquivo de modo de exibição e adicionar *.Mobile* ao nome do arquivo. Por exemplo, para criar uma exibição móvel do *Índice*, você pode copiar *Views\\Home\\Index.cshtml* para *Views\\Home\\Index.Mobile.cshtml*.

Nesta seção, você criará um arquivo de layout específico para dispositivos móveis.

Para iniciar, copie *Views\\Shared\\\_Layout.cshtml* para *Views\\Shared\\\_Layout.Mobile.cshtml*. Abra *\_Layout.Mobile.cshtml* e altere o título de **Aplicativo MVC5** para **Aplicativo MVC5 (móvel)**.

Em cada chamada `Html.ActionLink` para a barra de navegação, remova "Procurar por" em cada link *ActionLink*. O código a seguir mostra a marca `<ul class="nav navbar-nav">` concluída do arquivo de layout para dispositivos móveis.

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

Copie o arquivo *Views\\Home\\AllTags.cshtml* para *Views\\Home\\AllTags.Mobile.cshtml*. Abra o novo arquivo e altere o elemento `<h2>` de "Marcas" para "Marcas (M)":

    <h2>Tags (M)</h2>

Navegue até a página de marcas utilizando um navegador de desktop e o emulador do navegador móvel. O emulador do navegador móvel mostra as duas alterações que você fez (o título de *\_Layout.Mobile.cshtml* e o título de *AllTags.Mobile.cshtml*).

[][AllTagsMobile_LayoutMobile]

Por outro lado, a exibição da área de trabalho não foi alterada (com títulos de *\_Layout.cshtml* e *AllTags.cshtml*).

![][AllTagsMobile_LayoutMobileDesktop]

<h2><a name="bkmk_browserviews"></a>Criar modos de exibição para um navegador específico</h2>

Além dos modos de exibição específicos para navegadores móveis e de desktop, você pode criar modos de exibição para um único navegador. Por exemplo, você pode criar modos de exibição específicos para o navegador do iPhone ou do Android. Nesta seção, você criará um layout para o navegador do iPhone e uma versão para iPhone do modo de exibição *AllTags*.

Abra o arquivo *Global.asax* e adicione o seguinte código na parte inferior do método `Application_Start`.

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

Este código define um novo modo de exibição denominado "iPhone", que será comparado a todas as solicitações de entrada. Se a solicitação de entrada corresponder à condição definida (ou seja, se o agente do usuário contém a cadeia de caracteres do "iPhone"), o ASP.NET MVC procurará exibições cujo nome contém o "sufixo do iPhone".

>[AZURE.NOTE] Ao adicionar modos de exibição específicos para determinado navegador móvel, como para iPhone e Android, configure o primeiro argumento para `0` (inserido no topo da lista) para garantir que o modo específico para um navegador tenha precedência sobre o modelo para dispositivos móveis (**.Mobile.cshtml). Se o modelo para dispositivos móveis estiver no topo da lista, ele será selecionado no lugar do modo de exibição que você pretendia usar (a primeira correspondência predomina, e o modelo para dispositivos móveis corresponde a todos os navegadores móveis). 

No código, clique com botão direito do mouse em`DefaultDisplayMode`, escolha **Resolvere**, em seguida, escolha `usando System.Web.WebPages;`. Isso adiciona uma referência para o `System.Web.WebPages`namespace, que é onde os tipos `DisplayModeProvider`e `DefaultDisplayMode` são definidos.

![][ResolveDefaultDisplayMode]

Como alternativa, é possível adicionar apenas manualmente a seguinte linha para a seção `using` do arquivo.

    using System.Web.WebPages;

Salve as alterações. Copie o *Views\\Shared\\\_Layout.Mobile.cshtml* file to *Views\\Shared\\\_Layout.iPhone.cshtml*. Open the new file and then change the title from `MVC5 Application (Mobile)` to `MVC5 Application (iPhone)`.

Copie o arquivo *Views\\Home\\AllTags.Mobile.cshtml* para *Views\\Home\\AllTags.iPhone.cshtml*. In the new file, change the `<h2>` element from "Tags (M)" to "Tags (iPhone)".

Execute o aplicativo. Execute o emulador de navegador móvel com o agente do usuário definido com o "iPhone" e navegue até o modo de exibição *AllTags*. Se você estiver utilizando o emulador nas ferramentas de desenvolvedor do Internet Explorer 11 F12, configure a emulação para fazer o seguinte:

-   Perfil de navegador = **do Windows Phone**
-   Cadeia de caracteres de agente de usuário =  **personalizado**
-   Cadeia de caracteres personalizada = **Apple-iPhone5C1/1001.525**

A captura de tela a seguir mostra o modo de exibição *AllTags* renderizado no emulador das ferramentas de desenvolvedor do Internet Explorer 11 F12 com a cadeia de caracteres de agente do usuário personalizada (esta é uma cadeia de caracteres de agente do usuário do iPhone 5C).

![][AllTagsIPhone_LayoutIPhone]

No navegador móvel, selecione o link **Alto-falantes**. Porque não há um modo de exibição móvel (*AllSpeakers.Mobile.cshtml*), a exibição dos alto-falantes padrão (*Allspeakers*) é renderizada utilizando o modo de exibição de layout móvel (*\_Layout.Mobile.cshtml*). Conforme mostrado abaixo, o título do **Aplicativo MVC5 (Móvel)** é definido em *\_Layout.Mobile.cshtml*.

![][AllSpeakers_LayoutMobile]

Você pode desativar globalmente uma exibição padrão (não móvel) a partir da renderização dentro de um layout móvel, definindo `RequireConsistentDisplayMode` para `true` no arquivo *Views\\\_ViewStart.cshtml*, como este:

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

Quando `RequireConsistentDisplayMode` é definido como `true`, o layout móvel (*\_Layout.Mobile.cshtml*) é usado somente para visualizações móveis (ou seja, quando o arquivo de exibição tem o formato *** ViewName**.Cshtml*). Talvez você queiradefinir o `RequireConsistentDisplayMode` para `true` se o layout móvelnão funcionar bem com os modos de exibição em dispositivos não móveis. A captura de tela abaixo mostra como a *Speakers*página renderiza quando o `RequireConsistentDisplayMode` é definido como `true`( (sem a cadeia de caracteres "(Móvel)" na barra de navegação na parte superior).

![][AllSpeakers_LayoutMobileOverridden]

Você pode desativar o modo de exibição consistente em um modo específico, definindo `RequireConsistentDisplayMode` para `false` no arquivo de exibição. A seguinte marcação no arquivo *Views\\Home\\AllSpeakers.cshtml* define `RequireConsistentDisplayMode` Para `false`:

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

Nesta seção, você viu como criar layouts e exibições móveis e como criar layouts e exibições para dispositivos específicos, como o iPhone.
Porém, a principal vantagem da estrutura Bootstrap CSS é seu layout responsivo, o que significa que uma única folha de estilo pode ser aplicada em todos os navegadores de desktop, telefone e tablet para criar um visual e uma experiência consistentes. Na próxima seção, você verá como aproveitar o Bootstrap para criar modos de exibição para dispositivos móveis.

<h2><a name="bkmk_Improvespeakerslist"></a> Melhorar a lista de alto-falantes</h2>

Como você acabou de ver, a exibição *Speakers* está legível, mas os links são pequenos e difíceis de tocar em um dispositivo móvel. Nesta seção, você tornará a exibição *AllSpeakers* acessível para dispositivos móveis, que exibe links grandes, fáceis de tocar e contém uma caixa de pesquisa para localizar alto-falantes rapidamente.

É possível usar o estilo do [grupo de lista vinculada][] da inicialização para aprimorar a exibição de *Speakers*. No*Views\\Home\\AllSpeakers.cshtml*, substitua o conteúdo do arquivo pelo código abaixo.

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

O atributo `class="list-group"` na marca `<div>` aplica-se ao estilo de lista da inicialização e o atributo `class="input-group-item"` aplica-se aos estilos de item de lista de inicialização para cada link.

Atualize o navegador móvel. O modo de exibição atualizado tem esta aparência:

![][AllSpeakersFixed]

O estilo do [][grupo de lista vinculada] do Bootstrap torna clicável a caixa inteira de todos oslinks, proporcionando uma experiência de usuário muito melhor. Alterne para o modo de exibição de desktop e observe a consistência na aparência e no estilo.

![][AllSpeakersFixedDesktop]

Embora o modo de exibição do navegador móvel tenha sido aprimorado, é difícil navegar pela longa lista de alto-falantes. O Bootstrap não oferece uma funcionalidade de filtro de busca pronta para uso, mas você pode adicionar uma usando poucas linhas de código. Primeiro, adicione uma caixa de pesquisa à exibição, em seguida, vincule ao código do JavaScript para a função de filtro. No *Views\\Home\\AllSpeakers.cshtml*, adicione uma marca de \<formulário\> logo após a marca \<h2\>, conforme mostrado abaixo:

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

Observe que as marcas do `<formulário>` e da `<entrada>` têm os estilos de inicialização aplicados a eles. O eemento `<período>` adiciona um programa de inicialização [glyphicon][] à caixa de pesquisa.

Na pasta *Scripts*, adicione um arquivo JavaScript chamado *filter.js*. Abra o arquivo e cole o seguinte código nele:

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

Também será preciso incluir o filter.js em seus pacotes registrados. Aberto *App\_Start\\BundleConfig.CS* e altere os primeiros pacotes. Alterar a primeira declaração`bundles.Add` (para o pacote **jquery** ) para incluir *Scripts\\filter.js*, da seguinte maneira:

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

O pacote **jquery** já está renderizado pelo modo de exibição padrão *\_Layout*. Mais tarde, você pode utilizar o mesmo código JavaScript para aplicar a funcionalidade de filtro a outros modos de exibição de lista.

Atualize o navegador móvel e vá para a exibição *AllSpeakers*. Na caixa de pesquisa, digite "sc". Agora, a lista de alto-falantes deve ser filtrada de acordo com a cadeia de caracteres de busca.

![][AllSpeakersFixedSearchBySC]

<h2><a name="bkmk_improvetags"></a>Melhorar a lista de marcas</h2>

Como a exibição *Speakers*, a exibição *Tags* é legível, mas os links são pequenos e difíceis de tocar em um dispositivo móvel. Você pode corrigir a exibição *Tags* da mesma maneira que você corrige a exibição *Speakers*, se você usar as alterações de código descritas anteriormente, mas com a seguinte sintaxe de método `Html.ActionLink` em*Views\\Home\\AllTags.cshtml*:

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

O navegador de desktop atualizado terá a seguinte aparência:

![][AllTagsFixedDesktop]

E o navegador móvel atualizado terá a seguinte aparência: 

![][AllTagsFixed]

>[AZURE.NOTE] Se você perceber que o formato de lista original ainda permanece no navegador móvel e ficar imaginando o que aconteceu com o seu belo estilo do Bootstrap, isso é consequência de alguma alteração indesejada feita durante o processo para criar modos de exibição específicos para dispositivos móveis. No entanto, agora que você está usando o Framework de CSS Bootstrap para criar um design Web dinâmico, vá em frente e exclua os modos de exibição e os layouts específicos para dispositivos móveis. Assim que tiver feito isso, o navegador móvel atualizado vai exibir o estilo do Bootstrap.

<h2><a name="bkmk_improvedates"></a>Melhorar a lista de datas</h2>

Você pode melhorar a exibição *Dates* como você melhorou as exibições *Speakers* e
*Tags* se você usar as alterações de código descritas anteriormente, mas com a seguinte sintaxe de método `Html.ActionLink` em *Views\\Home\\AllDates.cshtml*:

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

A exibição atualizada do navegador móvel será como esta:

![][AllDatesFixed]

Você pode melhorar ainda mais a exibição *Dates*, organizando os valores de data e hora por data. Isso pode ser feito com o estilo
[painéis][] styling. Substitua o conteúdo do arquivo *Views\\Home\\AllDates.cshtml* pelo código a seguir:

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

Esse código cria uma marca separada `<div class="panel panel-primary">` separada para cada data da lista e usa o [grupo de lista vinculada][] para os respectivos links, como antes. Esta é a aparência do navegador móvel quando esse código é executado:

![][AllDatesFixed2]

Alterne para o navegador da área de trabalho. Novamente, repare na consistência de aparência.

![][AllDatesFixed2Desktop]

<h2><a name="bkmk_improvesessionstable"></a>Melhorar o modo de exibição da tabela de sessões</h2>

Nesta seção, você fará a exibição *SessionsTable* mais amigável para dispositivos móveis. Esta mudança é mais extensa que as anteriores.

No navegador móvel, toque o botão **Marca**, depois insira `asp` nacaixa de pesquisa.

![][AllTagsFixedSearchByASP]

Toque o link **ASP.NET**.

![][SessionsTableTagASP.NET]

Como você pode ver, a exibição está formatada como uma tabela, que foi desenhada para ser exibida em um navegador de desktop. No entanto, é um pouco difícil de ler em um navegador móvel. Para corrigir isso, abra
*Views\\Home\\SessionsTable.cshtml* e, em seguida, substitua o conteúdo do arquivo com o código a seguir:

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

-   usa o [grupo de lista vinculada personalizado][]do Bootstrap para formatar as informações da sessão verticalmente, de modo que elas sejam legíveis em um navegador móvel (usando classes como list-group-item-text)
-   aplica o [sistema de grades][] ao layout, de modo que os itens da sessão flutuem horizontalmente no navegador de desktop e verticalmente no navegador móvel (usando a classe col-md-4)
-   usa os [utilitários responsivos][] para ocultar as marcas de sessão, quando exibidas em um navegador móvel (usando a classe hidden-xs)

Você também pode tocar no link de um título para ir à respectiva sessão. A imagem abaixo reflete as alterações de código.

![][FixedSessionsByTag]

O sistema de grades do Bootstrap organiza as sessões verticalmente, de maneira automática, no navegador móvel. Perceba, também, que as marcas não são exibidas. Alterne para o navegador de desktop.

![][SessionsTableFixedTagASP.NETDesktop]

Perceba que, neste navegador, as marcas são exibidas. Você também pode notar que o sistema de grades do Bootstrap organiza os itens da sessão em duas colunas. Se ampliar o navegador, você perceberá que o arranjo muda para três colunas.

<h2><a name="bkmk_improvesessionbycode"></a>Melhorar o modo de exibição SessionByCode</h2>

Finalmente, você corrigirá a exibição *SessionByCode* para dispositivos móveis.

No navegador móvel, toque o botão **Marca**, depois insira `asp` nacaixa de pesquisa.

![][AllTagsFixedSearchByASP]

Toque o link **ASP.NET**. As sessões para a marca ASP.NET são exibidas.

![][FixedSessionsByTag]

Escolha o link **Construindo um aplicativo de página única com o ASP.NET e o link AngularJS**.

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

A nova marcação utiliza o estilo de painéis do Bootstrap para melhorar o modo de exibição no dispositivo móvel. 

Atualize o navegador móvel. A imagem a seguir reflete as alterações de códigoque você acabou de fazer:

[][SessionByCodeFixed3-644]

Conclusão e revisão
------------------

Este tutorial mostrou como usar o ASP.NET MVC 5 para desenvolver aplicativos Web com opção para dispositivos móveis. Elas incluem:

-	Implantar um aplicativo ASP.NET MVC 5 em um site do Microsoft Azure
-   Usar o Bootstrap para criar layouts Web dinâmicos em seu aplicativo MVC 5
-   Substituir layouts, modos de exibição e exibições parciais globalmente ou para um modo de exibição individual
-   Controlar a imposição de layout e de substituição parcial usando a propriedade `RequireConsistentDisplayMode`
-   Criar modos de exibição voltados para navegadores específicos, como o do iPhone
-   Aplicar estilos de inicialização no código do Razor

Consulte também
--------

-   [9 princípios básicos do design da Web responsivo](http://blog.froont.com/9-basic-principles-of-responsive-web-design/)
-   [Inicialização][BootstrapSite]
-   [Blog oficial do Bootstrap][]
-   [Tutorial do Twitter Bootstrap do Tutorial Republic][]
-   [The Bootstrap Playground][]
-   [Práticas recomendadas pela W3C para aplicativos Web móveis][]
-   [Recomendação Candidata (CR) do W3C para consultas de mídia][]

<!-- Internal Links -->
[Implantar o projeto inicial em um Site do Microsoft Azure]: #bkmk_DeployStarterProject
[Framework de CSS Bootstrap]: #bkmk_bootstrap
[Substitua os modos de exibição, layouts e exibições parciais]: #bkmk_overrideviews
[Criar modos de exibição para um navegador específico]:#bkmk_browserviews
[Melhorar a lista de alto-falantes]: #bkmk_Improvespeakerslist
[Melhorar a lista de marcas]: #bkmk_improvetags
[Melhorar a lista de datas]: #bkmk_improvedates
[Melhorar o modo de exibição da tabela de sessões]: #bkmk_improvesessionstable
[Melhorar o modo de exibição SessionByCode]: #bkmk_improvesessionbycode

<!-- External Links -->
[Visual Studio Express 2013]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
[AzureSDKVs2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
[Fiddler]: http://www.fiddler2.com/fiddler2/
[EmulatorIE11]: http://msdn.microsoft.com/library/ie/dn255001.aspx
[EmulatorChrome]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
[EmulatorOpera]: http://www.opera.com/developer/tools/mobile/
[StarterProject]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409
[CompletedProject]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409
[BootstrapSite]: http://getbootstrap.com/
[WebPIAzureSdk23NetVS13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png
[grupo de lista vinculada]: http://getbootstrap.com/components/#list-group-linked
[glyphicon]: http://getbootstrap.com/components/#glyphicons
[painéis]: http://getbootstrap.com/components/#panels
[grupo de lista vinculada personalizado]: http://getbootstrap.com/components/#list-group-custom-content
[sistema de grades]: http://getbootstrap.com/css/#grid
[utilitários responsivos]: http://getbootstrap.com/css/#responsive-utilities
[Blog oficial do Bootstrap]: http://blog.getbootstrap.com/
[Tutorial do Twitter Bootstrap do Tutorial Republic]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
[The Bootstrap Playground]: http://www.bootply.com/
[Práticas recomendadas pela W3C para aplicativos Web móveis]: http://www.w3.org/TR/mwabp/
[Recomendação Candidata (CR) do W3C para consultas de mídia]: http://www.w3.org/TR/css3-mediaqueries/

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


<!--HONumber=42-->
