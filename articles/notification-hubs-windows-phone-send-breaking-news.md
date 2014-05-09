<properties linkid="manage-services-web-sites-operating-system-functionality-available-to-applications" urlDisplayName="Funcionalidade do sistema operacional disponível para aplicativos em Sites do Azure" pageTitle="Funcionalidade do sistema operacional disponível para aplicativos em Sites do Azure" metaKeywords="" description="Conheça a funcionalidade do sistema operacional de linha de base comum que está disponível para todos os aplicativos em execução nos Sites do Azure, incluindo acesso a arquivos, acesso à rede e acesso ao Registro." metaCanonical="" services="notification-hubs" documentationCenter="" title="Usar Hubs de Notificação para envio de últimas notícias" authors="glenga" solutions="" manager="" editor="" />

# Usar Hubs de Notificação para envio de últimas notícias
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/pt-br/manage/services/notification-hubs/breaking-news-dotnet" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/manage/services/notification-hubs/breaking-news-wp8" title="" class="current">Windows Phone</a>Windows Phone<a href="/pt-br/manage/services/notification-hubs/breaking-news-ios" title="">iOS</a>iOS
</div>

Este tópico mostra como usar os Hubs de Notificação do Azure para transmitir notificações de últimas notícias para um aplicativo da Windows Store. Ao concluir, você poderá se registrar nas categorias de últimas notícias que desejar e receber notificações por push apenas para essas categorias. Esse cenário é um padrão comum para muitos aplicativos nos quais as notificações precisam ser enviadas para grupos de usuários que tenham anteriormente expressado seu interesse por elas; por ex., leitor de RSS, aplicativos para fãs de música, etc. 

Os cenários de transmissão são habilitados por meio da inclusão de uma ou mais _tags_ ao criar um registro no Hub de Notificação. Quando as notificações são enviadas para um rótulo, todos os dispositivos que foram registrados para o rótulo receberão a notificação. Como os rótulos são simplesmente cadeias de caracteres, eles não precisam ser provisionados com antecedência. Para obter mais informações sobre rótulos, consulte [Diretrizes dos Hubs de Notificação]. 

Este tutorial o orientará pelas etapas básicas a serem seguidas para habilitar este cenário:

1. [Adicionar a seleção de categorias ao aplicativo]
2. [Registrar-se para receber notificações]
3. [Enviar notificações de seu back-end]
4. [Executar o aplicativo e gerar notificações]

Este tópico se baseia no aplicativo criado em [Introdução aos Hubs de Notificação]. Antes de iniciar o tutorial, você deve primeiro concluir a [Introdução aos Hubs de Notificação].

##<a name="adding-categories"></a>Adicionar a seleção de categorias ao aplicativo

A primeira etapa é adicionar os elementos da interface do usuário na página principal existente que permitem ao usuário selecionar categorias para o registro. As categorias selecionadas por um usuário são armazenadas no dispositivo. Quando o aplicativo é iniciado, o registro do dispositivo é criado no seu hub de notificação com as categorias selecionadas como rótulos. 

1. Abra o arquivo de projeto MainPage.xaml e substitua o elementos **Grid** chamados `TitlePanel` e `ContentPanel` pelo seguinte código:
			
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

3. Copie o seguinte código na nova classe **Notifications**:

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

4. No código acima, substitua os espaços reservados `<hub name>` e `<connection string with listen access>` pelo nome do hub de notificação e a cadeia de conexão por *DefaultListenSharedAccessSignature* que você tiver obtido anteriormente.

	<div class="dev-callout"><strong>Observação</strong> 
		<p>Como as credenciais que são distribuídas com um aplicativo cliente não são geralmente seguras, você só deve distribuir a chave para acesso de escuta com o aplicativo cliente. O acesso de escuta permite que seu aplicativo se registre para receber notificações, mas os registros existentes não podem ser modificados e as notificações não podem ser enviadas. A chave de acesso completa é usada em um serviço back-end protegido para enviar notificações e alterar os registros existentes.</p>
	</div> 

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

##<a name="register"></a>Registrar-se para receber notificações

Estas etapas registram com o hub de notificação na inicialização, usando as categorias que foram armazenadas no armazenamento local. 

<div class="dev-callout"><strong>Observação</strong> 
	<p>Como o URI do canal atribuído pelo MPNS (Serviço de Notificação por Push da Microsoft) pode ser escolhido a qualquer momento, você deve se registrar para receber notificações com frequência para evitar falhas de notificação. Este exemplo registra a notificação a cada vez que o aplicativo é iniciado. Para os aplicativos que são executados com frequência, mais de uma vez por dia, é possível ignorar o registro para preservar a largura de banda se tiver passado menos de um dia desde o registro anterior.</p>
</div>  

1. Adicione o seguinte código à classe **Notificações**:

		public IEnumerable<string> RetrieveCategories()
		{
		    var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
		    return categories != null ? categories.Split(',') : new string[0];
		}

	Esse procedimento retorna as categorias definidas na classe.

1. Abra o arquivo App.xaml.cs e adicione o modificador **async** ao método **Application_Launching**.

2. No método **Application_Launching**, localize e substitua o código do registro de Hubs de Notificação que você adicionou na [Introdução aos Hubs de Notificação] pela seguinte linha de código:

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

<h2><a name="send"></a><span class="short-header">Enviar notificações</span>Enviar notificações de seu back-end</h2>

[WACOM.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

##<a name="test-app"></a>Executar o aplicativo e gerar notificações

1. No Visual Studio, pressione F5 para compilar e iniciar o aplicativo.

	![][1] 

	Observe que a interface do usuário do aplicativo fornece um conjunto de alternâncias que permite escolher as categorias nas quais você poderá assinar. 

2. Habilite uma ou mais alternâncias de categorias e depois clique em **Assinar**.

	O aplicativo converte as categorias selecionadas em rótulos e solicita um novo registro do dispositivo para os rótulos selecionados do hub de notificação. As categorias registradas são retornadas e exibidas em uma caixa de diálogo.

	![][2]

4. Envie uma nova notificação do back-end usando uma das seguintes maneiras:

	+ **Aplicativo do console:** inicie o aplicativo do console.

	+ **Serviços Móveis:** clique na guia **Agendador**, clique no trabalho e clique em **Executar uma vez**.

	As notificações para as categorias selecionadas são exibidas como notificações do sistema.

	![][3]

Você concluiu este tópico.

<!--## <a name="next-steps"> </a>Próximas etapas

Neste tutorial, aprendemos como enviar as notícias mais recentes por categoria. Considere a conclusão de um dos seguintes tutoriais que destacam outros cenários avançados de Hubs de Notificação:

+ [Usar os Hubs de Notificação para transmitir as últimas notícias localizadas]

	Saiba como expandir o aplicativo das últimas notícias para habilitar o envio de notificações localizadas. 

+ [Notificar usuários com Hubs de Notificação]

	Saiba como enviar notificações por push a usuários autenticados específicos. É uma boa solução enviar notificações somente a usuários específicos.
-->

<!-- Anchors. -->
[Adicionar seleção de categoria ao aplicativo]: #adding-categories
[Registrar-se para notificações]: #register
[Enviar notificações de seu back-end]: #send
[Executar o aplicativo e gerar notificações]: #test-app
[Próximas etapas]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[Introdução aos Hubs de Notificação]: /pt-br/manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Usar Hubs de Notificação para transmitir últimas notícias localizadas]: ./breakingnews-localized-wp8.md 
[Notificar os usuários com Hubs de Notificação]: /pt-br/manage/services/notification-hubs/notify-users/
[Serviço Móvel]: /pt-br/develop/mobile/tutorials/get-started
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/pt-br/library/jj927170.aspx
[Instruções sobre Hubs de Notificação para o Windows Phone]: ??

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/





