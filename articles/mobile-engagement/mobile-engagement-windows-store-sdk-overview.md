<properties 
	pageTitle="Visão geral do SDK do Windows Universal" 
	description="Visão geral do SDK do Windows Universal para o Mobile Engagement do Azure" 									
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/10/2015" 
	ms.author="piyushjo" />

#Visão geral do SDK do Windows Universal para o Mobile Engagement do Azure

Comece aqui para obter todos os detalhes sobre como integrar o Mobile Engagement do Azure em um aplicativo do Windows Universal. Se você gostaria de experimentá-lo primeiro, faça nosso [tutorial de 15 minutos](mobile-engagement-windows-store-dotnet-get-started.md).

Clique para ver o [Conteúdo do SDK](mobile-engagement-windows-store-sdk-content.md)

##Procedimentos de integração

1. Comece aqui: [Como integrar o Mobile Engagement em seu aplicativo do Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)

2. Para Notificações: [Como integrar o Reach (Notificações) em seu aplicativo do Windows Universal](mobile-engagement-windows-store-integrate-engagement-reach.md)

3. Implementação do plano de marcação: [Como usar a API de marcação avançada do Mobile Engagement em seu aplicativo do Windows Universal](mobile-engagement-windows-store-use-engagement-api.md).

##Notas de versão

###3\.2.0 (20/11/2015)

-   Suporte adicionado para aplicativos da Plataforma Universal do Windows do Windows 10.
-   Recurso de compartilhamento de canais de push adicionado para resolver conflitos de canal (agora compatível com os Hubs de Notificação do Azure).
-   Falha corrigida ao solicitar a ID do dispositivo logo após a inicialização.
-   Melhorias de logs do console.
-   Falha corrigida ao analisar algumas exceções não tratadas.

Para a versão anterior, consulte as [notas de versão completas](mobile-engagement-windows-store-release-notes.md)

##Procedimentos de atualização

Se você já tiver integrado uma versão anterior do Engagement no seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Talvez você precise seguir vários procedimentos se perdeu várias versões do SDK consulte os [Procedimentos de atualização](mobile-engagement-windows-store-upgrade-procedure.md) completos. Por exemplo, se você migrar do 0.10.1 para 0.11.0 você tem que primeiro seguir o procedimento "de 0.9.0 a 0.10.1” e depois o procedimento "de 0.10.1 a 0.11.0".

###De 3.1.0 a 3.2.0

#### Recursos
Esta etapa aborda apenas os recursos personalizados. Se você personalizou os recursos fornecidos pelo SDK (html, imagens, sobreposição), em seguida, você precisa fazer backup dos mesmos antes de atualizar e reaplicar sua personalização em recursos atualizados.

#### Integração do Modo de exibição da Web
Algumas melhorias para coincidir com fatores forma de dispositivo diferentes foram introduzidas nesta versão. Verifique se a integração da exibição da Web corresponde ao seguinte:

Na página XAML ():

			<WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			<WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

E no arquivo .cs associado:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
			/// <summary>
			/// An empty page that can be used on its own or navigated to within a Frame.
			/// </summary>
			public sealed partial class ExampleEngagementReachPage : EngagementPage
			{
			  public ExampleEngagementReachPage()
			  {
			    this.InitializeComponent();
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }
			
			  #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

			  /* When page is left ensure to detach SizeChanged handler. */
			  protected override void OnNavigatedFrom(NavigationEventArgs e)
			  {
			    Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
			    base.OnNavigatedFrom(e);
			  }
			  
			  /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
			  double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
			  double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
			  double width =  Window.Current.Bounds.Width;
			  double height =  Window.Current.Bounds.Height;
    #endif
			
			  /// <summary>
			  /// Set your webview elements to the correct size.
			  /// </summary>
			  /// <param name="width">The width of your current display.</param>
			  /// <param name="height">The height of your current display.</param>
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
			  /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
			  /// </summary>
			  /// <param name="sender">Original event trigger.</param>
			  /// <param name="e">Window Size Changed Event arguments.</param>
			  private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
			  {
			    double width = e.Size.Width;
			    double height = e.Size.Height;
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP			  
			  /// <summary>
			  /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
			  /// </summary>
			  /// <param name="sender">The related application view.</param>
			  /// <param name="e">Related event arguments.</param>
			  private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
			  {
			    double width = sender.VisibleBounds.Width;
			    double height = sender.VisibleBounds.Height;
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }
    #endif
			  #endregion
			}
    }

### Atualizar de versões anteriores

Consulte [Procedimentos de atualização](mobile-engagement-windows-store-upgrade-procedure/)

<!---HONumber=AcomDC_1125_2015-->