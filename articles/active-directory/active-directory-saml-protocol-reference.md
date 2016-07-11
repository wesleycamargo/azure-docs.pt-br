<properties
	pageTitle="Referência de protocolo SAML do Azure AD | Microsoft Azure"
	description="Este artigo fornece uma visão geral dos perfis SAML de Logon Único e Logout Único no Azure Active Directory."
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
	ms.date="06/23/2016"
	ms.author="priyamo"/>


# Como o Azure Active Directory usa o protocolo SAML

O Azure AD (Azure Active Directory) usa o protocolo SAML 2.0 para permitir que os aplicativos forneçam uma experiência de logon único para os usuários. Os perfis SAML [Logon Único](active-directory-single-sign-on-protocol-reference.md) e [Logout Único](active-directory-single-sign-out-protocol-reference.md) do Azure AD explicam como declarações, protocolos e associações SAML são usados no serviço do provedor de identidade.

O protocolo SAML requer que o provedor de identidade (Azure AD) e o provedor de serviço (o aplicativo) troquem informações sobre si mesmos.

Quando um aplicativo é registrado no Azure AD, o desenvolvedor do aplicativo registra informações relacionadas à federação no Azure AD. Isso inclui o **URI de redirecionamento** e o **URI de metadados** do aplicativo.

O Azure AD usa o **URI de metadados** do serviço de nuvem para recuperar a chave de assinatura e o URI de logout do serviço de nuvem. Se o aplicativo não dá suporte a um URI de metadados, o desenvolvedor deve contatar o suporte da Microsoft para fornecer o URI de logout e a chave de assinatura.

O Azure Active Directory expõe pontos de extremidade de logon único e logout único comuns e específicos de locatário (independente do locatário). Essas URLs representam os locais endereçáveis (não são apenas identificadores) para que você possa ir ao ponto de extremidade ler os metadados.

 - O ponto de extremidade específico do locatário está localizado em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. O espaço reservado <NomeDeDomínioDoLocatário> representa um nome de domínio registrado ou o GUID TenantID de um locatário do Azure AD. Por exemplo, os metadados federados do locatário contoso.com estão em: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

- O ponto de extremidade independente do locatário está localizado em `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Nesse endereço de ponto de extremidade, **comum** é exibido em vez de um nome de domínio ou ID de locatário.

Para obter informações sobre os documentos de metadados federados que o Azure AD publica, confira [Metadados Federados](active-directory-federation-metadata.md).

<!---HONumber=AcomDC_0629_2016-->