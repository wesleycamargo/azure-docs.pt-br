---
title: Autenticação e autorização para aplicativos de API no Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba mais sobre os serviços de autenticação e autorização fornecidos pelo Serviço de Aplicativo do Azure para Aplicativos de API.
services: app-service\api
documentationcenter: .net
author: tdykstra
manager: wpickett
editor: ''

ms.service: app-service-api
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: rachelap

---
# Autenticação e autorização para aplicativos de API no Serviço de Aplicativo do Azure
## Visão geral
> [!NOTE]
> Este tópico será migrado para um tópico consolidado de [Autenticação/Autorização de Serviço de Aplicativo](../app-service/app-service-authentication-overview.md), que abrange a Web, celulares e aplicativos de API.
> 
> 

O Serviço de Aplicativo do Azure oferece serviços de autenticação e autorização internos que implementam o [OAuth 2.0](#oauth) e o [OpenID Connect](#oauth). Este artigo descreve os serviços e as opções que estão disponíveis para Aplicativos de API no Serviço de Aplicativo do Azure.

O diagrama a seguir ilustra algumas características importantes da autenticação do Serviço de Aplicativo:

* Ele processa previamente as solicitações de API de entrada, o que significa que ele funciona com qualquer linguagem ou estrutura com suporte do Serviço de Aplicativo.
* Ele oferece várias opções para a quantidade de trabalho de autenticação que você deseja realizar em seu próprio código.
* Ele funciona para a autenticação de usuário final e de conta de serviço.
* Ele dá suporte a cinco provedores de autenticação: Active Directory do Azure, Facebook, Google, Twitter e Conta da Microsoft.
* Ele funciona da mesma forma para Aplicativos de API, Aplicativos Web e Aplicativos Móveis.

![](./media/app-service-api-authentication/api-apps-overview.png)

## Independente de linguagem
O processamento de autenticação do Serviço de Aplicativo acontece antes que as solicitações cheguem ao aplicativo de API. Isso significa que os recursos de autenticação funcionam para aplicativos de API escritos em qualquer linguagem ou estrutura. A API pode ser baseada em ASP.NET, Java, Node.js ou qualquer estrutura à qual o Serviço de Aplicativo dê suporte.

O Serviço de Aplicativo passa o token web JSON (JWT) no cabeçalho de Autorização de uma solicitação HTTP e o código escrito em qualquer linguagem ou estrutura pode obter as informações necessárias do token. Além disso, o Serviço de Aplicativo oferece acesso mais fácil às declarações mais comumente usadas definindo alguns cabeçalhos especiais, como:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

Em uma API .NET, você pode usar o atributo `Authorize` e, para uma autorização mais refinada, é possível escrever facilmente um código com base em declarações, pois as informações de declarações são preenchidas para você em classes .NET.

## Várias opções de proteção
O Serviço de Aplicativo pode impedir que as solicitações HTTP anônimas atinjam seu aplicativo de API, ele pode passar todas as solicitações e validar os tokens para solicitações que os incluem ou pode deixar passar todas as solicitações sem realizar qualquer ação sobre elas:

1. Permitir que apenas solicitações autenticadas cheguem ao aplicativo de API.
   
    Se for recebida uma solicitação anônima de um navegador, o Serviço de Aplicativo será redirecionado para uma página de logon para o provedor de autenticação (AD do Azure, Google, Twitter, etc.) que você escolher.
   
    Com essa opção, você não precisa escrever código de autenticação no aplicativo, e o código de autorização é simplificado porque as declarações mais importantes são fornecidas nos cabeçalhos HTTP.
2. Permitir que todas as solicitações cheguem ao aplicativo de API, mas validar as solicitações autenticadas e transmitir informações de autenticação nos cabeçalhos HTTP.
   
    Esta opção oferece mais flexibilidade no tratamento de solicitações anônimas, mas você precisa escrever código para impedir que usuários anônimos usem a API. Como as declarações mais populares são passadas nos cabeçalhos das solicitações HTTP, o código de autorização é relativamente simples.
3. Permitir que todas as solicitações cheguem à API e não executar ação alguma nas informações de autenticação nas solicitações.
   
    Essa opção deixa as tarefas de autenticação e autorização inteiramente a cargo do código do aplicativo.

No [portal do Azure](https://portal.azure.com/), selecione a opção desejada na folha **Autenticação/Autorização**.

![](./media/app-service-api-authentication/authblade.png)

Para as opções 1 e 2, ative a **Autenticação do Serviço de Aplicativo** e, na lista suspensa **Ação a ser tomada quando a solicitação não for autenticada**, escolha **Fazer logon** ou **Permitir a solicitação (nenhuma ação)**. Se escolher **Logon**, você precisará escolher um provedor de autenticação e configurá-lo.

![](./media/app-service-api-authentication/actiontotake.png)

Para obter informações detalhadas sobre como configurar a autenticação, consulte [Como configurar seu aplicativo do Serviço de Aplicativo para usar o logon do Active Directory do Azure](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). O artigo se aplica a aplicativos de API, bem como a aplicativos móveis, e fornece links de outros artigos para os outros provedores de autenticação.

## <a id="internal"></a> Autenticação da conta de serviço
A autenticação do Serviço de Aplicativo funciona para cenários internos, como chamar de um aplicativo de API para outro aplicativo de API. Neste cenário, você pode obter um token usando credenciais para uma conta de serviço em vez de credenciais de usuário final. Uma conta de serviço também pode ser chamada de *entidade de serviço* no Active Directory do Azure e a autenticação que usa essa conta também é conhecida como um cenário de serviço a serviço.

Para cenários de serviço a serviço, proteja o aplicativo de API chamado usando o Active Directory do Azure e forneça um token de autorização de entidade de serviço do AAD ao chamar o aplicativo de API. Você obtém um token fornecendo a ID do cliente e o segredo do cliente do aplicativo do AAD. Nenhum código especial exclusivo do Azure será exigido, como era o costume no tratamento do token Zumo dos Serviços Móveis. Um exemplo desse cenário que usa aplicativos de API ASP.NET é abordado pelo tutorial [Autenticação de entidade de serviço para Aplicativos de API](app-service-api-dotnet-service-principal-auth.md).

Se quiser lidar com um cenário de serviço a serviço sem usar a autenticação do Serviço de Aplicativo, você poderá usar a autenticação de certificados de cliente ou a autenticação básica. Para saber mais sobre certificados de cliente no Azure, veja [Como configurar a Autenticação Mútua TLS para aplicativos Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Para saber mais sobre a autenticação básica no ASP.NET, confira [Filtros de autenticação na API Web 2 ASP.NET](http://www.asp.net/web-api/overview/security/authentication-filters).

A autenticação de conta de serviço de um aplicativo lógico do Serviço de Aplicativo para um aplicativo de API é um caso especial, que é explicado em [Usando a API personalizada hospedada no Serviço de Aplicativo com aplicativos lógicos](../app-service-logic/app-service-logic-custom-hosted-api.md).

## Autenticação de cliente móvel
Para saber mais sobre como lidar com a autenticação de clientes móveis, confira a [documentação sobre autenticação para aplicativos móveis](../app-service-mobile/app-service-mobile-ios-get-started-users.md). A autenticação do Serviço de Aplicativo funciona da mesma forma para aplicativos móveis e para aplicativos de API.

## Mais informações
Para saber mais sobre autenticação e autorização no Serviço de Aplicativo do Azure, consulte os seguintes recursos:

* [Como expandir a autenticação/autorização do Serviço de Aplicativo](/blog/announcing-app-service-authentication-authorization/)
* [Como configurar seu aplicativo do Serviço de Aplicativo para usar o logon do Active Directory do Azure](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md) (Inclui links para outros provedores de autenticação na parte superior da página.)

Para saber mais sobre o OAuth 2.0, o OpenID Connect e os Tokens da Web JSON (JWT), confira os recursos a seguir.

* [Introdução ao OAuth 2.0](http://shop.oreilly.com/product/0636920021810.do "Introdução ao OAuth 2.0")
* [Introdução ao OAuth2, OpenID Connect e JSON Web Tokens (JWT) - PluralSight Course](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction)
* [Criando e protegendo uma API RESTful para vários clientes no ASP.NET - PluralSight course](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

Para saber mais sobre o Active Directory do Azure, confira os recursos a seguir.

* [Cenários do AD do Azure](http://aka.ms/aadscenarios)
* [Guia dos desenvolvedores do AD do Azure](http://aka.ms/aaddev)
* [Exemplos do AD do Azure](http://aka.ms/aadsamples)

## Próximas etapas
Este artigo explicou os recursos de autenticação e de autorização do Serviço de Aplicativo que você pode usar para aplicativos de API. O próximo tutorial da série Introdução mostra como implementar a [autenticação de usuário em Aplicativos de API do Serviço de Aplicativo](app-service-api-dotnet-user-principal-auth.md).

<!---HONumber=AcomDC_0713_2016-->