<properties 
	pageTitle="Integrar um aplicativo Web do Azure à CDN do Azure" 
	description="Um tutorial que ensina como implantar um aplicativo Web do Azure que fornece conteúdo de um ponto de extremidade da CDN do Azure integrado" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/04/2015" 
	ms.author="cephalin"/>

<a name="intro"></a>
# Integrar um aplicativo Web do Azure à CDN do Azure #

O Serviço de Aplicativo do Azure pode ser integrado à [CDN do Azure](http://azure.microsoft.com/services/cdn/), sendo adicionado aos recursos de dimensionamento global inerentes ao aplicativo Web do Serviço de Aplicativo fornecendo seu conteúdo de aplicativos Web globalmente de nós de servidor próximos de seus clientes (uma lista atualizada de todos os locais de nós atuais pode ser encontrada [aqui](http://msdn.microsoft.com/library/azure/gg680302.aspx)). Essa integração aumenta drasticamente o desempenho de seu Aplicativo Web do Serviço de Aplicativo do Azure e melhora significativamente a experiência do usuário de seu aplicativo Web em todo o mundo. 

A integração de aplicativos Web do Azure à CDN do Azure oferece as seguintes vantagens:

- Integrar a implantação de conteúdo (imagens, scripts e folhas de estilo) como parte do processo de [implantação contínua](web-sites-publish-source-control.md) de seu aplicativo Web do Azure
- Atualizar facilmente os pacotes do NuGet em seu aplicativo Web no Serviço de Aplicativo do Azure, como versões de Bootstrap ou jQuery 
- Gerenciar seu aplicativo Web e o conteúdo fornecido por CDN por meio da mesma interface do Visual Studio
- Integrar agrupamento e minificação ASP.NET à CDN do Azure

## O que você compilará ##

Você implantará um aplicativo Web para o Serviço de Aplicativo do Azure usando o modelo ASP.NET MVC padrão em Visual Studio, adicionará código para fornecer conteúdo de uma CDN do Azure integrada, como uma imagem, resultados de ação do controlador e os arquivos JavaScript e CSS padrão, e também escreverá código para configurar o mecanismo de fallback para grupos fornecidos caso a CDN esteja offline.

## O que será necessário ##

Este tutorial tem os seguintes pré-requisitos:

-	Uma [conta ativa do Microsoft Azure](http://azure.microsoft.com/account/)
-	Visual Studio 2013 com o [SDK do Azure](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)

> [AZURE.NOTE] Você precisa de uma conta do Azure para concluir este tutorial:
> Você pode [abrir uma conta do Azure gratuitamente](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) - você obtém créditos que podem ser usados para experimentar serviços pagos do Azure e, mesmo após eles serem utilizados, você pode manter a conta e utilizar os serviços gratuitos do Azure, como Aplicativos Web.
> Você pode [ativar benefícios para assinantes do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - todos os meses, sua assinatura do MSDN oferece créditos que podem ser usados para serviços pagos do Azure.

<a name="deploy"></a>
## Implantar um aplicativo Web do Azure com um ponto de extremidade integrado da CDN ##

Nesta seção, você implantará o modelo de aplicativo ASP.NET MVC padrão no Visual Studio 2013 a um aplicativo Web do Azure e, depois, o integrará a um novo ponto de extremidade da CDN. Siga as instruções abaixo:

1. No Visual Studio 2013, crie um novo aplicativo Web ASP.NET na barra de menus selecionando **Arquivo > Novo > Projeto > Web > Aplicativo Web ASP.NET**. Atribua um nome e clique em **OK**.

	![](media/cdn-websites-with-cdn/1-new-project.png)

3. Selecione **MVC** e clique em **Gerenciar Assinaturas**.

	![](media/cdn-websites-with-cdn/2-webapp-template.png)

4. Clique em **Entrar**.

	![](media/cdn-websites-with-cdn/3-manage-subscription.png)

6. Na página de entrada, entre com a conta da Microsoft que você utilizou para ativar a conta do Azure.
7. Após ter entrado, clique em **Fechar**. Em seguida, clique em **OK** para continuar.

	![](media/cdn-websites-with-cdn/4-signed-in.png)

8. Supondo que você não criou um aplicativo Web do Azure, o Visual Studio pode ajudá-lo a criar esse site. Na caixa de diálogo **Configurar o site do Microsoft Azure**, verifique se o que nome do site é exclusivo. Em seguida, clique em **OK**.

	![](media/cdn-websites-with-cdn/5-create-website.png)

9. Depois de criar seu aplicativo ASP.NET, publique-o no Azure no painel Atividade de Publicação na Web, clicando em **Publicar `<nome_do_aplicativo>` para esse site agora**. Clique em **Publicar** para concluir o processo.

	![](media/cdn-websites-with-cdn/6-publish-website.png)

	Quando a publicação estiver concluída, você verá seu aplicativo Web publicado no navegador. 

1. Para criar um ponto de extremidade da CDN, faça logon no [portal de gerenciamento do Azure](http://manage.windowsazure.com/). 
2. Clique em **Novo** > **Serviços de Aplicativos** > **CDN** > **Criação Rápida**. Selecione **http://*&lt;nome_do_site >*.azurewebsites.net/** e clique em **Criar**.

	![](media/cdn-websites-with-cdn/7-create-cdn.png)

	> [AZURE.NOTE] Após a criação do ponto de extremidade da CDN, o portal do Azure mostrará sua URL e o domínio de origem ao qual ele está integrado. No entanto, pode levar algum tempo para que a configuração do novo ponto de extremidade da CDN seja totalmente propagada a todos os locais de nó de CDN. 

3. No portal do Azure, na guia **CDN**, clique no nome do ponto de extremidade da CDN que você acabou de criar.

	![](media/cdn-websites-with-cdn/8-select-cdn.png)

3. Clique em **Habilitar Cadeia de Consulta** para habilitar cadeias de consulta no cache da CDN. Após você ter habilitado, o mesmo link acessado com diferentes cadeias de consulta será armazenado em cache como entradas diferentes.

	![](media/cdn-websites-with-cdn/9-enable-query-string.png)

	>[AZURE.NOTE] Embora habilitar a cadeia de consulta não seja necessário para esta seção do tutorial, é aconselhável fazer isso o quanto antes para maior conveniência, uma vez que qualquer alteração aqui levará tempo para ser propagada para todos os nós de CDN, e você não quer um acúmulo de conteúdo não habilitado para cadeia de consulta no cache da CDN (a atualização do conteúdo da CDN será abordada mais adiante).

2. Agora, clique no endereço do ponto de extremidade da CDN. Se o ponto de extremidade estiver pronto, você verá seu aplicativo Web exibido. Se você receber um erro **HTTP 404**, isso significa que o ponto de extremidade da CDN não está pronto. Talvez seja necessário aguardar até uma hora para a configuração de CDN ser propagada para todos os nós próximos aos limites. 

	![](media/cdn-websites-with-cdn/11-access-success.png)

1. Em seguida, tente acessar o arquivo **~/Content/bootstrap.css** em seu projeto ASP.NET. Na janela do navegador, vá até **http://*&lt;nome_da_CDN>*.vo.msecnd.net/Content/bootstrap.css**. Em minha configuração, essa URL é:

		http://az673227.vo.msecnd.net/Content/bootstrap.css

	Que corresponde à seguinte URL de origem no ponto de extremidade da CDN:

		http://cdnwebapp.azurewebsites.net/Content/bootstrap.css

	Quando você navegar para **http://*&lt;<cdnName>*.vo.msecnd.net/Content/bootstrap.css**, será solicitado que você baixe o bootstrap.css que veio de seu aplicativo Web publicado. 

	![](media/cdn-websites-with-cdn/12-file-access.png)

De modo semelhante, você pode acessar qualquer URL publicamente acessível em **http://*&lt;serviceName>*.cloudapp.net/**, diretamente do seu ponto de extremidade da CDN. Por exemplo:

-	Um arquivo .js do caminho /Script
-	Qualquer arquivo de conteúdo do caminho /Content
-	Qualquer controller/action 
-	Se cadeias de consulta estiverem habilitadas em seu ponto de extremidade da CDN, qualquer URL com cadeias de consulta
-	Todo o aplicativo Web, se todo o conteúdo é público

Observe que talvez pode não ser sempre uma boa ideia (ou geralmente uma boa ideia) fornecer conteúdo a um aplicativo Web inteiro por meio da CDN do Azure. Alguns dos empecilhos são:

-	Esta abordagem requer que todo o seu site seja público, por que a CDN do Azure não pode fornecer nenhum conteúdo privado.
-	Se o ponto de extremidade da CDN ficar offline por qualquer motivo, seja para uma manutenção programada ou por erro do usuário, todo o seu aplicativo Web ficará offline a menos que os clientes possam ser redirecionados à URL de origem, **http://*&lt;sitename>*.azurewebsites.net/**. 
-	Mesmo com as configurações personalizadas de controle de cache (consulte [Configurar opções de cache para arquivos estáticos em seu aplicativo Web](#caching)), um ponto de extremidade da CDN não melhora o desempenho de conteúdo altamente dinâmico. Se você tiver tentado carregar a página inicial de seu ponto de extremidade da CDN conforme mostrado acima, observe que foram necessários pelo menos 5 segundos para carregar pela primeira vez a página inicial, que é uma página bem simples. Imagine o que aconteceria com a experiência do cliente se esta página tivesse conteúdo dinâmico que precisasse ser atualizado a cada minuto. Fornecer conteúdo dinâmico de um ponto de extremidade da CDN requer uma validade curta do cache, o que significa frequentes perdas de cache no ponto de extremidade da CDN. Isso prejudica o desempenho de seu aplicativo Web e frustra o propósito de uma CDN.

A alternativa é determinar que conteúdo fornecer por meio da CDN do Azure caso a caso, em seu aplicativo Web. Para isso, você já viu como acessar arquivos de conteúdo individuais do ponto de extremidade da CDN. Você verá como fornecer uma ação de controlador específica por meio do ponto de extremidade da CDN em [Fornecer conteúdo por meio de ações do controlador por meio da CDN do Azure](#controller).

<a name="caching"></a>
## Configurar opções de cache para arquivos estáticos em seu aplicativo Web ##

Com a integração da CDN do Azure a seu aplicativo Web, você pode especificar como quer que o conteúdo estático seja armazenado em cache no ponto de extremidade da CDN. Para fazer isso, abra *Web.config* do seu projeto ASP.NET (por exemplo, **cdnwebapp**) e adicione um elemento `<staticContent>` a `<System. webServer>`. O XML configura o cache para expirar em 3 dias.  
<pre class="prettyprint">
&lt;system.webServer&gt;
  <mark>&lt;staticContent&gt;
    &lt;clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/&gt;
  &lt;/staticContent&gt;</mark>
  ...
&lt;/system.webServer&gt;
</pre>

Após você ter feito isso, todos os arquivos estáticos do aplicativo Web obedecerão à mesma regra do seu cache de CDN. Para ter um controle mais granular das configurações de cache, adicione um arquivo *Web.config* a uma pasta e adicione suas configurações. Por exemplo, adicione um arquivo *Web.config* à pasta *\Content* e substitua o conteúdo pelo seguinte XML:

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

Esta configuração faz com que todos os arquivos estáticos da pasta *\Content* sejam armazenados em cache por 15 dias.

Para obter mais informações sobre como configurar o elemento `<clientCache>`, consulte [Cache do cliente &lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

Em [Fornecer conteúdo por meio de ações do controlador por meio da CDN do Azure](#controller), também mostrarei como você pode definir as configurações de cache para os resultados de ações do controlados no cache de CDN.

<a name="controller"></a>
## Fornecer conteúdo por meio de ações do controlador por meio da CDN do Azure ##

Ao integrar o Aplicativo Web à CDN do Azure, é relativamente fácil fornecer conteúdo por meio de ações do controlador, por meio da CDN do Azure. Novamente, se você decidir fornecer conteúdo a todo o aplicativo Web por meio de sua CDN, não precisará fazer nada isso, visto que todas as ações do controlador já estarão acessíveis por meio da CDN. Mas, pelas razões que já mencionei em [Implantar um site do Azure com um ponto de extremidade da CDN integrado](#deploy), você pode decidir em não fazer isso e optar por selecionar a ação de controlador que deseja fornecer na CDN do Azure. [Maarten Balliauw](https://twitter.com/maartenballiauw) mostra como fazer isso com um divertido controlador MemeGenerator em [Reduzindo a latência na Web com a CDN do Microsoft Azure](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). Eu vou somente reproduzir isso aqui.

Suponha que, em seu aplicativo Web, você queira gerar memes com base em uma imagem de Chuck Norris jovem (foto de [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)) como esta:

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Você tem uma ação `Index` simples que permite que os clientes especifiquem os superlativos na imagem e gera o meme quando eles postam a ação. Como se trata de Chuck Norris, você esperaria que esta página se tornasse extremamente popular globalmente. Este é um bom exemplo do fornecimento de conteúdo semidinâmico com a CDN do Azure. 

Siga as etapas acima para configurar esta ação do controlador:

1. Na pasta *\Controllers*, crie um novo arquivo .cs chamado *MemeGeneratorController.cs* e substitua o conteúdo pelo código a seguir. Certifique-se de substituir a porção destacada pelo nome de sua CDN e caminho do arquivo.
	<pre class="prettyprint">
	using System;
	using System.Collections.Generic;
	using System.Diagnostics;
	using System.Drawing;
	using System.IO;
	using System.Net;
	using System.Web.Hosting;
	using System.Web.Mvc;
	using System.Web.UI;
	
	namespace cdnwebapp.Controllers
	{
	    public class MemeGeneratorController : Controller
	    {
	        static readonly Dictionary&lt;string, Tuple&lt;string ,string&gt;&gt; Memes = new Dictionary&lt;string, Tuple&lt;string, string&gt;&gt;();

	        public ActionResult Index()
	        {
	            return View();
	        }
	
	        [HttpPost, ActionName(&quot;Index&quot;)]
        	public ActionResult Index_Post(string top, string bottom)
	        {
	            var identifier = Guid.NewGuid().ToString();
	            if (!Memes.ContainsKey(identifier))
	            {
	                Memes.Add(identifier, new Tuple&lt;string, string&gt;(top, bottom));
	            }
	
	            return Content(&quot;&lt;a href=\&quot;&quot; + Url.Action(&quot;Show&quot;, new {id = identifier}) + &quot;\&quot;&gt;here&#39;s your meme&lt;/a&gt;&quot;);
	        }


	        [OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Show(string id)
	        {
	            Tuple&lt;string, string&gt; data = null;
	            if (!Memes.TryGetValue(id, out data))
	            {
	                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
	            }
	
	            if (Debugger.IsAttached) // Preserve the debug experience
	            {
	                return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
	            }
	            else // Get content from Azure CDN
	            {
	                return Redirect(string.Format(&quot;http://<mark>&lt;yourCDNName&gt;</mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}&quot;, data.Item1, data.Item2));
	            }
	        }

	        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Generate(string top, string bottom)
	        {
	            string imageFilePath = HostingEnvironment.MapPath(&quot;<mark>~/Content/chuck.bmp</mark>&quot;);
	            Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);
	
	            using (Graphics graphics = Graphics.FromImage(bitmap))
	            {
	                SizeF size = new SizeF();
	                using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
	                {
	                    graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
	                }
	                using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
	                {
	                    graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
	                }
	            }
	
	            MemoryStream ms = new MemoryStream();
	            bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
	            return File(ms.ToArray(), &quot;image/png&quot;);
	        }
	
	        private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
	        {
	            // Compute actual size, shrink if needed
	            while (true)
	            {
	                size = g.MeasureString(text, font);
	
	                // It fits, back out
	                if (size.Height &lt; i.Height &amp;&amp;
	                     size.Width &lt; i.Width) { return font; }
	
	                // Try a smaller font (90% of old size)
	                Font oldFont = font;
	                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
	                oldFont.Dispose();
	            }
	        }
	    }
	}
	</pre>

2. Clique com o botão direito na ação `Index()` padrão e selecione **Adicionar Exibição**.

	![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  Aceite as configurações acima e clique em **Adicionar**.

	![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. Abra o novo *Views\MemeGenerator\Index.cshtml* e substitua o conteúdo pelo seguinte HTML simples para enviar os superlativos:

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. Publique o aplicativo Web do Azure novamente e navegue até **http://*&lt;serviceName>*.cloudapp.net/MemeGenerator/Index** em seu navegador. 

Quando você envia os valores de formulário a `/MemeGenerator/Index`, o método de ação `Index_Post` retorna um link para o método de ação `Show` com o respectivo identificador da entrada. Quando clicar em senha, você chegará ao seguinte código:  
<pre class="prettyprint">
[OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
public ActionResult Show(string id)
{
    Tuple&lt;string, string&gt; data = null;
    if (!Memes.TryGetValue(id, out data))
    {
        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
    }

    if (Debugger.IsAttached) // Preserve the debug experience
    {
        return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
    else // Get content from Azure CDN
    {
        return Redirect(string.Format(&quot;http://<mark>&lt;yourCDNName&gt;</mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}&quot;, data.Item1, data.Item2));
    }
}
</pre>

Se o seu depurador local estiver anexado, você terá a experiência de depuração normal com um redirecionamento local. Se estiver sendo executado no aplicativo Web, será redirecionado para:

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Que corresponde ao seguinte URL de origem no ponto de extremidade da CDN:

	http://<yourSiteName>.azurewebsites.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Após a regra de reescrita de URL aplicada anteriormente, o arquivo que é de fato armazenado em cache em meu ponto de extremidade da CDN é:

	http://<yourSiteName>.azurewebsites.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Você pode, então, usar o atributo `OutputCacheAttribute` no método `Generate` para especificar como o resultado da ação deve ser armazenado em cache, e a CDN do Azure seguirá a especificação. O código abaixo especifica uma validade de cache de 1 hora (3.600 segundos).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Do mesmo modo, você pode fornecer conteúdo por meio de qualquer ação de controlador em seu aplicativo Web por meio da CDN do Azure, com a opção de cache desejada.

Na próxima seção, eu mostrarei como fornecer o CSS e scripts agrupados e minificados por meio da CDN do Azure. 

<a name="bundling"></a>
## Integrar agrupamento e minificação ASP.NET à CDN do Azure ##

Scripts e folhas de estilo CSS são alterados com pouca frequência e são candidatos ideais para o cache da CDN do Azure. Fornecer todo o aplicativo Web por meio da CDN do Azure é a maneira mais fácil de integrar agrupamento e minificação à CDN do Azure. No entanto, já que você pode decidir contra essa abordagem pelos motivos descritos em [Integrar um ponto de extremidade da CDN do Azure a seu site do Azure e fornecer conteúdo estático em suas páginas Web por meio da CDN do Azure](#deploy), vou mostrar a você como fazer isso, preservando as experiências de desenvolvedor desejada de agrupamento e minificação do ASP.NET, tais como:

-	Ótima experiência no modo de depuração
-	Implantação simplificada
-	Atualizações imediatas para clientes para atualizações de versão de script/CSS
-	Mecanismo de fallback quando ocorre falha em seu ponto de extremidade CDN
-	Minimizar modificações de código

No projeto ASP.NET que você criou em [Integrar um ponto de extremidade da CDN do Azure a seu aplicativo Web do Azure e fornecer conteúdo estático em suas páginas da Web da CDN do Azure](#deploy), abra *App_Start\BundleConfig.cs* e examine as chamadas de método `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

A primeira instrução `bundles.Add()` adiciona um pacote de scripts no diretório virtual `~/bundles/jquery`. Em seguida, abra *Views\Shared\_Layout.cshtml* para ver como a marca de pacote de scripts é processada. Você deve encontrar a seguinte linha de código Razor:

    @Scripts.Render("~/bundles/jquery")

Quando for executado no aplicativo Web do Azure, este código Razor renderizará uma marca `<script>` para o grupo de scripts semelhante ao seguinte: 

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

No entanto, quando for executado no Visual Studio digitando `F5`, ele renderizará cada arquivo de script do grupo individualmente (no caso acima, somente o arquivo de script está no grupo):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Isso permite que você depure o código JavaScript em seu ambiente de desenvolvimento enquanto reduz conexões clientes simultâneas (agrupamento) e melhora o desempenho do download de arquivos (minificação) em produção. É um ótimo recurso a ser preservado com a integração à CDN do Azure. Além disso, como o grupo renderizado já contém uma cadeia da versão gerada automaticamente, é aconselhável replicar essa funcionalidade para que sempre que você atualizar sua versão do jQuery por meio do NuGet, ela possa ser atualizada no lado do cliente assim que possível.

Siga as etapas abaixo para integrar agrupamento e minificação ASP.NET ao ponto de extremidade CDN.

1. Em *App_Start\BundleConfig.cs*, modifique os métodos `bundles.Add()` para usar um [Construtor de pacotes](http://msdn.microsoft.com/library/jj646464.aspx) diferente, que especifique um endereço de CDN. Para fazer isso, substitua a definição do método `RegisterBundles` pelo seguinte código:  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    <mark>bundles.UseCdn = true;
	    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://&lt;yourCDNName&gt;.vo.msecnd.net/{0}?v=&quot; + version;</mark>
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jquery&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;<mark>, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)</mark>).Include(
	                &quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;<mark>, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)</mark>).Include(
	                &quot;~/Scripts/bootstrap.js&quot;,
	                &quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;<mark>, string.Format(cdnUrl, &quot;Content/css&quot;)</mark>).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}
	</pre>

	Substitua `<nome_de_sua_CDN>` pelo nome da sua CDN do Azure.

	De maneira simples, você está definindo `bundles.UseCdn = true` e adicionou uma URL da CDN criada cuidadosamente para cada grupo. Por exemplo, o primeiro construtor do código:

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	é igual a: 

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	Este construtor instrui o agrupamento e minificação ASP.NET a renderizar arquivos de script individuais quando depurados localmente, mas usar o endereço CDN especificado para acessar o script em questão. No entanto, observe duas importantes características dessa URL da CDN criada cuidadosamente:
	
	-	A origem do URL da CDN é `http://<yourSiteName>.azurewebsites.net/bundles/jquery?v=<W.X.Y.Z>`, que na verdade é o diretório virtual do grupo de scripts em seu aplicativo Web.
	-	Como você está usando um construtor CDN, a marcação do script CDN para o grupo não contém mais a cadeia da versão gerada automaticamente na URL renderizada. Você deve gerar manualmente uma cadeia de versão única sempre que o grupo de scripts for modificado para gerar uma perda de cache em sua CDN do Azure. Ao mesmo tempo, essa cadeia de versão única deve permanecer constante ao longo da vida útil da implantação para maximizar as ocorrências no cache na CDN do Azure após a implantação do grupo.
	-	A cadeia de caracteres de consulta v=<W.X.Y.Z> extrai de *Properties\AssemblyInfo.cs* em seu projeto do ASP.NET. Você pode ter um fluxo de trabalho de implantação que inclua o incremento da versão de assembly sempre que você publicar no Azure. Ou pode apenar modificar  *Properties\AssemblyInfo.cs* em seu projeto para incrementar automaticamente a cadeia da versão sempre que compilar, usando o caractere curinga '*'. Por exemplo:
	
			[assembly: AssemblyVersion("1.0.0.*")]
	
		Qualquer outra estratégia para simplificar a geração de uma cadeia única para a vida útil de uma implantação funcionará aqui.

3. Republique o aplicativo ASP.NET e acesse a página inicial.
 
4. Exiba o código HTML da página. Você deverá ver a URL da CDN renderizada, com uma cadeia de versão única sempre que republicar mudanças em seu aplicativo Web do Azure. Por exemplo:  
	<pre class="prettyprint">
	...

    &lt;link href=&quot;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25449&quot; rel=&quot;stylesheet&quot;/&gt;

    &lt;script src=&quot;http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...

    &lt;script src=&quot;http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

    &lt;script src=&quot;http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...</pre>

5. No Visual Studio, depure o aplicativo ASP.NET em Visual Studio, digitando `F5`, 

6. Exiba o código HTML da página. Você ainda verá cada arquivo de script renderizado individualmente, para que possa ter uma experiência de depuração consistente no Visual Studio.  
	<pre class="prettyprint">
	...
	
	    &lt;link href=&quot;/Content/bootstrap.css&quot; rel=&quot;stylesheet&quot;/&gt;
	&lt;link href=&quot;/Content/site.css&quot; rel=&quot;stylesheet&quot;/&gt;
	
	    &lt;script src=&quot;/Scripts/modernizr-2.6.2.js&quot;&gt;&lt;/script&gt;
	
	...
	
	    &lt;script src=&quot;/Scripts/jquery-1.10.2.js&quot;&gt;&lt;/script&gt;
	
	    &lt;script src=&quot;/Scripts/bootstrap.js&quot;&gt;&lt;/script&gt;
	&lt;script src=&quot;/Scripts/respond.js&quot;&gt;&lt;/script&gt;
	
	...    
	</pre>

<a name="fallback"></a>
## Mecanismo de fallback para URLs da CDN ##

Quando seu ponto de extremidade da CDN do Azure falhar por qualquer motivo, é melhor que sua página da Web seja inteligente o suficiente para acessar o servidor Web de origem como a opção de fallback para carregar JavaScript ou Bootstrap. Perder imagens do aplicativo Web devido à indisponibilidade da CDN já é grave, mas perder funcionalidades fundamentais da página fornecidas por seus scripts e folhas de estilo é ainda muito mais.

A classe [Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) contém uma propriedade chamada [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) que permite que você configure o mecanismo de fallback para falha da CDN. Para usar essa propriedade, siga as etapas abaixo:

1. Em seu projeto ASP.NET, abra *App_Start\BundleConfig.cs*, em que você adicionou uma URL da CDN em cada [Construtor de pacotes](http://msdn.microsoft.com/library/jj646464.aspx) e faça as seguintes alterações realçadas para adicionar o mecanismo de fallback aos pacotes padrão:  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://cdnurl.vo.msecnd.net/.../{0}?&quot; + version;
	    bundles.UseCdn = true;
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;, string.Format(cdnUrl, &quot;bundles/jquery&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.jquery&quot; }</mark>
	                .Include(&quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;$.validator&quot; }</mark>
	            	.Include(&quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.Modernizr&quot; }</mark>
					.Include(&quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)) 	
					<mark>{ CdnFallbackExpression = &quot;$.fn.modal&quot; }</mark>
	        		.Include(
		              		&quot;~/Scripts/bootstrap.js&quot;,
		              		&quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;, string.Format(cdnUrl, &quot;Content/css&quot;)).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}</pre>

	Quando `CdnFallbackExpression` não é nulo, o script é injetado no HTML para testar se o grupo é carregado com sucesso e, se não, acessar o grupo diretamente por meio do servidor Web de origem. Esta propriedade precisa ser definida como uma expressão JavaScript que testa se o grupo CDN respectivo é carregado corretamente. A expressão necessária para testar cada grupo difere de acordo com o conteúdo. Para os grupos padrão acima:
	
	-	`window.jquery` é definido em jquery-{version}.js
	-	`$.validator` é definido em jquery.validate.js
	-	`window.Modernizr` é definido em modernizer-{version}.js
	-	`$.fn.modal` é definido em bootstrap.js
	
	Você pode ter notado que não defini CdnFallbackExpression para o grupo `~/Cointent/css`. Isso ocorre porque no momento há um [bug no System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) que injeta uma marca `<script>` para o CSS de fallback em vez da marca `<link>` esperada.
	
	Há, no entanto, um bom [Fallback de Grupo de Estilo](https://github.com/EmberConsultingGroup/StyleBundleFallback) oferecido pelo [Ember Consulting Group](https://github.com/EmberConsultingGroup). 

2. Para usar a solução alternativa para o CSS, crie um novo arquivo .cs na pasta  *App_Start* chamado *StyleBundleExtensions.cs*e substitua seu conteúdo pelo [código do GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs). 

4. Em *App_Start\StyleFundleExtensions.cs*, renomeie o namespace para o namespace de seu aplicativo ASP.NET (por exemplo, **cdnwebapp**). 

3. Volte para `App_Start\BundleConfig.cs` e modifique a última instrução `bundles.Add` com o seguinte código em destaque:  
	<pre class="prettyprint">
	bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
	    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
	    .Include(
	          "~/Content/bootstrap.css",
	          "~/Content/site.css"));
	</pre>

	Este novo método de extensão usa a mesma ideia para injetar script no HTML para verificar o DOM para um nome de classe, nome de regra e valor de regra correspondente no grupo CSS, e retorna ao servidor Web original se não encontrar um resultado correspondente.

4. Republique seu aplicativo Web do Azure e acesse a página inicial. 
5. Exiba o código HTML da página. Você deve encontrar scripts injetados semelhantes ao seguinte:    
	<pre class="prettyprint">...
	
		&lt;link href=&quot;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474&quot; rel=&quot;stylesheet&quot;/&gt;
	<mark>&lt;script&gt;(function() {
	                var loadFallback,
	                    len = document.styleSheets.length;
	                for (var i = 0; i &lt; len; i++) {
	                    var sheet = document.styleSheets[i];
	                    if (sheet.href.indexOf(&#39;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474&#39;) !== -1) {
	                        var meta = document.createElement(&#39;meta&#39;);
	                        meta.className = &#39;sr-only&#39;;
	                        document.head.appendChild(meta);
	                        var value = window.getComputedStyle(meta).getPropertyValue(&#39;width&#39;);
	                        document.head.removeChild(meta);
	                        if (value !== &#39;1px&#39;) {
	                            document.write(&#39;&lt;link href=&quot;/Content/css&quot; rel=&quot;stylesheet&quot; type=&quot;text/css&quot; /&gt;&#39;);
	                        }
	                    }
	                }
	                return true;
	            }())||document.write(&#39;&lt;script src=&quot;/Content/css&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	    &lt;script src=&quot;http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;(window.Modernizr)||document.write(&#39;&lt;script src=&quot;/bundles/modernizr&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	...	
	
	    &lt;script src=&quot;http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;(window.jquery)||document.write(&#39;&lt;script src=&quot;/bundles/jquery&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	    &lt;script src=&quot;http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;($.fn.modal)||document.write(&#39;&lt;script src=&quot;/bundles/bootstrap&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	...
	</pre>

	Observe que o script injetado para o grupo CSS ainda contém o excedente errante da propriedade `CdnFallbackExpression` na linha:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

	Mas como a primeira parte da expressão || sempre retornará o valor verdadeiro (na linha diretamente acima), a função document.write() nunca será executada.

6. Para testar se o script de fallback está funcionando, vá até o painel de controle do ponto de extremidade da CDN e clique em **Desabilitar Ponto de Extremidade**.

	![](media/cdn-websites-with-cdn/13-test-fallback.png)

7. Atualize a janela do navegador para o aplicativo Web do Azure. Agora você deve ver todos os scripts e folhas de estilo carregados corretamente.

# Mais informações #
- [Visão geral da CDN (Rede de Distribuição de Conteúdo) do Azure](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [Fornecer conteúdo da CDN do Azure em seu aplicativo Web](cdn-serve-content-from-cdn-in-your-web-application.md)
- [Integrar um serviço de nuvem à CDN do Azure](cdn-cloud-service-with-cdn.md)
- [Agrupamento e minificação ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Usando a CDN para Azure](cdn-how-to-use.md)

<!--HONumber=49-->