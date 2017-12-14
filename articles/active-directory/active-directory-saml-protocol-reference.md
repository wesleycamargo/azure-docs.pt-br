---
title: "Referência do Protocolo SAML do Azure AD | Microsoft Docs"
description: "Este artigo fornece uma visão geral dos perfis SAML de Logon Único e Logout Único no Azure Active Directory."
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mtillman
editor: 
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: priyamo
ms.openlocfilehash: 1fcc854362428d4ae32e815ccbf611f4562399ad
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Como o Azure Active Directory usa o protocolo SAML
O Azure AD (Azure Active Directory) usa o protocolo SAML 2.0 para permitir que os aplicativos forneçam uma experiência de logon único para os usuários. O perfis SAML do [Logon Único](active-directory-single-sign-on-protocol-reference.md) e do [Logout Único](active-directory-single-sign-out-protocol-reference.md) do Azure AD explicam como as declarações SAML, protocolos e vinculações são usados no serviço do provedor de identidade.

O protocolo SAML requer que o provedor de identidade (Azure AD) e o provedor de serviço (o aplicativo) troquem informações sobre si mesmos.

Quando um aplicativo é registrado no Azure AD, o desenvolvedor do aplicativo registra informações relacionadas à federação no Azure AD. Isso inclui o **URI de Redirecionamento** do aplicativo.

O Azure Active Directory expõe pontos de extremidade de logon único e logout único comuns e específicos de locatário (independente do locatário). Essas URLs representam os locais endereçáveis (não são apenas identificadores) para que você possa ir ao ponto de extremidade ler os metadados.

* O ponto de extremidade específico do locatário está localizado em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.  O espaço reservado <TenantDomainName> representa um nome de domínio registrado ou o GUID TenantID de um locatário do Azure AD. Por exemplo, os metadados de federação do locatário contoso.com estão em: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* O ponto de extremidade independente do locatário está localizado em `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Nesse endereço do ponto de extremidade, **comum** é exibido, em vez de um nome de domínio do locatário ou ID.

Para obter informações sobre os documentos de metadados federados que o Azure AD publica, confira [Metadados Federados](active-directory-federation-metadata.md).
