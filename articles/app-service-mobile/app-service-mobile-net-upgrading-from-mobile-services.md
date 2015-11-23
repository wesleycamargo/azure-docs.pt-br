<properties 
	pageTitle="Atualização de Serviços Móveis para o serviço de aplicativo do Azure" 
	description="Saiba como atualizar facilmente seu aplicativo de Serviços Móveis para um Aplicativo Móvel do Serviço de Aplicativo" 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="mahender"/>

# Atualize seu Serviço Móvel do Azure .NET para o Serviço de Aplicativo

Os Aplicativos Móveis do Serviço de Aplicativo é uma nova maneira de compilar aplicativos móveis usando o Microsoft Azure. Para saber mais, consulte [O que são Aplicativos Móveis?].

Este tópico descreve como atualizar um aplicativo de back-end do .NET dos Serviços Móveis do Azure para um novo Aplicativo Móvel do Serviço de Aplicativo. Ao realizar essa atualização, o seu aplicativo de Serviços Móveis pode continuar a operar.

Quando um back-end móvel é atualizado para o Serviço de Aplicativo do Azure, ele tem acesso a todos os recursos do Serviço de Aplicativo e são cobrados de acordo com os [preços do Serviço de Aplicativo] e não com os preços dos Serviços Móveis.

##Migração vs. atualização

[AZURE.INCLUDE [App-Service-Mobile-Migrate-VS-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

###Aprimoramentos no SDK do servidor .NET de aplicativos móveis

A atualização para os novos [pacotes NuGet de SDK de aplicativos móveis](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) oferece os seguintes benefícios:

- Mais flexibilidade em dependências do NuGet. O ambiente de hospedagem não fornece mais suas próprias versões de pacotes do NuGet, então você pode usar versões compatíveis alternativas. No entanto, se há novas correções de bugs críticas ou atualizações de segurança para o Mobile Server SDK ou dependências, você deve atualizar manualmente o serviço.

- Suporte para novos recursos de autenticação do Serviço de Aplicativo, que permitem que você use uma configuração de autenticação comum em seus aplicativos Web e móveis. Recursos de autenticação também estão agora em seu próprio componente de middleware OWIN, que significa que a mesma configuração de autenticação pode ser usada para dispositivos móveis e rotas da Web.

- Suporte para outros tipos de projeto do ASP.NET e rotas. Agora você pode hospedar os controladores MVC e API da Web no mesmo projeto como o projeto móvel de back-end.

- Mais flexibilidade no SDK do celular. Você pode controlar explicitamente quais recursos e rotas são configurados, como autenticação, APIs de tabela e o ponto de extremidade de registro push. Para obter mais informações, consulte [Como usar o SDK do servidor do .NET para Aplicativos Móveis do Azure](app-service-mobile-net-upgrading-from-mobile-services.md#server-project-setup).

- Mais opções para estruturas de injeção de dependência. O SDK do servidor do .NET dos Serviços Móveis tinha uma dependência no autofac, mas o servidor de aplicativos móveis SDK permite que você use outras estruturas, como [Unity](https://unity.codeplex.com/).

>[AZURE.NOTE]O cliente de Serviços Móveis SDKs não **são** compatível com o novo servidor de aplicativos móveis SDK. Se você tiver aplicativos de clientes móveis que estão se conectando ao serviço móvel existente, você deve manter o serviço em execução até que todos os clientes móveis tenham sido atualizados para os aplicativos móveis do cliente **SDK**.
> 
> O serviço móvel existente pode ser automaticamente *migrado* para o Serviço de Aplicativo sem qualquer alteração nos aplicativos existentes do cliente. Para saber mais sobre a atualização, consulte [Atualizar seu serviço móvel do .NET existente ao Serviço de Aplicativo].


##<a name="overview"></a>Visão geral da atualização básica
A maneira mais simples de atualizar é criar uma nova instância de um Aplicativo Móvel do Serviço de Aplicativo. Em muitos casos, migrar será tão simples quanto alternar para o novo SDK do servidor de aplicativos móveis e republicar seu código para um novo Aplicativo Móvel. Há, no entanto, alguns cenários que exigem alguma configuração adicional, como cenários avançados de autenticação e trabalhar com trabalhos agendados. Cada um deles é abordado nas seções a seguir.

>[AZURE.NOTE]É recomendável que você leia e entenda o restante deste tópico completamente antes de iniciar uma atualização. Tome nota de todos os recursos que você usar que são descritos abaixo.

Você pode mover e testar seu código no seu ritmo. Quando o back-end do Aplicativo Móvel estiver pronto, você poderá liberar uma nova versão do seu aplicativo cliente. Neste ponto, você terá duas cópias do seu back-end do aplicativo em execução lado a lado. Você precisa certificar-se de que as correções de bug feitas sejam aplicadas a ambas. Finalmente, depois de os usuários atualizarem para a versão mais recente, você pode excluir o Serviço Móvel original.

O conjunto completo de etapas para essa atualização é o seguinte:

1. Criar e configurar um novo aplicativo móvel
2. Abordar quaisquer preocupações sobre autenticação
3. Lançar uma nova versão do aplicativo cliente
4. Excluir a instância original de Serviços Móveis


##<a name="mobile-app-version"></a>Configurar uma versão do Aplicativo Móvel do seu aplicativo
A primeira etapa de atualização é criar o recurso de aplicativo móvel que hospedará a nova versão do seu aplicativo. Você pode criar um novo aplicativo móvel em [Portal de Gerenciamento do Azure de visualização]. Você pode consultar o tópico [Criar um Aplicativo Móvel] para obter mais detalhes.

Você provavelmente desejará usar o mesmo banco de dados e Hub de Notificação, como você fez nos Serviços Móveis. Você pode copiar esses valores da guia **Configurar** da seção de Serviços Móveis do [Portal de Gerenciamento do Azure]. Em **Cadeias de Conexão**, copie `MS_NotificationHubConnectionString` e `MS_TableConnectionString`. Navegue até o site do Aplicativo Móvel e selecione **Configurações**, **Configurações do Aplicativo** e adicione-as à seção **Cadeias de caracteres de conexão**, substituindo quaisquer valores existentes.

Os aplicativos móveis fornecem um novo [SDK do Servidor de Aplicativos Móveis], que oferece praticamente a mesma funcionalidade que o tempo de execução de Serviços Móveis. Primeiro, você deve remover o NuGet de Serviços Móveis do seu projeto existente e, em vez dele, incluir o SDK do Servidor. Para essa migração, a maioria dos atualização, a maioria dos desenvolvedores desejam baixar e instalar o pacote `Microsoft.Azure.Mobile.Server.Quickstart`, pois ele vai receber todo o conjunto necessário. Em seguida, em WebApiConfig.cs, você pode substituir

    // Use this class to set configuration options for your mobile service
    ConfigOptions options = new ConfigOptions();
    
    // Use this class to set WebAPI configuration options
    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

por:

    HttpConfiguration config = new HttpConfiguration();

    new MobileAppConfiguration()
	    .UseDefaultConfiguration()
	    .ApplyTo(config);


>[AZURE.NOTE]Se desejar saber mais sobre o novo SDK do servidor .NET e como adicionar ou remover recursos do seu aplicativo, consulte o tópico [Como usar o SDK do servidor .NET].

Há algumas outras alterações entre os Serviços móveis e SDKs de aplicativos móveis, mas eles são fáceis de resolver. Ao longo do projeto, talvez seja necessário fazer algumas modificações usando as instruções, tarefa com a qual o Visual Studio poderá ajudar.

Você precisa adicionar o atributo `[MobileAppController]` a todos os seus ApiControllers, simplesmente colocando esse decorador diretamente antes da definição de classe.

Não é mais um atributo `[AuthorizeLevel]` e você deve decorar, em vez disso, os controladores e os métodos com o atributo `[Authorize]` ASP.NET padrão. Lembre-se também que qualquer controlador que não tem um `[AuthorizeLevel]` não estará mais protegido por uma chave de aplicativo; ele será tratado como público. O novo SDK não usa mais uma Chave de aplicativo e a Chave mestra.

Para envios por push, o item principal que você pode descobrir estar ausente no SDK do Servidor é a classe PushRegistrationHandler. Registros são tratados de forma ligeiramente diferente em aplicativos móveis, e registros sem marca registros são habilitados por padrão. Gerenciar as marcas pode ser feito por meio de APIs personalizados. Consulte o tópico [Adicionar notificações por push ao seu aplicativo móvel] para obter mais informações.

Trabalhos agendados não são criados em aplicativos móveis, portanto, todos os trabalhos existentes que você tem no back-end do .NET precisam ser atualizado individualmente. Uma opção é criar um [Trabalho Web] agendado no site de código do Aplicativo Móvel. Você também pode configurar um controlador que contenha o código de trabalho e configurar o [Agendador do Azure] para atingir o ponto de extremidade conforme o esperado.

O objeto `ApiServices` não faz parte do SDK. Para acessar configurações de Aplicativo móvel, você pode usar o seguinte:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings(); 

Da mesma forma, o log agora é realizado usando a gravação de rastreamento padrão do ASP.NET:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>Considerações sobre autenticação
Uma das maiores diferenças entre Aplicativos Móveis e Serviços Móveis é que agora o logon é tratado pelo Gateway de Serviço de Aplicativo no caso de Aplicativos Móveis, não o site que executa o código. Se o grupo de recursos ainda não tiver um, você pode provisionar um gateway navegando até seu Aplicativo móvel do Azure no portal de gerenciamento. Selecione **Configurações** e, em seguida, **Autenticação de usuário** na categoria **Móvel**. Clique em **Criar** para associar um gateway com o seu Aplicativo móvel.

Além disso, a maioria dos aplicativos não exigirá qualquer ação adicional, mas há alguns cenários avançados que devem ser observados.

A maioria dos aplicativos pode simplesmente usar um novo registro com o provedor de identidade de destino, e você pode aprender sobre a adição de identidade a um aplicativo do Serviço de Aplicativo seguindo o tutorial [Adicionar autenticação ao seu Aplicativo Móvel].

Se seu aplicativo tiver dependências de IDs de usuário, como armazená-los em um banco de dados, é importante observar que as IDs de usuário entre os Serviços Móveis e Aplicativos Móveis do Serviço de Aplicativo são diferentes. No entanto, é possível obter a ID de usuário de Serviços Móveis em seu aplicativo do Aplicativo Móvel do Serviço de Aplicativo usando o seguinte (com o Facebook como um exemplo):

    MobileAppUser = (MobileAppUser) this.User;
    FacebookCredentials creds = await user.GetIdentityAsync<FacebookCredentials>();
    string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Além disso, se você usar todas as dependências das IDs de usuário, é importante aproveitar o mesmo registro com um provedor de identidade, se possível. IDs de usuário normalmente têm o escopo para o registro do aplicativo que foi usado, portanto, introduzir um novo registro pode criar problemas com os dados de usuários correspondentes. Se o seu aplicativo precisar usar o mesmo registro do provedor de identidade por algum motivo, você pode usar as seguintes etapas:

1. Copiar sobre a ID do cliente e as informações de conexão do segredo do cliente para cada provedor usado pelo seu aplicativo.
2. Adicione os pontos de extremidade /signin-* do gateway como um URI de redirecionamento adicional para cada provedor. 

>[AZURE.NOTE]Alguns provedores, como o Twitter e a Conta da Microsoft, não permitem que você especifique vários URIs de redirecionamento em domínios diferentes. Se seu aplicativo usar um desses provedores e assumir uma dependência de IDs de usuário, é recomendável não tentar atualizar neste momento.

##<a name="updating-clients"></a>Atualizando de clientes
Quando você tiver um back-end do Aplicativo Móvel operacional, poderá atualizar seu aplicativo cliente para consumir o novo aplicativo móvel. Aplicativos Móveis também incluirão uma nova versão do cliente dos serviços móveis SDKs, o que permitirá aos desenvolvedores usufruir dos novos recursos de serviço de aplicativo. Quando você tiver uma versão do aplicativo móvel do seu back-end, poderá liberar uma nova versão do seu aplicativo cliente que aproveite a nova versão do SDK.

A principal mudança que será necessária no código do cliente é no construtor. Além da URL do seu site Aplicativo móvel, você precisa fornecer a URL do Gateway de Serviço de Aplicativo que hospeda suas configurações de autenticação:

    public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://contoso.azurewebsites.net", // URL of the Mobile App
        "https://contoso-gateway.azurewebsites.net", // URL of the App Service Gateway
        "" // Formerly app key. To be removed in future client SDK update
    );

Isso permitirá que o cliente roteie solicitações para os componentes do aplicativo móvel. Você pode encontrar mais detalhes específicos à sua plataforma de destino usando o tópico [Criar um Aplicativo Móvel] adequado.

Na mesma atualização, você precisará ajustar qualquer chamada de registro de notificação por push feita. Há novas APIs que fazem melhorias no processo de registro (usando o Windows como um exemplo):

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    await MobileService.GetPush().Register(channel.Uri); 

Consulte os tópicos [Adicionar notificações por ao seu aplicativo móvel] e [Enviar notificações por push multiplataforma] para obter detalhes específicos para a plataforma de destino.

Depois que seus clientes tiverem a oportunidade de receber essas atualizações, você pode excluir a versão de serviços móveis do seu aplicativo. Neste ponto, você atualizou completamente para um aplicativo móvel de serviço de aplicativo usando o servidor de aplicativos móveis SDK mais recente.

<!-- URLs. -->

[Portal de Gerenciamento do Azure de visualização]: https://portal.azure.com/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[O que são Aplicativos Móveis?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /pt-BR/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[SDK do Servidor de Aplicativos Móveis]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Criar um Aplicativo Móvel]: app-service-mobile-xamarin-ios-get-started.md
[Adicionar notificações por ao seu aplicativo móvel]: app-service-mobile-xamarin-ios-get-started-push.md
[Adicionar notificações por push ao seu aplicativo móvel]: app-service-mobile-xamarin-ios-get-started-push.md
[Adicionar autenticação ao seu Aplicativo Móvel]: app-service-mobile-xamarin-ios-get-started-users.md
[Agendador do Azure]: /pt-BR/documentation/services/scheduler/
[Trabalho Web]: ../app-service-web/websites-webjobs-resources.md
[Enviar notificações por push multiplataforma]: app-service-mobile-xamarin-ios-push-notifications-to-user.md
[Como usar o SDK do servidor .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md
[Atualizar seu serviço móvel do .NET existente ao Serviço de Aplicativo]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md
[preços do Serviço de Aplicativo]: https://azure.microsoft.com/pt-BR/pricing/details/app-service/

<!---HONumber=Nov15_HO3-->