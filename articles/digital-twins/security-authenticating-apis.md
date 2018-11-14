---
title: Entenda a autenticação da API dos Gêmeos Digitais do Azure | Microsoft Docs
description: Use os Gêmeos Digitais do Azure para se conectar e autenticar nas APIs
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: f85ab05e785ea559962490b43e75b196d1602159
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016209"
---
# <a name="connect-and-authenticate-to-apis"></a>Conecte e autentique as APIs

O Gêmeos Digitais do Azure usa o Active Directory do Azure (Azure AD) para autenticar usuários e proteger aplicativos. O Microsoft Azure Active Directory oferece suporte à autenticação para diversas arquiteturas modernas. Todos eles são baseados nos protocolos padrão da indústria OAuth 2.0 ou OpenID Connect. Além disso, os desenvolvedores podem usar o Microsoft Azure Active Directory para criar aplicativos de locatário único e de linha de negócios (LOB). Os desenvolvedores também podem usar o  Microsoft Azure Active Directory para desenvolver aplicativos multilocatários.

Para obter uma visão geral do Microsoft Azure Active Directory, visite a [página de fundamentos](https://docs.microsoft.com/azure/active-directory/fundamentals/index) para obter guias passo a passo, conceitos e iniciações rápidas.

Para integrar um aplicativo ou serviço ao Azure AD, um desenvolvedor deve primeiro registrar o aplicativo no Azure AD. Para instruções detalhadas e screenshots, veja [este início rápido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

[Cinco cenários de aplicativos principais](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) são suportados pelo Microsoft Azure Active Directory:

* Aplicativo de uma única página (SPA): um usuário precisa entrar em um aplicativo de uma única página protegido pelo Microsoft Azure Active Directory.
* Navegador da Web para aplicativo da Web: um usuário precisa entrar em um aplicativo da Web protegido pelo Azure Active Directory.
* Aplicativo nativo para API da web: um aplicativo nativo que é executado em um telefone, tablet ou PC precisa autenticar um usuário para obter recursos de uma API da web protegida pelo Microsoft Azure Active Directory.
* Aplicativo Web para API Web: um aplicativo Web precisa obter recursos de uma API Web protegida pelo Azure AD.
* Daemon ou aplicativo de servidor para API da web: um aplicativo daemon ou um aplicativo de servidor sem interface da web precisa obter recursos de uma API da web protegida pela Microsoft Azure AD.

A biblioteca de autenticação do Microsoft Azure oferece várias maneiras para adquirir tokens do Active Directory. Para detalhes sobre a biblioteca e amostras de código, veja [este artigo](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Chame os Gêmeos Digitais do Azure de uma API da web de camada intermediária

Quando os desenvolvedores arquitetam os Gêmeos Digitais do Azure, eles geralmente criam um aplicativo ou API de camada intermediária. O aplicativo ou API chama o downstream dos Gêmeos Digitais do Azure. Os usuários primeiro se autenticam no aplicativo de camada intermediária e, em seguida, um fluxo de token em nome é usado para chamar downstream. Para obter instruções sobre como orquestrar o fluxo do serviço, consulte [esta página](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Você também pode exibir exemplos de código na [nesta página](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).


## <a name="test-with-the-postman-client"></a>Testar com o cliente do Postman

Para começar a usar as APIs dos Gêmeos Digitais do Azure, você pode usar um cliente como o Postman como um ambiente de API. O Postman ajuda a criar solicitações HTTP complexas rapidamente. As etapas a seguir mostram como obter um token do Azure Active Directory necessário para chamar Digital Twins na interface do usuário do Postman.


1. Vá para https://www.getpostman.com/ para fazer o download do aplicativo.
1. Siga as etapas em [este início rápido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) para criar um aplicativo do Azure Active Directory. Ou você pode reutilizar um registro existente. 
1. Em **Permissões necessárias**, digite "Gêmeos Digitais do Azure" e selecione **Permissões delegadas**. Em seguida, selecione **conceder permissões**.
1. Abra o manifesto do aplicativo e defina **oauth2AllowImplicitFlow** como verdade.
1. Configurar uma URL de resposta para [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback).
1. Selecione a guia **Autorização**, selecione **OAuth 2.0** e, em seguida, selecione **Obter novo token de acesso**.

    |**Campo**  |**Valor** |
    |---------|---------|
    | Tipo de concessão | Implícito |
    | URL de retorno de chamada | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | URL de autenticação | https://login.microsoftonline.com/<Your Azure AD Tenant e.g. Contoso>.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0 |
    | ID do cliente | Use o ID do aplicativo para o aplicativo do Azure Active Directory que foi criado ou redefinido da Etapa 2. |
    | Escopo | Deixe em branco. |
    | Estado | Deixe em branco. |
    | Autenticação de cliente | Enviar como cabeçalho de autenticação de Sites do plano Básico. |

1. Selecione **Solicitação de Token**.

    >[!NOTE]
    >Se você receber a mensagem de erro "OAuth 2 não pôde ser concluído", tente o seguinte:
    > * Feche o Postman e reabra-o e tente novamente.
   
1. Role para baixo e selecione **uso Token**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a segurança dos Gêmeos Digitais do Azure, leia [Criar e gerenciar atribuições de função](./security-create-manage-role-assignments.md).
