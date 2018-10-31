---
title: Noções básicas sobre a autenticação de API dos Gêmeos Digitais do Azure | Microsoft Docs
description: Usando os Gêmeos Digitais do Azure para se conectar e autenticar as APIs
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: dc5570b188bfdc0e1be78aa2bd5c5d92e884f377
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638011"
---
# <a name="connect-and-authenticate-to-apis"></a>Conecte e autentique as APIs

O Gêmeos Digitais do Azure usa o Active Directory do Azure (Azure AD) para autenticar usuários e proteger aplicativos. O Azure AD oferece suporte à autenticação para diversas arquiteturas modernas, que se baseiam nos protocolos padrão da indústria, OAuth 2.0 ou OpenID Connect. Além disso, o Azure AD habilita desenvolvedores que criam aplicativos de linha de negócios (LOB) com locatário único quanto desenvolvedores que buscam desenvolver aplicativos de multilocação.

Para obter uma visão geral do Azure AD, acesse a [página Conceitos básicos](https://docs.microsoft.com/azure/active-directory/fundamentals/index) para obter guias passo a passo, conceitos e inícios rápidos.

Para integrar um aplicativo ou serviço ao Azure AD, um desenvolvedor deve primeiro registrar o aplicativo no Azure AD. Para obter instruções detalhadas e capturas de tela, visualize as instruções disponíveis [aqui](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

Estes são os [cinco cenários de aplicativo principais](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) suportados pelo Azure AD:

* SPA (aplicativo de página única): um usuário precisa entrar em um aplicativo de página única protegida pelo Azure AD.
* Navegador da Web para aplicativo Web: um usuário precisa entrar em um aplicativo Web protegido pelo Azure AD.
* Aplicativo nativo para API Web: um aplicativo nativo executado em um telefone, tablet ou PC precisa autenticar um usuário para obter recursos de uma API Web protegida pelo Azure AD.
* Aplicativo Web para API Web: um aplicativo Web precisa obter recursos de uma API Web protegida pelo Azure AD.
* Servidor ou aplicativo daemon para API Web: um aplicativo daemon ou um aplicativo de servidor sem interface do usuário da Web precisa obter recursos de uma API Web protegida pelo Azure AD.

A biblioteca de autenticação do Microsoft Azure oferece várias maneiras para adquirir tokens do Active Directory. Para uma análise aprofundada na biblioteca, bem como para obter exemplos de código, dê uma olhada [aqui](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="calling-digital-twins-from-a-middle-tier-web-api"></a>Chamando os Gêmeos Digitais a partir de uma API da web de camada intermediária

Ao arquitetar os Gêmeos Digitais, os desenvolvedores de soluções comumente optam por criar um aplicativo de camada intermediária ou uma API que, em seguida, chama a API downstream dos Gêmeos Digitais. Primeiro, os usuários autenticariam o aplicativo da camada intermediária e, em seguida, um fluxo do token em nome de seria usado ao chamar downstream. Para obter instruções detalhadas sobre como coordenar o fluxo em seu nome, visite [esta página](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Você também pode visualizar os exemplos de código disponíveis [aqui](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).


## <a name="test-with-the-postman-client"></a>Testar com o cliente do Postman

Para entrar em funcionamento com as APIs dos Gêmeos Digitais, você pode usar um cliente, como o Postman, como um ambiente de API. O Postman ajuda a criar solicitações HTTP complexas rapidamente. As instruções abaixo se concentram em como obter um token do AD do Azure necessário para chamar os Gêmeos Digitais dentro da interface de usuário do Postman.


1. Navegue até https://www.getpostman.com/ para fazer o download do aplicativo
1. Siga as etapas disponíveis [aqui](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) para criar um aplicativo do Azure Active Directory (ou você pode optar por reutilizar um registro existente). 
1. Sob as permissões necessárias, adicione "Gêmeos Digitais do Azure" e selecione as permissões delegadas. Não se esqueça de clicar em Conceder permissões para finalizar.
1. Abra o manifesto do aplicativo e defina oauth2AllowImplicitFlow como true
1. Configure um URL de resposta para [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback).
1. Selecione a **guia de autorização**, clique em **OAuth 2.0**e selecione **Obter novo token de acesso**.

    |**Campo**  |**Valor** |
    |---------|---------|
    | Tipo de concessão | Implícito |
    | URL de retorno de chamada | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | URL de autenticação | [https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0](https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0)
    | Id do Cliente | Use a Id do aplicativo para o aplicativo do Azure AD que foi criado ou realocado a partir da etapa 1 |
    | Escopo | deixar em branco |
    | Estado | deixar em branco |
    | Autenticação de cliente | Enviar como cabeçalho de autenticação básica |

1. Clique em **Solicitar token**.

    >[!NOTE]
    >Se você receber a mensagem de erro "Não foi possível concluir o OAuth 2", tente o seguinte:
    > * Feche e reabra o Postman e tente novamente.
   
1. Role para baixo e clique em **Usar token**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a segurança dos Gêmeos Digitais do Azure, leia [Criar e gerenciar atribuições de função](./security-create-manage-role-assignments.md).
