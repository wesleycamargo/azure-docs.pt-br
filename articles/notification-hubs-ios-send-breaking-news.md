<properties linkid="notification-hubs-ios-send-breaking-news" pageTitle="Notification Hubs Breaking News Tutorial - iOS" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send breaking news notifications to iOS devices." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send breaking news" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Usar Hubs de Notificação para enviar notícias de última hora

<div class="dev-center-tutorial-selector sublanding">       
    <a href="/pt-br/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal" >Windows Universal</a><a href="/pt-br/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS" class="current">iOS</a>
    <a href="/pt-br/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android">Android</a>
</div>

Este tópico mostra como usar os Hubs de Notificação do Azure para transmitir notificações de últimas notícias a um aplicativo iOS. Ao concluir, você poderá se registrar nas categorias de últimas notícias que desejar e receber notificações por push apenas para essas categorias. Esse cenário é um padrão comum para muitos aplicativos nos quais as notificações precisam ser enviadas para grupos de usuários que tenham anteriormente expressado seu interesse por elas; por ex., leitor de RSS, aplicativos para fãs de música, etc.

Os cenários de transmissão são habilitados por meio da inclusão de um ou mais *rótulos* durante a criação de um registro no hub de notificação. Quando as notificações são enviadas para um rótulo, todos os dispositivos que foram registrados para o rótulo receberão a notificação. Como os rótulos são simplesmente cadeias de caracteres, eles não precisam ser provisionados com antecedência. Para obter mais informações sobre rótulos, consulte [Diretrizes dos Hubs de Notificação][Diretrizes dos Hubs de Notificação].

Este tutorial explicará estas etapas básicas a serem seguidas para habilitar este cenário:

1.  [Adicionar a seleção de categorias ao aplicativo][Adicionar a seleção de categorias ao aplicativo]
2.  [Registrar-se para receber notificações][Registrar-se para receber notificações]
3.  [Enviar notificações de seu back-end][Enviar notificações de seu back-end]
4.  [Executar o aplicativo e gerar notificações][Executar o aplicativo e gerar notificações]

Este tópico se baseia no aplicativo criado em [Introdução aos Hubs de Notificação][Introdução aos Hubs de Notificação]. Antes de iniciar o tutorial, você deve primeiro concluir a [Introdução aos Hubs de Notificação][Introdução aos Hubs de Notificação].

## <a name="adding-categories"></a>Adicionar a seleção de categorias ao aplicativo

A primeira etapa é adicionar os elementos da interface do usuário a seu storyboard existente que permite que o usuário selecione as categorias a serem registradas. As categorias selecionadas por um usuário são armazenadas no dispositivo. Quando o aplicativo é iniciado, o registro do dispositivo é criado no seu hub de notificação com as categorias selecionadas como rótulos.

1.  No MainStoryboard\_iPhone.storyboard, adicione os seguintes componentes da biblioteca de objetos:

    -   Um rótulo com o texto "Breaking News",
    -   Rótulos com os textos das categorias "World", "Politics", "Business", "Technology", "Science", "Sports"
    -   Seis comutadores, um por categoria,
    -   No botão rotulado "Subscribe"

    O storyboard deve ter a seguinte aparência:

    ![][]

2.  No editor do assistente, crie saídas para todos os comutadores e chame-os de "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"

    ![][1]

3.  Crie uma Ação para o botão chamado "subscriber". Seu BreakingNewsViewController.h deve conter o seguinte:

        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

        - (IBAction)subscribe:(id)sender;

4.  Crie uma nova classe chamada `Notifications`. Copie o código a seguir na seção da interface do arquivo Notifications.h:

        @property NSData* deviceToken;

        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*) categories completion: (void (^)(NSError* error))completion;
        - (void)subscribeWithCategories:(NSSet*) categories completion:(void (^)(NSError *))completion;

5.  Adicione a seguinte diretiva de importação a Notifications.m:

        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6.  Copie o código a seguir na seção de implementação do arquivo Notifications.m:

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

7.  No código acima, substitua os espaços reservados `<hub name>` e `<connection string with listen access>` pelo nome do hub de notificação e a cadeia de conexão por *DefaultListenSharedAccessSignature* que você tiver obtido anteriormente.

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> 
    <p>Como as credenciais que s&atilde;o distribu&iacute;das com um aplicativo cliente n&atilde;o s&atilde;o geralmente seguras, voc&ecirc; s&oacute; deve distribuir a chave para acesso de escuta com o aplicativo cliente. O acesso de escuta permite que seu aplicativo se registre para receber notifica&ccedil;&otilde;es, mas os registros existentes n&atilde;o podem ser modificados e as notifica&ccedil;&otilde;es n&atilde;o podem ser enviadas. A chave de acesso completa &eacute; usada em um servi&ccedil;o back-end protegido para enviar notifica&ccedil;&otilde;es e alterar os registros existentes.</p>
</div>

8.  No arquivo BreakingNewsAppDelegate.h, adicione a seguinte propriedade:

        @property (nonatomic) Notifications* notifications;

    Isso cria uma instância de singleton da classe Notification no AppDelegate.

9.  No método **didFinishLaunchingWithOptions** em BreakingNewsAppDelegate.m, adicione o seguinte código antes de **registerForRemoteNotificationTypes**:

        self.notifications = [[Notifications alloc] init];

    Isso inicializa o singleton Notification.

10. No método **didRegisterForRemoteNotificationsWithDeviceToken** em BreakingNewsAppDelegate.m, remova a chamada para **registerNativeWithDeviceToken** e adicione o seguinte código:

        self.notifications.deviceToken = deviceToken;

    Observe que neste ponto não deve haver nenhum outro código no método **didRegisterForRemoteNotificationsWithDeviceToken**.

11. Adicione o seguinte método em BreakingNewsAppDelegate.m:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
            (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

    Este método trata as notificações recebidas quando o aplicativo está em execução exibindo um simples **UIAlert**.

12. Em BreakingNewsViewController.m, copie o código a seguir no método **subscribe** gerado por XCode:

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

## <a name="register"></a>Registrar-se para receber notificações

Estas etapas registram com o hub de notificação na inicialização, usando as categorias que foram armazenadas no armazenamento local.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> 
    <p>Como o token do dispositivo atribu&iacute;do pelo APNS (Servi&ccedil;o de Notifica&ccedil;&atilde;o por Push da Apple) pode ser escolhido a qualquer momento, voc&ecirc; deve se registrar para receber notifica&ccedil;&otilde;es com frequ&ecirc;ncia para evitar falhas de notifica&ccedil;&atilde;o. Este exemplo registra a notifica&ccedil;&atilde;o a cada vez que o aplicativo &eacute; iniciado. Para os aplicativos que s&atilde;o executados com frequ&ecirc;ncia, mais de uma vez por dia, &eacute; poss&iacute;vel ignorar o registro para preservar a largura de banda se tiver passado menos de um dia desde o registro anterior.</p>
</div>

1.  Adicione o seguinte método na seção de interface do arquivo Notifications.h:

        - (NSSet*)retrieveCategories;

    Esse código recupera as categorias na classe Notifications.

2.  Adicione a implementação correspondente no arquivo Notifications.m:

        - (NSSet*)retrieveCategories {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

            if (!categories) return [[NSSet alloc] init];
            return [[NSSet alloc] initWithArray:categories];
        }

3.  Adicione o seguinte código no método **didRegisterForRemoteNotificationsWithDeviceToken**:

        Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];
        [notifications subscribeWithCategories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

    Isso garante que o aplicativo recupere as categorias do armazenamento local e solicite um registro para essas categorias toda vez que ele for iniciado.

4.  No BreakingNewsViewController.h, adicione o seguinte código no método **viewDidLoad**:

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

## <a name="send"></a><span class="short-header">Enviar notificações</span>Enviar notificações de seu back-end

[WACOM.INCLUDE [notification-hubs-back-end][notification-hubs-back-end]]

## <a name="test-app"></a>Executar o aplicativo e gerar notificações

1.  Pressione o botão Executar para compilar o projeto e iniciar o aplicativo.

    ![][2]

    Observe que a interface do usuário do aplicativo fornece um conjunto de alternâncias que permite escolher as categorias nas quais você poderá assinar.

2.  Habilite uma ou mais alternâncias de categorias e depois clique em **Assinar**.

    Quando você escolher **Subscribe**, o aplicativo converterá as categorias selecionadas em marcas e solicitará um novo registro de dispositivo para as marcas selecionadas no hub de notificação.

3.  Envie uma nova notificação do back-end usando uma das seguintes maneiras:

    -   **Aplicativo do console:** inicie o aplicativo de console.

    -   **Java/PHP:** execute seu aplicativo/script.

4.  As notificações para as categorias selecionadas são exibidas como notificações do sistema.

## <a name="next-steps"> </a>Próximas etapas

Neste tutorial, aprendemos como enviar as notícias mais recentes por categoria. Considere a conclusão de um dos seguintes tutoriais que destacam outros cenários avançados de Hubs de Notificação:

-   **[Usar os Hubs de Notificação para transmitir as últimas notícias localizadas][Usar os Hubs de Notificação para transmitir as últimas notícias localizadas]**

    Saiba como expandir o aplicativo das últimas notícias para habilitar o envio de notificações localizadas.

-   **[Notificar usuários com Hubs de Notificação][Notificar usuários com Hubs de Notificação]**

    Saiba como enviar notificações por push a usuários autenticados específicos. É uma boa solução enviar notificações somente a usuários específicos.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Universal]: /pt-br/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/ "Windows Universal"
  [Windows Phone]: /pt-br/documentation/articles/notification-hubs-windows-phone-send-breaking-news/ "Windows Phone"
  [iOS]: /pt-br/documentation/articles/notification-hubs-ios-send-breaking-news/ "iOS"
  [Android]: /pt-br/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/ "Android"
  [Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/pt-br/library/jj927170.aspx
  [Adicionar a seleção de categorias ao aplicativo]: #adding-categories
  [Registrar-se para receber notificações]: #register
  [Enviar notificações de seu back-end]: #send
  [Executar o aplicativo e gerar notificações]: #test-app
  [Introdução aos Hubs de Notificação]: /pt-br/manage/services/notification-hubs/get-started-notification-hubs-ios/
  []: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png
  [1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios3.png
  [notification-hubs-back-end]: ../includes/notification-hubs-back-end.md
  [2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
  [Usar os Hubs de Notificação para transmitir as últimas notícias localizadas]: /pt-br/manage/services/notification-hubs/breaking-news-localized-dotnet/
  [Notificar usuários com Hubs de Notificação]: /pt-br/manage/services/notification-hubs/notify-users/
