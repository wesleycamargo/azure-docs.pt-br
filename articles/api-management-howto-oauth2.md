<properties pageTitle="How to authorize developer accounts using OAuth 2.0 in Azure API Management" metaKeywords="" description="Learn how to authorize users using OAuth 2.0 in API Management." metaCanonical="" services="api-management" documentationCenter="API Management" title="How to authorize developer accounts using OAuth 2.0 in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Como autorizar contas de desenvolvedor usando o OAuth 2.0 no Gerenciamento de API do Azure

O Gerenciamento de API fornece a capacidade para que os usuários acessem o portal do desenvolvedor usando a autorização do OAuth 2.0. Este guia mostra como configurar uma instância de serviço de Gerenciamento de API para usar a autorização OAuth 2.0.

> Para obter mais informações sobre OAuth 2.0, consulte a especificação em [http://oauth.net/2/][http://oauth.net/2/].

## Neste tópico

-   [Pré-requisitos][Pré-requisitos]
-   [Configurar um servidor de autorização do OAuth 2.0 no Gerenciamento de API][Configurar um servidor de autorização do OAuth 2.0 no Gerenciamento de API]
-   [Configurar uma API para usar a autorização do usuário do OAuth 2.0][Configurar uma API para usar a autorização do usuário do OAuth 2.0]
-   [Testar a autorização do usuário do OAuth 2.0 no Portal do Desenvolvedor][Testar a autorização do usuário do OAuth 2.0 no Portal do Desenvolvedor]

## <a name="prerequisites"> </a>Pré-requisitos

Este guia mostra como configurar sua instância de serviço de Gerenciamento de API para usar a autorização OAuth 2.0 para contas do desenvolvedor, mas não mostra como configurar um provedor OAuth 2.0. A configuração para cada provedor OAuth 2.0 é diferente, embora as etapas sejam semelhantes e as peças de informações necessárias usadas ao configurar o OAuth 2.0 na sua instância de serviço de Gerenciamento de API sejam as mesmas. Este tópico mostra exemplos ao usar o Active Directory do Azure como um provedor OAuth 2.0.

> Para obter mais informações sobre a configuração do OAuth 2.0 usando o Active Directory do Azure, consulte a amostra [WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet].

## <a name="step1"> </a>Configurar um servidor de autorização do OAuth 2.0 no Gerenciamento de API

Para começar, clique em **Console de gerenciamento** no Portal do Azure para acessar o serviço de Gerenciamento de API. Isso levará você ao portal administrativo do Gerenciamento de API.

![Console de Gerenciamento de API][api-management-management-console]

> Se você ainda não criou uma instância de serviço de Gerenciamento de API, consulte [Criar uma instância de serviço de Gerenciamento de API][Criar uma instância de serviço de Gerenciamento de API] no tutorial [Introdução ao Gerenciamento de API do Azure][Introdução ao Gerenciamento de API do Azure].

Clique em **Segurança** no menu de **Gerenciamento da API** à esquerda, clique em **OAuth 2.0** e em **Adicionar servidor de autorização**.

![OAuth 2.0][api-management-oauth2]

Após clicar em **Adicionar servidor de autorização**, a nova forma do servidor de autorização é exibida.

![Novo servidor][api-management-oauth2-server-1]

Digite um nome e uma descrição opcional nos campos **Nome** e **Descrição**.

> Esses campos são usados para identificar o servidor de autorização OAuth 2.0 dentro da instância do serviço de Gerenciamento de API atual e seus valores não vêm do servidor OAuth 2.0.

Digite a **URL da página de registro do cliente**. Essa página é onde os usuários podem criar e gerenciar suas contas e varia de acordo com o provedor OAuth 2.0 usado.

A seção seguinte da forma contém as configurações dos **tipos de concessão do código de autorização**, da **URL do ponto de extremidade de autorização** e do **método de solicitação de autorização**.

![Novo servidor][api-management-oauth2-server-2]

Especifique os **tipos de concessão do código de autorização** ao verificar os tipos desejados. O **código de autorização** é especificado por padrão.

Digite a **URL do ponto de extremidade de autorização**. Para o Active Directory do Azure, essa URL será semelhante à URL seguinte, onde `<client_id>` é substituído pela ID do cliente que identifica seu aplicativo para o servidor OAuth 2.0.

    https://login.windows.net/<client_id>/oauth2/authorize

O **método de solicitação de autorização** especifica como a solicitação de autorização é enviada para o servidor OAuth 2.0. O **GET** é selecionado por padrão.

A seção seguinte está onde a **URL de ponto de extremidade do token**, **os métodos de autenticação do cliente**, **o método de envio de token de acesso** e **o escopo padrão** são especificados.

![Novo servidor][api-management-oauth2-server-3]

Para o servidor OAuth 2.0 do Active Directory do Azure, a **URL do ponto de extremidade do token** terá o seguinte formato, onde `<APPID>` tem o formato de `yourapp.onmicrosoft.com`.

    https://login.windows.net/<APPID>/oauth2/token

A configuração padrão para os **métodos de autenticação do cliente** é **Básico** e o **método de envio do token de acesso** é **cabeçalho de autorização**. Esses valores são configurados nesta seção da forma, junto com o **Escopo padrão**.

A seção **credenciais do cliente** contém a **ID do cliente** e a **senha do cliente**, que são obtidas durante o processo de criação e de configuração do seu servidor OAuth 2.0. Uma vez que a **ID do cliente** e a **senha do cliente** são especificadas, o **redirect\_uri** para o **código de autorização** é gerado. Essa URI é usada para configurar a URL de resposta em sua configuração do servidor OAuth 2.0.

![Novo servidor][api-management-oauth2-server-4]

Se os **Tipos de concessão do código de autorização** são definidos para **senha do proprietário do recurso**, a seção de **credenciais de senha do proprietário de recurso** é usada para especificar estas credenciais; por outro lado, você pode deixá-la em branco.

![Novo servidor][api-management-oauth2-server-5]

Uma vez que o formulário está concluído, clique em **Salvar** para salvar a configuração do servidor de autorização do OAuth 2.0 de Gerenciamento de API. Quando a configuração do servidor for salva, você pode configurar as APIs para usar esta configuração, conforme mostrado na seção seguinte.

## <a name="step2"> </a>Configurar uma API para usar a autorização do usuário do OAuth 2.0

Clique em **APIs** no menu de **Gerenciamento de API** à esquerda, clique no nome da API desejada, clique em **Segurança** e marque a caixa para **OAuth 2.0**.

![Autorização do usuário][api-management-user-authorization]

Selecione o **Servidor de autorização** desejado a partir da lista suspensa e clique em **Salvar**.

![Autorização do usuário][api-management-user-authorization-save]

## <a name="step3"> </a>Testar a autorização do usuário do OAuth 2.0 no Portal do Desenvolvedor

Depois de configurar seu servidor de autorização OAuth 2.0 e configurar sua API para usar esse servidor, você pode testá-lo indo para o Portal do Desenvolvedor e chamando a API. Clique em **Portal do desenvolvedor** no menu superior direito.

![Portal do desenvolvedor][api-management-developer-portal-menu]

Clique em **APIs** no menu superior e selecione **API de Eco**.

![API de Eco][api-management-apis-echo-api]

> Se você tem apenas uma API configurada ou visível na conta, clicar em APIs levará você diretamente às operações dessa API.

Selecione a operação **Recurso GET**, clique em **Abrir Console** e selecione **Código de Autorização** na lista suspensa.

![Abrir console][api-management-open-console]

Quando o **Código de autorização** está selecionado, uma janela pop-up é exibida com a forma de entrada do provedor OAuth 2.0. Neste exemplo, o formulário de inscrição é fornecido pelo Active Directory do Azure.

> Se você tiver pop-ups desativados, será solicitado a habilitá-los pelo navegador. Depois de habilitar, selecione **Código de autorização** novamente e a forma de entrada será exibida.

![Entrar][api-management-oauth2-signin]

Depois de entrar, os **Cabeçalhos de solicitação** são preenchidos com um cabeçalho `Authorization : Bearer` que autoriza a solicitação.

![Token do cabeçalho da solicitação][api-management-request-header-token]

Nesse momento, você pode configurar os valores desejados para os parâmetros restantes e enviar a solicitação.

  [http://oauth.net/2/]: http://oauth.net/2/
  [Pré-requisitos]: #prerequisites
  [Configurar um servidor de autorização do OAuth 2.0 no Gerenciamento de API]: #step1
  [Configurar uma API para usar a autorização do usuário do OAuth 2.0]: #step2
  [Testar a autorização do usuário do OAuth 2.0 no Portal do Desenvolvedor]: #step3
  [WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
  [Criar uma instância de serviço de Gerenciamento de API]: ../api-management-get-started/#create-service-instance
  [Introdução ao Gerenciamento de API do Azure]: ../api-management-get-started
  [api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
  [api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
  [api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
  [api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
  [api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
  [api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
  [api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
  [api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
  [api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
  [api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
  [api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
  [api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
  [api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
  [api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png