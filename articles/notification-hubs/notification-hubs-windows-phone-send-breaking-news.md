<properties
	pageTitle="Usar Hubs de notificação para enviar as últimas notícias (Windows Phone)"
	description="Use Hubs de notificação do Azure para usar a etiqueta (tag) nos registros e para enviar as últimas notícias para um aplicativo do Windows Phone."
	services="notification-hubs"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/18/2015" 
	ms.author="wesmc"/>

# Usar hubs de notificação para enviar notícias recentes

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##Visão geral

Este tópico mostra como usar os Hubs de Notificação do Azure para transmitir notificações de últimas notícias para um aplicativo do Windows Phone 8.0/8.1 Silverlight. Se você estiver selecionando o aplicativo da Windows Store ou do Windows Phone 8.1, por favor refira-se à versão [Windows Universal](notification-hubs-windows-store-dotnet-send-breaking-news.md). Ao concluir, você poderá se registrar nas categorias de últimas notícias que desejar e receber notificações por push apenas para essas categorias. Esse cenário é um padrão comum para muitos aplicativos nos quais as notificações precisam ser enviadas para grupos de usuários que tenham anteriormente expressado seu interesse por elas; por ex., leitor de RSS, aplicativos para fãs de música, etc.

Os cenários de transmissão são habilitados por meio da inclusão de um ou mais _rótulos_ durante a criação de um registro no hub de notificação. Quando as notificações são enviadas para um rótulo, todos os dispositivos que foram registrados para o rótulo receberão a notificação. Como os rótulos são simplesmente cadeias de caracteres, eles não precisam ser provisionados com antecedência. Para obter mais informações sobre rótulos, consulte [Diretrizes dos Hubs de Notificação].

##Pré-requisitos

Este tópico se baseia no aplicativo criado em [Introdução aos Hubs de Notificação]. Antes de iniciar o tutorial, você deve primeiro concluir a [Introdução aos Hubs de Notificação].

##Adicionar a seleção de categorias ao aplicativo

A primeira etapa é adicionar os elementos da interface do usuário na página principal existente que permitem ao usuário selecionar categorias para o registro. As categorias selecionadas por um usuário são armazenadas no dispositivo. Quando o aplicativo é iniciado, o registro do dispositivo é criado no seu hub de notificação com as categorias selecionadas como rótulos.

1. Abra o arquivo de projeto MainPage.xaml e substitua os elementos **Grid** chamados `TitlePanel` e `ContentPanel` pelo seguinte código:

        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>

        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>

2. No projeto, crie uma nova classe denominada **Notificações**, adicione o modificador **público** à definição de classe e depois adicione as seguintes instruções **de uso** para o novo arquivo de código:

		using Microsoft.Phone.Notification;
		using Microsoft.WindowsAzure.Messaging;
		using System.IO.IsolatedStorage;

3. Copie o seguinte código para a nova classe **Notificações**:

		private NotificationHub hub;

		public Notifications()
		{
		    hub = new NotificationHub("<hub name>", "<connection string with listen access>");
		}

		public async Task StoreCategoriesAndSubscribe(IEnumerable<string> categories)
		{
		    var categoriesAsString = string.Join(",", categories);
		    var settings = IsolatedStorageSettings.ApplicationSettings;
		    if (!settings.Contains("categories"))
		    {
		        settings.Add("categories", categoriesAsString);
		    }
		    else
		    {
		        settings["categories"] = categoriesAsString;
		    }
		    settings.Save();

		    await SubscribeToCategories(categories);
		}

		public async Task SubscribeToCategories(IEnumerable<string> categories)
		{
		    var channel = HttpNotificationChannel.Find("MyPushChannel");

		    if (channel == null)
		    {
		        channel = new HttpNotificationChannel("MyPushChannel");
		        channel.Open();
		        channel.BindToShellToast();
		    }

		    await hub.RegisterNativeAsync(channel.ChannelUri.ToString(), categories);
		}

    Essa classe usa o armazenamento local para armazenar as categorias de notícias que este dispositivo deverá receber. Ela também contém métodos para se registrar nessas categorias.

4. No código acima, substitua os espaços reservados `<hub name>` e `<connection string with listen access>` pelo nome de seu hub de notificação e a cadeia de conexão para *DefaultListenSharedAccessSignature* obtidos anteriormente.

	> [AZURE.NOTE]Como as credenciais que são distribuídas com um aplicativo cliente não são geralmente seguras, você só deve distribuir a chave para acesso de escuta com o aplicativo cliente. O acesso de escuta permite que seu aplicativo se registre para receber notificações, mas os registros existentes não podem ser modificados e as notificações não podem ser enviadas. A chave de acesso completa é usada em um serviço back-end protegido para enviar notificações e alterar os registros existentes.

4. No arquivo de projeto App.xaml.cs, adicione a propriedade a seguir na classe **Aplicativo**:

		public Notifications notifications = new Notifications();

	Esta propriedade é usada para criar e acessar uma instância **Notificações**.

5. Em MainPage.xaml.cs, adicione a seguinte linha:

		using Windows.UI.Popups;

6. No arquivo de projeto MainPage.xaml.cs, adicione o seguinte método:

		private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
		{
		    var categories = new HashSet<string>();
		    if (WorldCheckBox.IsChecked == true) categories.Add("World");
		    if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
		    if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
		    if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
		    if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
		    if (SportsCheckBox.IsChecked == true) categories.Add("Sports");

		    await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

		    MessageBox.Show("Subscribed to: " + string.Join(",", categories));
		}

	Esse método cria uma lista de categorias e usa a classe **Notificações** para armazenar a lista no armazenamento local e registrar os rótulos correspondentes com o hub de notificação. Quando as categorias são alteradas, o registro é recriado com as novas categorias.

Seu aplicativo agora é capaz de armazenar um conjunto de categorias no armazenamento local do dispositivo e registrar com o hub de notificação, sempre que o usuário alterar a seleção de categorias.

##Registrar-se para receber notificações

Estas etapas registram com o hub de notificação na inicialização, usando as categorias que foram armazenadas no armazenamento local.

> [AZURE.NOTE]Como o URI do canal atribuído pelo MPNS (Serviço de Notificação por Push da Microsoft) pode mudar a qualquer momento, você deve se registrar para receber notificações com frequência para evitar falhas de notificação. Este exemplo registra notificações cada vez que o aplicativo é iniciado. Para os aplicativos que são executados com frequência, mais de uma vez por dia, é possível ignorar o registro para preservar a largura de banda se tiver passado menos de um dia desde o registro anterior.

1. Adicione o seguinte código à classe **Notificações**:

		public IEnumerable<string> RetrieveCategories()
		{
		    var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
		    return categories != null ? categories.Split(',') : new string[0];
		}

	Esse procedimento retorna as categorias definidas na classe.

1. Abra o arquivo App.xaml.cs e adicione o modificador **async** ao método **Application\_Launching**.

2. No método **Application\_Launching** localize e substitua o código do registro de Hubs de Notificação que você adicionou na [Introdução aos Hubs de Notificação] pela seguinte linha de código:

		await notifications.SubscribeToCategories(notifications.RetrieveCategories());

	Isso garante que o aplicativo recupere as categorias do armazenamento local e solicite um registro para essas categorias toda vez que ele for iniciado.

3. No arquivo de projeto MainPage.xaml.cs, adicione o seguinte código que implementa o método **OnNavigatedTo**:

		protected override void OnNavigatedTo(NavigationEventArgs e)
		{
		    var categories = ((App)Application.Current).notifications.RetrieveCategories();

		    if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
		    if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
		    if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
		    if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
		    if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
		    if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
		}

	Isso atualiza a página principal com base no status de categorias salvas anteriormente.

O aplicativo agora está completo e pode armazenar um conjunto de categorias no armazenamento local do dispositivo utilizado para registrá-las com o hub de notificação, sempre que o usuário alterar a seleção de categorias. Em seguida, definiremos um back-end que possa enviar notificações de categoria para esse aplicativo.

##Enviar notificações de seu back-end

[AZURE.INCLUDE [notification-hubs-back-end](../../includes/notification-hubs-back-end.md)]

##Executar o aplicativo e gerar notificações

1. No Visual Studio, pressione F5 para compilar e iniciar o aplicativo.

	![][1]

	Observe que a interface do usuário do aplicativo fornece um conjunto de alternâncias que permite escolher as categorias nas quais você poderá assinar.

2. Habilite uma ou mais alternâncias de categorias e depois clique em **Assinar**.

	O aplicativo converte as categorias selecionadas em rótulos e solicita um novo registro do dispositivo para os rótulos selecionados do hub de notificação. As categorias registradas são retornadas e exibidas em uma caixa de diálogo.

	![][2]

4. Envie uma nova notificação do back-end usando uma das seguintes maneiras:

	+ **Aplicativo de console:** inicie o aplicativo de console.

	+ **Java/PHP:** execute seu aplicativo/script.

	As notificações para as categorias selecionadas são exibidas como notificações do sistema.

	![][3]

Você concluiu este tópico.

<!--##Next steps

In this tutorial we learned how to broadcast breaking news by category. Consider completing one of the following tutorials that highlight other advanced Notification Hubs scenarios:

+ [Use Notification Hubs to broadcast localized breaking news]

	Learn how to expand the breaking news app to enable sending localized notifications.

+ [Notify users with Notification Hubs]

	Learn how to push notifications to specific authenticated users. This is a good solution for sending notifications only to specific users.
-->

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[Introdução aos Hubs de Notificação]: /manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Use Notification Hubs to broadcast localized breaking news]: ../breakingnews-localized-wp8.md
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone]: ??

<!---HONumber=AcomDC_1210_2015-->