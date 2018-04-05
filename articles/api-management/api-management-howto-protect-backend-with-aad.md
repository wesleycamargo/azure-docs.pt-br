---
title: Proteger uma API usando OAuth 2.0 com o Azure Active Directory e o Gerenciamento de API | Microsoft Docs
description: Saiba como proteger um back-end de API da Web com o Active Directory do Azure e Gerenciamento de API
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2018
ms.author: apimpm
ms.openlocfilehash: 3caa3d2b8640c83f1001aeac3b0a5e9ada143183
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-protect-an-api-using-oauth-20-with-azure-active-directory-and-api-management"></a>Como proteger uma API usando OAuth 2.0 com o Azure Active Directory e o Gerenciamento de API

Este guia mostra como configurar sua instância do APIM (Gerenciamento de API) para proteger uma API usando o protocolo OAuth 2.0 com o AAD (Azure Active Directory). 

## <a name="prerequisite"></a>Pré-requisito
Para seguir as etapas deste artigo, você deve ter:
* Uma instância do APIM
* Uma API que está sendo publicada usando a instância do APIM
* Um locatário do Azure AD

## <a name="overview"></a>Visão geral

Este guia mostra como proteger uma API com OAuth 2.0 no APIM. Neste artigo, é usado o Azure AD como o Servidor de Autorização (Servidor OAuth). Abaixo está uma visão geral das etapas:

1. Registrar um aplicativo (aplicativo de back-end) no Azure AD para representar a API
2. Registrar outro aplicativo (aplicativo cliente) no Azure AD para representar um aplicativo cliente que precisa chamar a API
3. No Azure AD, conceda permissões para permitir que o aplicativo cliente chame o aplicativo de back-end
4. Configurar o Console do Desenvolvedor para usar a autorização de usuário OAuth 2.0
5. Adicionar política validate-jwt para validar o token OAuth para cada solicitação de entrada

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registrar um aplicativo no Azure AD para representar a API

Para proteger uma API com o Azure AD, a primeira etapa é registrar um aplicativo no Azure AD que represente a API. 

Navegue até o locatário do Azure AD e navegue até **Registros de aplicativo**.

Selecione **Novo registro de aplicativo**. 

Forneça um nome do aplicativo. Neste exemplo, `backend-app` é usado.  

Escolha **Aplicativo Web/API** como o **Tipo de aplicativo**. 

Para **URL de logon**, você pode usar `https://localhost` como um espaço reservado.

Clique em **Criar**.

Depois que o aplicativo for criado, anote a **ID do aplicativo** para uso em uma etapa posterior. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrar outro aplicativo no Azure AD para representar um aplicativo cliente

Todos os aplicativos cliente que precisam chamar a API também precisam ser registrados como aplicativos no Azure AD. Neste guia, usaremos o Console do Desenvolvedor no Portal do Desenvolvedor do APIM como o aplicativo cliente de exemplo. 

É preciso registrar outro aplicativo no Azure AD para representar o Console do Desenvolvedor.

Clique em **Novo registro de aplicativo** novamente. 

Forneça um nome do aplicativo e escolha **Aplicativo Web/API** como o **Tipo de aplicativo**. Neste exemplo, `client-app` é usado.  

Para **URL de logon**, você pode usar `https://localhost` como um espaço reservado ou usar a URL de entrada da sua instância do APIM. Neste exemplo, `https://contoso5.portal.azure-api.net/signin` é usado.

Clique em **Criar**.

Depois que o aplicativo for criado, anote a **ID do aplicativo** para uso em uma etapa posterior. 

Agora, precisamos criar um segredo do cliente para esse aplicativo, para uso em uma etapa posterior.

Clique em **Configurações** novamente e vá para **Chaves**.

Em **Senhas**, forneça uma **Descrição da chave**, escolha quando a chave deve expirar e clique em **Salvar**.

Anote o valor da chave. 

## <a name="grant-permissions-in-aad"></a>Conceder permissões no AAD

Agora que registramos dois aplicativos para representar a API (ou seja, o aplicativo de back-end) e o Console do Desenvolvedor (ou seja, o aplicativo cliente), é preciso conceder permissões para permitir que o aplicativo cliente chame o aplicativo de back-end.  

Navegue até **Registros de aplicativo** novamente. 

Clique em `client-app` e vá para **Configurações**.

Clique em **Permissões necessárias** e **Adicionar**.

Clique em **Selecionar uma API** e procure `backend-app`.

Verifique `Access backend-app` em **Permissões Delegadas**. 

Clique em **Selecionar** e **Concluído**. 

> [!NOTE]
> Se o **Microsoft** **Azure Active Directory** não estiver listado em permissões para outros aplicativos, clique em **Adicionar** e adicione-o da lista.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Habilitar a Autorização de Usuário OAuth 2.0 no Console do Desenvolvedor

Neste ponto, criamos nossos aplicativos no Azure AD e recebemos as permissões adequadas para permitir que o aplicativo cliente chame o aplicativo de back-end. 

Neste guia, usaremos o Console do Desenvolvedor como o aplicativo cliente. As etapas a seguir descrevem como habilitar a Autorização do Usuário OAuth 2.0 no Console do Desenvolvedor 

Navegue até sua instância do APIM.

Clique em **OAuth 2.0** e **Adicionar**.

Forneça um **Nome de exibição** e uma **Descrição**.

Para a URL de Página de registro do cliente,** insira um valor de espaço reservado como `http://localhost`.  A **URL de página de registro do cliente** aponta para a página que os usuários podem usar para criar e configurar suas próprias contas para provedores OAuth 2.0 que dão suporte a gerenciamento de contas por usuários. Neste exemplo, os usuários não criam e configuram suas próprias contas para que um espaço reservado seja usado.

Marque o **Código de autorização** como **Tipos de concessão de autorização**.

Em seguida, especifique a **URL de ponto de extremidade de autorização** e a **URL de ponto de extremidade de Token**.

Esses valores podem ser recuperados da página **pontos de extremidade** no locatário do Azure AD. Para acessar os pontos de extremidade, navegue até a página **Registros de aplicativo** novamente e clique em **Pontos de extremidade**.

Copie o **Ponto de Extremidade da Autorização OAuth 2.0** e cole-o na caixa de texto **URL de ponto de extremidade de autorização**.

Copie o **Ponto de Extremidade do Token OAuth 2.0** e cole-o na caixa de texto **URL de ponto de extremidade de token**.

Além de colar o ponto de extremidade de token, inclua um parâmetro de corpo adicional denominado **recurso** e, para o valor, use **ID do Aplicativo** para o aplicativo de back-end.

Em seguida, especifique as credenciais do cliente. Essas são as credenciais para o aplicativo cliente.

Para **Id do cliente**, use a **ID de aplicativo** do aplicativo cliente.

Para **Segredo do cliente**, use a chave que você criou para o aplicativo cliente anterior. 

Imediatamente após o segredo do cliente, está **redirect_url** para o tipo de concessão de código de autorização.

Anote essa URL.

Clique em **Criar**.

Navegue de volta para a página **Configurações** do aplicativo cliente.

Clique em **URLs de resposta** e cole **redirect_url** na primeira linha. Neste exemplo, substituímos `https://localhost` pela URL na primeira linha.  

Agora que configuramos um Servidor de Autorização OAuth 2.0, o Console do Desenvolvedor deverá obter tokens de acesso do Azure AD. 

A próxima etapa é habilitar a autorização do usuário OAuth 2.0 para a API, para que o Console do Desenvolvedor saiba que precisa obter um token de acesso em nome do usuário antes de fazer chamadas para a API.

Navegue até sua instância do APIM e vá para **APIs**.

Clique na API que você deseja proteger. Neste exemplo, usaremos `Echo API`.

Vá para **Configurações**.

Em **Segurança**, escolha **OAuth 2.0** e selecione o servidor OAuth 2.0 configurado anteriormente. 

Clique em **Save**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Chamar com êxito a API do portal do desenvolvedor

Agora que a autorização de usuário OAuth 2.0 está habilitada no `Echo API`, o Console do Desenvolvedor obterá um token de acesso em nome do usuário antes de chamar a API.

Navegue até qualquer operação do `Echo API` no Portal do Desenvolvedor e clique em **Experimentar**, o que o levará para o Console do Desenvolvedor.

Observe um novo item na seção **Autorização** correspondente ao servidor de autorização que você acabou de adicionar.

Selecione **Código de autorização** na lista suspensa de autorização e você será solicitado a entrar no locatário do Azure AD. Se você já estiver conectado com a conta, talvez não seja solicitado.

Depois que você entrar com êxito, um cabeçalho `Authorization` será adicionado à solicitação com um token de acesso do Azure AD. 

Um token de exemplo é semelhante ao exemplo abaixo. Ele é codificado em Base64.

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
```

Clique em **Enviar** e você poderá chamar a API com êxito.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurar uma diretiva de validação de JWT para pré-autorizar solicitações

Neste ponto, quando um usuário tentar fazer uma chamada por meio do Console do Desenvolvedor, o usuário será solicitado a entrar, e o Console do Desenvolvedor obterá um Token de Acesso em nome do usuário. Tudo está funcionando conforme o esperado. No entanto, e se alguém chamar a API sem um token ou com um token inválido? Por exemplo, você pode tentar excluir o cabeçalho `Authorization` e verá que ainda consegue chamar a API. O motivo é que o APIM não valida o Token de Acesso nesse momento. Ele passa o cabeçalho `Auhtorization` para a API de back-end.

Podemos usar a política [Validar JWT](api-management-access-restriction-policies.md#ValidateJWT) para pré-autorizar solicitações no APIM, validando tokens de acesso de cada solicitação recebida. Se uma solicitação não tiver um token válido, será bloqueada pelo Gerenciamento de API e não será passada para o back-end. É possível adicionar a política abaixo a `Echo API`. 

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

## <a name="next-steps"></a>Próximas etapas
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre o Gerenciamento de API.
* Para outras maneiras de proteger seu serviço de back-end, confira [Autenticação de certificado mútuo](api-management-howto-mutual-certificates.md).

[Create an API Management service instance]: get-started-create-service-instance.md
[Manage your first API]: import-and-publish.md
