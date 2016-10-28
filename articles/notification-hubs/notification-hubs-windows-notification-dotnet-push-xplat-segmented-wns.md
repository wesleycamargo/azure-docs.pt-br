<properties
	pageTitle="Usar Hubs de Notificação para enviar as notícias mais recentes (Windows Universal)"
	description="Use Hubs de notificação do Azure com marcas no registro para enviar as últimas notícias para um aplicativo Windows universal."
	services="notification-hubs"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="erikre"
	editor=""/>


<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/29/2016"
	ms.author="wesmc"/>

# Usar hubs de notificação para enviar notícias recentes


[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##Visão geral

Este tópico mostra como usar os Hubs de Notificação do Azure para difundir notificações de últimas notícias para um aplicativo da Windows Store ou Windows Phone 8.1 (não Silverlight). Se você estiver selecionando o Windows Phone 8.1 Silverlight, então refira-se à versão [Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md). Ao concluir, você poderá se registrar nas categorias de últimas notícias que desejar e receber notificações por push apenas para essas categorias. Esse cenário é um padrão comum para muitos aplicativos em que as notificações devem ser enviadas a grupos de usuários que tenham expressado anteriormente seu interesse por elas, por exemplo, leitor RSS, aplicativos para fãs de música e assim por diante.

Os cenários de transmissão são habilitados por meio da inclusão de um ou mais _rótulos_ durante a criação de um registro no hub de notificação. Quando as notificações são enviadas para um rótulo, todos os dispositivos que foram registrados para o rótulo receberão a notificação. Como os rótulos são simplesmente cadeias de caracteres, eles não precisam ser provisionados com antecedência. Para obter mais informações sobre marcas, consulte [Expressões de Marca e Roteamento dos Hubs de Notificação](notification-hubs-tags-segment-push-message.md).

##Pré-requisitos

Este tópico se baseia no aplicativo criado em [Introdução aos Hubs de Notificação][get-started]. Antes de iniciar o tutorial, você deve primeiro concluir a [Introdução aos Hubs de Notificação][get-started].

##Adicionar a seleção de categorias ao aplicativo

A primeira etapa é adicionar os elementos da interface do usuário na página principal existente que permitem ao usuário selecionar categorias para o registro. As categorias selecionadas por um usuário são armazenadas no dispositivo. Quando o aplicativo é iniciado, o registro do dispositivo é criado no seu hub de notificação com as categorias selecionadas como rótulos.

1. Abra o arquivo de projetos MainPage.xaml e copie o seguinte código no elemento **Grid**:

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>


2. Clique com o botão direito do mouse no projeto **Compartilhado** e adicione uma nova classe chamada **Notificações**, adicione o modificador **public** à definição de classe e depois adicione as seguintes instruções **using** ao novo arquivo de código:

		using Windows.Networking.PushNotifications;
		using Microsoft.WindowsAzure.Messaging;
		using Windows.Storage;
		using System.Threading.Tasks;

3. Copie o seguinte código para a nova classe **Notificações**:

		private NotificationHub hub;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            return await SubscribeToCategories(categories);
        }

		public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }

        public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration to support notifications across platforms.
			// Any template notifications that contain messageParam and a corresponding tag expression
			// will be delivered for this registration.

            const string templateBodyWNS = "<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>";

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
					categories);
        }

    Essa classe usa o armazenamento local para armazenar as categorias de notícias que este dispositivo deverá receber. Observe que, em vez de chamar o método *RegisterNativeAsync*, chamamos *RegisterTemplateAsync* para se registrar para as categorias usando um registro de modelo.
	
	Também fornecemos um nome para o modelo ("simpleWNSTemplateExample"), porque queremos registrar mais de um modelo (por exemplo um para notificações do sistema e um para notificação de bloco) e precisamos nomeá-los para que possam ser atualizados ou excluídos.

	Observe que se um dispositivo registrar vários modelos com a mesma marca, uma mensagem de entrada direcionada para aquela marca resultará em várias notificações entregues ao dispositivo (um para cada modelo). Esse comportamento é útil quando a mesma mensagem lógica precisa resultar em várias notificações visuais, por exemplo, mostrando uma notificação e uma notificação do sistema em um aplicativo da Windows Store.

	Para obter mais informações sobre o uso de modelos, consulte [Modelos](notification-hubs-templates-cross-platform-push-messages.md).




4. No arquivo de projeto App.xaml.cs, adicione a propriedade a seguir na classe **Aplicativo**:

		public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

	Esta propriedade é usada para criar e acessar uma instância **Notificações**.

	No código acima, substitua os espaços reservados `<hub name>` e `<connection string with listen access>` pelo nome de seu hub de notificação e a cadeia de conexão para *DefaultListenSharedAccessSignature* obtidos anteriormente.

	> [AZURE.NOTE] Como as credenciais que são distribuídas com um aplicativo cliente não são geralmente seguras, você só deve distribuir a chave para acesso de escuta com o aplicativo cliente. O acesso de escuta permite que seu aplicativo se registre para receber notificações, mas os registros existentes não podem ser modificados e as notificações não podem ser enviadas. A chave de acesso completa é usada em um serviço de back-end seguro para enviar notificações e alterar os registros existentes.

5. Em MainPage.xaml.cs, adicione a seguinte linha:

		using Windows.UI.Popups;

6. No arquivo de projeto MainPage.xaml.cs, adicione o seguinte método:

		private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

	Esse método cria uma lista de categorias e usa a classe **Notificações** para armazenar a lista no armazenamento local e registrar os rótulos correspondentes com o hub de notificação. Quando as categorias são alteradas, o registro é recriado com as novas categorias.

Seu aplicativo agora é capaz de armazenar um conjunto de categorias no armazenamento local do dispositivo e registrar com o hub de notificação, sempre que o usuário alterar a seleção de categorias.

##Registrar-se para receber notificações

Estas etapas registram com o hub de notificação na inicialização, usando as categorias que foram armazenadas no armazenamento local.

> [AZURE.NOTE] Como a URI do canal atribuído pelo Serviço de Notificação do Windows (WNS) pode ser escolhida a qualquer momento, você deve se registrar para receber notificações com frequência para evitar falhas de notificação. Este exemplo registra a notificação a cada vez que o aplicativo é iniciado. Para os aplicativos que são executados com frequência, mais de uma vez por dia, é possível ignorar o registro para preservar a largura de banda se tiver passado menos de um dia desde o registro anterior.

1. Abra o arquivo App.xaml.cs e atualize o método **InitNotificationsAsync** para usar a classe `notifications` para assinar com base em categorias.

		// *** Remove or comment out these lines *** 
	    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
	    //var hub = new NotificationHub("your hub name", "your listen connection string");
	    //var result = await hub.RegisterNativeAsync(channel.Uri);
	
	    var result = await notifications.SubscribeToCategories();

	Isso garante que o aplicativo recupere as categorias do armazenamento local e solicite um registro para essas categorias toda vez que ele for iniciado. O método **InitNotificationsAsync** foi criado como parte do tutorial [Introdução aos Hubs de Notificação][get-started].

3. No arquivo de projeto MainPage.xaml.cs, adicione o seguinte código ao método *OnNavigatedTo*:

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldToggle.IsOn = true;
            if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
            if (categories.Contains("Business")) BusinessToggle.IsOn = true;
            if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
            if (categories.Contains("Science")) ScienceToggle.IsOn = true;
            if (categories.Contains("Sports")) SportsToggle.IsOn = true;
        }

	Isso atualiza a página principal com base no status de categorias salvas anteriormente.

O aplicativo agora está completo e pode armazenar um conjunto de categorias no armazenamento local do dispositivo utilizado para registrá-las com o hub de notificação, sempre que o usuário alterar a seleção de categorias. Em seguida, definiremos um back-end que possa enviar notificações de categoria para esse aplicativo.

##Enviando notificações marcadas

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##Executar o aplicativo e gerar notificações

1. No Visual Studio, pressione F5 para compilar e iniciar o aplicativo.

	![][1]

	Observe que a interface do usuário do aplicativo fornece um conjunto de alternâncias que permite escolher as categorias nas quais você poderá assinar.

2. Habilite uma ou mais alternâncias de categorias e depois clique em **Assinar**.

	O aplicativo converte as categorias selecionadas em rótulos e solicita um novo registro do dispositivo para os rótulos selecionados do hub de notificação. As categorias registradas são retornadas e exibidas em uma caixa de diálogo.

	![][19]

4. Envie uma nova notificação do back-end usando uma das seguintes maneiras:

	+ **Aplicativo de console:** inicie o aplicativo de console.

	+ **Java/PHP:** execute seu aplicativo/script.

	As notificações para as categorias selecionadas são exibidas como notificações do sistema.

	![][14]

##Próximas etapas

Neste tutorial, aprendemos a enviar as últimas notícias por categoria. Considere a conclusão de um dos seguintes tutoriais que destacam outros cenários avançados de Hubs de Notificação:

+ [Usar os Hubs de Notificação para transmitir as últimas notícias localizadas]

	Saiba como expandir o aplicativo das últimas notícias para habilitar o envio de notificações localizadas.



<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Usar os Hubs de Notificação para transmitir as últimas notícias localizadas]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591

<!---HONumber=AcomDC_0907_2016-->