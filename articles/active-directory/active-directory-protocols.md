<properties
	pageTitle="Protocolos de autenticação do Active Directory | Microsoft Azure"
	description="Este artigo fornece uma visão geral de vários protocolos de autenticação e de autorização com suporte do Azure Active Directory."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>

# Protocolos de autenticação do Active Directory

O Azure Active Directory (AD do Azure) oferece suporte a vários dos protocolos de autenticação e autorização mais usados.

Neste conjunto de artigos, examinaremos os protocolos com suporte e sua implementação no Azure AD. Teremos solicitações e respostas de exemplo e, já que estamos integrando diretamente com os protocolos, estes artigos são amplamente independentes de linguagem.

- [OAuth 2.0 no Azure AD](active-directory-protocols-oauth-code.md): saiba mais sobre o fluxo de concessão de autorização do OAuth 2.0 e sua implementação no Azure AD.
- [OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md)Ç saiba como usar o protocolo de autenticação OpenID Connect no Azure AD.
- [Referência de Protocolo SAML](active-directory-saml-protocol-reference.md): saiba como usar o protocolo SAML para dar suporte ao [Logon Único](active-directory-single-sign-on-protocol-reference.md) e ao [Logoff Único](active-directory-single-sign-out-protocol-reference.md) no Azure AD.


## Referência e solução de problemas

Este conjunto de artigos fornece informações adicionais que podem ser úteis para solucionar problemas de seus aplicativos do Azure AD, bem como para ajudar você a compreender mais profundamente o Azure AD.

- [Metadados de Federação](active-directory-federation-metadata.md): saiba como localizar e interpretar os documentos de metadados gerados pelo Azure AD.
- [Token e Tipos de Declaração com Suporte](active-directory-token-and-claims.md): saiba mais sobre as diferentes declarações nos tokens emitidas pelo Azure AD.
- [Sobreposição de Chave de Assinatura no Azure AD](active-directory-signing-key-rollover.md): saiba mais sobre a cadência de sobreposição de chave de assinatura do Azure AD e como atualizar a chave para os cenários de aplicativo mais comuns.
- [Solucionando Problemas de Protocolos de Autenticação](active-directory-error-handling.md): saiba como interpretar e resolver a maioria dos erros comuns ao usar o OAuth 2.0 e o Azure AD.
- [Práticas Recomendadas para o OAuth 2.0 no Azure AD](active-directory-oauth-best-practices.md): saiba mais sobre as práticas recomendadas ao usar o OAuth 2.0 no Azure AD e evite as armadilhas comuns.

<!---HONumber=AcomDC_0601_2016-->