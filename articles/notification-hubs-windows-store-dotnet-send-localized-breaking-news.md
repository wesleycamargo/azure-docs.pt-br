<properties linkid="develop-notificationhubs-tutorials-send-localized-breaking-news-windowsdotnet" urlDisplayName="Últimas notícias localizadas" pageTitle="Tutorial de últimas notícias localizadas dos Hubs de Notificação" metaKeywords="" description="Saiba como usar os Hubs de Notificação de Barramento de Serviço do Azure para enviar notificações de últimas Notícias." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Usar Hubs de Notificação para enviar últimas notícias localizadas" authors="ricksal" solutions="" manager="" editor="" />
# Usar Hubs de Notificação para enviar últimas notícias localizadas

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/pt-br/manage/services/notification-hubs/breaking-news-localized-dotnet" title="C# da Windows Store" class="current">C# da Windows Store</a><a href="/pt-br/manage/services/notification-hubs/breaking-news-localized-ios" title="iOS">iOS</a>
</div>

Este tópico mostra como usar o recurso **template** dos Hubs de Notificação do Azure para transmitir notificações de últimas notícias que foram localizadas por idioma e dispositivo. Neste tutorial você começa com o aplicativo da Windows Store criado em [Usar Hubs de Notificação para enviar últimas notícias]. |Ao concluir, você poderá se registrar em categorias de seu interesse, especificar um idioma no qual deseja receber as notificações e receber notificações por push para as categorias selecionadas nesse idioma.

Este tutorial o orientará pelas etapas básicas a serem seguidas para habilitar este cenário:

1. [Conceitos de modelo] 
2. [Interface do usuário do aplicativo]
3. [Criando o aplicativo cliente da Windows Store]
4. [Enviar notificações a partir de seu back-end]


Há duas partes que compõem esse cenário:

- o aplicativo da Windows Store permite que dispositivos cliente especifiquem um idioma e se inscrevam em diferentes categorias de últimas notícias; 

- o back-end transmite as notificações usando os recursos **tag** e **template** dos Hubs de Notificação do Azure.



##Pré-requisitos ##

Você já deve ter concluído o tutorial [Usar Hubs de Notificação para envio de últimas notícias] e ter o código disponível, porque este tutorial se baseia diretamente nesse código. 

O Visual Studio 2012 também é necessário.


<h2><a name="concepts"></a><span class="short-header">conceitos</span>Conceitos de modelo</h2>

Em [Usar Hubs de Notificação para envio de últimas notícias] você criou um aplicativo que usou **marcas** para assinar notificações para diferentes categorias de notícias.
No entanto, muitos aplicativos são destinados a vários mercados e requerem localização. Isso significa que o próprio conteúdo das notificações deve ser localizado e entregue para o conjunto de dispositivos correto.
Neste tópico, mostraremos como usar o recurso **template** de Hubs de Notificação para entregar facilmente notificações de últimas notícias localizadas.

Observação: uma maneira de enviar notificações localizadas é criar várias versões de cada marca. Por exemplo, para oferecer suporte a inglês, francês e mandarim, precisamos de três marcas diferentes para notícias do mundo: "world_en", "world_fr" e "world_ch". Em seguida, precisamos enviar uma versão localizada das notícias do mundo para cada uma dessas marcas. Neste tópico, usamos modelos para evitar a proliferação de marcas e a necessidade de enviar várias mensagens.

Em um alto nível, os modelos são uma maneira de especificar como um dispositivo específico deve receber uma notificação. O modelo especifica o formato exato da carga referindo-se às propriedades que fazem parte da mensagem enviada por seu aplicativo de back-end. Em nosso caso, enviaremos uma mensagem independente de localidade contendo todos os idiomas com suporte:

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

Em seguida, verificaremos se os dispositivos se registram com um modelo que faz referência à propriedade correta. Por exemplo, um aplicativo da Windows Store que deseja receber uma mensagem de notificação do sistema simples se registrará no seguinte modelo:

	<toast>
	  <visual>
	    <binding template=\"ToastText01\">
	      <text id=\"1\">$(News_English)</text>
	    </binding>
	  </visual>
	</toast>



Os modelos são um recurso muito avançado sobre o qual você pode aprender em nosso artigo [Diretrizes dos Hubs de Notificação]. Uma referência à linguagem de expressão do modelo é incluída em [Instruções dos Hubs de Notificação para a Windows Store].


<h2><a name="ui"></a><span class="short-header">Interface do usuário do aplicativo</span>A interface de usuário do aplicativo</h2>

Agora vamos modificar o aplicativo Breaking News que você criou no tópico [Usar Hubs de Notificação para envio de últimas notícias] para enviar últimas notícias localizadas usando modelos.


Para adaptar seus aplicativos cliente para receber mensagens localizadas, você precisa substituir seus registros *nativos* (ou seja, registros que você especifica em um modelo) pelos registros do modelo.


Em seu aplicativo da Windows Store:

Altere seu MainPage.xaml para incluir uma caixa de combinação de localidade:

	<Grid Margin="120, 58, 120, 80"  
			Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="Button_Click" />
    </Grid>

<h2><a name="building-client"></a><span class="building app">App ui</span>Building the Windows Store client app</h2>

1. In your Notifications class, add a locale parameter to your  *StoreCategoriesAndSubscribe* and *SubscribeToCateories* methods.

		public async Task StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            await SubscribeToCategories(locale, categories);
        }

        public async Task SubscribeToCategories(string locale, IEnumerable<string> categories)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            var template = String.Format(@"<toast><visual><binding template=""ToastText01""><text id=""1"">$(News_{0})</text></binding></visual></toast>", locale);

            await hub.RegisterTemplateAsync(channel.Uri, template, "newsTemplate", categories);
        }

	Observe que, em vez de chamar o método *RegisterNativeAsync*, chamamos *RegisterTemplateAsync*: estamos registrando um formato de notificação específico em que o template depende da localidade. Também fornecemos um nome para o modelo ("newsTemplate"), porque queremos registrar mais de um modelo (por exemplo um para notificações dosistema e um para notificação de bloco) e precisamos nomeá-los para que possam ser atualizados ou excluídos.

	Observe que se um dispositivo registrar vários modelos com a mesma marca, uma mensagem de entrada direcionada para aquela marca resultará em várias notificações entregues ao dispositivo (um para cada modelo). Esse comportamento é útil quando a mesma mensagem lógica precisa resultar em várias notificações visuais, por exemplo, mostrando uma notificação e uma notificação do sistema em um aplicativo da Windows Store.

2. Adicione o seguinte método para recuperar a localidade armazenada:

		public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3. No MainPage.xaml.cs, atualize o manipulador de clique de botão recuperando o valor atual da caixa de combinação de localidade e fornecendo-o à chamada para a classe Notifications, conforme mostrado:

		 var locale = (string)Locale.SelectedItem;
            
         var categories = new HashSet<string>();
         if (WorldToggle.IsOn) categories.Add("World");
         if (PoliticsToggle.IsOn) categories.Add("Politics");
         if (BusinessToggle.IsOn) categories.Add("Business");
         if (TechnologyToggle.IsOn) categories.Add("Technology");
         if (ScienceToggle.IsOn) categories.Add("Science");
         if (SportsToggle.IsOn) categories.Add("Sports");

         await ((App)Application.Current).Notifications.StoreCategoriesAndSubscribe(locale, categories);

         var dialog = new MessageDialog(String .Format("Locale: {0}; Subscribed to: {1}", locale, string.Join(",", categories)));
         dialog.Commands.Add(new UICommand("OK"));
         await dialog.ShowAsync();

4. Finalmente, em seu arquivo App.xaml.cs, 
certifique-se de atualizar a sua chamada para o singleton Notificações no método *OnLaunched*:

		Notifications.SubscribeToCategories(Notifications.RetrieveLocale(), Notifications.RetrieveCategories());


<h2><a name="send"></a><span class="short-header">Enviar notificações localizadas</span>Enviar notificações localizadas de seu back-end</h2>

[WACOM.INCLUDE [notification-hubs-localized-back-end](../includes/notification-hubs-localized-back-end.md)]





## Próximas etapas

Para obter mais informações sobre como usar modelos, consulte [Notificar usuários com Hubs de Notificação: ASP.NET], [Notificar usuários com Hubs de Notificação: Serviços Móveis] e também [Diretrizes de Hubs de Notificação]. Uma referência à linguagem de expressão do modelo é [Instruções dos Hubs de Notificação para a Windows Store].

<!-- Anchors. -->
[Conceitos de modelo]: #concepts
[Interface do usuário do aplicativo]: #ui
[Compilando o aplicativo cliente da Windows Store]: #building-client
[Enviar notificações a partir de seu back-end]: #send
[Próximas etapas]:#next-steps

<!-- Images. -->





















<!-- URLs. -->
[Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started
[Notificar usuários com Hubs de Notificação: ASP.NET]: /pt-br/manage/services/notification-hubs/notify-users-aspnet
[Notificar usuários com Hubs de Notificação: Serviços Móveis]: /pt-br/manage/services/notification-hubs/notify-users
[Usar Hubs de Notificação para enviar últimas notícias]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet 

[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-dotnet
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-dotnet
[Notificações por push para usuários de aplicativos]: /pt-br/develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-push-js

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Objeto wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Diretrizes de Hubs de Notificação]: http://msdn.microsoft.com/pt-br/library/jj927170.aspx
[Instruções de Hubs de Notificação para iOS]: http://msdn.microsoft.com/pt-br/library/jj927168.aspx
[Instruções dos Hubs de Notificação para a Windows Store]: http://msdn.microsoft.com/pt-br/library/jj927172.aspx
