<properties
	pageTitle="Migrar dos Serviços Móveis para o Aplicativo Móvel do Serviço de Aplicativo"
	description="Saiba como migrar facilmente seu aplicativo de Serviços Móveis para um Aplicativo Móvel do Serviço de Aplicativo"
	services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/25/2015"
	ms.author="mahender"/>

# Migrar seu Serviço Móvel do Azure para o Serviço de Aplicativo

Este tópico mostra como migrar um aplicativo existente dos Serviços Móveis do Azure para um novo Aplicativo Móvel do Serviço de Aplicativo. Qualquer serviço móvel existente pode ser facilmente migrado para um novo back-end de Aplicativo Móvel. Durante a migração, o serviço móvel existente continuará a funcionar.

Quando um serviço móvel é migrado para o Serviço de Aplicativo, ele tem acesso a todos os recursos do Serviço de Aplicativo e você recebe uma cobrança de acordo com os [preços do Serviço de Aplicativo] e não com os preços dos Serviços Móveis.

Outra observação é que os trabalhos agendados serão transferidos de um plano do Agendador do Azure gerenciado pela Microsoft para um plano do Agendador do Azure em sua própria assinatura e sob seu controle. Assim como os benefícios da migração para o Serviço de Aplicativo, isso permite que você utilize toda a potência do Agendador do Azure.

### <a name="why-host"></a>Por que hospedar no Serviço de Aplicativo?

O Serviço de Aplicativo fornece um ambiente de hospedagem mais rico em recursos para seu aplicativo. Ao se hospedar no Serviço de Aplicativo, o serviço obterá acesso a slots de preparação, a domínios personalizados, a suporte ao Gerenciador de Tráfego e mais. Embora seja possível atualizar um serviço móvel para um back-end de Aplicativo Móvel depois de migrar para o Serviço de Aplicativo, alguns clientes podem querer aproveitar esses recursos imediatamente, sem executar a atualização do SDK imediatamente.

Para saber mais sobre os benefícios do Serviço de Aplicativo, confira [Serviços Móveis versus Serviço de Aplicativo].

## Migração vs. atualização

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

- Para projetos de servidor baseados em Node.js, o novo [SDK do Node.js de Aplicativos Móveis](https://github.com/Azure/azure-mobile-apps-node) oferece vários recursos novos. Por exemplo, você pode agora fazer desenvolvimento local e a depuração, usar qualquer versão do Node. js acima de 0,10 e personalizar com qualquer middleware Express. js.

- Para projetos de servidor baseados em .NET, os novos [pacotes NuGet de SDK de aplicativos móveis](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) têm mais flexibilidade em dependências do NuGet, dão suporte a novos recursos de autenticação do serviço de aplicativo e compõe com qualquer projeto ASP.NET, inclusive o MVC. Para saber mais sobre a atualização, confira [Atualizar seu serviço de celular do .NET existentes ao Serviço de Aplicativo](app-service-mobile-net-upgrading-from-mobile-services.md).


## <a name="understand"></a>Noções básicas sobre Aplicativos Móveis do Serviço de Aplicativo

Os Aplicativos Móveis no Serviço de Aplicativo é uma nova maneira de compilar aplicativos móveis usando o Azure. Você pode saber mais sobre os Aplicativos Móveis em [O que são Aplicativos Móveis?]

Ao migrar para os Aplicativos Móveis, toda as funcionalidades existentes do aplicativo, incluindo sua lógica de negócios, podem ser preservadas. Além disso, novos recursos estão disponíveis para o aplicativo. No modelo dos Aplicativos Móveis, seu código é executado em um aplicativo Web no Serviço de Aplicativo, que é a nova versão dos Sites do Azure. Você tem controle total sobre o aplicativo Web e como ele funciona. Além disso, os recursos de aplicativos Web que estavam indisponíveis para clientes dos Serviços Móveis, como o Gerenciador de Tráfego e os Slots de Desenvolvimento, agora podem ser usados.

A principal limitação da atualização é que os trabalhos agendados nos Serviços Móveis não são integrados, e o Agendador do Azure deverá ser configurado para chamar as APIs personalizadas ou os recursos dos Trabalhos Web deverão ser habilitados.

## Migrando seu Serviço Móvel com o assistente de Migração para o Serviço de Aplicativo

A maneira mais fácil e recomendada de migrar seu serviço móvel para o Serviço de Aplicativo é usar o assistente de Migração para o Serviço de Aplicativo, disponível no portal clássico do Azure. Navegue até o [portal clássico do Azure], procure os serviços móveis e selecione um deles para migrar; na parte inferior da tela você verá um botão **Migrar para o Serviço de Aplicativo** que orientará você pelo processo de migração de seu serviço móvel.

### <a name="what-gets-migrated"></a>O que é migrado?

O assistente de migração altera o gerenciamento dos farms de servidores que hospedam o serviço móvel, dos Serviços Móveis para o Serviço de Aplicativo. Ele também move os planos do Agendador que eram gerenciados pelos Serviços Móveis de uma assinatura gerenciada pela Microsoft para sua assinatura. Quando os farms de servidores são transferidos para o controle de gerenciamento do Serviço de Aplicativo, todos os Serviços Móveis associados a esse farm de servidores são transferidos ao mesmo tempo.

Os Serviços Móveis organizam os seus serviços móveis em farms de servidores com base na região e no SKU (gratuito, básico, padrão). Todos os serviços gratuitos em uma região estão no mesmo farm de servidores e são migrados juntos; alguns serviços básicos em uma região são hospedados juntos no mesmo farm, mas se você tiver muitos serviços básicos, eles ficarão em planos separados; cada serviço padrão fica em seu próprio farm de servidores. Se, em algum momento, você quiser migrar um único serviço ou quiser evitar que um serviço seja transferido com seus outros sites, atualize-o para o básico e ele ficará em seu próprio plano independente.

Os planos do Agendador do Azure também serão adicionados à sua assinatura, caso você estivesse usando trabalhos agendados. Há um número limitado de trabalhos agendados gratuitos por assinatura, portanto, pode haver algum custo associado a isso que convém analisar após a assinatura.

###  Usando o assistente de migração

1. Navegue até o [portal clássico do Azure] e clique em Serviços Móveis. 

2. Selecione o serviço móvel para migrar e clique em "Migrar para o Serviço de Aplicativo" na barra de menu inferior. Isso apresentará um pop-up com a lista de Serviços Móveis que serão migrados. Confira a [seção anterior](#what-gets-migrated) para obter detalhes sobre quais serviços aparecerão e por quê.

3. Digite o nome do serviço móvel para confirmar a migração e clique na marca de seleção para continuar. Quando vários serviços móveis forem incluídos na migração, ainda será necessário digitar o nome do serviço original selecionado.

4. Após o inicio da migração, você poderá exibir o status atual na lista de Serviços Móveis no [portal clássico do Azure]. Ele aparecerá como "migrando" para todos os serviços atualmente em migração. Após a conclusão de todas as migrações, você poderá exibi-las no [portal do Azure] em Aplicativos Móveis. Durante e após a migração, os serviços móveis continuarão funcionando e a URL não será alterada.

## Migração manual de para back-end .NET de Serviços Móveis

>[AZURE.NOTE]Observe que a maneira recomendada de migrar um serviço móvel é por meio do assistente de Migração para o Serviço de Aplicativo, descrito na seção anterior. A migração manual deve ser usada somente em situações nas quais o assistente não pode ser usado.

Esta seção mostra como hospedar um projeto de Serviços Móveis .NET no Serviço de Aplicativo do Azure. Um aplicativo hospedado dessa maneira pode usar todos os tutoriais de tempo de execução do .NET dos *Serviços Móveis*, embora seja necessário substituir qualquer URL usando o domínio azure-mobile.net com o domínio da instância do Serviço de Aplicativo.

Um projeto de Serviços Móveis também pode ser hospedado em um [Ambiente de Serviço de Aplicativo]. Todo o conteúdo deste tópico ainda se aplica, mas o site terá o formato `contoso.contosoase.p.azurewebsites.net` em vez de `contoso.azurewebsites.net`.

>[AZURE.NOTE]Se você executar uma migração manual, *não será possível* preservar a URL **service.azure mobile.net** existente. Se você tiver clientes móveis que estão se conectando a essa URL, você deve usar a opção de migração automática ou manter seu serviço móvel em execução até que todos os clientes móveis atualizou para a nova URL.


### <a name="app-settings"></a>Configurações do Aplicativo
Os Serviços Móveis exigem que várias configurações de aplicativos estejam disponíveis no ambiente, que estão descritos nesta seção.

Para definir as configurações do aplicativo em seu back-end de Aplicativo Móvel, primeiro entre no [portal do Azure]. Navegue até o aplicativo do Serviço de Aplicativo que você deseja usar como o back-end do Aplicativo Móvel, clique em **Configurações** > **Configurações do Aplicativo** e, em seguida, role para baixo até **Configurações do aplicativo**, que é onde você pode definir os seguintes pares necessários de chave-valor:

+ **MS\_MobileServiceName** deve ser definido como o nome do seu aplicativo. Por exemplo, quando a URL do seu back-end for `contoso.azurewebsites.net`, *contoso* será o valor correto.

+ **MS\_MobileServiceDomainSuffix** deve ser definido como o nome do seu aplicativo Web. Por exemplo, quando a URL do seu back-end for `contoso.azurewebsites.net`, *azurewebsites.net* será o valor correto.

+ **MS\_ApplicationKey** pode ser definida como qualquer valor, mas ele deve ser o mesmo valor usado pelo SDK do cliente. Um GUID é recomendado.

+ **MS\_MasterKey** pode ser definida como qualquer valor, mas ele deve ser o mesmo valor usado por quaisquer APIs/clientes de administração. Um GUID é recomendado.

Ao migrar um serviço móvel, a chave mestra e a chave do aplicativo poderão ser obtidas na guia **Configurar** da seção Serviços Móveis do [portal clássico do Azure]. Clique em **Gerenciar Chaves** na parte inferior e copie as chaves.


### <a name="client-sdk"></a>Como modificar o SDK do cliente

No projeto do aplicativo cliente, modifique o construtor do objeto cliente do Serviços Móveis para aceitar a URL do novo aplicativo (por exemplo, `https://contoso.azurewebsites.net`) e a chave de aplicativo configurada anteriormente. A versão do SDK do cliente deve ser uma versão dos **Serviços Móveis** e **não** deve ser atualizada. Para clientes iOS e Android, use as versões 2.x e, para o Windows/Xamarin, use a 1.3.2. Os clientes JavaScript devem usar a versão 1.2.7.

### <a name="data"></a>Como habilitar os recursos de dados

Para trabalhar com as classes padrão do Entity Framework nos Serviços Móveis, você precisa de duas configurações adicionais.

As cadeias de conexão são armazenadas na seção **Cadeias de conexão** da folha Configurações do Aplicativo, logo abaixo da seção **Configurações do aplicativo**. A cadeia de conexão para o seu banco de dados deve ser definida na chave **MS\_TableConnectionString**. Ao migrar um serviço móvel existente para o Serviço de Aplicativo, navegue até a seção **Cadeias de Conexão** da guia **Configurar** dos serviços móveis no [portal clássico do Azure](https://manage.windowsazure.com/). Clique em **Mostrar Cadeias de Conexão** e copie o valor.

Por padrão, o esquema a ser usado é **MS\_MobileServiceName**, mas isso pode ser substituído pela configuração **MS\_TableSchema**. Novamente em **Configurações de aplicativo**, defina **MS\_TableSchema** como o nome do esquema a ser usado. Se um Aplicativo do Serviços Móveis existente estiver sendo movido, um esquema já foi criado usando o Entity Framework - esse é o nome do Serviço Móvel, e não da instância do Serviço de Aplicativo que hospedará o código agora.

### <a name="push"></a>Como habilitar os recursos de push

Para que o push funcione, o aplicativo Web também precisará conhecer as informações sobre o hub de Notificação.

Em **Cadeias de conexão**, defina **MS\_NotificationHubConnectionString** com a cadeia de conexão DefaultFullSharedAccessSignature para o hub de notificação. Para migrar um serviço móvel existente, navegue até a seção **Cadeias de Conexão** da guia **Configurar** dos serviços móveis no [portal clássico do Azure](https://manage.windowsazure.com/). Clique em **Mostrar Cadeias de Conexão** e copie o valor.

A configuração de aplicativo **MS\_NotificationHubName** deve ser definida como o nome do hub. Ao mover um serviço móvel existente, você poderá obter esse valor na guia **Push** dos serviços móveis no [portal clássico do Azure](https://manage.windowsazure.com/). Os outros campos dessa guia são vinculados ao hub em si e não precisam ser copiados para lugar algum.

### <a name="auth"></a>Como habilitar os recursos de autenticação

Os recursos de identidade também têm requisitos de configuração de aplicativo para os provedores individuais. Se você estiver movendo de serviços móvel existente, cada um dos campos na guia **Identidade** do portal clássico do Azure terá uma configuração de aplicativo correspondente.

Conta da Microsoft

* **MS\_MicrosoftClientID**

* **MS\_MicrosoftClientSecret**

* **MS\_MicrosoftPackageSID**

Facebook

* **MS\_FacebookAppID**

* **MS\_FacebookAppSecret**

Twitter

* **MS\_TwitterConsumerKey**

* **MS\_TwitterConsumerSecret**

Google

* **MS\_GoogleClientID**

* **MS\_GoogleClientSecret**

AAD

* **MS\_AadClientID**

* **MS\_AadTenants** -Observação: **MS\_AadTenants** é armazenado como uma lista separada por vírgulas de domínios de locatário (os campos **Locatários Permitidos** no portal clássico do Azure).

### <a name="publish"></a>Como publicar o projeto de serviços móvel

1. No [portal do Azure], navegue até seu aplicativo, clique em **Obter perfil de publicação** na barra de comandos e salve o perfil baixado no computador local.
2. No Visual Studio, clique com o botão direito no projeto de servidor de Serviços Móveis e clique em **Publicar**. 
3. Na guia do perfil, escolha **Importar** e navegue até o perfil baixado.
3. Clique em **Publicar** para implantar o código no Serviço de Aplicativo.

Os logs são tratados por meio dos recursos de log do Serviço de Aplicativo padrão. Para saber mais sobre o log, consulte [Habilitar o log de diagnóstico no Serviço de Aplicativo do Azure].



<!-- URLs. -->

[portal do Azure]: https://portal.azure.com/
[portal clássico do Azure]: https://manage.windowsazure.com/
[O que são Aplicativos Móveis?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /pt-BR/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /pt-BR/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[Send cross-platform push notifications]: app-service-mobile-xamarin-ios-push-notifications-to-user.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md


[Habilitar o log de diagnóstico no Serviço de Aplicativo do Azure]: web-sites-enable-diagnostic-log.md
[preços do Serviço de Aplicativo]: https://azure.microsoft.com/pt-BR/pricing/details/app-service/
[Ambiente de Serviço de Aplicativo]: app-service-app-service-environment-intro.md
[Serviços Móveis versus Serviço de Aplicativo]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[migrate a mobile service to a mobile app on App Service]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md

<!---HONumber=AcomDC_1203_2015-->