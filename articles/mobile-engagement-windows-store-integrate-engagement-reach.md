<properties 
	pageTitle="Integração do Reach no SDK da Windows Store para o Mobile Engagement do Azure" 
	description="Atualizações mais recentes e procedimentos para o SDK da Windows Store para o Mobile Engagement do Azure" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

# Como integrar o Engagement Reach no Windows

Você deve seguir o procedimento de integração descrito no documento [Como integrar o Engagement no Windows](mobile-engagement-windows-store-integrate-engagement.md) antes de seguir este guia.

## Incorporar o SDK do Engagement Reach em seu projeto do Windows

Você não tem nada a adicionar. `EngagementReach` referências e recursos já estão em seu projeto.

> [AZURE.TIP] Você pode personalizar imagens localizadas na pasta `Resources`do seu projeto, especialmente o ícone de marca (esse padrão para o ícone do Engagement).

## Adicione recursos

O SDK do Engagement Reach precisa de alguns recursos adicionais.

Abra seu arquivo`Package.appxmanifest` e vá no painel`Declarações`. Selecione `Associações de Tipo de Arquivo` na caixa de rolagem `Declarações Disponíveis`e adicione-o. Defina o Nome para `engagement_reach_content`e Tipo de arquivo para `.txt`.

## Habilitar o Serviço de Notificação do Windows

Para usar o **Serviço de Notificação do Windows** (chamado WNS) em seu arquivo`Package.appxmanifest` em `Application UI`clique em `Todos os Ativos de Imagem` na caixa bot à esquerda. À direita da caixa em `Notificações` altere `compatível com toast` de `(não definido)` para `Sim`.

Além disso, você precisa sincronizar seu aplicativo para sua conta da Microsoft e plataforma do engagement. No front-end do engagement vá para sua configuração de aplicativo no `push nativo` e cole suas credenciais. Depois disso, clique com botão direito no projeto, selecione `armazenar` e `Associate App with the Store...`.

## Inicializar o SDK do Engagement Reach

Modifique o `App.xaml.cs`:

-   Adicione às suas instruções`using`:

			using Microsoft.Azure.Engagement;

-   Insira `EngagementReach.Instance.Init`logo após `EngagementAgent.Instance.Init` em `OnLaunched`:

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			  EngagementReach.Instance.Init(args);
			}

> [AZURE.NOTE] O `EngagementReach.Instance.Init`é executado em um thread dedicado. Você não precisa fazer isto sozinho.

-   Se você quiser iniciar o engagement reach quando seu aplicativo for ativado, substitua o método `OnActivated`:

			protected override void OnActivated(IActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			  EngagementReach.Instance.Init(args);
			}

> [AZURE.TIP] Você pode especificar o nome do canal de notificação WNS do seu aplicativo no arquivo `Resources\EngagementConfiguration.xml` do projeto em `<channelName></channelName>`. Por padrão, o Engagement cria um nome baseado na appId. Você não precisa especificar o nome, exceto se você planeja usar o canal de notificação fora do Engagement.

## Integração

O Engagement oferece duas maneiras para implementar o anúncio e notificação do Reach. A Integração de sobreposição e a integração do Modo de exibição da Web.

[Integração de sobreposição](#overlay-integration) não exige muito código para gravar no seu aplicativo. Basta marcar seus arquivos de paginação, xaml e cs com o EngagementPageOverlay. Além disso se você personalizar a exibição padrão do Engagement sua personalização será compartilhada com todas as páginas marcadas e apenas definidas uma vez. Mas se suas páginas precisam herdar de um outro objeto além do EngagementPageOverlay você está preso e forçado a usar a integração do Modo de exibição da Web.

[Integração do Modo de exibição da Web](#web-view-integration) é mais complicada para ser implementada. Mas se suas páginas do Aplicativo precisam herdar de outro objeto além do "Page", então você precisa integrar o Modo de exibição da Web e seu comportamento.

Uma prática recomendada em aplicativos de armazenamento do Windows 8.1, é que você precisa adicionar um elemento de primeiro nível `<Grid></Grid>`para envolver todo o conteúdo da página. Para a integração do Modo de exibição da Web, basta adicionar o Modo de exibição da Web como filho desta grade. Se você precisar configurar o componente do Engagement em outro lugar, lembre-se de que você precisa gerenciar o tamanho de exibição por conta própria.

### Integração de sobreposição

O Engagement fornece uma sobreposição para exibição de lançamento e notificação.

Se você quiser usá-la, não use a [Integração do Modo de exibição da Web](#web-view-integration).

Em seu arquivo .xaml altere a referência de EngagementPage para EngagementPageOverlay

-   Adicione às suas declarações de namespaces:

			xmlns:engagement="using:using:Microsoft.Azure.Engagement.Overlay"

-   Substitua `engagementpor :EngagementPage``engagement:EngagementPageOverlay` :

**Com EngagementPage:**

			<engagement:EngagementPage 
			    xmlns:engagement="using:Microsoft.Azure.Engagement">
			
			    <!-- layout -->
			</engagement:EngagementPage>

**Com EngagementPageOverlay:**

			<engagement:EngagementPageOverlay 
			    xmlns:engagement="using:using:Microsoft.Azure.Engagement.Overlay">
			
			    <!-- layout -->
			</engagement:EngagementPageOverlay>

> **Com EngagementPageOverlay para 8.1:**

			<engagement:EngagementPageOverlay 
			    xmlns:engagement="using:using:Microsoft.Azure.Engagement.Overlay">
			    <Grid>
			      <!-- layout -->
			    </Grid>
			</engagement:EngagementPageOverlay>

Em seguida, no arquivo .cs marque sua página em "EngagementPageOverlay" em vez de "EngagementPage" e importe "Microsoft.Azure.Engagement.Overlay".

			using Microsoft.Azure.Engagement.Overlay;

-   Substitua `EngagementPage`por `EngagementPageOverlay` :

**Com EngagementPage:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPage
			  {
			    [...]
			  }
			}

**Com EngagementPageOverlay:**

			using Microsoft.Azure.Engagement.Overlay;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPageOverlay 
			  {
			    [...]
			  }
			}

Agora, essa página usa o mecanismo de sobreposição do engagement, você não precisa inserir um modo de exibição da web.

A sobreposição do Engagement usa o primeiro elemento "Grid" que encontra no arquivo xaml para adicionar modos de exibição da web na sua página. Se você quiser localizar onde os modos de exibição da web serão definidos, você pode definir uma grade chamada "EngagementGrid" como esta:

			<Grid x:Name="EngagementGrid"></Grid>

Você pode personalizar o anúncio e a notificação de sobreposição diretamente em seus arquivos xaml e cs:

-   `EngagementAnnouncement.html` : Design html do modo de exibição da web do`Anúncio`.
-   `EngagementOverlayAnnouncement.xaml` : O design xaml do `Anúncio` .
-   `EngagementOverlayAnnouncement.xaml.cs` : O código vinculado `EngagementOverlayAnnouncement.xaml` .
-   `EngagementNotification.html` : O design html do modo de exibição de `Notificação`.
-   `EngagementOverlayNotification.xaml` : O design xaml de `Notificação` .
-   `EngagementOverlayNotification.xaml.cs` : O código vinculado `EngagementOverlayNotification.xaml`.
-   `EngagementPageOverlay.cs` : O código de exibição de anúncio e notificação `Sobreposição`.

### Integração do Modo de exibição da Web

Se você quiser usá-la, não use a [Overlay integration](#overlay-integration).

Para exibir o conteúdo do engagement você precisa integrar os dois Modos de exibição da Web xaml em cada página que você precisa exibir o anúncio e a notificação. Então adicione esse código em seu arquivo xaml:

			<WebView x:Name="engagement_notification_content" Visibility="Collapsed" ScriptNotify="scriptEvent" Height="64" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			<WebView x:Name="engagement_announcement_content" Visibility="Collapsed" ScriptNotify="scriptEvent" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

> **Para integração do 8.1:**

			<engagement:EngagementPage
			    xmlns:engagement="using:Microsoft.Azure.Engagement">
			    <Grid>
			      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" ScriptNotify="scriptEvent" Height="64" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" ScriptNotify="scriptEvent" HorizontalAlignment="Right" VerticalAlignment="Top"/> 
			      <!-- layout -->
			    </Grid>
			</engagement:EngagementPage>

E seu arquivo .cs associado precisa se parecer com:

			/// <summary>
			/// An empty page that can be used on its own or navigated to within a Frame.
			/// </summary>
			public sealed partial class ExampleEngagementReachPage : EngagementPage
			{
			  public ExampleEngagementReachPage()
			  {
			    this.InitializeComponent();
			
			   /* Set your webview elements to the correct size */
			    SetWebView(width, height);
			
			    Window.Current.SizeChanged += DisplayProperties_OrientationChanged;
			  }
			
			  #region to implement
			  /* Allow webview script to notify system */
			  private void scriptEvent(object sender, NotifyEventArgs e)
			  {
			  }
			
			  /* When page is left ensure to detach SizeChanged handler */
			  protected override void OnNavigatedFrom(NavigationEventArgs e)
			  {
			    Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
			    base.OnNavigatedFrom(e);
			  }
			
			  /* "width" is the current width of your application display */
			  double width = Window.Current.Bounds.Width;
			
			  /* "height" is the current height of your application display */
			  double height = Window.Current.Bounds.Height;
			
			  /// <summary>
			  /// Set your webview elements to the correct size
			  /// </summary>
			  /// <param name="width">The width of your current display</param>
			  /// <param name="height">The height of your current display</param>
			  private void SetWebView(double width, double height)
			  {
			    #pragma warning disable 4014
			    CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
			            () =>
			            {
			              this.engagement_notification_content.Width = width;
			              this.engagement_announcement_content.Width = width;
			              this.engagement_announcement_content.Height = height;
			            });
			  }
			
			  /// <summary>
			  /// Handler that take the Windows.Current.SizeChanged and indicate that webview have to be resized
			  /// </summary>
			  /// <param name="sender">Original event trigger</param>
			  /// <param name="e">Window Size Changed Event argument</param>
			  private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
			  {
			    double width = e.Size.Width;
			    double height = e.Size.Height;
			
			    /* Set your webview elements to the correct size */
			    SetWebView(width, height);
			  }
			  #endregion
			}

> Essa implementação incorpora o redimensionamento do Modo de exibição da Web quando a tela do dispositivo é girada.

## Manipular o push de dados (opcional)

Se desejar que o aplicativo seja capaz de receber push de dados do Reach, você precisa implementar dois eventos da classe EngagementReach:

Em App.xaml.cs em "Public App(){}" adicione:

			EngagementReach.Instance.DataPushStringReceived += (body) =>
			{
			  Debug.WriteLine("String data push message received: " + body);
			  return true;
			};
			
			EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
			{
			  Debug.WriteLine("Base64 data push message received: " + encodedBody);
			  // Do something useful with decodedBody like updating an image view
			  return true;
			};

Você pode ver que o retorno de chamada de cada método retorna um valor booleano. O Engagement envia um comentário ao seu back-end após distribuir o push de dados. Se o retorno de chamada retorna false, o comentário `sair` será enviado.  Caso contrário, ele será `ação`.  Se nenhum retorno de chamada for definido para os eventos, o comentário `soltar` serão retornado ao Engagement.

> [AZURE.WARNING] O Engagement não é capaz de receber múltiplos comentários para um push de dados. Se você pretende definir vários manipuladores em um evento, lembre-se de que os comentários corresponderá ao último enviado. Nesse caso, é recomendável sempre retornar o mesmo valor para evitar que os comentários sejam confusos no front-end.

## Personalizar a interface do usuário (opcional)

### Primeira etapa

Permitir que você personalize a interface do usuário do reach.

Para fazer isso, você precisa criar uma subclasse da classe `EngagementReachHandler`.

**Exemplo de código:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  internal class ExampleReachHandler : EngagementReachHandler
			  {
			   // Override EngagementReachHandler methods depending on your needs
			  }
			}

Depois, defina o conteúdo do campo `EngagementReach.Instance.Handler` com seu objeto personalizado em sua classe `App.xaml.cs` dentro do método `App()`.

**Exemplo de código:**

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  // your app initialization 
			  EngagementReach.Instance.Handler = new ExampleReachHandler();
			  // Engagement Agent and Reach initialization
			}

> [AZURE.NOTE] Por padrão, o Engagement usa a sua própria implementação de `EngagementReachHandler`.
> Você não precisa criar sua própria e se você fizer isso, você não precisa substituir cada método. O comportamento padrão é selecionar o objeto base do Engagement.

### Modo de exibição da Web

Por padrão, o Reach usará os recursos incorporados da DLL para exibir as notificações e páginas.

Para fornecer possibilidades de personalização completa possibilidades usamos somente o modo de exibição da web. Se você desejar personalizar layouts substitua diretamente os arquivos de recursos `EngagementAnnouncement.html` e `EngagementNotification.html`. O Engagement precisa de todos os códigos em `<body></body>` para executar corretamente. Mas você pode adicionar a marca externa `engagement_webview_area`.

No entanto, você pode optar por usar seus próprios recursos.

Você pode substituir os métodos `EngagementReachHandler` em sua subclasse para informar o Engagement para usar seus layouts, mas tome cuidado com o mecanismo incorporado do engagement:

**Exemplo de código:**
			
			// In your subclass of EngagementReachHandler
			
			public override string GetAnnouncementHTML()
			{
			  return base.GetAnnouncementHTML();
			}
			public override string GetAnnouncementName()
			{
			  return base.GetAnnouncementName();
			}
			public override string GetNotfificationHTML()
			{
			  return base.GetNotfificationHTML();
			}
			public override string GetNotfificationName()
			{
			  return base.GetNotfificationName();
			}


Por padrão, AnnouncementHTML é `ms-appx-web:///Resources/EngagementAnnouncement.html`. Representa o arquivo html que cria o conteúdo de uma mensagem de envio por push (anúncio de texto, anúncio da web e anúncio de pesquisa). AnnouncementName é `engagement_announcement_content`. É o nome do design do modo de exibição da web em sua página xaml.

NotfificationHTML é `ms-appx-web:///Resources/EngagementNotification.html`. Representa o arquivo html que cria a notificação de uma mensagem de envio por push. NotfificationName é `engagement_notification_content`. É o nome do design do modo de exibição da web em sua página xaml.

### Personalização

Você pode personalizar o modo de exibição da web de notificação e anúncio como você quiser se preservar o objeto Engagement. Tome cuidado para que o objeto do modo de exibição da web seja descrito três vezes. Primeira vez no xaml, segunda vez no arquivo .cs no método "setwebview()" e a terceira vez no arquivo html.

-   Em seu xaml você descreve o componente do modo de exibição da web do layout gráfico atual.
-   Em seu arquivo .cs, você pode definir "setwebview()" no qual você define a dimensão dos dois modos de exibição da web (notificação, anúncio). É muito eficiente quando o aplicativo é redimensionado.
-   No arquivo html do Engagement descrevemos o conteúdo do modo de exibição da web, o design e as posições dos elementos entre si.

### Iniciar mensagem

Quando um usuário clica em um sistema de notificação (um toast), o Engagement inicia o aplicativo, carrega o conteúdo das mensagens de envio por push e exibe a página da campanha correspondente.

Há um atraso entre a inicialização do aplicativo e a exibição da página (dependendo da velocidade da sua rede).

Para indicar ao usuário que algo está sendo carregado, você deve fornecer um informações visuais, como uma barra de progresso ou um indicador de progresso. O Engagement não pode manipular isto por conta própria, mas oferece alguns manipuladores para você.

Para implementar o retorno de chamada, em App.xaml.cs em "Public App(){}" adicione:

			/* The application has launched and the content is loading.
			 * You should display an indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
			
			/* The application has finished loading the content and the page
			 * is about to be displayed.
			 * You should hide the indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
			
			/* The content has been loaded, but an error has occurred.
			 * You can provide an information to the user.
			 * You should hide the indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Você pode definir o retorno de chamada em seu método "Public App() {}" de seu arquivo `App.xaml.cs`, de preferência antes da chamada `EngagementReach.Instance.Init()`.

> [AZURE.TIP] Cada manipulador é chamado pelo Thread de interface do usuário. Você não precisa se preocupar ao usar uma MessageBox ou algo relacionado à interface do usuário.

## Dica de esquema personalizado

Fornecemos o uso de esquema personalizado. Você pode enviar o tipo de URI diferente do front-end do Engagement para ser usado em seu aplicativo do engagement. O Esquema padrão como `http, ftp, ...` são gerenciados pelo Windows, uma janela será solicitada se não houver aplicativo padrão instalado no dispositivo. Outro esquema como o esquema de aplicativos pode ser usado. Além disso, você pode usar o esquema personalizado para seu aplicativo.

A maneira simples de definir um esquema personalizado em seu aplicativo é abrir o `Package.appxmanifest` vá para o painel `Declarações`.  Selecione `Protocolo` na caixa de rolagem de Declarações Disponíveis e adicione-o.  Edite o campo `Nome` com o novo nome de protocolo desejado.

Agora, para usar esses protocolo edite seu método `App.xaml.cs` com o método `OnActivated` e não se esqueça de inicializar o engagement aqui também:

			/// <summary>
			/// Enter point when app his called by another way than user click
			/// </summary>
			/// <param name="args">Activation args</param>
			protected override void OnActivated(IActivatedEventArgs args)
			{
			  /* Init engagement like it was launch by a custom uri scheme */
			  EngagementAgent.Instance.Init(args);
			  EngagementReach.Instance.Init(args);
			
			  //TODO design action to do when app is launch
			
			  #region Custom scheme use
			  if (args.Kind == ActivationKind.Protocol)
			  {
			    ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;
			
			    if (myProtocol.Uri.Scheme.Equals("protocolName"))
			    {
			      string path = myProtocol.Uri.AbsolutePath;
			    }
			  }
			  #endregion

<!--HONumber=47-->
