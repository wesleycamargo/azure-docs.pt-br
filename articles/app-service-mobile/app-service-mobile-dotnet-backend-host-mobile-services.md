<properties
	pageTitle="Hospedar um projeto de Serviços Móveis no Serviço de Aplicativo | Microsoft Azure"
	description="Saiba como executar um projeto de Serviços Móveis dentro do Serviço de Aplicativo"
	documentationCenter=""
	authors="mattchenderson"
	manager="dwrede"
	editor="na"
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.date="10/08/2015"
	ms.author="mahender"/>

# Como hospedar um projeto de Serviços Móveis do .NET no Serviço de Aplicativo

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Este tópico mostra como hospedar um projeto do Serviços Móveis .NET no Serviço de Aplicativo do Azure. Os aplicativos executados dessa maneira obtêm acesso a todos os recursos do Serviço de Aplicativo e são cobrados de acordo com os [preços do Serviço de Aplicativo] e não com os preços dos Serviços Móveis.

Um aplicativo hospedado dessa maneira pode usar todos os tutoriais de tempo de execução do .NET dos Serviços Móveis, embora seja necessário substituir qualquer URL usando o domínio azure-mobile.net com o domínio da instância do Serviço de Aplicativo.

Um projeto de Serviços Móveis também pode ser hospedado em um [Ambiente de Serviço de Aplicativo]. Todo o conteúdo deste tópico ainda se aplica, mas o site terá o formato contoso.contosoase.p.azurewebsites.net em vez de contoso.azurewebsites.net.

## <a name="app-settings"></a>Por que hospedar no Serviço de Aplicativo?

O Serviço de Aplicativo fornece um ambiente de hospedagem mais rico em recursos para seu aplicativo. Ao se hospedar no Serviço de Aplicativo, o serviço obterá acesso a slots de preparação, a domínios personalizados, a suporte ao Gerenciador de Tráfego e mais. Embora seja possível [migrar um serviço móvel para um aplicativo móvel no Serviço de Aplicativo], alguns clientes podem desejar tirar proveito desses recursos imediatamente, sem executar a atualização do SDK ainda.

A principal limitação da hospedagem no Serviço de Aplicativo é que os trabalhos agendados no Serviços Móveis não são integrados e o Agendador do Azure deverá ser configurado para atingir as APIs personalizadas ou os recursos dos Trabalhos Web deverão ser habilitados..

Para saber mais sobre os benefícios do Serviço de Aplicativo, consulte o tópico [Serviços Móveis versus Serviço de Aplicativo].

## <a name="app-settings"></a>Configurações do Aplicativo
O Serviços Móveis exige que várias configurações estejam disponíveis no ambiente. Esta seção acompanhará a adição de cada uma delas. Para definir as configurações do aplicativo em seu aplicativo Web, primeiro entre no [Portal de Gerenciamento do Azure Preview]. Navegue até o aplicativo Web, móvel ou de API que deseja usar e selecione “Configurações” e “Configurações do Aplicativo”. Role para baixo até a seção "Configurações do aplicativo". Aqui você pode definir os pares chave-valor necessários.
 
**MS\_MobileServiceName** deve ser definido como o nome do seu aplicativo. Por exemplo, se você estiver executando em um aplicativo com a URL contoso.azurewebsites.net, "contoso" será o valor correto.
 
**MS\_MobileServiceDomainSuffix** deve ser definido como o nome do seu aplicativo Web. Por exemplo, se você estiver executando em um aplicativo com a URL contoso.azurewebsites.net, "azurewebsites.net" será o valor correto.
 
**MS\_ApplicationKey** pode ser definida como qualquer valor, mas ele deve ser o mesmo valor usado pelo SDK do cliente. Um GUID é recomendado.
 
**MS\_MasterKey** pode ser definida como qualquer valor, mas ele deve ser o mesmo valor usado por quaisquer APIs/clientes de administração. Um GUID é recomendado.
 
Se um Aplicativo do Serviços Móveis existente estiver sendo movido, a chave mestra e a chave do aplicativo deverão ser obtidas na guia “Configurar” da seção Serviços Móveis do [Portal de Gerenciamento do Azure]. Selecione a ação "Gerenciar Chaves" na parte inferior e copie as chaves.


## <a name="client-sdk"></a>Como modificar o SDK do cliente

No projeto do aplicativo cliente, modifique o construtor do objeto cliente do Serviços Móveis para aceitar a URL do aplicativo (por exemplo, contoso.azurewebsites.net) e a chave de aplicativo configurada anteriormente. A versão do SDK do cliente deve ser uma versão do Serviços Móveis e **não** deve ser atualizada. Para clientes iOS e Android, use as versões 2.x e, para o Windows/Xamarin, use a 1.3.2. Os clientes JavaScript devem usar a versão 1.2.7.

## <a name="data"></a>Como habilitar os recursos de dados

Para trabalhar com as classes padrão do Entity Framework nos Serviços Móveis, são necessárias duas Configurações de Aplicativo adicionais.
 
As cadeias de conexão são armazenadas na seção "Cadeias de conexão" da folha Configurações do Aplicativo, logo abaixo da seção "Configurações do aplicativo". A cadeia de conexão para o seu banco de dados deve ser definida na chave **MS\_TableConnectionString**. Se um Aplicativo do Serviços Móveis existente estiver sendo movido, navegue até a seção “Cadeias de Conexão” da guia Configurar do portal de Serviços Móveis. Clique em "Mostrar Cadeias de Conexão" e copie o valor.
 
Por padrão, o esquema a ser usado é **MS\_MobileServiceName**, mas isso pode ser substituído pela configuração **MS\_TableSchema**. Novamente em "Configurações de aplicativo", defina **MS\_TableSchema** como o nome do esquema a ser usado. Se um Aplicativo do Serviços Móveis existente estiver sendo movido, um esquema já foi criado usando o Entity Framework - esse é o nome do Serviço Móvel, e não da instância do Serviço de Aplicativo que hospedará o código agora.

## <a name="push"></a>Como habilitar os recursos de push

Para que o push funcione, o aplicativo Web adicionalmente precisará conhecer as informações sobre o hub de Notificação.
 
Em "Cadeias de conexão", defina **MS\_NotificationHubConnectionString** com a cadeia de conexão DefaultFullSharedAccessSignature para o Hub de Notificação. Se um Aplicativo do Serviços Móveis existente estiver sendo movido, navegue até a seção “Cadeias de Conexão” da guia Configurar do portal de Serviços Móveis. Clique em "Mostrar Cadeias de Conexão" e copie o valor.

A configuração de aplicativo **MS\_NotificationHubName** deve ser definida como o nome do hub. Se um Aplicativo do Serviços Móveis existente estiver sendo movido, você poderá obter esse valor da guia de push no portal de Serviços Móveis. Os outros campos dessa guia são vinculados ao hub em si e não precisam ser copiados para lugar algum.
 
## <a name="auth"></a>Como habilitar os recursos de auth

Os recursos de identidade também têm requisitos de configuração de aplicativo para os provedores individuais. Se um Aplicativo do Serviços Móveis existente estiver sendo movido, cada um dos campos da guia Identidade do portal de Serviços Móveis terá uma configuração de aplicativo correspondente.
 
Conta da Microsoft: * **MS\_MicrosoftClientID** * **MS\_MicrosoftClientSecret** * **MS\_MicrosoftPackageSID**
 
Facebook: * **MS\_FacebookAppID** * **MS\_FacebookAppSecret**
 
Twitter * **MS\_TwitterConsumerKey** * **MS\_TwitterConsumerSecret**
 
Google * **MS\_GoogleClientID** * **MS\_GoogleClientSecret**
 
AAD * **MS\_AadClientID** * **MS\_AadTenants** -Observação: **MS\_AadTenants** é armazenado como uma lista separada por vírgulas dos domínios de locatário (os campos "Locatários Permitidos" no portal de Serviços Móveis).

## <a name="publish"></a>Como publicar o projeto de Serviços Móveis

1. No portal de Visualização, navegue até seu aplicativo e selecione "Obter perfil de publicação” na barra de comandos. Salve o perfil baixado no computador local.
2. No Visual Studio, clique com o botão direito do mouse no projeto de servidor de Serviços Móveis e selecione “Publicar”. Na guia do perfil, escolha "Importar" e navegue até o perfil baixado.
3. Clique em publicar para implantar o código no Serviço de Aplicativo.

Os logs são tratados por meio dos recursos de log do Serviço de Aplicativo padrão. Para saber mais sobre o log, consulte [Habilitar o log de diagnóstico no Serviço de Aplicativo do Azure].

<!-- URLs. -->

[Portal de Gerenciamento do Azure Preview]: https://portal.azure.com/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Habilitar o log de diagnóstico no Serviço de Aplicativo do Azure]: web-sites-enable-diagnostic-log.md
[preços do Serviço de Aplicativo]: https://azure.microsoft.com/pt-BR/pricing/details/app-service/
[Ambiente de Serviço de Aplicativo]: app-service-app-service-environment-intro.md
[Serviços Móveis versus Serviço de Aplicativo]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[migrar um serviço móvel para um aplicativo móvel no Serviço de Aplicativo]: app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md

<!---HONumber=Nov15_HO1-->