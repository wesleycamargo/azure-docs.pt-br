<properties
	pageTitle="Autenticação e autorização no Serviço de Aplicativo do Azure | Microsoft Azure"
	description="Referência conceitual e visão geral do recurso Autenticação/Autorização para o Serviço de Aplicativo do Azure"
	services="app-service"
	documentationCenter=""
	authors="mattchenderson"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="04/25/2016"
	ms.author="mahender"/>
    
# Autenticação e autorização no Serviço de Aplicativo do Azure

## O que é a Autenticação/Autorização do Serviço de Aplicativo?

Autenticação/Autorização do Serviço de Aplicativo é um recurso que permite que o seu aplicativo faça logon dos usuários sem alterações de código necessárias no back-end do aplicativo. Ele fornece uma maneira fácil de proteger o aplicativo e trabalhar com dados por usuário.

O Serviço de Aplicativo usa uma identidade federada, na qual um provedor de identidade de terceiros armazena contas e autentica usuários. O aplicativo se baseia nas informações de identidade do provedor em vez de armazenar essas informações por conta própria. O Serviço de Aplicativo dá suporte a cinco provedores de identidade prontos para uso: _Active Directory do Azure_, _Facebook_, _Google_, _Conta da Microsoft_ e _Twitter_. Seu aplicativo pode utilizar qualquer quantidade desses provedores de identidade para fornecer aos usuários opções de como fazer logon. Também é possível expandir esse suporte interno integrando outro provedor de identidade ou [sua própria solução de identidade personalizada][custom-auth].

Se você deseja começar agora mesmo, veja um dos seguintes tutoriais:

- [Adicionar autenticação ao aplicativo iOS][iOS] (ou [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms] ou [Cordova])
- [Autenticação de usuário para Aplicativos de API no Serviço de Aplicativo do Azure][apia-user]

## Como funciona a autenticação no Serviço de Aplicativo

Para autenticar o uso de um dos provedores de identidade, primeiro você precisa configurar o provedor de identidade para conhecer seu aplicativo. O provedor de identidade, em seguida, fornecerá as IDs e os segredos que você fornecer de volta para o Serviço de Aplicativo. Isso conclui a relação de confiança e permite que o Serviço de Aplicativo valide as declarações de usuário do provedor de identidade, como tokens de autenticação.

Para conectar um usuário com um desses provedores, o usuário deverá ser redirecionado para um ponto de extremidade de logon desse provedor. Se os clientes estiverem usando um navegador da Web, será possível fazer com que o Serviço de Aplicativo direcione automaticamente todos os usuários não autenticados para o ponto de extremidade de logon. Caso contrário, você precisará direcionar seus clientes para `{your App Service base URL}/.auth/login/<provider>`, em que `<provider>` é um destes: _aad_, _facebook_, _google_, _microsoft_ ou _twitter_. Os cenários Móveis e de API são abordados nas seções abaixo.

Os usuários que interagem com seu aplicativo por meio de um navegador da Web terão um cookie definido, para que possam permanecer autenticados conforme navegam no aplicativo. Para outros tipos de cliente, como móvel, um JWT (token Web JSON) será emitido para o cliente que deve ser apresentado no cabeçalho `X-ZUMO-AUTH`. Os SDKs do cliente dos Aplicativos Móveis cuidarão disso para você. Como alternativa, um token de identidade ou de acesso do Azure Active Directory poderá ser incluído diretamente no cabeçalho `Authorization` como um [token de portador](https://tools.ietf.org/html/rfc6750).

O Serviço de Aplicativo validará qualquer cookie ou token emitido de seu aplicativo, permitindo que os usuários sejam autenticados. Para restringir os usuários que tem permissão de acessar seu aplicativo, veja a seção [Autorização](#authorization) abaixo.

### Autenticação móvel com um SDK do provedor

Depois que tudo estiver configurado no back-end, é possível modificar os clientes móveis para fazer logon com o Serviço de Aplicativo. Há duas abordagens aqui:

- Utilize um SDK publicado por um provedor de identidade específico para estabelecer a identidade e, em seguida, obtenha acesso ao Serviço de Aplicativo.
- Usando uma única linha de código, permita que o SDK do cliente dos Aplicativos Móveis conectem os usuários.

>[AZURE.TIP] A maior parte dos aplicativos deve usar um SDK do provedor para obter uma experiência de logon que se parece mais com a nativa e para aproveitar o suporte de atualização e outros benefícios específicos do provedor.

Trabalhar com um SDK do provedor possibilita que a experiência de logon interaja mais intimamente com o SO da plataforma em que o aplicativo está sendo executado. Isso também lhe fornece um token do provedor e algumas informações de usuário no cliente, o que torna muito mais fácil consumir Graph APIs e personalizar a experiência do usuário. Ocasionalmente, em blogs e fóruns você verá isso designado como “fluxo do cliente” ou “fluxo direcionado pelo cliente”, já que o código no cliente está manipulando o logon, e o código do cliente tem acesso a um token do provedor.

Depois que um token do provedor é obtido, ele precisa ser enviado ao Serviço de Aplicativo para validação. Assim que o Serviço de Aplicativo valida o token, ele cria um novo token do Serviço de Aplicativo que é retornado ao cliente. O SDK do cliente dos Aplicativos Móveis tem métodos auxiliares para gerenciar essa troca e anexa automaticamente o token a todas as solicitações para o back-end do aplicativo. O desenvolvedor também pode manter uma referência ao token do provedor se desejar.

### Autenticação móvel sem um SDK do provedor

Se não desejar configurar um SDK do provedor, você poderá permitir que os Aplicativos Móveis do Serviço de Aplicativo realizem o logon para você. O SDK do cliente dos Aplicativos Móveis abrirá uma exibição da Web para o provedor de sua escolha e concluirá a conexão. Ocasionalmente, em blogs e fóruns, você verá isso sendo chamado de “fluxo do servidor” ou “fluxo direcionado pelo servidor”, já que o servidor gerencia o logon e o SDK do cliente nunca recebe o token do provedor.

O código necessário para iniciar esse fluxo é abordado no tutorial de autenticação de cada plataforma. Ao final do fluxo, o SDK do cliente tem um token do Serviço de Aplicativo, e o token é anexado automaticamente a todas as solicitações para o back-end do aplicativo.

### Autenticação serviço a serviço

Além de fornecer aos usuários acesso ao seu aplicativo, também é possível permitir que outro aplicativo confiável chame sua própria API. Por exemplo, você poderia fazer com que um aplicativo do Serviço de Aplicativo chamasse uma API em outro. Neste cenário, você pode obter um token usando credenciais para uma conta de serviço em vez de credenciais de usuário final. Uma conta de serviço também é conhecida como *entidade de serviço* no jargão do Azure Active Directory, e a autenticação que usa essa conta também é chamada de cenário de serviço a serviço.

>[AZURE.IMPORTANT] Como elas são executadas em dispositivos do cliente, os aplicativos móveis fazem _não_ contam como aplicativos confiáveis e não devem usar um fluxo da entidade de serviço. Em vez disso, eles devem usar um dos fluxos de usuário detalhados acima.

Para cenários de serviço a serviço, o Serviço de Aplicativo pode proteger seu aplicativo usando o Azure Active Directory. O aplicativo de chamada precisa apenas fornecer um token de autorização da entidade de serviço do AAD obtido pelo fornecimento da ID do cliente e do segredo do cliente por meio do AAD. Um exemplo desse cenário que usa aplicativos de API ASP.NET é abordado pelo tutorial [Autenticação de entidade de serviço para Aplicativos de API][apia-service].

Se quiser lidar com um cenário de serviço a serviço sem usar a autenticação do Serviço de Aplicativo, você poderá usar a autenticação de certificados de cliente ou a autenticação básica. Para saber mais sobre certificados de cliente no Azure, veja [Como configurar a Autenticação Mútua TLS para aplicativos Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Para obter mais informações sobre a autenticação básica no ASP.NET, veja [Authentication Filters in ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters) (Filtros de autenticação na API Web ASP.NET 2).

A autenticação de conta de serviço de um aplicativo lógico do Serviço de Aplicativo para um aplicativo de API é um caso especial, que é explicado detalhadamente em [Usando a API personalizada hospedada no Serviço de Aplicativo com Aplicativos Lógicos](../app-service-logic/app-service-logic-custom-hosted-api.md).

## <a name="authorization"></a>Como funciona a autorização no Serviço de Aplicativo

Você tem controle total sobre quais solicitações têm permissão de acessar o aplicativo. A Autenticação/Autorização do Serviço de Aplicativo pode ser configurada com qualquer um dos seguintes comportamentos:

- Permitir que apenas solicitações autenticadas cheguem ao aplicativo.

	Se uma solicitação anônima for recebida de um navegador, o Serviço de Aplicativo será redirecionado para uma página de logon do provedor de identidade escolhido. Se a solicitação for proveniente de um dispositivo móvel, será retornada uma resposta HTTP _401 Não Autorizado_.

	Com essa opção, você não precisa escrever nenhum código de autenticação em seu aplicativo. Se precisar de uma autorização mais refinada, as informações sobre o usuário estarão disponíveis para seu código.

- Permitir que todas as solicitações cheguem ao aplicativo, mas validar as solicitações autenticadas e transmitir informações de autenticação nos cabeçalhos HTTP.

	Essa opção adia as decisões de autorização no código do aplicativo. Ela oferece mais flexibilidade no tratamento de solicitações anônimas, mas exige que você escreva um código.
	
- Permitir que todas as solicitações cheguem ao aplicativo e não executar nenhuma ação em relação às informações de autenticação nas solicitações.

	Nesse caso, o recurso Autenticação/Autorização é desativado. As tarefas de autenticação e autorização ficam inteiramente a cargo do código do aplicativo.

Os comportamentos descritos acima são controlados pela **Ação a ser tomada quando a solicitação não for autenticada** no portal. A escolha de “Fazer logon com...” para um dos provedores exigirá a autenticação de todas as solicitações. A opção “Permitir solicitação (nenhuma ação)” adia a decisão de autorização no código, mas ainda fornece informações de autenticação. Se deseja que seu código lide com tudo, é possível desabilitar o recurso Autenticação/Autorização.

## Trabalhando com identidades de usuário em seu aplicativo

O Serviço de Aplicativo transmite algumas informações do usuário para seu aplicativo usando cabeçalhos especiais. Esses cabeçalhos não são permitidos por meio de solicitações externas e apenas estarão presentes se forem definidos pela Autenticação/Autorização do Serviço de Aplicativo. Alguns cabeçalhos de exemplo incluem:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

Um código escrito em qualquer linguagem ou estrutura pode obter as informações necessárias desses cabeçalhos. Para aplicativos ASP.NET 4.6, ClaimsPrincipal é definido automaticamente com os valores apropriados.

O aplicativo também pode obter detalhes adicionais do usuário por meio de um HTTP GET em seu ponto de extremidade `/.auth/me`. Se um token válido foi incluído na solicitação, isso retornará uma carga JSON com detalhes sobre o provedor que está sendo usado, o token do provedor subjacente e algumas outras informações do usuário. Os SDKs do servidor dos Aplicativos Móveis fornecem métodos auxiliares para trabalhar com esses dados ([Node.JS](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md/#howto-tables-getidentity), [.NET](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md/#user-info)).

## Documentação e recursos adicionais

### Provedores de identidade
Os seguintes tutoriais mostram como configurar o Serviço de Aplicativo para aproveitar provedores de autenticação diferentes:

- [Como configurar seu aplicativo para usar o logon do Active Directory do Azure][AAD]
- [Como configurar seu aplicativo para usar o logon do Facebook][Facebook]
- [Como configurar seu aplicativo para usar o logon do Google][Google]
- [Como configurar seu aplicativo para usar o logon da Conta da Microsoft][MSA]
- [Como configurar seu aplicativo para usar o logon do Twitter][Twitter]

Se você deseja usar um sistema de identidade diferente daqueles fornecidos aqui, também é possível utilizar o [suporte de autenticação personalizada da preview no SDK do servidor do .NET dos Aplicativos Móveis][custom-auth], que pode ser usado em Aplicativos Web, Móveis ou de API.

### Aplicativos móveis
Os seguintes tutoriais mostram como adicionar a autenticação aos clientes móveis usando o fluxo direcionado pelo servidor:

- [Adicione autenticação ao seu aplicativo do iOS][iOS]
- [Adicionar a Autenticação ao aplicativo Android][Android]
- [Adicionar autenticação ao seu aplicativo do Windows][Windows]
- [Adicionar autenticação ao aplicativo Xamarin.iOS][Xamarin.iOS]
- [Adicione autenticação ao aplicativo Xamarin.Android][Xamarin.Android]
- [Adicionar autenticação ao aplicativo Xamarin.Forms][Xamarin.Forms]
- [Adicionar a Autenticação ao aplicativo Cordova][Cordova]

Se desejar usar o fluxo direcionado pelo cliente para o AAD:

- [Use a Biblioteca de Autenticação do Active Directory para iOS][ADAL-iOS]
- [Use a Biblioteca de Autenticação do Active Directory para Android][ADAL-Android]
- [Use a Biblioteca de Autenticação do Active Directory para Windows e Xamarin][ADAL-dotnet]

### Aplicativos de API
Os tutoriais a seguir mostram como proteger seus aplicativos de API:

- [Autenticação de usuário para Aplicativos de API no Serviço de Aplicativo do Azure][apia-user]
- [Autenticação de entidade de serviço para Aplicativos de API no Serviço de Aplicativo do Azure][apia-service]









[apia-user]: ../app-service-api/app-service-api-dotnet-user-principal-auth.md
[apia-service]: ../app-service-api/app-service-api-dotnet-service-principal-auth.md

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: ../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: ../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md
[MSA]: ../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: ../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md/#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md/#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md/#adal

<!---HONumber=AcomDC_0504_2016-->