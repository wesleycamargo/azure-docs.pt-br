---
title: "Gerenciamento de sessão de SSO usando políticas personalizadas – Azure AD B2C | Microsoft Docs"
description: "Saiba como gerenciar sessões de SSO usando políticas personalizadas no Azure AD B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 809f6000-2e52-43e4-995d-089d85747e1f
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: parja
ms.openlocfilehash: ff767993eaf0305168176d0ad6e15c068b8c85eb
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2017
---
# <a name="azure-ad-b2c-single-sign-on-sso-session-management"></a>Azure AD B2C: gerenciamento de sessão de SSO (Logon Único)

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure AD B2C permite que o administrador controle como o Azure AD B2C interage com um usuário depois que ele já tiver sido autenticado. Isso é feito por meio do gerenciamento de sessão de SSO. Por exemplo, o administrador pode controlar se a seleção de provedores de identidade é exibida ou se os detalhes de conta local precisam ser inseridos novamente. Este artigo descreve como definir as configurações de SSO para o Azure AD B2C.

## <a name="overview"></a>Visão geral

O gerenciamento de sessão de SSO tem duas partes. A primeira lida com interações do usuário diretamente com o Azure AD B2C e a outra lida com interações do usuário com parceiros externos, como Facebook. O Azure AD B2C não substitui ou ignora as sessões de SSO que podem ser mantidas por parceiros externos. Em vez disso, a rota por meio do Azure AD B2C para chegar ao parceiro externo é “lembrada”, evitando a necessidade de solicitar novamente que o usuário selecione seu provedor de identidade social ou empresarial. A decisão de SSO final permanece com o parceiro externo.

## <a name="how-does-it-work"></a>Como ele funciona?

O gerenciamento de sessão de SSO usa a mesma semântica que qualquer outro perfil técnico em políticas personalizadas. Quando uma etapa de orquestração é executada, o perfil técnico associado à etapa é consultado quanto a uma referência `UseTechnicalProfileForSessionManagement`. Se existir uma, o provedor de sessão de SSO referenciado será verificado para ver se o usuário é um participante da sessão. Se sim, o provedor de SSO de sessão será usado para popular novamente a sessão. Da mesma forma, quando a execução de uma etapa de orquestração for concluída, o provedor será usado para armazenar informações na sessão se um provedor de sessão de SSO tiver sido especificado.

O Azure AD B2C definiu vários provedores de sessão de SSO que podem ser usados:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

As classes de gerenciamento de SSO são especificadas usando o elemento `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` de um perfil de técnico.

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Como o nome indica, este provedor não faz nada. Esse provedor pode ser usado para suprimir o comportamento de SSO para um determinado perfil técnico.

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Esse provedor pode ser usado para armazenar as declarações em uma sessão. Normalmente, esse provedor é referenciado em um perfil técnico usado para gerenciar contas locais. 

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

Para adicionar declarações à sessão, use o elemento `<PersistedClaims>` do perfil técnico. Quando o provedor é usado para popular novamente a sessão, as declarações persistentes são adicionadas ao conjunto de declarações. `<OutputClaims>` é usado para recuperar as declarações da sessão.

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Esse provedor é usado para suprimir a tela "escolher o provedor de identidade". Normalmente, ele é referenciado em um perfil técnico configurado para um provedor de identidade externo, como o Facebook. 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Esse provedor é usado para gerenciar as sessões de SAML do Azure AD B2C entre aplicativos, bem como provedores de identidade SAML externos.

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

Há dois itens de metadados no perfil técnico:

| Item | Valor Padrão | Valores possíveis | Descrição
| --- | --- | --- | --- |
| IncludeSessionIndex | verdadeiro | true/false | Indica ao provedor que o índice de sessão deve ser armazenado. |
| RegisterServiceProviders | verdadeiro | true/false | Indica que o provedor deve registrar todos os provedores de serviço SAML que emitiram uma declaração. |

Ao usar o provedor para armazenar uma sessão de provedor de identidade SAML, os itens acima devem ser falsos. Ao usar o provedor para armazenar a sessão de SAML do B2C, os itens acima devem ser verdadeiros ou omitidos, já que os padrões são verdadeiros.

>[!NOTE]
> O logoff da sessão de SAML requer que `SessionIndex` e `NameID` sejam concluídos.

## <a name="next-steps"></a>Próximas etapas

Adoramos comentários e sugestões! Se você tiver dificuldade com este tópico, poste no Stack Overflow usando a marca ['azure-ad-b2c'](https://stackoverflow.com/questions/tagged/azure-ad-b2c). Para solicitações de recursos, vote no nosso [fórum de comentários](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

