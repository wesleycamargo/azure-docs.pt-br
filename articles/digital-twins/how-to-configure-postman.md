---
title: Como configurar o Postman para os Gêmeos Digitais do Azure | Microsoft Docs
description: Como configurar o Postman para os Gêmeos Digitais do Azure
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adgera
ms.openlocfilehash: b22bf34a06966f917cdcdd07c28ead2d042061c1
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52163989"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Como configurar o Postman para os Gêmeos Digitais do Azure

Este artigo descreve como configurar um aplicativo do Azure Active Directory (Azure AD) para usar o fluxo de concessão implícita do OAuth 2.0. Em seguida, discute como configurar o cliente Postman REST para fazer solicitações HTTP de influência de token para suas APIs de gerenciamento.

## <a name="postman-summary"></a>Resumo do Postman

Começar em nos Gêmeos Digitais do Azure usando uma ferramenta de cliente REST como [Postman](https://www.getpostman.com/) para preparar seu ambiente de teste local. O cliente Postman ajuda a criar rapidamente as solicitações HTTP complexas. Faça o download da versão da área de trabalho do cliente Postman acessando [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) é uma ferramenta de teste REST que localiza as funcionalidades de solicitação HTTP principal na área de trabalho útil e GUI baseado em plugin. Por meio do cliente Postman, os desenvolvedores de soluções podem especificar o tipo de solicitação HTTP (POST, GET, UPDATE, PATCH e DELETE), ponto de extremidade de API para a chamada e o uso de SSL. O Postman também dá suporte para adicionar cabeçalhos de solicitação HTTP, parâmetros, dados de formulário e corpos.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Configurar o Azure Active Directory para usar o fluxo de concessão implícita do OAuth 2.0

Configurar o aplicativo Azure Active Directory para usar o fluxo de concessão implícita do OAuth 2.0.

1. Siga as etapas neste [início rápido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) para criar um aplicativo do Azure Active Directory do tipo Nativo. Ou você pode reutilizar um registro de aplicativo Nativo.

1. Em **Permissões necessárias**, digite `Azure Digital Twins` e selecione **Permissões Delegadas**. Em seguida, selecione **conceder permissões**.

    ![API de registros do aplicativo do Azure Active Directory](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

1. Clique em **Manifesto** para abrir o manifesto do aplicativo para seu aplicativo. Definir *oauth2AllowImplicitFlow* para `true`.

      ![Fluxo implícito do Azure AD][1]

1. Configurar uma **Reply URL** para [`https://www.getpostman.com/oauth2/callback`](https://www.getpostman.com/oauth2/callback).

      ![URL de resposta do Azure Active Directory][2]

1. Copie e mantenha a **ID do aplicativo** do seu aplicativo do Azure AD. É usado abaixo.

## <a name="configure-the-postman-client"></a>Configure o cliente do Postman

Em seguida, instale e configure o Postman para obter um token do Azure Active Directory. Em seguida, faça uma solicitação HTTP autenticada para os Gêmeos Digitais do Azure usando o token adquirido:

1. Vá para [www.getpostman.com]([https://www.getpostman.com/) para baixar o aplicativo.
1. Certifique-se de que sua **URL de autorização** está correto. Deve pegar o formato:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | NOME  | Substitua por | Exemplo |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | O nome do seu locatário ou organização | `microsoft` |

1. Selecione a guia **Autorização**, selecione **OAuth 2.0** e, em seguida, selecione **Obter novo token de acesso**.

    | Campo  | Valor |
    |---------|---------|
    | Tipo de concessão | `Implicit` |
    | URL de retorno de chamada | [`https://www.getpostman.com/oauth2/callback`](https://www.getpostman.com/oauth2/callback) |
    | URL de autenticação | Use a **URL de Autorização** da etapa 2 acima |
    | ID do cliente | Use a **ID do aplicativo** para o aplicativo do Azure Active Directory que foi criado ou redefinido na seção anterior |
    | Escopo | Deixar em branco |
    | Estado | Deixar em branco |
    | Autenticação de cliente | `Send as Basic Auth header` |

1. O cliente agora deve se parecer com:

   ![Exemplo de cliente Postman][3]

1. Selecione **Solicitação de Token**.

    >[!NOTE]
    >Se você receber a mensagem de erro "OAuth 2 não pôde ser concluído", tente o seguinte:
    > * Feche o Postman e reabra-o e tente novamente.
  
1. Role para baixo e selecione **uso Token**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre autenticar as APIs de Gerenciamento, leia [Autenticar com APIs](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-configure-postman/implicit-flow.png
[2]: media/how-to-configure-postman/reply-url.png
[3]: media/how-to-configure-postman/postman-oauth-token.png
