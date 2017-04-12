---
title: "Protocolos de Autenticação do Azure Active Directory | Microsoft Docs"
description: "Uma visão geral dos protocolos de autenticação suportados pelo Azure Active Directory (AD)"
documentationcenter: dev-center-name
author: bryanla
services: active-directory
manager: mbaldwin
editor: 
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/07/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: dd673ba36689c83021a27d593139f0676b0c5e79
ms.lasthandoff: 04/12/2017


---
# <a name="azure-active-directory-authentication-protocols"></a>Protocolos de autenticação do Azure Active Directory
O Azure Active Directory (AD do Azure) oferece suporte a vários dos protocolos de autenticação e autorização mais usados. Os tópicos nesta seção descrevem os protocolos suportados e sua implementação no Azure AD. Os tópicos incluem uma revisão dos tipos de declarações com suporte, uma introdução ao uso de metadados de federação, documentação detalhada de referência dos protocolos OAuth 2.0. e SAML 2.0 e uma seção de solução de problemas.

## <a name="authentication-protocols-articles-and-reference"></a>Artigos e referência dos protocolos de autenticação
* [Informações importantes sobre substituição de chave de assinatura no Azure AD](active-directory-signing-key-rollover.md) – Saiba mais sobre a cadência de substituição de chave de assinatura no Azure AD, as alterações que você pode fazer para atualizar a chave automaticamente e discussões sobre como atualizar os cenários mais comuns do aplicativo.
* [Token e tipos de declaração suportados](active-directory-token-and-claims.md) - Saiba mais sobre as declarações nos tokens que o Azure AD emite.
* [Metadados de federação](active-directory-federation-metadata.md) - Saiba como localizar e interpretar os documentos de metadados que o Azure AD gera.
* [OAuth 2.0 no Azure AD](active-directory-protocols-oauth-code.md) - Saiba mais sobre a implementação do OAuth 2.0 no Azure AD.
* [OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) - Saiba como usar o OAuth 2.0, um protocolo de autorização para autenticação.
* [Referência de protocolo SAML](active-directory-saml-protocol-reference.md) - Saiba mais sobre os perfis de logon único e logout único SAML do Azure AD.

## <a name="see-also"></a>Consulte também
[Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md)

[Usando o Azure AD para autenticação](../../app-service-web/web-sites-authentication-authorization.md)

[Exemplos de código do Active Directory](active-directory-code-samples.md)


