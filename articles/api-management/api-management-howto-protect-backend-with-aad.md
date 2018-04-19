---
title: Proteger uma API usando OAuth 2.0 com o Azure Active Directory e o Gerenciamento de API | Microsoft Docs
description: Saiba como proteger um back-end de API da web com o Active Directory do Azure e Gerenciamento de API.
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
ms.openlocfilehash: f5662a4082487137dfd642cc3264a90f8ab19054
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Proteger uma API usando OAuth 2.0 com o Azure Active Directory e o Gerenciamento de API

Este guia mostra como configurar sua instância do Gerenciamento de API do Azure para proteger uma API usando o protocolo OAuth 2.0 com o Microsoft Azure Active Directory (Azure AD). 

## <a name="prerequisites"></a>pré-requisitos
Para seguir as etapas deste artigo, você deve ter:
* Uma instância de Gerenciamento de API
* Uma API que está sendo publicada que usa a instância de Gerenciamento de API
* Um locatário do Azure AD

## <a name="overview"></a>Visão geral

Aqui está uma visão geral das etapas:

1. Registrar um aplicativo (aplicativo de back-end) no Microsoft Azure Active Directory para representar a API.
2. Registrar outro aplicativo (aplicativo cliente) no Microsoft Azure Active Directory para representar um aplicativo cliente que precisa chamar a API.
3. No Microsoft Azure Active Directory, conceda permissões para permitir que o aplicativo cliente chame o aplicativo de back-end.
4. Configurar o Console do Desenvolvedor para usar a autorização de usuário OAuth 2.0.
5. Adicionar política **validate-jwt** para validar o token OAuth para cada solicitação de entrada.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registrar um aplicativo no Azure AD para representar a API

Para proteger uma API com o Azure AD, a primeira etapa é registrar um aplicativo no Azure AD que represente a API. 

1. Navegue até o locatário do Microsoft Azure Active Directory e navegue até **Registros de aplicativo**.

2. Selecione **Novo registro de aplicativo**. 

3. Forneça um nome do aplicativo. (Neste exemplo, o nome é `backend-app`.)  

4. Escolha **Aplicativo Web/API** como o **Tipo de aplicativo**. 

5. Para **URL de logon**, você pode usar `https://localhost` como um espaço reservado.

6. Selecione **Criar**.

Quando o aplicativo for criado, anote a **ID do aplicativo** para uso em uma etapa posterior. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrar outro aplicativo no Azure AD para representar um aplicativo cliente

Todos os aplicativos cliente que chamam a API também precisam ser registrados como aplicativos no Microsoft Azure Active Directory. Neste exemplo, o aplicativo cliente de exemplo é o Console do Desenvolvedor no Portal do Desenvolvedor do Gerenciamento de API. É assim que se registra outro aplicativo no Microsoft Azure Active Directory para representar o Console do Desenvolvedor.

1. Selecione **Novo registro de aplicativo**. 

2. Forneça um nome do aplicativo. (Neste exemplo, o nome é `client-app`.)

3. Escolha **Aplicativo Web/API** como o **Tipo de aplicativo**.  

4. Para **URL de logon**, você pode usar `https://localhost` como um espaço reservado ou usar a URL de entrada da sua instância do Gerenciamento de API. (Neste exemplo, a URL é `https://contoso5.portal.azure-api.net/signin`.)

5. Selecione **Criar**.

Quando o aplicativo for criado, anote a **ID do aplicativo** para uso em uma etapa posterior. 

Agora, crie um segredo do cliente para esse aplicativo, para uso em uma etapa posterior.

1. Selecione **Configurações** novamente e vá para **Chaves**.

2. Em **Senhas**, forneça uma **Descrição da chave**. Escolha quando a chave deve expirar e selecione **Salvar**.

Anote o valor da chave. 

## <a name="grant-permissions-in-azure-ad"></a>Conceder permissões no Microsoft Azure Active Directory

Agora que você registrou dois aplicativos para representar a API e o Console do Desenvolvedor, é preciso conceder permissões para permitir que o aplicativo cliente chame o aplicativo de back-end.  

1. Navegue até **Registros de aplicativo**. 

2. Selecione `client-app` e vá para **Configurações**.

3. Selecione **Permissões necessárias** > **Adicionar**.

4. Selecione **Selecionar uma API** e procure `backend-app`.

5. Em **Permissões Delegadas**, selecione `Access backend-app`. 

6. Selecione **Selecionar** e, em seguida, selecione **Concluído**. 

> [!NOTE]
> Se o **Microsoft Azure Active Directory** não estiver listado em permissões para outros aplicativos, selecione **Adicionar** para adicioná-lo da lista.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Habilitar a autorização de usuário OAuth 2.0 no Console do Desenvolvedor

Neste ponto, você criou seus aplicativos no Microsoft Azure Active Directory e concedeu as permissões adequadas para permitir que o aplicativo cliente chame o aplicativo de back-end. 

Neste exemplo, o Console do Desenvolvedor é o aplicativo cliente. As etapas a seguir descrevem como habilitar a autorização do usuário OAuth 2.0 no Console do Desenvolvedor. 

1. Navegue até instância de Gerenciamento de API.

2. Selecione **OAuth 2.0** > **Adicionar**.

3. Forneça um **Nome de exibição** e uma **Descrição**.

4. Para a **URL de Página de registro do cliente**, insira um valor de espaço reservado como `http://localhost`. A **URL de página de registro do cliente** aponta para a página que os usuários podem usar para criar e configurar suas próprias contas para provedores OAuth 2.0 que dão suporte a isso. Neste exemplo, os usuários não criam nem configuram suas próprias contas, então um espaço reservado é usado no lugar.

5. Para **Tipos de concessão de autorização**, selecione **Código de autorização**.

6. Especifique a **URL de ponto de extremidade de autorização** e a **URL de ponto de extremidade de Token**. Recupere esses valores da página **Pontos de extremidade** no locatário do Microsoft Azure Active Directory. Navegue até a página **Registros de aplicativo** novamente e selecione **Pontos de extremidade**.

7. Copie o **Ponto de Extremidade da Autorização OAuth 2.0** e cole-o na caixa de texto **URL de ponto de extremidade de autorização**.

8. Copie o **Ponto de Extremidade do Token OAuth 2.0** e cole-o na caixa de texto **URL de ponto de extremidade de token**. Além de colar no ponto de extremidade do token, adicione um parâmetro de corpo denominado **recurso**. Para o valor desse parâmetro, use a **ID de aplicativo** do aplicativo de back-end.

9. Em seguida, especifique as credenciais do cliente. Essas são as credenciais para o aplicativo cliente.

10. Para **ID do cliente**, use a **ID de aplicativo** do aplicativo cliente.

11. Para **Segredo do cliente**, use a chave que você criou para o aplicativo cliente anterior. 

12. Imediatamente após o segredo do cliente, está **redirect_url** para o tipo de concessão de código de autorização. Anote essa URL.

13. Selecione **Criar**.

14. Volte para a página **Configurações** do aplicativo cliente.

15. Selecione **URLs de resposta** e cole **redirect_url** na primeira linha. Neste exemplo, você substituiu `https://localhost` pela URL na primeira linha.  

Agora que você configurou um Servidor de Autorização OAuth 2.0, o Console do Desenvolvedor pode obter tokens de acesso do Microsoft Azure Active Directory. 

A próxima etapa é habilitar a autorização do usuário do OAuth 2.0 para a sua API. Isso permite que o Console do Desenvolvedor saiba o que precisa para obter um token de acesso em nome do usuário, antes de fazer chamadas à API.

1. Navegue até instância gerenciamento de API e vá para **APIs**.

2. Selecione a API que você deseja proteger. Neste exemplo, você usará a `Echo API`.

3. Vá para **Configurações**.

4. Em **Segurança**, escolha **OAuth 2.0** e selecione o servidor OAuth 2.0 configurado anteriormente. 

5. Selecione **Salvar**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Chamar com êxito a API do portal do desenvolvedor

Agora que a autorização de usuário OAuth 2.0 está habilitada no `Echo API`, o Console do Desenvolvedor obtém um token de acesso em nome do usuário antes de chamar a API.

1. Navegue até qualquer operação em `Echo API` no portal do desenvolvedor e selecione **Experimentar**. Isso o levará até o Console do Desenvolvedor.

2. Observe um novo item na seção **Autorização**, correspondente ao servidor de autorização que você acabou de adicionar.

3. Selecione **Código de autorização** na lista suspensa de autorização e você será solicitado a entrar no locatário do Microsoft Azure Active Directory. Se você já estiver conectado com a conta, talvez não seja solicitado.

4. Depois que você entrar com êxito, um cabeçalho `Authorization` será adicionado à solicitação com um token de acesso do Microsoft Azure Active Directory. O exemplo abaixo é um exemplo de token (em codificação Base64):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Selecione **Enviar** e você poderá chamar a API com êxito.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurar uma diretiva de validação de JWT para pré-autorizar solicitações

Neste ponto, quando um usuário tenta fazer uma chamada a partir do Console do Desenvolvedor, o usuário é solicitado a entrar. O Console do Desenvolvedor obtém um token de acesso em nome do usuário.

Mas e se alguém chamar a API sem um token ou com um token inválido? Por exemplo, você ainda pode chamar a API mesmo se excluir o cabeçalho `Authorization`. O motivo é que o Gerenciamento de API não valida o token de acesso nesse momento. Ele simplesmente passa o cabeçalho `Authorization` para a API de back-end.

Você pode usar a política [Validar JWT](api-management-access-restriction-policies.md#ValidateJWT) para pré-autorizar solicitações no Gerenciamento de API, validando tokens de acesso de cada solicitação recebida. Se uma solicitação não tiver um token válido, o Gerenciamento de API a bloqueará. Por exemplo, você pode adicionar a política a seguir à seção de política `<inbound>` do `Echo API`. Ele verifica a declaração de público em um token de acesso e retorna uma mensagem de erro se o token não for válido. Para obter informações sobre como configurar as políticas, consulte [Definir ou editar políticas](set-edit-policies.md).

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

## <a name="build-an-application-to-call-the-api"></a>Criar um aplicativo para chamar a API

Neste guia, você usou o Console do Desenvolvedor no Gerenciamento de API como o aplicativo cliente de exemplo para chamar o `Echo API` protegido pelo OAuth 2.0. Para saber mais sobre como criar um aplicativo e implementar o OAuth 2.0, consulte [Exemplos de código do Microsoft Azure Active Directory](../active-directory/develop/active-directory-code-samples.md).

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [Azure Active Directory e o OAuth2.0](../active-directory/develop/active-directory-authentication-scenarios.md).
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre o Gerenciamento de API.
* Para outras maneiras de proteger seu serviço de back-end, confira [Autenticação de certificado mútuo](api-management-howto-mutual-certificates.md).

* [Criar uma instância do serviço de Gerenciamento de API](get-started-create-service-instance.md).

* [Gerenciar sua primeira API](import-and-publish.md).
