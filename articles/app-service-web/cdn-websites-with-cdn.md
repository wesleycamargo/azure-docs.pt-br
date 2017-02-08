---
title: "Usar o CDN do Azure no Serviço de Aplicativo do Azure"
description: "Um tutorial que ensina como implantar um aplicativo Web para o Serviço de Aplicativo do Azure que fornece conteúdo de um ponto de extremidade CDN do Azure integrado"
services: app-service\web,cdn
documentationcenter: .net
author: cephalin
manager: wpickett
editor: jimbe
ms.assetid: b81ee930-dd6d-4b65-971f-c4cb7902168c
ms.service: app-service
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/01/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 0b1f13f816c7836557cd6f646f2d9b38f7bc5b14


---
# <a name="use-azure-cdn-in-azure-app-service"></a>Usar o CDN do Azure no Serviço de Aplicativo do Azure
O [Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) pode ser integrado à [CDN do Azure](/services/cdn/), sendo adicionado aos recursos de dimensionamento global inerentes a [aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) fornecendo seu conteúdo de aplicativos Web globalmente de nós de servidor próximos de seus clientes (uma lista atualizada de todos os locais de nós atuais pode ser encontrada [aqui](http://msdn.microsoft.com/library/azure/gg680302.aspx)). Em cenários como o fornecimento de imagens estáticas, essa integração pode aumentar drasticamente o desempenho de seus aplicativos Web do Serviço de Aplicativo do Azure e melhora significativamente a experiência do usuário de seu aplicativo Web em todo o mundo. 

A integração de aplicativos Web à CDN do Azure oferece as seguintes vantagens:

* Integrar a implantação de conteúdo (imagens, scripts e folhas de estilo) como parte do processo de [implantação contínua](app-service-continuous-deployment.md) de seu aplicativo Web
* Atualizar facilmente os pacotes do NuGet em seu aplicativo Web no Serviço de Aplicativo do Azure, como versões de Bootstrap ou jQuery 
* Gerenciar seu aplicativo Web e o conteúdo fornecido por CDN por meio da mesma interface do Visual Studio
* Integrar agrupamento e minificação ASP.NET à CDN do Azure

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-build"></a>O que você compilará
Você implantará um aplicativo Web para o Serviço de Aplicativo do Azure usando o modelo ASP.NET MVC padrão em Visual Studio, adicionará código para fornecer conteúdo de uma CDN do Azure integrada, como uma imagem, resultados de ação do controlador e os arquivos JavaScript e CSS padrão, e também escreverá código para configurar o mecanismo de fallback para grupos fornecidos caso a CDN esteja offline.

## <a name="what-you-will-need"></a>O que será necessário
Este tutorial tem os seguintes pré-requisitos:

* Uma [conta do Microsoft Azure](/account/)
* Visual Studio 2015 com o [SDK do Azure para .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409). Se você usar o Visual Studio, as etapas poderão variar.

> [!NOTE]
> Você de uma conta do Azure para concluir este tutorial:
> 
> * É possível [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/) – você obtém créditos que podem ser usados para experimentar serviços pagos do Azure e, mesmo após eles serem utilizados, é possível manter a conta e utilizar os serviços gratuitos do Azure, como Aplicativos Web.
> * É possível [ativar os benefícios para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) – todos os meses, sua assinatura do Visual Studio concede créditos que podem ser usados para experimentar serviços pagos do Azure.
> 
> Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

## <a name="deploy-a-web-app-to-azure-with-an-integrated-cdn-endpoint"></a>Implantar um aplicativo Web no Azure com um ponto de extremidade da CDN integrado
Nesta seção, você implantará o modelo de aplicativo do ASP.NET MVC padrão no Visual Studio 2015 ao Serviço de Aplicativo e, em seguida, o integrará a um novo ponto de extremidade da CDN. Siga as instruções abaixo:

1. No Visual Studio 2015, crie um novo aplicativo Web ASP.NET na barra de menus selecionando **Arquivo > Novo > Projeto > Web > Aplicativo Web ASP.NET**. Dê um nome a ele e clique em **OK**.
   
    ![](media/cdn-websites-with-cdn/1-new-project.png)
2. Selecione **MVC** e clique em **OK**.
   
    ![](media/cdn-websites-with-cdn/2-webapp-template.png)
3. Se ainda não estiver conectado à sua conta do Azure, clique no ícone de conta no canto superior direito e siga a caixa de diálogo para fazer logon em sua conta do Azure. Quando terminar, configure o aplicativo conforme mostrado abaixo e clique em **Novo** para criar um novo plano de Serviço de Aplicativo para ele.  
   
    ![](media/cdn-websites-with-cdn/3-configure-webapp.png)
4. Configure um novo plano de Serviço de Aplicativo na caixa de diálogo, conforme mostrado abaixo, e clique em **OK**. 
   
    ![](media/cdn-websites-with-cdn/4-app-service-plan.png)
5. Clique em **Criar** para criar o aplicativo Web.
   
    ![](media/cdn-websites-with-cdn/5-create-website.png)
6. Depois de criar seu aplicativo ASP.NET, publique-o no Azure, no painel Atividade do Serviço de Aplicativo do Azure, clicando em **Publicar `<app name>` para esse aplicativo Web agora**. Clique em **Publicar** para concluir o processo.
   
    ![](media/cdn-websites-with-cdn/6-publish-website.png)
   
    Quando a publicação estiver concluída, você verá seu aplicativo Web publicado no navegador. 
7. Para criar um ponto de extremidade da CDN, faça logon no [portal do Azure](https://portal.azure.com). 
8. Clique em **+ Novo** > **Mídia + CDN** > **CDN**.
   
    ![](media/cdn-websites-with-cdn/create-cdn-profile.png)
9. Especifique a **CDN**, o **Local**, o **Grupo de recursos** e **Tipo de preço** e clique em **Criar**
   
    ![](media/cdn-websites-with-cdn/7-create-cdn.png)    
10. Na folha **Perfil da CDN**, clique no botão **+ Ponto de extremidade**. Dê um nome a ele, selecione **Aplicativo Web** no menu suspenso **Tipo de Origem** e seu aplicativo Web no menu suspenso **Nome do host de origem** e clique em **Adicionar**.  
    
     ![](media/cdn-websites-with-cdn/cdn-profile-blade.png)

    > [AZURE.NOTE] Após a criação do ponto de extremidade da CDN, a folha **Ponto de extremidade** mostrará a URL da CDN e o domínio da origem ao qual ele está integrado. No entanto, pode levar algum tempo para que a configuração do novo ponto de extremidade da CDN seja totalmente propagada a todos os locais de nó da CDN. 

1. De volta à folha **Ponto de extremidade** , clique no nome do ponto de extremidade da CDN que você acabou de criar.
   
    ![](media/cdn-websites-with-cdn/8-select-cdn.png)
2. Clique no botão **Configurar** . Na folha **Configurar**, selecione **Armazenar em cache cada URL exclusiva** no menu suspenso **Comportamento do caching da cadeia de consulta** e clique no botão **Salvar**.

    ![](media/cdn-websites-with-cdn/9-enable-query-string.png)

Após você ter habilitado, o mesmo link acessado com diferentes cadeias de consulta será armazenado em cache como entradas diferentes.

> [!NOTE]
> Embora habilitar a cadeia de consulta não seja necessário para esta seção do tutorial, é aconselhável fazer isso o quanto antes para maior conveniência, uma vez que qualquer alteração aqui levará tempo para ser propagada para todos os nós de CDN, e você não quer um acúmulo de conteúdo não habilitado para cadeia de consulta no cache da CDN (a atualização do conteúdo da CDN será abordada mais adiante).
> 
> 

1. Agora, navegue até o endereço do ponto de extremidade da CDN. Se o ponto de extremidade estiver pronto, você verá seu aplicativo Web exibido. Se você receber um erro **HTTP 404** , isso significa que o ponto de extremidade da CDN não está pronto. Talvez seja necessário aguardar até uma hora para a configuração de CDN ser propagada para todos os nós próximos aos limites. 
   
    ![](media/cdn-websites-with-cdn/11-access-success.png)
2. Em seguida, tente acessar o arquivo **~/Content/bootstrap.css** em seu projeto ASP.NET. Na janela do navegador, vá até **http://*&lt;nomedoCdn>*.azureedge.net/Content/bootstrap.css**. Em minha configuração, essa URL é:
   
        http://az673227.azureedge.net/Content/bootstrap.css
   
    Que corresponde à seguinte URL de origem no ponto de extremidade da CDN:
   
        http://cdnwebapp.azurewebsites.net/Content/bootstrap.css
   
    Quando você navegar até **http://*&lt;nomedoCdn>*.azureedge.net/Content/bootstrap.css**, será solicitado que você baixe o bootstrap.css que veio de seu aplicativo Web publicado. 
   
    ![](media/cdn-websites-with-cdn/12-file-access.png)

De maneira semelhante, você pode acessar qualquer URL acessível publicamente em **http://*&lt;nomedoServiço>*.cloudapp.net/**, diretamente de seu ponto de extremidade CDN. Por exemplo:

* Um arquivo .js do caminho /Script
* Qualquer arquivo de conteúdo do caminho /Content
* Qualquer controller/action 
* Se cadeias de consulta estiverem habilitadas em seu ponto de extremidade da CDN, qualquer URL com cadeias de consulta
* Todo o aplicativo Web, se todo o conteúdo é público

Observe que talvez pode não ser sempre uma boa ideia (ou geralmente uma boa ideia) fornecer conteúdo a um aplicativo Web inteiro por meio da CDN do Azure. Alguns dos empecilhos são:

* Esta abordagem requer que todo o seu site seja público, por que a CDN do Azure não pode fornecer nenhum conteúdo privado.
* Se o ponto de extremidade da CDN ficar offline por qualquer motivo, seja para uma manutenção programada ou por erro do usuário, todo o seu aplicativo Web ficará offline a menos que os clientes possam ser redirecionados à URL de origem, **http://*&lt;nomedosite>*.azurewebsites.net/**. 
* Mesmo com as configurações personalizadas de controle de cache (consulte [Configurar opções de cache para arquivos estáticos em seu aplicativo Web](#configure-caching-options-for-static-files-in-your-azure-web-app)), um ponto de extremidade da CDN não melhora o desempenho de conteúdo altamente dinâmico. Se você tiver tentado carregar a página inicial de seu ponto de extremidade da CDN conforme mostrado acima, observe que foram necessários pelo menos 5 segundos para carregar pela primeira vez a página inicial, que é uma página bem simples. Imagine o que aconteceria com a experiência do cliente se esta página tivesse conteúdo dinâmico que precisasse ser atualizado a cada minuto. Fornecer conteúdo dinâmico de um ponto de extremidade da CDN requer uma validade curta do cache, o que significa frequentes perdas de cache no ponto de extremidade da CDN. Isso prejudica o desempenho de seu aplicativo Web e frustra o propósito de uma CDN.

A alternativa é determinar que conteúdo fornecer por meio da CDN do Azure caso a caso, em seu aplicativo Web. Para isso, você já viu como acessar arquivos de conteúdo individuais do ponto de extremidade da CDN. Você verá como fornecer uma ação de controlador específica por meio do ponto de extremidade CDN em [Fornecer conteúdo por meio de ações do controlador por meio da CDN do Azure](#serve-content-from-controller-actions-through-azure-cdn).

## <a name="configure-caching-options-for-static-files-in-your-azure-web-app"></a>Configurar opções de cache para arquivos estáticos em seu aplicativo Web
Com a integração da CDN do Azure a seu aplicativo Web, você pode especificar como quer que o conteúdo estático seja armazenado em cache no ponto de extremidade da CDN. Para fazer isso, abra *Web.config* por meio do projeto ASP.NET (por exemplo, **cdnwebapp**) e adicione um elemento `<staticContent>` a `<system.webServer>`. O XML configura o cache para expirar em 3 dias.  

    <system.webServer>
      <staticContent>
        <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
      </staticContent>
      ...
    </system.webServer>

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

Para saber mais sobre como configurar o elemento `<clientCache>`, consulte [Cache do cliente&lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

Na próxima seção, também mostrarei como você pode definir configurações de cache para resultados de ação do controlador no cache da CDN.

## <a name="serve-content-from-controller-actions-through-azure-cdn"></a>Fornecer conteúdo por meio de ações do controlador por meio da CDN do Azure
Ao integrar aplicativos Web à CDN do Azure, é relativamente fácil fornecer conteúdo por meio de ações do controlador, por meio da CDN do Azure. Novamente, se você decidir fornecer conteúdo a todo o aplicativo Web por meio de sua CDN, não precisará fazer nada isso, visto que todas as ações do controlador já estarão acessíveis por meio da CDN. Mas, pelas razões que já mencionei em [Implantar um site do Azure com um ponto de extremidade da CDN integrado](#deploy-a-web-app-to-azure-with-an-integrated-cdn-endpoint), você pode decidir não fazer isso e optar por selecionar a ação de controlador que deseja fornecer na CDN do Azure. [Maarten Balliauw](https://twitter.com/maartenballiauw) mostra como fazer isso com um divertido controlador MemeGenerator em [Reduzindo a latência na Web com a CDN do Azure](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). Eu vou somente reproduzir isso aqui.

Suponha que, em seu aplicativo Web, você queira gerar memes com base em uma imagem de Chuck Norris jovem (foto de [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)) como esta:

![](media/cdn-websites-with-cdn/cdn-5-memegenerator.PNG)

Você tem uma ação `Index` simples que permite que os clientes especifiquem os superlativos na imagem e gera o meme quando eles postam a ação. Como se trata de Chuck Norris, você esperaria que esta página se tornasse extremamente popular globalmente. Este é um bom exemplo do fornecimento de conteúdo semidinâmico com a CDN do Azure. 

Siga as etapas acima para configurar esta ação do controlador:

1. Na pasta *\Controllers*, crie um novo arquivo .cs chamado *MemeGeneratorController.cs* e substitua o conteúdo pelo código a seguir. Substitua o caminho do arquivo por `~/Content/chuck.bmp` e o nome da CDN por `yourCDNName`.

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
            static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();

            public ActionResult Index()
            {
              return View();
            }

            [HttpPost, ActionName("Index")]
            public ActionResult Index_Post(string top, string bottom)
            {
              var identifier = Guid.NewGuid().ToString();
              if (!Memes.ContainsKey(identifier))
              {
                Memes.Add(identifier, new Tuple<string, string>(top, bottom));
              }

              return Content("<a href=\"" + Url.Action("Show", new {id = identifier}) + "\">here's your meme</a>");
            }

            [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
            public ActionResult Show(string id)
            {
              Tuple<string, string> data = null;
              if (!Memes.TryGetValue(id, out data))
              {
                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
              }

              if (Debugger.IsAttached) // Preserve the debug experience
              {
                return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
              }
              else // Get content from Azure CDN
              {
                return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
              }
            }

            [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
            public ActionResult Generate(string top, string bottom)
            {
              string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
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
              return File(ms.ToArray(), "image/png");
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

1. Clique com o botão direito na ação `Index()` padrão e selecione **Adicionar Exibição**.
   
    ![](media/cdn-websites-with-cdn/cdn-6-addview.PNG)
2. Aceite as configurações acima e clique em **Adicionar**.
   
   ![](media/cdn-websites-with-cdn/cdn-7-configureview.PNG)
3. Abra o novo *Views\MemeGenerator\Index.cshtml* e substitua o conteúdo pelo HTML simples a seguir para enviar os superlativos:
   
        <h2>Meme Generator</h2>
   
        <form action="" method="post">
            <input type="text" name="top" placeholder="Enter top text here" />
            <br />
            <input type="text" name="bottom" placeholder="Enter bottom text here" />
            <br />
            <input class="btn" type="submit" value="Generate meme" />
        </form>
4. Publique o aplicativo Web do Azure novamente e navegue até **http://*&lt;nomedoServiço>*.cloudapp.net/MemeGenerator/Index** em seu navegador. 

Quando você envia os valores de formulário a `/MemeGenerator/Index`, o método de ação `Index_Post` retorna um link para o método de ação `Show` com o respectivo identificador de entrada. Quando clicar em senha, você chegará ao seguinte código:  

    [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
    public ActionResult Show(string id)
    {
      Tuple<string, string> data = null;
      if (!Memes.TryGetValue(id, out data))
      {
        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
      }

      if (Debugger.IsAttached) // Preserve the debug experience
      {
        return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
      }
      else // Get content from Azure CDN
      {
        return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
      }
    }

Se o seu depurador local estiver anexado, você terá a experiência de depuração normal com um redirecionamento local. Se estiver sendo executado no aplicativo Web, será redirecionado para:

    http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Que corresponde ao seguinte URL de origem no ponto de extremidade da CDN:

    http://<yourSiteName>.azurewebsites.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Após a regra de reescrita de URL aplicada anteriormente, o arquivo que é de fato armazenado em cache em meu ponto de extremidade da CDN é:

    http://<yourSiteName>.azurewebsites.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Você então pode usar o atributo `OutputCacheAttribute` no método `Generate` para especificar como o resultado da ação deve ser armazenado em cache, e a CDN do Azure seguirá a especificação. O código abaixo especifica uma validade de cache de 1 hora (3.600 segundos).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Do mesmo modo, você pode fornecer conteúdo por meio de qualquer ação de controlador em seu aplicativo Web por meio da CDN do Azure, com a opção de cache desejada.

Na próxima seção, eu mostrarei como fornecer o CSS e scripts agrupados e minificados por meio da CDN do Azure. 

## <a name="integrate-aspnet-bundling-and-minification-with-azure-cdn"></a>Integrar agrupamento e minificação ASP.NET à CDN do Azure
Scripts e folhas de estilo CSS são alterados com pouca frequência e são candidatos ideais para o cache da CDN do Azure. Fornecer todo o aplicativo Web por meio da CDN do Azure é a maneira mais fácil de integrar agrupamento e minificação à CDN do Azure. No entanto, já que você pode decidir contra essa abordagem pelos motivos descritos em [Integrar um ponto de extremidade da CDN do Azure a seu site do Azure e fornecer conteúdo estático em suas páginas Web por meio da CDN do Azure](#deploy-a-web-app-to-azure-with-an-integrated-cdn-endpoint), vou mostrar a você como fazer isso, preservando as experiências de desenvolvedor desejada de agrupamento e minificação do ASP.NET, como:

* Ótima experiência no modo de depuração
* Implantação simplificada
* Atualizações imediatas para clientes para atualizações de versão de script/CSS
* Mecanismo de fallback quando ocorre falha em seu ponto de extremidade CDN
* Minimizar modificações de código

No projeto ASP.NET que você criou em [Integrar um ponto de extremidade da CDN do Azure a seu site do Azure e fornecer conteúdo estático em suas páginas Web na CDN do Azure](#deploy-a-web-app-to-azure-with-an-integrated-cdn-endpoint), abra *App_Start\BundleConfig.cs* e examine as chamadas de método `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

A primeira instrução `bundles.Add()` adiciona um grupo de script ao diretório virtual `~/bundles/jquery`. Depois, abra *Views\Shared\_Layout.cshtml* para ver como a marcação do grupo de script é renderizada. Você deve encontrar a seguinte linha de código Razor:

    @Scripts.Render("~/bundles/jquery")

Quando for executado no aplicativo Web do Azure, este código Razor renderizará uma marca `<script>` para o grupo de scripts semelhante ao seguinte: 

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

No entanto, quando for executado no Visual Studio digitando `F5`, ele renderizará cada arquivo de script do grupo individualmente (no caso acima, somente o arquivo de script está no grupo):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Isso permite que você depure o código JavaScript em seu ambiente de desenvolvimento enquanto reduz conexões clientes simultâneas (agrupamento) e melhora o desempenho do download de arquivos (minificação) em produção. É um ótimo recurso a ser preservado com a integração à CDN do Azure. Além disso, como o grupo renderizado já contém uma cadeia da versão gerada automaticamente, é aconselhável replicar essa funcionalidade para que sempre que você atualizar sua versão do jQuery por meio do NuGet, ela possa ser atualizada no lado do cliente assim que possível.

Siga as etapas abaixo para integrar agrupamento e minificação ASP.NET ao ponto de extremidade CDN.

1. Em *App_Start\BundleConfig.cs*, modifique os métodos `bundles.Add()` para usar um [Construtor de pacotes](http://msdn.microsoft.com/library/jj646464.aspx) diferente, um que especifique um endereço CDN. Para fazer isso, substitua a `RegisterBundles` definição do método pelo seguinte código:  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
          bundles.UseCdn = true;
          var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
            .GetName().Version.ToString();
          var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?" + version;
   
          bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
                "~/Scripts/jquery-{version}.js"));
   
          bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
                "~/Scripts/jquery.validate*"));
   
          // Use the development version of Modernizr to develop with and learn from. Then, when you're
          // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
          bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizr")).Include(
                "~/Scripts/modernizr-*"));
   
          bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
                "~/Scripts/bootstrap.js",
                "~/Scripts/respond.js"));
   
          bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                "~/Content/bootstrap.css",
                "~/Content/site.css"));
        }

    Substitua `<yourCDNName>` pelo nome da sua CDN do Azure.

    De maneira simples, você está definindo `bundles.UseCdn = true` e adicionou uma URL da CDN criada cuidadosamente a cada grupo. Por exemplo, o primeiro construtor do código:

        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

    é igual a: 

        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?<W.X.Y.Z>"))

    Este construtor instrui o agrupamento e minificação ASP.NET a renderizar arquivos de script individuais quando depurados localmente, mas usar o endereço CDN especificado para acessar o script em questão. No entanto, observe duas importantes características dessa URL da CDN criada cuidadosamente:

    - A origem do URL da CDN é `http://<yourSiteName>.azurewebsites.net/bundles/jquery?<W.X.Y.Z>`, que, na verdade, é o diretório virtual do grupo de scripts em seu aplicativo Web.
    - Como você está usando um construtor CDN, a marcação do script CDN para o grupo não contém mais a cadeia da versão gerada automaticamente na URL renderizada. Você deve gerar manualmente uma cadeia de versão única sempre que o grupo de scripts for modificado para gerar uma perda de cache em sua CDN do Azure. Ao mesmo tempo, essa cadeia de versão única deve permanecer constante ao longo da vida útil da implantação para maximizar as ocorrências no cache na CDN do Azure após a implantação do grupo.

1. A cadeia de consulta `<W.X.Y.Z>` efetua o pull de *Properties\AssemblyInfo.cs* em seu projeto ASP.NET. Você pode ter um fluxo de trabalho de implantação que inclua o incremento da versão de assembly sempre que você publicar no Azure. Ou pode apenar modificar *Properties\AssemblyInfo.cs* em seu projeto para incrementar automaticamente a cadeia da versão sempre que compilar, usando o caractere curinga “*”. Por exemplo, altere `AssemblyVersion` conforme mostrado abaixo:
   
        [assembly: AssemblyVersion("1.0.0.*")]
   
    Qualquer outra estratégia para simplificar a geração de uma cadeia única para a vida útil de uma implantação funcionará aqui.
2. Republique o aplicativo ASP.NET e acesse a página inicial.
3. Exiba o código HTML da página. Você deverá ver a URL da CDN renderizada, com uma cadeia de versão única sempre que republicar mudanças em seu aplicativo Web do Azure. Por exemplo:  
   
        ...
        <link href="http://az673227.azureedge.net/Content/css?1.0.0.25449" rel="stylesheet"/>
        <script src="http://az673227.azureedge.net/bundles/modernizer?1.0.0.25449"></script>
        ...
        <script src="http://az673227.azureedge.net/bundles/jquery?1.0.0.25449"></script>
        <script src="http://az673227.azureedge.net/bundles/bootstrap?1.0.0.25449"></script>
        ...
4. No Visual Studio, depure o aplicativo ASP.NET em Visual Studio, digitando `F5`., 
5. Exiba o código HTML da página. Você ainda verá cada arquivo de script renderizado individualmente, para que possa ter uma experiência de depuração consistente no Visual Studio.  
   
        ...
        <link href="/Content/bootstrap.css" rel="stylesheet"/>
        <link href="/Content/site.css" rel="stylesheet"/>
        <script src="/Scripts/modernizr-2.6.2.js"></script>
        ...
        <script src="/Scripts/jquery-1.10.2.js"></script>
        <script src="/Scripts/bootstrap.js"></script>
        <script src="/Scripts/respond.js"></script>
        ...    

## <a name="fallback-mechanism-for-cdn-urls"></a>Mecanismo de fallback para URLs da CDN
Quando seu ponto de extremidade da CDN do Azure falhar por qualquer motivo, é melhor que sua página da Web seja inteligente o suficiente para acessar o servidor Web de origem como a opção de fallback para carregar JavaScript ou Bootstrap. Perder imagens do aplicativo Web devido à indisponibilidade da CDN já é grave, mas perder funcionalidades fundamentais da página fornecidas por seus scripts e folhas de estilo é ainda muito mais.

A classe [Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) contém uma propriedade chamada [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) que permite que você configure o mecanismo de fallback para falha da CDN. Para usar essa propriedade, siga as etapas abaixo:

1. No projeto ASP.NET, abra *App_Start\BundleConfig.cs*, no qual você adicionou uma URL da CDN a cada [Construtor de pacotes](http://msdn.microsoft.com/library/jj646464.aspx), e adicione o código `CdnFallbackExpression` a quatro lugares, como mostrado, para adicionar um mecanismo de fallback aos pacotes padrão.  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
          var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
            .GetName().Version.ToString();
          var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
          bundles.UseCdn = true;
   
          bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")) 
                { CdnFallbackExpression = "window.jquery" }
                .Include("~/Scripts/jquery-{version}.js"));
   
          bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")) 
                { CdnFallbackExpression = "$.validator" }
                .Include("~/Scripts/jquery.validate*"));
   
          // Use the development version of Modernizr to develop with and learn from. Then, when you're
          // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
          bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")) 
                { CdnFallbackExpression = "window.Modernizr" }
                .Include("~/Scripts/modernizr-*"));
   
          bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap"))     
                { CdnFallbackExpression = "$.fn.modal" }
                .Include(
                        "~/Scripts/bootstrap.js",
                        "~/Scripts/respond.js"));
   
          bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                "~/Content/bootstrap.css",
                "~/Content/site.css"));
        }
   
    Quando `CdnFallbackExpression` não é nulo, o script é injetado no HTML para testar se o grupo é carregado com sucesso e, se não, acessar o grupo diretamente por meio do servidor Web de origem. Esta propriedade precisa ser definida como uma expressão JavaScript que testa se o grupo CDN respectivo é carregado corretamente. A expressão necessária para testar cada grupo difere de acordo com o conteúdo. Para os grupos padrão acima:
   
   * `window.jquery` é definido em jquery-{version}.js
   * `$.validator` é definido em jquery.validate.js
   * `window.Modernizr` é definido em modernizer-{version}.js
   * `$.fn.modal` é definido em bootstrap.js
     
     Você pode ter notado que não defini CdnFallbackExpression para o grupo `~/Cointent/css` . Isso ocorre porque no momento há um [bug no System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) que injeta uma marcação `<script>` para o CSS de fallback em vez da marcação `<link>` esperada.
     
     Há, no entanto, um bom [Fallback de Grupo de Estilo](https://github.com/EmberConsultingGroup/StyleBundleFallback) oferecido pelo [Ember Consulting Group](https://github.com/EmberConsultingGroup). 
2. Para usar a solução alternativa para CSS, crie um novo arquivo .cs na pasta *App_Start* de seu projeto ASP.NET, nomeie esse arquivo como *StyleBundleExtensions.cs* e substitua seu conteúdo pelo [código do GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs). 
3. Em *App_Start\StyleFundleExtensions.cs*, renomeie o namespace como o namespace de seu aplicativo ASP.NET (por exemplo, **cdnwebapp**). 
4. Volte para `App_Start\BundleConfig.cs` e substitua a última instrução `bundles.Add` pelo seguinte código:  
   
        bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
          .IncludeFallback("~/Content/css", "sr-only", "width", "1px")
          .Include(
            "~/Content/bootstrap.css",
            "~/Content/site.css"));
   
    Este novo método de extensão usa a mesma ideia para injetar script no HTML para verificar o DOM para um nome de classe, nome de regra e valor de regra correspondente no grupo CSS, e retorna ao servidor Web original se não encontrar um resultado correspondente.
5. Republique seu aplicativo Web do Azure e acesse a página inicial. 
6. Exiba o código HTML da página. Você deve encontrar scripts injetados semelhantes ao seguinte:    
   
    ```
    ...
    <link href="http://az673227.azureedge.net/Content/css?1.0.0.25474" rel="stylesheet"/>
   <script>(function() {
                var loadFallback,
                    len = document.styleSheets.length;
                for (var i = 0; i < len; i++) {
                    var sheet = document.styleSheets[i];
                    if (sheet.href.indexOf('http://az673227.azureedge.net/Content/css?1.0.0.25474') !== -1) {
                        var meta = document.createElement('meta');
                        meta.className = 'sr-only';
                        document.head.appendChild(meta);
                        var value = window.getComputedStyle(meta).getPropertyValue('width');
                        document.head.removeChild(meta);
                        if (value !== '1px') {
                            document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
                        }
                    }
                }
                return true;
            }())||document.write('<script src="/Content/css"><\/script>');</script>
   
    <script src="http://az673227.azureedge.net/bundles/modernizer?1.0.0.25474"></script>
     <script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>
    ... 
    <script src="http://az673227.azureedge.net/bundles/jquery?1.0.0.25474"></script>
    <script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>
   
     <script src="http://az673227.azureedge.net/bundles/bootstrap?1.0.0.25474"></script>
     <script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>
    ...
    ```
   
    Observe que o script injetado para o grupo CSS ainda contém o excedente errante da propriedade `CdnFallbackExpression` na linha:
   
        }())||document.write('<script src="/Content/css"><\/script>');</script>
   
    Mas como a primeira parte da expressão || sempre retornará o valor verdadeiro (na linha diretamente acima), a função document.write() nunca será executada.
7. Para testar se o script de fallback está funcionando, volte à folha do ponto de extremidade da CDN e clique em **Parar**.
   
    ![](media/cdn-websites-with-cdn/13-test-fallback.png)
8. Atualize a janela do navegador para o aplicativo Web do Azure. Agora você deve ver todos os scripts e folhas de estilo carregados corretamente.

## <a name="more-information"></a>Mais informações
* [Visão geral da Rede de Distribuição de Conteúdo (CDN) do Azure](../cdn/cdn-overview.md)
* [Usando o Azure CDN](../cdn/cdn-create-new-endpoint.md)
* [Integrar um serviço de nuvem à CDN do Azure](../cdn/cdn-cloud-service-with-cdn.md)
* [Agrupamento e minificação ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)




<!--HONumber=Jan17_HO3-->


