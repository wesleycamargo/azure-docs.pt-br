<properties
	pageTitle="Autenticação e autorização para aplicativos de API no Serviço de Aplicativo do Azure | Microsoft Azure"
	description="Saiba mais sobre os serviços de autenticação e autorização fornecidos pelo Serviço de Aplicativo do Azure para Aplicativos de API."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="12/04/2015"
	ms.author="tdykstra"/>

# Autenticação e autorização para aplicativos de API no Serviço de Aplicativo do Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Visão geral 

Este artigo descreve as opções para lidar com autenticação e autorização para Aplicativos de API no Serviço de Aplicativo do Azure.

O diagrama a seguir ilustra algumas características importantes da autenticação do Serviço de Aplicativo:

* Ele pré-processa solicitações de entrada de API, dando-lhe várias opções para a quantidade de trabalho de autenticação que você deseja realizar em seu próprio código. 
* Ele dá suporte a cinco provedores de autenticação: Active Directory do Azure, Facebook, Google, Twitter e Conta da Microsoft.
* Ele funciona para a autenticação de usuário final e de entidade de serviço. 
* Ele funciona da mesma forma para Aplicativos de API, Aplicativos Web e Aplicativos Móveis.

![](./media/app-service-api-authentication/api-apps-overview.png)

## Pré-processamento de solicitações de entrada

O Serviço de Aplicativo pode impedir que solicitações HTTP anônimas cheguem ao aplicativo de API ou autenticar aquelas que têm tokens antes que elas cheguem ao aplicativo de API. Para um aplicativo de API, você pode configurar uma destas três opções:

1. Permitir que apenas solicitações autenticadas cheguem ao aplicativo de API.

	Se uma solicitação anônima for recebida de um navegador, o Serviço de Aplicativo será redirecionado para uma página de logon.

	Isso funciona se você souber de antemão qual provedor de autenticação (Google, Twitter etc.) irá usar. Você pode configurar o Serviço de Aplicativo para lidar com o processo de logon para você. Como alternativa, você pode especificar sua própria URL para a qual o Serviço de Aplicativo redirecionará as solicitações anônimas. Você pode então oferecer aos usuários uma variedade de provedores de autenticação.

	Com essa opção, você não precisa escrever código de autenticação no aplicativo, e a autorização é simplificada, pois as declarações mais importantes são fornecidas nos cabeçalhos HTTP.

2. Permitir que todas as solicitações cheguem ao aplicativo de API, mas validar as solicitações autenticadas e transmitir informações de autenticação nos cabeçalhos HTTP.

	Essa opção oferece mais flexibilidade para lidar com solicitações anônimas e torna mais fácil escrever código que precisa de acesso às declarações mais comuns. Ao contrário da opção 1, você precisará escrever código caso deseje impedir que usuários anônimos usem a API.

3. Permitir que todas as solicitações cheguem à API e não executar ação alguma nas informações de autenticação nas solicitações.

	Essa opção deixa as tarefas de autenticação e autorização inteiramente a cargo do código do aplicativo.

No [portal do Azure](https://portal.azure.com/), selecione a opção desejada na folha **Autenticação/Autorização**.

![](./media/app-service-api-authentication/authblade.png)

Para as opções 1 e 2, ative a **Autenticação do Serviço de Aplicativo** e, na lista suspensa **Ação a ser tomada quando as solicitações não são autenticadas**, escolha **Logon** ou **Permitir a solicitação (nenhuma ação)**. Se escolher **Logon**, você precisará escolher um provedor de autenticação e configurá-lo.

![](./media/app-service-api-authentication/actiontotake.png)
 
## Independente de linguagem

O processamento de autenticação do Serviço de Aplicativo acontece antes que as solicitações cheguem ao aplicativo de API. Isso significa que os recursos de autenticação funcionam para aplicativos de API escritos em qualquer linguagem ou estrutura. A API pode ser baseada em ASP.NET, Java, Node.js ou qualquer estrutura à qual o Serviço de Aplicativo dê suporte.

O Serviço de Aplicativo passa o token JWT no cabeçalho de Autorização de uma solicitação HTTP e o código escrito em qualquer linguagem ou estrutura pode obter as informações necessárias do token. Além disso, o Serviço de Aplicativo oferece acesso mais fácil às declarações mais comumente usadas definindo alguns cabeçalhos especiais, como:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON
 
Em uma API .NET, você pode usar o atributo `Authorize` e, para uma autorização mais refinada, é possível escrever facilmente um código com base em declarações, pois as informações de declarações são preenchidas para você em classes .NET.

## <a id="internal"></a> Autenticação da conta de serviço

Você também pode usar a autenticação do Serviço de Aplicativo para cenários internos, como chamar de um aplicativo de API para outro aplicativo de API. Nesse cenário, você pode usar as credenciais de uma conta de serviço em vez das credenciais de usuário final para autenticação. Uma conta de serviço também pode ser chamada de *entidade de serviço* no Active Directory do Azure e a autenticação que usa essa conta também é conhecida como um cenário de serviço a serviço.

Para cenários de serviço a serviço, você pode proteger o aplicativo de API chamado usando o Active Directory do Azure e fornecer um token de autorização de entidade de serviço do AAD ao chamar o aplicativo de API. Você pode solicitar o token fornecendo a ID do cliente e o segredo do cliente do aplicativo do AAD. Nenhum código especial exclusivo do Azure será exigido, como era o costume no tratamento do token Zumo dos Serviços Móveis. Um exemplo desse cenário que usa aplicativos de API ASP.NET é abordado pelo tutorial [Autenticação de entidade de serviço para Aplicativos de API](app-service-api-dotnet-service-principal-auth.md).

Se quiser lidar com um cenário de serviço a serviço sem usar a autenticação do Serviço de Aplicativo, você poderá usar a autenticação de certificados de cliente ou a autenticação básica. Para saber mais sobre certificados de cliente no Azure, veja [Como configurar a Autenticação Mútua TLS para aplicativos Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Para saber mais sobre como configurar a autenticação básica no ASP.NET, confira [Filtros de autenticação na API Web 2 ASP.NET](http://www.asp.net/web-api/overview/security/authentication-filters).

A autenticação de conta de serviço de um aplicativo lógico do Serviço de Aplicativo para um aplicativo de API é um caso especial, que é explicado em [Usando a API personalizada hospedada no Serviço de Aplicativo com aplicativos lógicos](../app-service-logic/app-service-logic-custom-hosted-api.md).

## Mais informações

Para saber mais sobre os serviços de autenticação e autorização no Serviço de Aplicativo do Azure, consulte [Expansão da autenticação/autorização do Serviço de Aplicativo](/blog/announcing-app-service-authentication-authorization/).

## Próximas etapas

Este artigo explicou os recursos de autenticação e autorização de aplicativos de API do Serviço de Aplicativo.

Se você estiver seguindo a sequência dos tutoriais de introdução ao ASP.NET e aos Aplicativos de API, experimente esses recursos no próximo tutorial, [Autenticação de usuário em Aplicativos de API do Serviço de Aplicativo](app-service-api-dotnet-user-principal-auth.md).

Para saber mais sobre como usar Node e Java no Serviço de Aplicativo do Azure, veja a [Central de Desenvolvedores do Node.js](/develop/nodejs/) e a [Central de Desenvolvedores do Java](/develop/java/).

<!---HONumber=AcomDC_1217_2015-->