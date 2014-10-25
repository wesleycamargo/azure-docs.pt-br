<properties linkid="cdn-cloud-service-with-cdn" urlDisplayName="Integrate a cloud service with Azure CDN" pageTitle="Integrate a cloud service with Azure CDN" metaKeywords="Azure tutorial, Azure web app tutorial, ASP.NET, CDN, MVC, cloud service" description="A tutorial that teaches you how to deploy a cloud service that serves content from an integrated Azure CDN endpoint" metaCanonical="" services="cdn,cloud-services" documentationCenter=".NET" title="Integrate a cloud service with Azure CDN" authors="cephalin" solutions="" manager="wpickett" editor="tysonn" />

<tags ms.service="cdn" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

<a name="intro"></a>

# Integrar um serviço de nuvem à CDN do Azure

***Atualizado em 23 de julho de 2014***

A CDN do Azure pode ser integrada a um serviço de nuvem, fornecendo qualquer conteúdo do diretório CDN do serviço de nuvem. Esta abordagem lhe dá as seguintes vantagens:

-   Implantar e atualizar, de maneira fácil, imagens, scripts e folhas de estilo nos diretórios de projetos de seu serviço de nuvem
-   Atualizar de maneira fácil os pacotes NuGet em seu serviço de nuvem, como as versões jQuery ou Bootstrap
-   Gerenciar seu aplicativo Web e o conteúdo fornecido por CDN por meio da mesma interface do Visual Studio
-   Fluxo de trabalho de implantação unificado para seu aplicativo Web e o conteúdo fornecido por CDN
-   Integrar agrupamento e minificação ASP.NET à CDN do Azure

## O que você aprenderá

Neste tutorial, você aprenderá a:

-   [Integrar um ponto de extremidade do Azure a seu serviço de nuvem e fornecer conteúdo estático em suas páginas Web por meio da CDN do Azure][Integrar um ponto de extremidade do Azure a seu serviço de nuvem e fornecer conteúdo estático em suas páginas Web por meio da CDN do Azure]
-   [Definir configurações de cache para conteúdo estático em seu serviço de nuvem][Definir configurações de cache para conteúdo estático em seu serviço de nuvem]
-   [Fornecer conteúdo por meio de ações do controlador por meio da CDN do Azure][Fornecer conteúdo por meio de ações do controlador por meio da CDN do Azure]
-   [Fornecer conteúdo agrupado e minificado por meio da CDN do Azure enquanto preserva a experiência de depuração de script no Visual Studio][Fornecer conteúdo agrupado e minificado por meio da CDN do Azure enquanto preserva a experiência de depuração de script no Visual Studio]
-   [Configurar o fallback de seus scripts e CSS quando a CDN do Azure estiver offline][Configurar o fallback de seus scripts e CSS quando a CDN do Azure estiver offline]

## O que você compilará

Você implantará uma função Web de serviço de nuvem usando o modelo ASP.NET MVC padrão, adicionará código para fornecer conteúdo de uma CDN do Azure integrada, como uma imagem, resultados de ação do controlador e os arquivos JavaScript e CSS padrão, e também escreverá código para configurar o mecanismo de fallback para grupos fornecidos caso o CDN esteja offline.

## O que será necessário

Este tutorial tem os seguintes pré-requisitos:

-   Uma [conta do Microsoft Azure][conta do Microsoft Azure] ativa
-   Visual Studio 2013 com o [SDK do Azure][SDK do Azure]

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure:</h5>
  <ul>
    <li>Voc&ecirc; pode <a href="http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=A261C142F">abrir uma conta do Azure gratuitamente</a> - Voc&ecirc; obt&eacute;m cr&eacute;ditos que podem ser usados para experimentar servi&ccedil;os pagos do Azure e, mesmo ap&oacute;s eles serem utilizados, voc&ecirc; pode manter a conta e utilizar os servi&ccedil;os gratuitos do Azure, como os Sites.</li>
    <li>Voc&ecirc; pode <a href="http://azure.microsoft.com/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">ativar benef&iacute;cios para assinantes do MSDN</a> - Todos os meses, sua assinatura do MSDN oferece cr&eacute;ditos que podem ser usados para servi&ccedil;os pagos do Azure.</li>
  <ul>
</div>

<a name="deploy"></a>

## Implantar um serviço de nuvem com um ponto de extremidade CDN integrado

Nesta seção, você implantará o modelo de aplicativo ASP.NET MVC padrão no Visual Studio 2013 em uma função Web de serviço de nuvem e, depois, o integrará a um novo ponto de extremidade CDN. Siga as instruções abaixo:

1.  No Visual Studio 2013, crie um novo serviço de nuvem do Azure por meio da barra de menu indo para **Arquivo \> Novo \> Projeto \> Nuvem \> Serviço de Nuvem do Microsoft Azure**. Dê um nome a ele e clique em **OK**.

    ![][]

2.  Selecione **Função Web ASP.NET** e clique no botão **\>**. Clique em OK.

    ![][1]

3.  Selecione **MVC** e clique em **OK**.

    ![][2]

4.  Agora, publique esta função Web em um serviço de nuvem do Azure. Clique com o botão direito do mouse no projeto do serviço de nuvem e selecione **Publicar**.

    ![][3]

5.  Se ainda não tiver entrado no Microsoft Azure, clique no botão **Entrar**.

    ![][4]

6.  Na página de entrada, entre com a conta da Microsoft que você utilizou para ativar a conta do Azure.
7.  Após ter entrado, clique em **Avançar**.

    ![][5]

8.  Se você ainda não tiver criado uma conta de serviço de nuvem ou armazenamento, o Visual Studio o ajudará a criar ambas. Na caixa de diálogo **Criar Serviço de Nuvem e Conta**, digite o nome do serviço desejado. Depois, clique em **Criar**.

    ![][6]

    > [WACOM.NOTE]Observe que estou usando **Ásia Oriental** como região deste serviço de nuvem. Minha meta aqui é garantir que o serviço de nuvem (e o conteúdo na conta de armazenamento) esteja longe o suficiente do cliente (neste caso, meu computador nos EUA) para testar o CDN mais tarde.

9.  Na página de configurações de publicação, verifique a configuração e clique em **Publicar**.

    ![][7]

    > [WACOM.NOTE] O processo de publicação para serviços de nuvem leva muito tempo. A opção Habilitar Implantação da Web para todas as funções pode acelerar muito a depuração do serviço de nuvem fornecendo atualizações rápidas (mas temporárias) para suas funções Web. Para obter mais informações sobre esta opção, consulte [Publicando um Serviço de Nuvem usando as Ferramentas do Azure][Publicando um Serviço de Nuvem usando as Ferramentas do Azure].

    Quando o **Log de atividade do Microsoft Azure** mostrar que o status de publicação é **Concluída**, você criará um ponto de extremidade CDN integrado a este serviço de nuvem.

10. Para criar um ponto de extremidade CDN, faça logon no [portal de gerenciamento do Azure][portal de gerenciamento do Azure].
11. Clique em **Novo** \> **Serviços de Aplicativos** \> **CDN** \> **Criação Rápida**. Selecione **http://*\<nomedoserviço\>*.cloudapp.net/cdn/** e clique em **Criar**.

    ![][8]

    > [WACOM.NOTE] Após a criação do ponto de extremidade CDN, o portal do Azure mostrará seu URL e o domínio de origem ao qual ele está integrado. No entanto, pode levar algum tempo para que a configuração do novo ponto de extremidade CDN seja totalmente propagada a todos os locais de nó de CDN.

    Observe que o ponto de extremidade CDN está vinculado ao caminho **cdn/** do serviço de nuvem. Você pode criar uma pasta **cdn** em seu projeto **FunçãoDaWeb1** ou usar o URL reescrito para remover todos o links de entrada deste caminho. Neste tutorial, você adotará a segunda opção.

12. No portal do Azure, na guia **CDN**, clique no nome do ponto de extremidade CDN que você acabou de criar.

    ![][9]

13. Clique em **Habilitar Cadeia de Consulta** para habilitar cadeias de consulta no cache do CDN. Após você ter habilitado, o mesmo link acessado com diferentes cadeias de consulta será armazenado em cache como entradas diferentes.

    ![][10]

    > [WACOM.NOTE] Embora habilitar a cadeia de consulta não seja necessário para esta seção do tutorial, é aconselhável fazer isso o quanto antes para maior conveniência, uma vez que qualquer alteração aqui levará tempo para ser propagada para todos os nós de CDN, e você não quer um acúmulo de conteúdo não habilitado para cadeia de consulta no cache do CDN (a atualização do conteúdo CDN será abordada mais adiante).

14. Execute o ping de seu ponto de extremidade CDN para garantir que ele esteja propagado para os nós de CDN. Talvez seja necessário esperar por até uma hora antes dele responder a pings.

    ![][11]

15. No Visual Studio 2013, abra **Web.config** no projeto **FunçãoDaWeb1** e adicione o seguinte código à marcação `<system.webServer>`:

	<pre class="prettyprint">
	&lt;system.webServer&gt;
	  <mark>&lt;rewrite&gt;
	    &lt;rules&gt;
	      &lt;rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;&gt;
	        &lt;match url=&quot;^cdn/(.*)$&quot;/&gt;
	        &lt;action type=&quot;Rewrite&quot; url=&quot;{R:1}&quot;/&gt;
	      &lt;/rule&gt;
	    &lt;/rules&gt;
	  &lt;/rewrite&gt;</mark>
      ...
	&lt;/system.webServer&gt;
	</pre>

16. Publique o serviço de nuvem novamente. Clique com o botão direito do mouse no projeto do serviço de nuvem e selecione **Publicar**.

    ![][3]

17. Quando o status da publicação for **Concluída**, abra uma janela do navegador e navegue até **http://*\<cdnName\>*.vo.msecnd.net/Content/bootstrap.css**. Em minha configuração, o URL é:

        http://az632148.vo.msecnd.net/Content/bootstrap.css

    Que corresponde ao seguinte URL de origem no ponto de extremidade CDN:

        http://cephalinservice.cloudapp.net/cdn/Content/bootstrap.css

    Após reescrever o URL no aplicativo da Web, o arquivo que é de fato armazenado em meu cache CDN é:

        http://cephalinservice.cloudapp.net/Content/bootstrap.css

    Quando navegar até **<http://>*\<cdnName\>*.vo.msecnd.net/Content/bootstrap.css**, você será solicitado a baixar o bootstrap.css que veio ao aplicativo da Web publicado.

    ![][12]

De maneira semelhante, você pode acessar qualquer URL acessível publicamente em **http://*\<serviceName\>*.cloudapp.net/**, diretamente de seu ponto de extremidade CDN. Por exemplo:

-   Um arquivo .js do caminho /Script
-   Qualquer arquivo de conteúdo do caminho /Content
-   Qualquer controller/action
-   Se cadeias de consulta estiverem habilitadas em seu ponto de extremidade CDN, qualquer URL com cadeias de consulta

De fato, com a configuração acima, você pode hospedar todo o serviço de nuvem por meio de **http://*\<cdnName\>*.vo.msecnd.net/**. Se navegar até **<http://az632148.vo.msecnd.net/>**, eu obtenho o resultado da ação de Home/Index.

![][13]

No entanto, isso não significa que sempre é uma boa ideia (ou uma boa ideia de modo geral) fornecer um serviço de nuvem inteiro por meio da CDN do Azure. Alguns dos empecilhos são:

-   Esta abordagem requer que todo o seu site seja público, por que a CDN do Azure não pode fornecer nenhum conteúdo privado.
-   Se o ponto de extremidade CDN ficar offline por qualquer motivo, seja para uma manutenção programada ou por erro do usuário, todo o seu serviço de nuvem ficará offline a menos que os clientes possam ser redirecionados ao URL de origem **http://*\<serviceName\>*.cloudapp.net/**.
-   Mesmo com as configurações personalizadas de controle de cache (consulte [Configurar opções de cache para arquivos estáticos em seu serviço de nuvem][Definir configurações de cache para conteúdo estático em seu serviço de nuvem]), um ponto de extremidade CDN não melhora o desempenho de conteúdo altamente dinâmico. Se você tiver tentado carregar a página inicial de seu ponto de extremidade CDN conforme mostrado acima, observe que foram necessários pelo menos 5 segundos para carregar pela primeira vez a página inicial, que é uma página bem simples. Imagine o que aconteceria com a experiência do cliente se esta página tivesse conteúdo dinâmico que precisasse ser atualizado a cada minuto. Fornecer conteúdo dinâmico de um ponto de extremidade CDN requer uma validade curta do cache, o que significa frequentes perdas de cache no ponto de extremidade CDN. Isso prejudica o desempenho de seu serviço de nuvem e anula o objetivo de um CDN.

A alternativa é determinar que conteúdo fornecer por meio da CDN do Azure caso a caso em seu serviço de nuvem. Para isso, você já viu como acessar arquivos de conteúdo individuais do ponto de extremidade CDN. Você verá como fornecer uma ação de controlador específica por meio do ponto de extremidade CDN em [Fornecer conteúdo por meio de ações do controlador por meio da CDN do Azure][Fornecer conteúdo por meio de ações do controlador por meio da CDN do Azure].

Você pode especificar uma regra mais restritiva de reescrita de URL para limitar o conteúdo acessível por meio do ponto de extremidade CDN. Por exemplo, para limitar a reescrita de URL à pasta *\\Scripts*, altere a regra de reescrita acima da seguinte forma:

<pre class="prettyprint">
&lt;rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;&gt;
  &lt;match url=&quot;^cdn/<mark>Scripts/</mark>(.*)$&quot;/&gt;
  &lt;action type=&quot;Rewrite&quot; url=&quot;<mark>Scripts/</mark>{R:1}&quot;/&gt;
&lt;/rule&gt;
</pre>

<a name="caching"></a>

## Configurar opções de cache para arquivos estáticos em seu serviço de nuvem

Com a integração da CDN do Azure a seu serviço de nuvem, você pode especificar como quer que o conteúdo estático seja armazenado em cache no ponto de extremidade CDN. Para fazer isso, abra *Web.config* por meio do projeto de função Web (por exemplo, FunçãoDaWeb1) e adicione um elemento `<staticContent>` a `<system.webServer>`. O XML configura o cache para expirar em 3 dias.

<pre class="prettyprint">
&lt;system.webServer&gt;
  <mark>&lt;staticContent&gt;
    &lt;clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/&gt;
  &lt;/staticContent&gt;</mark>
  ...
&lt;/system.webServer&gt;
</pre>

Após você ter feito isso, todos os arquivos estáticos do serviço de nuvem obedecerão à mesma regra do cache de CDN. Para ter um controle mais granular das configurações de cache, adicione um arquivo *Web.config* a uma pasta e adicione suas configurações. Por exemplo, adicione um arquivo *Web.config* à pasta *\\Content* e substitua o conteúdo pelo seguinte XML:

    <?xml version="1.0"?>
    <configuration>
      <system.webServer>
        <staticContent>
          <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
        </staticContent>
      </system.webServer>
    </configuration>

Esta configuração faz com que todos os arquivos estáticos da pasta *\\Content* sejam armazenados em cache por 15 dias.

Para obter mais informações sobre como configurar o elemento `<clientCache>`, consulte [Cache do Cliente \<clientCache\>][Cache do Cliente \<clientCache\>].

Em [Fornecer conteúdo por meio de ações do controlador por meio da CDN do Azure][Fornecer conteúdo por meio de ações do controlador por meio da CDN do Azure], eu também mostrarei como você pode definir as configurações de cache para os resultados de ações do controlados no cache de CDN.

<a name="controller"></a>

## Fornecer conteúdo por meio de ações do controlador por meio da CDN do Azure

Quando você integra uma função Web de um serviço de nuvem à CDN do Azure, é relativamente fácil fornecer conteúdo de ações do controlador por meio da CDN do Azure. Além de fornecer o serviço de nuvem diretamente da CDN do Azure (demonstrado acima), [Maarten Balliauw][Maarten Balliauw] mostra como fazer isso com um divertido controlador MemeGenerator em [Reduzindo a latência na Web com o Microsoft Azure CDN][Reduzindo a latência na Web com o Microsoft Azure CDN]. Eu vou somente reproduzir aqui.

Suponha que, em seu serviço de nuvem, você queira gerar memes com base em uma imagem de Chuck Norris jovem (foto de [Alan Light][Alan Light]) como esta:

![][14]

Você tem uma ação `Index` simples que permite que os clientes especifiquem os superlativos na imagem e gera o meme quando eles postam a ação. Como se trata de Chuck Norris, você esperaria que esta página se tornasse extremamente popular globalmente. Este é um bom exemplo do fornecimento de conteúdo semidinâmico com a CDN do Azure.

Siga as etapas acima para configurar esta ação do controlador:

1.  Na pasta *\\Controllers*, crie um novo arquivo .cs chamado *MemeGeneratorController.cs* e substitua o conteúdo pelo código a seguir. Certifique-se de substituir a porção destacada pelo nome de seu CDN.

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
	
	namespace WebRole1.Controllers
	{
	    public class MemeGeneratorController : Controller
	    {
	        static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();

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
	            Tuple<string, string> data = null;
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
	                return Redirect(string.Format(&quot;http://<mark>&lt;cdnName&gt;</mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}&quot;, data.Item1, data.Item2));
	            }
	        }

	        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Generate(string top, string bottom)
	        {
	            string imageFilePath = HostingEnvironment.MapPath(&quot;~/Content/chuck.bmp&quot;);
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
	                if (size.Height < i.Height &&
	                     size.Width < i.Width) { return font; }
	
	                // Try a smaller font (90% of old size)
	                Font oldFont = font;
	                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
	                oldFont.Dispose();
	            }
	        }
		}
	}
	</pre>

2.  Clique com o botão direito na ação `Index()` padrão e selecione **Adicionar Exibição**.

    ![][15]

3.  Aceite as configurações acima e clique em **Adicionar**.

    ![][16]

4.  Abra o novo *Views\\MemeGenerator\\Index.cshtml* e substitua o conteúdo pelo HTML simples a seguir para enviar os superlativos:

        <h2>Meme Generator</h2>

        <form action="" method="post">
            <input type="text" name="top" placeholder="Enter top text here" />
            <br />
            <input type="text" name="bottom" placeholder="Enter bottom text here" />
            <br />
            <input class="btn" type="submit" value="Generate meme" />
        </form>

5.  Publique o serviço de nuvem novamente e navegue até **http://*\<serviceName\>*.cloudapp.net/MemeGenerator/Index** em seu navegador.

Quando você envia os valores de formulário a `/MemeGenerator/Index`, o método de ação `Index_Post` retorna um link para o método de ação `Show` com o respectivo identificador de entrada. Quando clicar em senha, você chegará ao seguinte código:

<pre class="prettyprint">
[OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
public ActionResult Show(string id)
{
    Tuple<string, string> data = null;
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
        return Redirect(string.Format(&quot;http://<mark>&lt;cdnName&gt;</mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}&quot;, data.Item1, data.Item2));
    }
}
</pre>

Se o depurador estiver anexado, você terá a experiência de depuração normal com um redirecionamento local. Se estiver sendo executado no serviço de nuvem, será redirecionado para:

    http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Que corresponde ao seguinte URL de origem no ponto de extremidade CDN:

    http://<youCloudServiceName>.cloudapp.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Após a regra de reescrita de URL aplicada anteriormente, o arquivo que é de fato armazenado em cache em meu ponto de extremidade CDN é:

    http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Você pode, então, usar o atributo `OutputCacheAttribute` no método `Generate` para especificar como o resultado da ação deve ser armazenado em cache, e a CDN do Azure seguirá a especificação. O código abaixo especifica uma validade de cache de 1 hora (3.600 segundos).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Da mesma forma, você pode fornecer conteúdo de qualquer ação de controlador no serviço de nuvem por meio da CDN do Azure, com a opção de cache desejada.

Na próxima seção, eu mostrarei como fornecer o CSS e scripts agrupados e minificados por meio da CDN do Azure.

<a name="bundling"></a>

## Integrar agrupamento e minificação à CDN do Azure

Scripts e folhas de estilo CSS são alterados com pouca frequência e são candidatos ideais para o cache da CDN do Azure. Fornecer toda a função Web por meio da CDN do Azure é a maneira mais fácil de integrar agrupamento e minificação à CDN do Azure. No entanto, como talvez você não queira fazer isso, eu vou mostrar como fazê-lo enquanto preserva a experiência do desenvolvedor desejada de agrupamento e minificação ASP.NET, como:

-   Ótima experiência no modo de depuração
-   Implantação simplificada
-   Atualizações imediatas para clientes para atualizações de versão de script/CSS
-   Mecanismo de fallback quando ocorre falha em seu ponto de extremidade CDN
-   Minimizar modificações de código

No projeto **FunçãoDaWeb1** que você criou em [TODO][TODO], abra *App\_Start\\BundleConfig.cs* e veja as chamadas de método `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

A primeira instrução `bundles.Add()` adiciona um grupo de script ao diretório virtual `~/bundles/jquery`. Depois, abra *Views\\Shared\_Layout.cshtml* para ver como a marcação do grupo de script é renderizada. Você deve encontrar a seguinte linha de código Razor:

    @Scripts.Render("~/bundles/jquery")

Quando for executado na função Web do Azure, este código Razor renderizará uma marcação `<script>` para o grupo de script semelhante à seguinte:

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

No entanto, quando for executado no Visual Studio digitando `F5`, ele renderizará cada arquivo de script do grupo individualmente (no caso acima, somente o arquivo de script está no grupo):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Isso permite que você depura o código JavaScript em seu ambiente de desenvolvimento enquanto reduz conexões clientes simultâneas (agrupamento) e melhora o desempenho do download de arquivos (minificação) em produção. É um ótimo recurso a ser preservado com a integração à CDN do Azure. Além disso, como o grupo renderizado já contém uma cadeia da versão gerada automaticamente, é aconselhável replicar essa funcionalidade para que sempre que você atualizar sua versão do jQuery por meio do NuGet, ela possa ser atualizada no lado do cliente assim que possível.

Siga as etapas abaixo para integrar agrupamento e minificação ASP.NET ao ponto de extremidade CDN.

1.  Em *App\_Start\\BundleConfig.cs*, modifique os métodos `bundles.Add()` para usar um [Construtor de grupo][Construtor de grupo] diferente, um que especifique um endereço CDN. Para fazer isso, substitua a definição do método `RegisterBundles` pelo código a seguir:

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

    Certifique-se de substituir `<yourCDNName>` pelo nome da sua CDN do Azure.

    De maneira simples, você está definindo `bundles.UseCdn = true` e adicionou uma URL da CDN criada cuidadosamente para cada grupo. Por exemplo, o primeiro construtor do código:

        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

    é igual a:

        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

    Este construtor instrui o agrupamento e minificação ASP.NET a renderizar arquivos de script individuais quando depurados localmente, mas usar o endereço CDN especificado para acessar o script em questão. No entanto, observe duas importantes características desse URL da CDN criado cuidadosamente:

    -   A origem do URL da CDN é `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, que na verdade é o diretório virtual do grupo de scripts em seu serviço de nuvem.
    -   Como você está usando um construtor CDN, a marcação do script CDN para o grupo não contém mais a cadeia da versão gerada automaticamente no URL renderizado. Você deve gerar manualmente uma cadeia de versão única sempre que o grupo de scripts for modificado para gerar uma perda de cache em sua CDN do Azure. Ao mesmo tempo, essa cadeia de versão única deve permanecer constante ao longo da vida útil da implantação para maximizar as ocorrências no cache na CDN do Azure após a implantação do grupo.
    -   A cadeia de consulta v=<w.x.y.z> efetua pull de *Properties\\AssemblyInfo.cs* no projeto de função Web. Você pode ter um fluxo de trabalho de implantação que inclua o incremento da versão de assembly sempre que você publicar no Azure. Ou pode apenar modificar *Properties\\AssemblyInfo.cs* em seu projeto para incrementar automaticamente a cadeia da versão sempre que compilar, usando o caractere curinga “\*”. Por exemplo:

            [assembly: AssemblyVersion("1.0.0.*")]

        Qualquer outra estratégia para simplificar a geração de uma cadeia única para a vida útil de uma implantação funcionará aqui.

2.  Publique o serviço de nuvem novamente e acesse a página inicial.

3.  Exiba o código HTML da página. Você deverá ver o URL da CDN renderizado, com uma cadeia de versão única sempre que republicar mudanças em seu serviço de nuvem. Por exemplo:

	<pre class="prettyprint">
	...

    &lt;link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25449&quot; rel=&quot;stylesheet&quot;/&gt;

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...</pre>

4.  No Visual Studio, depure o serviço de nuvem no Visual Studio digitando `F5`.,

5.  Exiba o código HTML da página. Você ainda verá cada arquivo de script renderizado individualmente, para que possa ter uma experiência de depuração consistente no Visual Studio.

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

## Mecanismo de fallback para URLs da CDN

Quando seu ponto de extremidade da CDN do Azure falhar por qualquer motivo, é aconselhável que sua página da Web seja inteligente o suficiente para acessar o servidor Web de origem como a opção de fallback para carregar JavaScript ou Bootstrap. Uma coisa é perder imagens do site devido à indisponibilidade da CDN, mas perder funcionalidades fundamentais da página fornecidas por seus scripts e folhas de estilo é outra coisa.

A classe [Bundle][Bundle] contém uma propriedade chamada [CdnFallbackExpression][CdnFallbackExpression] que permite que você configure o mecanismo de fallback para falha da CDN. Para usar essa propriedade, siga as etapas abaixo:

1.  No projeto de função Web, abra *App\_Start\\BundleConfig.cs*, onde você adicionou um URL da CDN a cada [Construtor de grupo][Construtor de grupo] e faça as seguintes alterações em destaque para adicionar um mecanismo de fallback aos grupos padrão:

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

    -   `window.jquery` é definido em jquery-{version}.js
    -   `$.validator` é definido em jquery.validate.js
    -   `window.Modernizr` é definido em modernizer-{version}.js
    -   `$.fn.modal` é definido em bootstrap.js

    Você pode ter notado que não defini CdnFallbackExpression para o grupo `~/Cointent/css`. Isso ocorre porque no momento há um [bug no System.Web.Optimization][bug no System.Web.Optimization] que injeta uma marcação `<script>` para o CSS de fallback em vez da marcação `<link>` esperada.

    Há, no entanto, um bom [Fallback de Grupo de Estilo][Fallback de Grupo de Estilo] oferecido pelo [Ember Consulting Group][Ember Consulting Group].

2.  Para usar a solução alternativa, crie um novo arquivo .cs na pasta *App\_Start* do projeto de função Web chamado *StyleBundleExtensions.cs* e substitua seu conteúdo pelo [código do GitHub][código do GitHub].

3.  Em *App\_Start\\StyleFundleExtensions.cs*, renomeie o namespace como nome da sua função Web (por exemplo, **FunçãoDaWeb1**).

4.  Volte para `App_Start\BundleConfig.cs` e modifique a última instrução `bundles.Add` como seguinte código em destaque:

	<pre class="prettyprint">
	bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
	    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
	    .Include(
	          "~/Content/bootstrap.css",
	          "~/Content/site.css"));
	</pre>

5.  Publique o serviço de nuvem novamente e acesse a página inicial.
6.  Exiba o código HTML da página. Você deve encontrar scripts injetados semelhantes ao seguinte:

	<pre class="prettyprint">...
	
		&lt;link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474&quot; rel=&quot;stylesheet&quot;/&gt;
	<mark>&lt;script&gt;(function() {
	                var loadFallback,
	                    len = document.styleSheets.length;
	                for (var i = 0; i &lt; len; i++) {
	                    var sheet = document.styleSheets[i];
	                    if (sheet.href.indexOf(&#39;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474&#39;) !== -1) {
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
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;(window.Modernizr)||document.write(&#39;&lt;script src=&quot;/bundles/modernizr&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	...	
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;(window.jquery)||document.write(&#39;&lt;script src=&quot;/bundles/jquery&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;($.fn.modal)||document.write(&#39;&lt;script src=&quot;/bundles/bootstrap&quot;&gt;&lt;\/script&gt;&#39;);&lt;/script&gt;</mark>
	
	...
	</pre>

    Observe que o script injetado para o grupo CSS ainda contém o excedente errante da propriedade `CdnFallbackExpression` na linha:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

    Mas como a primeira parte da expressão || sempre retornará o valor verdadeiro (na linha diretamente acima), a função document.write() nunca será executada.

# Mais informações

-   [Visão geral da Rede de Distribuição de Conteúdo (CDN) do Azure][Visão geral da Rede de Distribuição de Conteúdo (CDN) do Azure]
-   [Fornecer conteúdo da CDN do Azure em seu aplicativo Web][Fornecer conteúdo da CDN do Azure em seu aplicativo Web]
-   [Agrupamento e minificação ASP.NET][Agrupamento e minificação ASP.NET]
-   [Usando a CDN para Azure][Usando a CDN para Azure]

  [Integrar um ponto de extremidade do Azure a seu serviço de nuvem e fornecer conteúdo estático em suas páginas Web por meio da CDN do Azure]: #deploy
  [Definir configurações de cache para conteúdo estático em seu serviço de nuvem]: #caching
  [Fornecer conteúdo por meio de ações do controlador por meio da CDN do Azure]: #controller
  [Fornecer conteúdo agrupado e minificado por meio da CDN do Azure enquanto preserva a experiência de depuração de script no Visual Studio]: #bundling
  [Configurar o fallback de seus scripts e CSS quando a CDN do Azure estiver offline]: #fallback
  [conta do Microsoft Azure]: http://azure.microsoft.com/en-us/account/
  [SDK do Azure]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
  [abrir uma conta do Azure gratuitamente]: http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=A261C142F
  [ativar benefícios para assinantes do MSDN]: http://azure.microsoft.com/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  []: media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG
  [1]: media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG
  [2]: media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG
  [3]: media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png
  [4]: media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png
  [5]: media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png
  [6]: media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png
  [7]: media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png
  [Publicando um Serviço de Nuvem usando as Ferramentas do Azure]: http://msdn.microsoft.com/en-us/library/ff683672.aspx
  [portal de gerenciamento do Azure]: http://manage.windowsazure.com/
  [8]: media/cdn-cloud-service-with-cdn/cdn-cs-10-createcdn.png
  [9]: media/cdn-cloud-service-with-cdn/cdn-cs-11-disablequerya.png
  [10]: media/cdn-cloud-service-with-cdn/cdn-cs-12-disablequeryb.png
  [11]: media/cdn-cloud-service-with-cdn/cdn-cs-13-testcdn.png
  [12]: media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG
  [13]: media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG
  [Cache do Cliente \<clientCache\>]: http://www.iis.net/configreference/system.webserver/staticcontent/clientcache
  [Maarten Balliauw]: https://twitter.com/maartenballiauw
  [Reduzindo a latência na Web com o Microsoft Azure CDN]: http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN
  [Alan Light]: http://www.flickr.com/photos/alan-light/218493788/
  [14]: media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG
  [15]: media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG
  [16]: media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG
  [TODO]: #integrate
  [Construtor de grupo]: http://msdn.microsoft.com/en-us/library/jj646464.aspx
  [Bundle]: http://msdn.microsoft.com/en-us/library/system.web.optimization.bundle.aspx
  [CdnFallbackExpression]: http://msdn.microsoft.com/en-us/library/system.web.optimization.bundle.cdnfallbackexpression.aspx
  [bug no System.Web.Optimization]: https://aspnetoptimization.codeplex.com/workitem/104
  [Fallback de Grupo de Estilo]: https://github.com/EmberConsultingGroup/StyleBundleFallback
  [Ember Consulting Group]: https://github.com/EmberConsultingGroup
  [código do GitHub]: https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs
  [Visão geral da Rede de Distribuição de Conteúdo (CDN) do Azure]: http://msdn.microsoft.com/library/azure/ff919703.aspx
  [Fornecer conteúdo da CDN do Azure em seu aplicativo Web]: http://azure.microsoft.com/pt-br/documentation/Articles/cdn-serve-content-from-cdn-in-your-web-application/
  [Agrupamento e minificação ASP.NET]: http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification
  [Usando a CDN para Azure]: http://azure.microsoft.com/pt-br/documentation/articles/cdn-how-to-use/
