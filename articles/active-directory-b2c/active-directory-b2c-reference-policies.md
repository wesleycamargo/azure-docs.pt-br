---
title: Fluxos dos usuários no Azure Active Directory B2C | Microsoft Docs
description: Um tópico sobre a estrutura de política extensível do Azure Active Directory B2C e como criar vários tipos de fluxo de usuário.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d4a93b04b8ad86a6a6d36a5bdaf3209b49e7a9dc
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877075"
---
# <a name="azure-active-directory-b2c-user-flows"></a>Azure Active Directory B2C: Fluxos dos usuários


A estrutura de política extensível do Azure AD (Azure Active Directory) B2C é o principal ponto forte do serviço. As políticas descrevem totalmente as experiências de identidade do consumidor, como inscrição, entrada ou edição de perfil. Para ajudá-lo a configurar as tarefas de identidade mais comuns, o portal do Azure AD B2C inclui políticas predefinidas e configuráveis chamadas **fluxos dos usuários**. Por exemplo, um fluxo de usuário de inscrição permite controlar comportamentos definindo as seguintes configurações:

* Tipos de conta (contas sociais, como o Facebook ou contas locais, como um endereço de email) que os consumidores podem usar para se inscrever no aplicativo
* Atributos (como nome, código postal e tamanho do calçado, por exemplo) que serão coletados junto ao consumidor durante a inscrição
* Uso da Autenticação Multifator do Microsoft Azure
* A aparência e funcionalidade de todas as páginas de inscrição
* Informações (que manifestam-se como declarações em um token) que o aplicativo recebe quando a execução de fluxo de usuário termina

É possível criar vários fluxos dos usuários de diferentes tipos no locatário e usá-los nos aplicativos, conforme necessário. Fluxos dos usuários podem ser reutilizados nos aplicativos. Essa flexibilidade permite aos desenvolvedores definir e modificar experiências de identidade do consumidor com pouca ou nenhuma alteração no código.

Fluxos de usuários estão disponíveis para uso por meio de uma interface simples do desenvolvedor. O aplicativo dispara um fluxo de usuário, usando uma solicitação de autenticação HTTP padrão (passando um parâmetro de fluxo de usuário na solicitação) e recebe um token personalizado como resposta. Por exemplo, a única diferença entre solicitações que invocam um fluxo de usuário de inscrição e solicitações que invocam um fluxo de usuário de entrada é o nome do fluxo de usuário usado no parâmetro de cadeia de caracteres de consulta "p":

```

https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow

```

```

https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow

```

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Criar um fluxo de usuário de inscrição ou entrada

Esse fluxo de usuário lida com ambas as experiências de inscrição e entrada do consumidor com uma única configuração. Os consumidores são conduzidos para o caminho certo (inscrição ou credenciais), dependendo do contexto. Ele também descreve o conteúdo de tokens que o aplicativo receberá mediante inscrições ou entradas bem-sucedidas.  Um exemplo de código para o fluxo de usuário de **inscrição ou entrada** está [disponível aqui](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  É recomendável usar esse fluxo de usuário em um fluxo de usuário de **inscrição** ou um fluxo de usuário de **entrada**.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-user-flow"></a>Criar um fluxo de usuário de inscrição

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-user-flow"></a>Criar um fluxo de usuário de entrada

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-user-flow"></a>Criar um fluxo de usuário de edição de perfil

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-user-flow"></a>Criar um fluxo de usuário de redefinição de senha

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-user-flows"></a>Visualizar fluxos dos usuários

Na medida em que lançamos novos recursos, alguns deles talvez não estejam disponíveis nos fluxos dos usuários ou políticas existentes.  Planejamos substituir as versões mais antigas pelas mais recentes, da mesma forma quando esses fluxos dos usuários entram em GA.  Os fluxos dos usuários ou políticas existentes não serão alterados e, para aproveitar esses novos recursos, será necessário criar novos fluxos dos usuários.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-do-i-link-a-sign-up-or-sign-in-user-flow-with-a-password-reset-user-flow"></a>Como fazer para vincular um fluxo de usuário de inscrição ou entrada a um fluxo de usuário de redefinição de senha?
Ao criar um fluxo de usuário de **inscrição ou entrada** (com contas locais), você verá um link **Esqueceu a senha?** na primeira página da experiência. Clicar nesse link não dispara automaticamente um fluxo de usuário de redefinição de senha. 

Em vez disso, o código de erro **`AADB2C90118`** é retornado para seu aplicativo. O aplicativo precisa lidar com esse código de erro invocando um fluxo de usuário de redefinição de senha específico. Para obter mais informações, consulte um exemplo [que demonstra a abordagem de vinculação dos fluxos dos usuários](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-user-flow-or-a-sign-up-user-flow-and-a-sign-in-user-flow"></a>Devo usar um fluxo de usuário de inscrição ou entrada ou um fluxo de usuário de inscrição e um fluxo de usuário de entrada?
É recomendável que você use um fluxo de usuário de **inscrição ou entrada** em um fluxo de usuário de **inscrição** e um fluxo de usuário de **entrada**.  

O fluxo de usuário de **inscrição ou entrada** tem mais recursos que o fluxo de usuário de **entrada**. Ela também permite que você use a personalização da interface do usuário da página e tem melhor suporte para localização. 

O fluxo de usuário de **entrada** é recomendado se não for necessário localizar os fluxos dos usuários, apenas precisar de recursos menores de personalização para a identidade visual e quiser redefinir a senha incorporada.

## <a name="next-steps"></a>Próximas etapas
* [Configuração de token, de sessão e de logon único](active-directory-b2c-token-session-sso.md)
* [Desabilitar a verificação de email durante a inscrição do consumidor](active-directory-b2c-reference-disable-ev.md)

