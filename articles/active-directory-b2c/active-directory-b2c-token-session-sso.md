---
title: Configuração de Token, sessão e logon único no Azure Active Directory B2C | Microsoft Docs
description: Configuração de token, sessão e logon único no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c93bc018aea92a63adac4889d9496356543c1e52
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842723"
---
# <a name="token-session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Configuração de Token, sessão e logon único no Azure Active Directory B2C

Esse recurso oferece um controle refinado, [com base em cada fluxo de usuário](active-directory-b2c-reference-policies.md), de:

- Vida útil dos tokens de segurança emitidos pelo Azure Active Directory (Azure AD) B2C.
- Vida útil de sessões dos aplicativos Web gerenciados pelo Azure AD B2C.
- Formatos de declarações importantes nos tokens de segurança emitidos pelo Azure AD B2C.
- Comportamento de logon único (SSO) em vários aplicativos e políticas no locatário do Azure AD B2C.

Você pode usar esse recurso em qualquer tipo de política, mas este exemplo mostra como usar o recurso com um fluxo de usuário de inscrição ou entrada. Para fluxos de usuário, você pode usar esse recurso no seu diretório do Azure AD B2C conforme a seguir:

1. Clique em **Fluxos de usuário**.
2. Abra um fluxo de usuário clicando nele. Por exemplo, clique em **B2C_1_SiUpIn**.
3. Clique em **Propriedades**.
4. Em **configurações de compatibilidade de Token**, faça as alterações desejadas. Saiba mais sobre as propriedades disponíveis nas seções seguintes.
5. Clique em **Salvar** na parte superior do menu.

## <a name="token-lifetimes-configuration"></a>Configuração da vida útil de tokens

O Azure AD B2C dá suporte ao [protocolo de autorização OAuth 2.0](active-directory-b2c-reference-protocols.md) para habilitar o acesso seguro a recursos protegidos. Para implementar esse suporte, o Azure AD B2C emite vários [tokens de segurança](active-directory-b2c-reference-tokens.md). 

As seguintes propriedades são usadas para gerenciar tempos de vida de tokens de segurança emitidos pelo Azure AD B2C:

- **Duração dos tokens de acesso e ID (minutos)**: o tempo de vida do token portador do OAuth 2.0 usado para obter acesso a um recurso protegido.
    - Padrão = 60 minutos.
    - Mínimo (inclusive) = 5 minutos.
    - Máximo (inclusive) = 1440 minutos.
- **Atualizar tempo de vida do token (dias)** - O período máximo antes do qual um token de atualização pode ser usado para adquirir um novo token de acesso ou ID (e, opcionalmente, um novo token de atualização, se seu aplicativo tiver recebido o `offline_access` escopo).
    - Padrão = 14 dias.
    - Mínimo (inclusive) = 1 dia.
    - Máximo (inclusive) = 90 dias.
- **Atualizar vida útil da janela deslizante do token (dias)** - Após esse período de tempo, o usuário é forçado a autenticar novamente, independentemente do período de validade do token de atualização mais recente adquirido pelo aplicativo. Ele só poderá ser fornecido se a opção estiver definida como **Bounded**. Ele precisa ser maior ou igual ao valor **Vida útil do token de atualização (dias)** . Se a opção estiver definida como **Unbounded**, você não poderá fornecer um valor específico.
    - Padrão = 90 dias.
    - Mínimo (inclusive) = 1 dia.
    - Máximo (inclusive) = 365 dias.

Os casos de uso a seguir são ativados usando estas propriedades:

- Permitir que um usuário permaneça conectado a um aplicativo móvel indefinidamente, contanto que o usuário esteja continuamente ativo no aplicativo. Você pode definir **Atualizar duração da janela deslizante do token (dias)** para **Não consolidado** em seu fluxo de usuário.
- Atenda aos requisitos de conformidade e segurança de seu setor definindo a vida de tokens de acesso adequadamente.

Essas configurações não estão disponíveis para os fluxos de usuário de redefinição de senha. 

## <a name="token-compatibility-settings"></a>Configurações de compatibilidade de token

As seguintes propriedades permitem que os clientes optem conforme necessário:

- **Emissor (iss) reivindicação** - essa propriedade identifica o locatário do Azure AD B2C que emitiu o token.
    - `https://<domain>/{B2C tenant GUID}/v2.0/` - este é o valor padrão.
    - `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`: esse valor inclui IDs do inquilino B2C e do fluxo de usuário usado na solicitação de token. Se seu aplicativo ou biblioteca precisar do Azure AD B2C para ser compatível com as [especificações do OpenID Connect Discovery 1.0](https://openid.net/specs/openid-connect-discovery-1_0.html), use esse valor.
- **Subject (sub) declaração** - Esta propriedade identifica a entidade para a qual o token afirma informações.
    - **ObjectID** -esta propriedade é o valor padrão. Ele ocupa a ID de objeto do usuário no diretório para a declaração `sub` no token.
    - **Não suportado** - Esta propriedade só é fornecida para compatibilidade com versões anteriores, e recomendamos que você mude para **ObjectID** assim que for possível.
- **Reivindicação representando o ID da política**: essa propriedade identifica o tipo de reivindicação no qual o ID da política usado na solicitação de token é preenchido.
    - **TFP** -esta propriedade é o valor padrão.
    - **Acr** -esta propriedade é fornecida somente para compatibilidade com versões anteriores.

## <a name="session-behavior"></a>Comportamento da sessão

O Azure AD B2C dá suporte ao [protocolo de autenticação do OpenID Connect](active-directory-b2c-reference-oidc.md) para habilitar o logon seguro em aplicativos Web. Você pode usar as seguintes propriedades para gerenciar sessões de aplicativos da Web:

- **Duração da sessão do aplicativo Web (minutos)** - A vida útil do cookie de sessão do Azure AD B2C armazenado no navegador do usuário após a autenticação bem-sucedida.
    - Padrão = 1440 minutos.
    - Mínimo (inclusive) = 15 minutos.
    - Máximo (inclusive) = 1440 minutos.
- **Tempo limite da sessão do aplicativo da Web**: se essa opção for definida como **Absoluta**, o usuário será forçado a autenticar novamente após o período especificado por **duração da sessão do aplicativo Web (minutos)** decorre. Se essa opção estiver definida como **Rolling** (a configuração padrão), o usuário permanecerá conectado desde que esteja ativo de forma contínua em seu aplicativo Web.

Os casos de uso a seguir são ativados usando estas propriedades:

- Atenda aos requisitos de conformidade e segurança de seu setor definindo adequadamente a vida útil da sessão do aplicativo Web.
- Forçar autenticação após um período de tempo definido durante a interação do usuário com uma parte de alto nível de segurança do seu aplicativo web. 

Essas configurações não estão disponíveis para os fluxos de usuário de redefinição de senha.

## <a name="single-sign-on-sso-configuration"></a>Configuração de SSO (Logon Único)

Se você tiver vários aplicativos e fluxos de usuário em seu locatário B2C, poderá gerenciar interações do usuário entre eles usando a propriedade **Configuração de logon único**. Você pode definir a propriedade com uma das seguintes configurações:

- **Locatário** -essa configuração é o padrão. O uso dessa configuração permite a vários aplicativos e fluxos de usuário em seu locatário B2C compartilhar a mesma sessão de usuário. Por exemplo, depois que um usuário faz login em um aplicativo, o usuário também pode fazer login em outro, a Contoso Pharmacy, ao acessá-lo.
- **Aplicação** - Esta configuração permite que você mantenha uma sessão de usuário exclusivamente para um aplicativo, independente de outros aplicativos. Por exemplo, se você quiser que o usuário entre na Contoso Pharmacy (com as mesmas credenciais), mesmo que o usuário já esteja conectado ao Contoso Shopping, outro aplicativo no mesmo locatário B2C. 
- **Política** - Essa configuração permite que você mantenha uma sessão de usuário exclusivamente para um fluxo de usuário, independentemente dos aplicativos que a usam. Por exemplo, se o usuário já tiver feito login e concluído uma etapa de autenticação de vários fatores (MFA), o usuário poderá ter acesso a partes de maior segurança de vários aplicativos, desde que a sessão vinculada ao fluxo de usuário não expire.
- **Desabilitado** – essa configuração orces o usuário a percorrer o fluxo do usuário inteira em cada execução da política. Por exemplo, isso permite que vários usuários se inscrevam em seu aplicativo (em um cenário de área de trabalho compartilhada), mesmo quando um usuário permanecer conectado o tempo todo.

Essas configurações não estão disponíveis para os fluxos de usuário de redefinição de senha. 

