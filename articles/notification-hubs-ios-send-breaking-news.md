<properties linkid="develop-notificationhubs-tutorials-send-breaking-news-ios" urlDisplayName="Últimas notícias" pageTitle="Tutorial de últimas notícias dos Hubs de Notificação - iOS" metaKeywords="" description="Saiba como usar os Hubs de Notificação do Barramento de Serviço do Azure para enviar notificações de últimas notícias a dispositivos iOS." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Usar Hubs de Notificação para envio de últimas notícias" authors="ricksal" solutions="" manager="" editor="" />

# Usar Hubs de Notificação para envio de últimas notícias
<div class="dev-center-tutorial-selector sublanding">     	
	<a href="/pt-br/manage/services/notification-hubs/breaking-news-dotnet" title="C# da Windows Store" >C# da Windows Store</a><a href="/pt-br/manage/services/notification-hubs/breaking-news-wp8" title="">Windows Phone</a>Windows Phone<a href="/pt-br/manage/services/notification-hubs/breaking-news-ios" title="" class="current">iOS</a>iOS
</div>

Este tópico mostra como usar os Hubs de Notificação do Azure para transmitir notificações de últimas notícias a um aplicativo iOS. Ao concluir, você poderá se registrar nas categorias de últimas notícias que desejar e receber notificações por push apenas para essas categorias. Esse cenário é um padrão comum para muitos aplicativos nos quais as notificações precisam ser enviadas para grupos de usuários que tenham anteriormente expressado seu interesse por elas; por ex., leitor de RSS, aplicativos para fãs de música, etc. 

Os cenários de transmissão são habilitados por meio da inclusão de um ou mais _tags_ durante a criação de um registro no hub de notificação. Quando as notificações são enviadas para um rótulo, todos os dispositivos que foram registrados para o rótulo receberão a notificação. Como os rótulos são simplesmente cadeias de caracteres, eles não precisam ser provisionados com antecedência. Para obter mais informações sobre rótulos, consulte [Diretrizes dos Hubs de Notificação]. 

Este tutorial o orientará pelas etapas básicas a serem seguidas para habilitar este cenário:

1. [Adicionar a seleção de categorias ao aplicativo]
2. [Registrar-se para receber notificações]
3. [Enviar notificações de seu back-end]
4. [Executar o aplicativo e gerar notificações]

Este tópico se baseia no aplicativo criado em [Introdução aos Hubs de Notificação][get-started] Antes de iniciar o tutorial, você deve primeiro concluir a [Introdução aos Hubs de Notificação][get-started].

##<a name="adding-categories"></a>Adicionar a seleção de categorias ao aplicativo

A primeira etapa é adicionar os elementos da interface do usuário a seu storyboard existente que permite que o usuário selecione as categorias a serem registradas. As categorias selecionadas por um usuário são armazenadas no dispositivo. Quando o aplicativo é iniciado, o registro do dispositivo é criado no seu hub de notificação com as categorias selecionadas como rótulos. 

2. No MainStoryboard_iPhone.storyboard, adicione os seguintes componentes da biblioteca de objetos:
	+ Um rótulo com o texto "Breaking News",
	+ Rótulos com os textos das categorias "World", "Politics", "Business", "Technology", "Science", "Sports"
	+ Seis comutadores, um por categoria,
	+ No botão rotulado "Subscribe"
	
	O storyboard deve ter a seguinte aparência:
	
	![][3]
    
3. No editor do assistente, crie saídas para todos os comutadores e chame-os de "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"

	![][4]

4. Crie uma Ação para o botão chamado "subscriber". Seu BreakingNewsViewController.h deve conter o seguinte:
			
		@property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

		- (IBAction)subscribe:(id)sender;

5. Crie uma nova classe chamada `Notifications`. Copie o código a seguir na seção da interface do arquivo Notifications.h:

		- (void)storeCategoriesAndSubscribeWithCategories:(NSArray*) categories completion: (void (^)(NSError* error))completion;
		- (void)subscribeWithCategories:(NSSet*) categories completion:(void (^)(NSError *))completion;

6. Adicione a seguinte diretiva de importação a Notifications.m:

		#import <WindowsAzureMessaging/WindowsAzureMessaging.h>

7. Copie o código a seguir na seção de implementação do arquivo Notifications.m:
		
		- (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
		    
		    [self subscribeWithCategories:categories completion:completion];
		}

		- (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion{
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string with listen access>" notificationHubPath:@"<hub name>"];
		    
		    [hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];
		}
 
	Essa classe usa o armazenamento local para armazenar as categorias de notícias que esse dispositivo precisa receber. Além, disso, ele contém métodos para registrar essas categorias.

4. No código acima, substitua os espaços reservados `<hub name>` e `<connection string with listen access>` pelo nome do hub de notificação e a cadeia de conexão por *DefaultListenSharedAccessSignature* que você tiver obtido anteriormente.

	<div class="dev-callout"><strong>Observação</strong> 
		<p>Como as credenciais que são distribuídas com um aplicativo cliente não são geralmente seguras, você só deve distribuir a chave para acesso de escuta com o aplicativo cliente. O acesso de escuta permite que seu aplicativo se registre para receber notificações, mas os registros existentes não podem ser modificados e as notificações não podem ser enviadas. A chave de acesso completa é usada em um serviço back-end protegido para enviar notificações e alterar os registros existentes.</p>
	</div> 

8. No arquivo BreakingNewsAppDelegate.h, adicione a seguinte propriedade:

		@property (nonatomic) Notifications* notifications;

	Isso cria uma instância de singleton da classe Notification no AppDelegate.
	
9. No método **didFinishLaunchingWithOptions** em BreakingNewsAppDelegate.m, adicione o seguinte código antes de **registerForRemoteNotificationTypes**:
	
		self.notifications = [[Notifications alloc] init];
					   
	Isso inicializa o singleton Notification. 

10. No método **didRegisterForRemoteNotificationsWithDeviceToken** em BreakingNewsAppDelegate.m, remova a chamada para **registerNativeWithDeviceToken** e adicione o seguinte código:
	
		self.notifications.deviceToken = deviceToken;

	Observe que neste ponto não deve haver nenhum outro código no método **didRegisterForRemoteNotificationsWithDeviceToken**. 

11.	Adicione o seguinte método em BreakingNewsAppDelegate.m:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:
			(NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
		    [alert show];
	    }

	Este método trata as notificações recebidas quando o aplicativo está em execução exibindo um simples **UIAlert**.

9. Em BreakingNewsViewController.m, copie o código a seguir no método **subscribe** gerado por XCode:

		NSMutableArray* categories = [[NSMutableArray alloc] init];
	    
	    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
	    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
	    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
	    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
	    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
	    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];
	    
	    Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];
	    
	    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
	        if (!error) {
	            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
	                                  @"Subscribed!" delegate:nil cancelButtonTitle:
	                                  @"OK" otherButtonTitles:nil, nil];
	            [alert show];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];
	
	Esse método cria um **NSMutableArray** de categorias e usa a classe **Notifications** para armazenar a lista no armazenamento local e registra as marcas correspondentes com seu hub de notificação. Quando as categorias são alteradas, o registro é recriado com as novas categorias.

Seu aplicativo agora é capaz de armazenar um conjunto de categorias no armazenamento local do dispositivo e registrar com o hub de notificação, sempre que o usuário alterar a seleção de categorias. 

##<a name="register"></a>Registrar-se para receber notificações

Estas etapas registram com o hub de notificação na inicialização, usando as categorias que foram armazenadas no armazenamento local. 

<div class="dev-callout"><strong>Observação</strong> 
	<p>Como o token do dispositivo atribuído pelo APNS (Serviço de Notificação por Push da Apple) pode ser escolhido a qualquer momento, você deve se registrar para receber notificações com frequência para evitar falhas de notificação. Este exemplo registra a notificação a cada vez que o aplicativo é iniciado. Para os aplicativos que são executados com frequência, mais de uma vez por dia, é possível ignorar o registro para preservar a largura de banda se tiver passado menos de um dia desde o registro anterior.</p>
</div>  

1. Adicione o seguinte método na seção de interface do arquivo Notifications.h:

		- (NSSet*)retrieveCategories;

	Esse código recupera as categorias na classe Notifications.

2. Adicione a implementação correspondente no arquivo Notifications.m:
	
		- (NSSet*)retrieveCategories {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    
		    NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];
		    
		    if (!categories) return [[NSSet alloc] init];
		    return [[NSSet alloc] initWithArray:categories];
		}

2. Adicione o seguinte código no método **didRegisterForRemoteNotificationsWithDeviceToken**:

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];
    
	    NSSet* categories = [notifications retrieveCategories];
	    [notifications subscribeWithCategories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

	Isso garante que o aplicativo recupere as categorias do armazenamento local e solicite um registro para essas categorias toda vez que ele for iniciado.

3. No BreakingNewsViewController.h, adicione o seguinte código no método **viewDidLoad**:

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];
    
	    NSSet* categories = [notifications retrieveCategories];
	    
	    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
	    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
	    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
	    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
	    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
	    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;

	Isso atualiza a interface do usuário na inicialização com base no status das categorias salvas anteriormente.

O aplicativo agora está completo e pode armazenar um conjunto de categorias no armazenamento local do dispositivo utilizado para registrá-las com o hub de notificação, sempre que o usuário alterar a seleção de categorias. Em seguida, você definirá um back-end que pode enviar notificações por categoria para esse aplicativo.

<h2><a name="send"></a><span class="short-header">Enviar notificações</span>Enviar notificações de seu back-end</h2>

[WACOM.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

##<a name="test-app"></a>Executar o aplicativo e gerar notificações

1. Pressione o botão Executar para compilar o projeto e iniciar o aplicativo.

	![][2] 

	Observe que a interface do usuário do aplicativo fornece um conjunto de alternâncias que permite escolher as categorias nas quais você poderá assinar. 

2. Habilite uma ou mais alternâncias de categorias e depois clique em **Assinar**.

	Quando você escolher **Subscribe**, o aplicativo converterá as categorias selecionadas em marcas e solicitará um novo registro de dispositivo para as marcas selecionadas no hub de notificação. 

4. Envie uma nova notificação do back-end usando uma das seguintes maneiras:

	+ **Aplicativo do console:** inicie o aplicativo do console.

	+ **Serviços Móveis:** clique na guia **Agendador**, clique no trabalho e clique em **Executar uma vez**.

5. As notificações para as categorias selecionadas são exibidas como notificações do sistema.

## <a name="next-steps"> </a>Próximas etapas

Neste tutorial, aprendemos como enviar as notícias mais recentes por categoria. Considere a conclusão de um dos seguintes tutoriais que destacam outros cenários avançados de Hubs de Notificação:

+ **[Usar os Hubs de Notificação para transmitir as últimas notícias localizadas]**

	Saiba como expandir o aplicativo das últimas notícias para habilitar o envio de notificações localizadas. 

+ **[Notificar usuários com Hubs de Notificação]**

	Saiba como enviar notificações por push a usuários autenticados específicos. É uma boa solução enviar notificações somente a usuários específicos.

<!-- Anchors. -->
[Adicionar seleção de categoria ao aplicativo]: #adding-categories
[Registrar-se para notificações]: #register
[Enviar notificações de seu back-end]: #send
[Executar o aplicativo e gerar notificações]: #test-app
[Próximas etapas]: #next-steps

<!-- Images. -->
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png
[4]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios3.png







<!-- URLs. -->
[Instruções sobre os Hubs de Notificação do Barramento de Serviço (aplicativos iOS)]: http://msdn.microsoft.com/pt-br/library/jj927168.aspx
[Usar os Hubs de Notificação para transmitir as últimas notícias localizadas]: /pt-br/manage/services/notification-hubs/breaking-news-localized-dotnet/
[Serviço Móvel]: /pt-br/develop/mobile/tutorials/get-started
[Notificar os usuários com Hubs de Notificação]: /pt-br/manage/services/notification-hubs/notify-users/

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/pt-br/library/jj927170.aspx
[Instruções sobre Hubs de Notificação para iOS]: http://msdn.microsoft.com/pt-br/library/jj927168.aspx
[Introdução]: /pt-br/manage/services/notification-hubs/get-started-notification-hubs-ios/


