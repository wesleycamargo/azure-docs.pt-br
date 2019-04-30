---
title: Venda de SaaS por meio do Azure – APIs | Microsoft Docs
description: Explica como criar uma oferta de SaaS por meio de APIs do marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: d6ab18d68d6508a18f0b36ab5a39e15fa7c0555a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594503"
---
# <a name="saas-sell-through-azure---apis"></a>Venda de SaaS por meio do Azure – APIs

Este artigo explica como criar uma oferta de SaaS com APIs. As APIs são necessárias para permitir assinaturas para sua oferta de SaaS se você tem a opção Venda por meio do Azure selecionada.  Este artigo é dividido em duas seções:

-   Autenticação serviço a serviço entre um serviço SaaS e o Azure Marketplace
-   Métodos de API e pontos de extremidade

As APIs a seguir são fornecidas para ajudá-lo a integrar o seu serviço SaaS ao Azure:

-   Resolver
-   Assinar
-   Converter
-   Cancelar assinatura

O diagrama a seguir mostra o fluxo de assinatura de um novo cliente e quando essas APIs são usadas:

![Fluxo de API da oferta de SaaS](./media/saas-offer-publish-api-flow.png)


## <a name="service-to-service-authentication-between-saas-service-and-azure-marketplace"></a>Autenticação serviço a serviço entre um serviço SaaS e o Azure Marketplace

Azure não impõe nenhuma restrição quanto à autenticação que o serviço SaaS expõe a seus usuários finais. No entanto, quando se trata da comunicação entre o serviço SaaS e as APIs do Azure Marketplace, a autenticação é feita no contexto de um Aplicativo Azure AD (Azure Active Directory).

A seção a seguir descreve como criar um Aplicativo Azure AD.


### <a name="register-an-azure-ad-application"></a>Registrar um Aplicativo Azure AD

Para que você possa usar os recursos do AD do Azure em qualquer aplicativo desejado, primeiramente é preciso registrá-lo em um locatário do AD do Azure. Esse processo de registro envolve fornecer ao Azure AD detalhes sobre seu aplicativo, como a URL em que ele está localizado, a URL à qual enviar respostas depois que um usuário é autenticado, o URI que identifica o aplicativo, etc.

Para registrar um novo aplicativo usando o portal do Azure, realize as seguintes etapas:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Se a sua conta der acesso a mais de um, clique em sua conta no canto superior direito e defina a sessão do portal para o desejado de locatário do Azure AD.
3. No painel de navegação esquerdo, clique no serviço **Azure Active Directory**, clique em **Registros do aplicativo** e clique em **Novo registro de aplicativo**.

   ![Registros de Aplicativo do AD SaaS](./media/saas-offer-app-registration.png)

4. Na página Criar, insira as informações de registro do aplicativo:
   - **Nome**: insira um nome significativo de aplicativo
   - **Tipo de aplicativo**: 
     - Selecione **Nativo** para [aplicativos cliente](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) que são instalados localmente em um dispositivo. Essa configuração é usada para [clientes nativos](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) públicos OAuth.
     - Selecione **Aplicativo Web/API** para [aplicativos cliente](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) e [recurso/aplicativos de API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) que estão instalados em um servidor seguro. Essa configuração é usada para [clientes Web](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) confidenciais OAuth e [clientes baseados em agente de usuário](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client) públicos.
     O mesmo aplicativo também pode expor um cliente e o recurso/API.
   - **URL de logon**: para aplicativos e API da Web, informe a URL base do aplicativo. Por exemplo, **http:\//localhost:31544** pode ser a URL para um aplicativo web em execução no seu computador local. Os usuários usariam então essa URL para entrar em um aplicativo cliente Web.
   - **URI de redirecionamento**: para aplicativos públicos, informe o URI usado pelo Microsoft Azure Active Directory para retornar respostas de token. Insira um valor específico para seu aplicativo, por exemplo **http:\//MyFirstAADApp**.

     ![Registros de Aplicativo do AD de SaaS](./media/saas-offer-app-registration-2.png) Para obter exemplos específicos de aplicativos Web ou aplicativos nativos, confira as configurações de início rápido guiado que estão disponíveis na seção de Introdução do [Guia de desenvolvedores do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5. Ao terminar, clique em **Criar**. Uma ID do aplicativo exclusiva é atribuída pelo Azure AD ao aplicativo e você é levado à página de registro principal do aplicativo. Dependendo de se o seu aplicativo é Web ou nativo, diferentes opções serão fornecidas para adicionar mais recursos ao aplicativo.

   **Observação:** por padrão, o aplicativo recém-registrado é configurado para permitir que somente usuários do mesmo locatário entrem no aplicativo.

<a name="api-methods-and-endpoints"></a>Métodos de API e pontos de extremidade
-------------------------

As seções a seguir descrevem os métodos de API e os pontos de extremidade disponíveis para habilitar assinaturas para uma oferta de SaaS.

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Obter um token com base no Aplicativo Azure AD

Método HTTP

`GET`

*URL de Solicitação*

**https://login.microsoftonline.com/*{tenantId}*/oauth2/token**

*Parâmetro URI*

|  **Nome do parâmetro**  | **Obrigatório**  | **Descrição**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | ID do locatário do aplicativo AAD registrado   |
|  |  |  |


*Cabeçalho da solicitação*

|  **Nome do cabeçalho**  | **Obrigatório** |  **Descrição**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Tipo de conteúdo     | True         | Tipo de conteúdo associado à solicitação. O valor padrão é `application/x-www-form-urlencoded`.  |
|  |  |  |


*Corpo da solicitação*

| **Nome da propriedade**   | **Obrigatório** |  **Descrição**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Tipo de concessão. O valor padrão é `client_credentials`.                    |
|  Client_id          | True         |  Identificador do cliente/aplicativo associado ao Aplicativo Azure AD.                  |
|  client_secret      | True         |  Senha associada ao Aplicativo Azure AD.                               |
|  Resource           | True         |  Recurso de destino para o qual o token é solicitado. O valor padrão é `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Resposta*

|  **Nome**  | **Tipo**       |  **Descrição**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | Solicitação bem-sucedida   |
|  |  |  |

*TokenResponse*

Token de resposta de exemplo:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```

### <a name="marketplace-api-endpoint-and-api-version"></a>Ponto de extremidade de API e a versão de API do Marketplace

O ponto de extremidade para a API do Azure Marketplace é `https://marketplaceapi.microsoft.com`.

A versão atual da API é `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Resolver assinatura

A ação POST ao resolver o ponto de extremidade permite que os usuários resolvam um token do marketplace para uma ID de recurso persistente.  A ID de recurso é o identificador exclusivo para a assinatura de SAAS. 

Quando um usuário é redirecionado para um site do ISV, a URL contém um token nos parâmetros de consulta. O ISV deve usar esse token e fazer uma solicitação para resolvê-lo. A resposta contém a ID de assinatura exclusiva de SAAS, nome, ID de oferta e o plano para o recurso. Esse token é válido por apenas uma hora.

*Solicitação*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Nome do Parâmetro** |     **Descrição**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  api-version        |  A versão da operação a ser usada para esta solicitação.   |
|  |  |


*Cabeçalhos*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Não            | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
| x-ms-correlationid | Não            | Um valor de cadeia de caracteres exclusiva para a operação no cliente. Isso correlaciona todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| Content-type       | Sim          | `application/json`                                        |
| authorization      | Sim          | O token de portador JWT (Token Web JSON).                    |
| x-ms-marketplace-token| Sim| O parâmetro de consulta de token na URL quando o usuário é redirecionado ao site do ISV SaaS partindo do Azure. **Observação:** Esse token é válido somente por 1 hora. Realize também a decodificação URL do valor do token do navegador antes de usá-lo.|
|  |  |  |
  

*Corpo da resposta*

``` json
{
    "id": "",
    "subscriptionName": "",
    "offerId": "",
    "planId": "",
}
```

| **Nome do parâmetro** | **Tipo de dados** | **Descrição**                       |
|--------------------|---------------|---------------------------------------|
| ID                 | Cadeia de caracteres        | A ID da assinatura de SaaS.          |
| subscriptionName| Cadeia de caracteres| Nome da assinatura de SaaS definida pelo usuário no Azure ao assinar o serviço SaaS.|
| OfferId            | Cadeia de caracteres        | ID da oferta que o usuário assinou. |
| planId             | Cadeia de caracteres        | ID do plano que o usuário assinou.  |
|  |  |  |


*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Token resolvido com êxito.                                                            |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão ausentes ou um valor inválido de api-version foi especificado. Falha ao resolver o token porque ou ele está malformado ou expirou (o token só é válido por 1 hora depois de gerado). |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                                 |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                                |
| 503                  | `ServiceUnavailable` | Serviço inativo temporariamente, tente novamente mais tarde.                                        |
|  |  |  |


*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | O valor da ID de solicitação recebido do cliente.                                                                   |
| x-ms-correlationid | Sim          | A ID de correlação se passada pelo cliente. Caso contrário, esse valor é a ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do serviço. Isso é usado para quaisquer eventuais reconciliações. |
| Retry-After        | Não            | Esse valor é definido somente para uma resposta 429.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Assinar

O ponto de extremidade do assinante permite aos usuários iniciar uma assinatura de um serviço SaaS para um plano específico e habilitar a cobrança no sistema de comércio.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nome do Parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID exclusiva da assinatura de SaaS, que é obtida depois de resolver o token por meio da API de resolução.                              |
| api-version         | A versão da operação a ser usada para esta solicitação. |
|  |  |

*Cabeçalhos*

|  **Chave de cabeçalho**        | **Obrigatório** |  **Descrição**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   Não          | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| x-ms-correlationid     |   Não          | Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse valor é para correlacionar todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| If-Match/If-None-Match |   Não          |   Valor de ETag de validador forte.                                                          |
| content-type           |   Sim        |    `application/json`                                                                   |
|  authorization         |   Sim        |    O token de portador JWT (Token Web JSON).                                               |
| x-ms-marketplace-session-mode| Não  | Sinalizador para habilitar o modo de simulação ao assinar uma oferta de SaaS. Se definido, a assinatura não será cobrada. Isso é útil para cenários de teste de ISV. Defina-o como **'dryrun'**|
|  |  |  |

*Corpo*

``` json
{
    "lanId": "",
}
```

| **Nome do elemento** | **Tipo de dados** | **Descrição**                      |
|------------------|---------------|--------------------------------------|
| planId           | Cadeia de caracteres (obrigatória)        | A ID do plano do serviço SaaS que o usuário está assinando.  |
|  |  |  |

*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Ativação da assinatura de SaaS recebida para um plano específico.                   |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão faltando ou então o corpo do JSON está malformado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                   |
| 404                  | `NotFound`           | A assinatura não foi encontrada com a ID especificada                                  |
| 409                  | `Conflict`           | Outra operação está em andamento na assinatura.                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                  |
| 503                  | `ServiceUnavailable` | Serviço inativo temporariamente, tente novamente mais tarde.                          |
|  |  |  |

Para obter uma resposta 202, acompanhe o status da operação de solicitação no cabeçalho 'Localização da operação'. A autenticação é a mesma que a de outras APIs do Marketplace.

*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | O valor da ID de solicitação recebido do cliente.                                                                   |
| x-ms-correlationid | Sim          | A ID de correlação se passada pelo cliente. Caso contrário, esse valor é a ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do serviço. Esse valor é usado para quaisquer eventuais reconciliações. |
| Retry-After        | Sim          | Intervalo com o qual o cliente pode verificar o status.                                                       |
| Operation-Location | Sim          | Vincule a um recurso para obter o status da operação.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Ponto de extremidade de alteração de plano

O ponto de extremidade de alteração permite que o usuário converta seu plano inscrito atualmente em um novo plano.

**PATCH**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nome do Parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | A ID da assinatura de SaaS.                              |
| api-version         | A versão da operação a ser usada para esta solicitação. |
|  |  |

*Cabeçalhos*

| **Chave de cabeçalho**          | **Obrigatório** | **Descrição**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | Não            | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente. Recomende um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
| x-ms-correlationid      | Não            | Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse valor é para correlacionar todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| If-Match/If-None-Match | Não            | Valor de ETag de validador forte.                              |
| content-type            | Sim          | `application/json`                                        |
| authorization           | Sim          | O token de portador JWT (Token Web JSON).                    |
|  |  |  |

*Corpo*

```json
{
    "planId": ""
}
```

|  **Nome do elemento** |  **Tipo de dados**  | **Descrição**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  Cadeia de caracteres (obrigatória)         | A ID do plano do serviço SaaS que o usuário está assinando.          |
|  |  |  |

*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Ativação da assinatura de SaaS recebida para um plano específico.                   |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão faltando ou então o corpo do JSON está malformado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                   |
| 404                  | `NotFound`           | A assinatura não foi encontrada com a ID especificada                                  |
| 409                  | `Conflict`           | Outra operação está em andamento na assinatura.                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                  |
| 503                  | `ServiceUnavailable` | Serviço inativo temporariamente, tente novamente mais tarde.                          |
|  |  |  |

*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | O valor da ID de solicitação recebido do cliente.                                                                   |
| x-ms-correlationid | Sim          | A ID de correlação se passada pelo cliente. Caso contrário, esse valor é a ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do serviço. Esse valor é usado para quaisquer eventuais reconciliações. |
| Retry-After        | Sim          | Intervalo com o qual o cliente pode verificar o status.                                                       |
| Operation-Location | Sim          | Vincule a um recurso para obter o status da operação.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Excluir assinatura

A ação de exclusão no ponto de extremidade de assinatura permite que um usuário exclua uma assinatura com uma ID especificada.

*Solicitação*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nome do Parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | A ID da assinatura de SaaS.                              |
| api-version         | A versão da operação a ser usada para esta solicitação. |
|  |  |

*Cabeçalhos*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | Não            | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente. Recomende um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.                                                           |
| x-ms-correlationid | Não            | Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse valor é para correlacionar todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| autorização      | Sim          | O token de portador JWT (Token Web JSON).                    |
|  |  |  |

*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Ativação da assinatura de SaaS recebida para um plano específico.                   |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão faltando ou então o corpo do JSON está malformado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                   |
| 404                  | `NotFound`           | A assinatura não foi encontrada com a ID especificada                                  |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                  |
| 503                  | `ServiceUnavailable` | Serviço inativo temporariamente. Tente novamente mais tarde.                          |
|  |  |  |

Para obter uma resposta 202, acompanhe o status da operação de solicitação no cabeçalho 'Localização da operação'. A autenticação é a mesma que a de outras APIs do Marketplace.

*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | O valor da ID de solicitação recebido do cliente.                                                                   |
| x-ms-correlationid | Sim          | A ID de correlação se passada pelo cliente. Caso contrário, é a ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do serviço. Isso é usado para quaisquer eventuais reconciliações. |
| Retry-After        | Sim          | Intervalo com o qual o cliente pode verificar o status.                                                       |
| Operation-Location | Sim          | Vincule a um recurso para obter o status da operação.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Obter status da operação

Esse ponto de extremidade permite que o usuário acompanhe o status de uma operação assíncrona disparada (assinar/cancelar assinatura/alterar plano).

*Solicitação*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}*?api-version=2017-04-15**

| **Nome do Parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | ID exclusiva para a operação disparada.                |
| api-version         | A versão da operação a ser usada para esta solicitação. |
|  |  |

*Cabeçalhos*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Não            | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente. Recomende um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
| x-ms-correlationid | Não            | Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse valor é para correlacionar todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
| autorização      | Sim          | O token de portador JWT (Token Web JSON).                    |
|  |  |  | 

*Corpo da resposta*

```json
{
    "id": "",
    "status": "",
    "resourceLocation": "",
    "created": "",
    "lastModified": ""
}
```

| **Nome do parâmetro** | **Tipo de dados** | **Descrição**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| ID                 | Cadeia de caracteres        | A ID da operação.                                                                      |
| status             | Enum          | Status da operação, um dos seguintes: `In Progress`, `Succeeded` ou `Failed`.          |
| resourceLocation   | Cadeia de caracteres        | Link para a assinatura que foi criada ou modificada. Isso ajuda o cliente a obter o estado atualizado após a operação. Esse valor não é definido para operações `Unsubscribe`. |
| criado            | DateTime      | Hora de criação de operação em UTC.                                                           |
| lastModified       | DateTime      | Última atualização da operação em UTC.                                                      |
|  |  |  |

*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | A solicitação get foi resolvida com êxito e o corpo contém a resposta.    |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão ausentes ou um valor inválido de api-version foi especificado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                      |
| 404                  | `NotFound`           | A assinatura não foi encontrada com a ID especificada                                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                     |
| 503                  | `ServiceUnavailable` | Serviço inativo temporariamente, tente novamente mais tarde.                             |
|  |  |  |

*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | O valor da ID de solicitação recebido do cliente.                                                                   |
| x-ms-correlationid | Sim          | A ID de correlação se passada pelo cliente. Caso contrário, é a ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do serviço. Isso é usado para quaisquer eventuais reconciliações. |
| Retry-After        | Sim          | Intervalo com o qual o cliente pode verificar o status.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Obter assinatura

A ação Get no ponto de extremidade da assinatura permite que um usuário recupere uma assinatura com um identificador de recurso especificado.

*Solicitação*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nome do Parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | A ID da assinatura de SaaS.                              |
| api-version         | A versão da operação a ser usada para esta solicitação. |
|  |  |

*Cabeçalhos*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Não            | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.                                                           |
| x-ms-correlationid | Não            | Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse valor é para correlacionar todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| autorização      | Sim          | O token de portador JWT (Token Web JSON).                                                                    |
|  |  |  |

*Corpo da resposta*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Nome do parâmetro**     | **Tipo de dados** | **Descrição**                               |
|------------------------|---------------|-----------------------------------------------|
| ID                     | Cadeia de caracteres        | ID do recurso de assinatura de SaaS no Azure.    |
| offerId                | Cadeia de caracteres        | ID da oferta que o usuário assinou.         |
| planId                 | Cadeia de caracteres        | ID do plano que o usuário assinou.          |
| saasSubscriptionName   | Cadeia de caracteres        | O nome da assinatura de SaaS.                |
| saasSubscriptionStatus | Enum          | Status da operação.  Um dos seguintes:  <br/> - `Subscribed`: a assinatura está ativa.  <br/> - `Pending`: o usuário cria o recurso, mas ele não é ativado pelo ISV.   <br/> - `Unsubscribed`: o usuário cancelou a assinatura.   <br/> - `Suspended`: o usuário suspendeu a assinatura.   <br/> - `Deactivated`:  a assinatura do Azure está suspensa.  |
| criado                | DateTime      | Valor do carimbo de data/hora de criação da assinatura em UTC. |
| lastModified           | DateTime      | Valor do carimbo de data/hora de modificação da assinatura em UTC. |
|  |  |  |

*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | A solicitação get foi resolvida com êxito e o corpo contém a resposta.    |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão ausentes ou um valor inválido de api-version foi especificado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                      |
| 404                  | `NotFound`           | A assinatura não foi encontrada com a ID especificada                                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                     |
| 503                  | `ServiceUnavailable` | Serviço inativo temporariamente, tente novamente mais tarde.                             |
|  |  |  |

*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | O valor da ID de solicitação recebido do cliente.                                                                   |
| x-ms-correlationid | Sim          | A ID de correlação se passada pelo cliente. Caso contrário, é a ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do serviço. Isso é usado para quaisquer eventuais reconciliações. |
| Retry-After        | Não            | Intervalo com o qual o cliente pode verificar o status.                                                       |
| eTag               | Sim          | Vincule a um recurso para obter o status da operação.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Obter assinaturas

A ação Get no ponto de extremidade de assinaturas permite que um usuário recupere todas as assinaturas para todas as ofertas do ISV.

*Solicitação*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Nome do Parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| api-version         | A versão da operação a ser usada para esta solicitação. |
|  |  |

*Cabeçalhos*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Não            | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente. Recomende um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.             |
| x-ms-correlationid | Não            | Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse valor é para correlacionar todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| autorização      | Sim          | O token de portador JWT (Token Web JSON).                    |
|  |  |  |

*Corpo da resposta*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Nome do parâmetro**     | **Tipo de dados** | **Descrição**                               |
|------------------------|---------------|-----------------------------------------------|
| ID                     | Cadeia de caracteres        | ID do recurso de assinatura de SaaS no Azure.    |
| offerId                | Cadeia de caracteres        | ID da oferta que o usuário assinou.         |
| planId                 | Cadeia de caracteres        | ID do plano que o usuário assinou.          |
| saasSubscriptionName   | Cadeia de caracteres        | O nome da assinatura de SaaS.                |
| saasSubscriptionStatus | Enum          | Status da operação.  Um dos seguintes:  <br/> - `Subscribed`: a assinatura está ativa.  <br/> - `Pending`: o usuário cria o recurso, mas ele não é ativado pelo ISV.   <br/> - `Unsubscribed`: o usuário cancelou a assinatura.   <br/> - `Suspended`: o usuário suspendeu a assinatura.   <br/> - `Deactivated`:  a assinatura do Azure está suspensa.  |
| criado                | DateTime      | Valor do carimbo de data/hora de criação da assinatura em UTC. |
| lastModified           | DateTime      | Valor do carimbo de data/hora de modificação da assinatura em UTC. |
|  |  |  |

*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | A solicitação get foi resolvida com êxito e o corpo contém a resposta.    |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão ausentes ou um valor inválido de api-version foi especificado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                      |
| 404                  | `NotFound`           | A assinatura não foi encontrada com a ID especificada                                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                     |
| 503                  | `ServiceUnavailable` | Serviço inativo temporariamente. Tente novamente mais tarde.                             |
|  |  |  |

*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | O valor da ID de solicitação recebido do cliente.                                                                   |
| x-ms-correlationid | Sim          | A ID de correlação se passada pelo cliente. Caso contrário, é a ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do serviço. Isso é usado para quaisquer eventuais reconciliações. |
| Retry-After        | Não            | Intervalo com o qual o cliente pode verificar o status.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>Webhook de SaaS

Um webhook de SaaS é usado para notificar sobre alterações de forma proativa para o serviço de SaaS. Essa API de POSTAGEM deve ser autenticado e será chamado pelo serviço da Microsoft. O serviço de SaaS deve chamar as operações API para validar e autorizar antes de fazer a notificação de webhook. 

*Corpo*

``` json
  { 
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "sampleSaaSOffer", // Provided with "Update" action
    "publisherId": "contoso", 
    "planId": "silver",     // Provided with "Update" action
    "action": "Activate", // Activate/Delete/Suspend/Reinstate/Update
    "timeStamp": "2018-12-01T00:00:00"
  }
```

| **Nome do parâmetro**     | **Tipo de dados** | **Descrição**                               |
|------------------------|---------------|-----------------------------------------------|
| ID  | Cadeia de caracteres       | ID exclusiva para a operação disparada.                |
| activityId   | Cadeia de caracteres        | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do serviço. Isso é usado para quaisquer eventuais reconciliações.               |
| subscriptionId                     | Cadeia de caracteres        | ID do recurso de assinatura de SaaS no Azure.    |
| offerId                | Cadeia de caracteres        | ID da oferta que o usuário assinou. Fornecido somente com a ação "Atualizar".        |
| publisherId                | Cadeia de caracteres        | ID do editor da oferta de SaaS         |
| planId                 | Cadeia de caracteres        | ID do plano que o usuário assinou. Fornecido somente com a ação "Atualizar".          |
| ação                 | Cadeia de caracteres        | A ação que está disparando esta notificação. Os valores possíveis: ativar, excluir, suspender, restabelecer, atualizar          |
| timeStamp                 | Cadeia de caracteres        | Valor de carimbo de hora em UTC quando essa notificação foi disparada.          |
|  |  |  |
