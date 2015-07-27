<properties 
	pageTitle="Migrar dos Serviços Móveis para o Aplicativo Móvel do Serviço de Aplicativo" 
	description="Saiba como migrar facilmente seu aplicativo de Serviços Móveis para um Aplicativo Móvel do Serviço de Aplicativo" 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/23/2015" 
	ms.author="mahender"/>

# Migrar Serviço Móvel do Azure para um Aplicativo Móvel do Serviço de Aplicativo do Azure

Este tópico mostra como migrar um aplicativo existente dos Serviços Móveis do Azure para um novo Aplicativo Móvel do Serviço de Aplicativo. Todos os aplicativos de Serviços Móveis existentes podem ser migrados facilmente para um novo Aplicativo Móvel do Serviço de Aplicativo. Durante a migração, o seu aplicativo de Serviços Móveis pode continuar a operar. Ao longo do tempo, o processo de migração se tornará ainda mais fácil, mas, para aqueles que desejem migrar hoje, as etapas a seguir podem ser usadas.

>[AZURE.NOTE]As migrações atualmente só têm suporte para clientes que usam o back-end .NET dos Serviços Móveis. Aplicativos que usam o back-end Node. js precisarão permanecer nos Serviços Móveis no momento.

##<a name="understand"></a>Noções básicas sobre Aplicativos Móveis de Serviço de Aplicativo

Os Aplicativos Móveis do Serviço de Aplicativo é uma nova maneira de compilar aplicativos móveis usando o Microsoft Azure. Você pode saber mais sobre os Aplicativos Móveis no tópico [O que são Aplicativos Móveis?]

Em uma migração para Aplicativos Móveis, todas as funções existentes do aplicativo (e código) podem ser preservadas. Além disso, novos recursos estão disponíveis para o aplicativo. No modelo de Aplicativos Móveis, seu código é executado, na verdade, em um aplicativo Web (a nova versão dos Sites do Azure). Você tem controle total sobre o aplicativo Web e como ele funciona. Além disso, os recursos de aplicativos Web que estavam anteriormente indisponíveis para clientes de Serviços Móveis, como Gerenciador de Tráfego e Slots de Desenvolvimento, agora podem ser usados.

O novo modelo também aborda uma das principais dificuldades de trabalhar com os Serviços Móveis. Agora, qualquer versão de qualquer pacote NuGet pode ser implantada sem se preocupar com conflitos de dependência. Mais sobre os benefícios de migração pode ser encontrada no tópico [Eu já uso sites e serviços móveis – como o Serviço de Aplicativo pode me ajudar?]

Quando você cria um Aplicativo Móvel do Serviço de Aplicativo, você obtém:

- Um recurso de Aplicativo Móvel, que contém a nova funcionalidade 
- Um site de Código do Aplicativo Móvel, que executa seu projeto de API da Web usando o SDK do Servidor de Aplicativo Móvel
- Um Gateway de Serviço de Aplicativo, que lida com logon e ajuda a adicionar Aplicativos de API de Serviço de Aplicativo ao seu aplicativo

##<a name="overview"></a>Visão geral da migração básica
A maneira mais simples de migrar é criar uma nova instância de um Aplicativo Móvel do Serviço de Aplicativo. Em muitos casos, migrar será tão simples quanto alternar para o novo SDK do servidor e republicar seu código para um novo Aplicativo Móvel. Há, no entanto, alguns cenários que exigem alguma configuração adicional, como cenários avançados de autenticação e trabalhar com trabalhos agendados. Cada um deles é abordado nas seções a seguir.

>[AZURE.NOTE]É recomendável que você leia e entenda o restante deste tópico completamente antes de iniciar a migração. Tome nota de todos os recursos que você usar que são descritos abaixo.

Você pode mover e testar seu código no seu ritmo. Quando o back-end do Aplicativo Móvel estiver pronto, você poderá liberar uma nova versão do seu aplicativo cliente. Neste ponto, você terá duas cópias do seu aplicativo em execução lado a lado. Você precisa certificar-se de que as correções de bug feitas sejam aplicadas a ambas. Finalmente, depois de os usuários atualizarem para a versão mais recente, você pode excluir o Serviço Móvel original.

O conjunto completo de etapas para essa migração é o seguinte:

1. Criar e configurar um novo aplicativo móvel
2. Abordar quaisquer preocupações sobre autenticação
3. Lançar uma nova versão do aplicativo cliente
4. Excluir a instância original de Serviços Móveis


##<a name="mobile-app-version"></a>Configurar uma versão do Aplicativo Móvel do seu aplicativo
A primeira etapa da migração é criar o Serviço de Aplicativo que hospedará a nova versão do seu aplicativo. Você pode criar um novo aplicativo móvel em [Portal de Gerenciamento do Azure de visualização]. Você pode consultar o tópico [Criar um Aplicativo Móvel] para obter mais detalhes.

Você provavelmente desejará usar o mesmo banco de dados e Hub de Notificação, como você fez nos Serviços Móveis. Você pode copiar esses valores da guia **Configurar** da seção de Serviços Móveis do [Portal de Gerenciamento do Azure]. Em **Cadeias de Conexão**, copie `MS_NotificationHubConnectionString` e `MS_TableConnectionString`. Navegue até o site de código do Aplicativo Móvel e selecione **Configurações**, **Configurações do Aplicativo** e adicione essas cadeias de caracteres de conexão, substituindo quaisquer valores existentes. Além disso, você deve adicionar esses valores ao recurso de Aplicativo Móvel. Para fazer isso, navegue até a folha do aplicativo móvel, selecione **Configurações** e **Propriedades**. Clique no link de rotulado **Host da API** do Aplicativo para exibir o site que hospeda o recurso de Aplicativo Móvel. Vá para **Configurações**, **Configurações do aplicativo** e cole as cadeias de conexão como no site de código. Não altere outros valores, pois isso poderia interromper a funcionalidade do Aplicativos Móvel. Observe que, no momento, a lâmina folha de Aplicativo Móvel continuará a mostrar as conexões existentes mesmo após essa etapa de configuração. Ações adicionais podem ser necessárias depois que a experiência de Aplicativos Móveis tiver sido atualizada.

Os aplicativos móveis fornecem um novo [SDK do Servidor de Aplicativos Móveis], que oferece praticamente a mesma funcionalidade que o tempo de execução de Serviços Móveis. Você deve remover o NuGet de Serviços Móveis do seu projeto existente e, em vez dele, incluir o SDK do Servidor. Talvez seja necessário fazer algumas modificações usando as instruções, tarefa com a qual o Visual Studio poderá ajudar. O item principal que você pode descobrir estar ausente no SDK do Servidor é a classe PushRegistrationHandler. Registros são tratados de forma ligeiramente diferente em aplicativos móveis, e registros sem marca registros são habilitados por padrão. Gerenciar as marcas pode ser feito por meio de APIs personalizados. Consulte o tópico [Adicionar notificações por push ao seu aplicativo móvel] para obter mais informações.

Trabalhos agendados não são criados em aplicativos móveis, portanto, todos os trabalhos existentes que você tem no back-end do .NET precisam ser migrados individualmente. Uma opção é criar um [Trabalho Web] agendado no site de código do Aplicativo Móvel. Você também pode configurar um controlador que contenha o código de trabalho e configurar o [Agendador do Azure] para atingir o ponto de extremidade conforme o esperado.


##<a name="authentication"></a>Considerações sobre autenticação
Uma das maiores diferenças entre Aplicativos Móveis e Serviços Móveis é que o logon é tratado pelo Gateway de Serviço de Aplicativo no caso de Aplicativos Móveis, não no site do código. Para a maioria dos aplicativos, isso não exigirá qualquer ação adicional, mas há alguns cenários avançados que devem ser observados.

A maioria dos aplicativos pode simplesmente usar um novo registro com o provedor de identidade de destino, e você pode aprender sobre a adição de identidade a um aplicativo do Serviço de Aplicativo seguindo o tutorial [Adicionar autenticação ao seu Aplicativo Móvel].

Se seu aplicativo tiver dependências de IDs de usuário, como armazená-los em um banco de dados, é importante observar que as IDs de usuário entre os Serviços Móveis e Aplicativos Móveis do Serviço de Aplicativo são diferentes. No entanto, é possível obter a ID de usuário de Serviços Móveis em seu aplicativo do Aplicativo Móvel do Serviço de Aplicativo usando o seguinte (com o Facebook como um exemplo):

    ServiceUser user = (ServiceUser) this.User;
    FacebookCredentials creds = (await user.GetIdentitiesAsync()).OfType< FacebookCredentials >().FirstOrDefault();
    string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Além disso, se você usar todas as dependências das IDs de usuário, é importante aproveitar o mesmo registro com um provedor de identidade, se possível. IDs de usuário normalmente têm o escopo para o registro do aplicativo que foi usado, portanto, introduzir um novo registro pode criar problemas com os dados de usuários correspondentes. Se o seu aplicativo precisar usar o mesmo registro do provedor de identidade por algum motivo, você pode usar as seguintes etapas:

1. Copiar sobre a ID do cliente e as informações de conexão do segredo do cliente para cada provedor usado pelo seu aplicativo.
2. Adicione os pontos de extremidade /signin-* do gateway como um URI de redirecionamento adicional para cada provedor. 

>[AZURE.NOTE]Alguns provedores, como o Twitter e a Conta da Microsoft, não permitem que você especifique vários URIs de redirecionamento em domínios diferentes. Se seu aplicativo usar um desses provedores e assumir uma dependência de IDs de usuário, é recomendável não tentar migrar neste momento.

##<a name="updating clients"></a>Atualizando de clientes
Quando você tiver um back-end do Aplicativo Móvel operacional, poderá atualizar seu aplicativo cliente para consumir o novo aplicativo móvel. Aplicativos Móveis também incluirão uma nova versão do cliente dos serviços móveis SDKs, o que permitirá aos desenvolvedores usufruir dos novos recursos de serviço de aplicativo. Quando você tiver uma versão do aplicativo móvel do seu back-end, poderá liberar uma nova versão do seu aplicativo cliente que aproveite a nova versão do SDK.

A principal mudança que será necessária no código do cliente é no construtor. Além da URL do seu site de código do aplicativo móvel e da chave de aplicativo, você precisa fornecer a URL do Gateway de Serviço de Aplicativo que hospeda suas configurações de autenticação:

    public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://contoso-code.azurewebsites.net", //URL of the Mobile App Code
        "https://contosogateway.azurewebsites.net", //URL of the App Service Gateway
        "983682c4-f043-483e-a75b-8a8545fc1846"
    );

Isso permitirá que o cliente roteie solicitações para os componentes do aplicativo móvel. Você pode encontrar mais detalhes específicos à sua plataforma de destino usando o tópico [Criar um Aplicativo Móvel] adequado.

Na mesma atualização, você precisará ajustar qualquer chamada de registro de notificação por push feita. Há novas APIs que fazem melhorias no processo de registro (usando o Windows como um exemplo):

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    await MobileService.GetPush().Register(channel.Uri); 

Consulte os tópicos [Adicionar notificações por ao seu aplicativo móvel] e [Enviar notificações por push multiplataforma] para obter detalhes específicos para a plataforma de destino.

Depois que seus clientes tiverem a oportunidade de receber essas atualizações, você pode excluir a versão de serviços móveis do seu aplicativo. Neste ponto, você terá migrado completamente para um Aplicativo Móvel do Serviço de Aplicativo.

<!-- URLs. -->

[Portal de Gerenciamento do Azure de visualização]: https://portal.azure.com/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[O que são Aplicativos Móveis?]: app-service-mobile-value-prop-preview.md
[Eu já uso sites e serviços móveis – como o Serviço de Aplicativo pode me ajudar?]: /pt-br/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services-preview
[SDK do Servidor de Aplicativos Móveis]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Criar um Aplicativo Móvel]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md
[Adicionar notificações por ao seu aplicativo móvel]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md
[Adicionar notificações por push ao seu aplicativo móvel]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md
[Adicionar autenticação ao seu Aplicativo Móvel]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md
[Agendador do Azure]: /pt-br/documentation/services/scheduler/
[Trabalho Web]: ../app-service-web/websites-webjobs-resources.md
[Enviar notificações por push multiplataforma]: app-service-mobile-dotnet-backend-xamarin-ios-push-notifications-to-user-preview.md

<!---HONumber=July15_HO3-->