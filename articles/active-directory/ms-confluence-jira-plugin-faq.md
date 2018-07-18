---
title: Perguntas frequentes sobre o plug-in de SSO do Microsoft Azure Active Directory | Microsoft Docs
description: Obtenha respostas para perguntas frequentes sobre como configurar o logon único entre o Microsoft Azure Active Directory e Jira/Confluence.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: b642d6de9fd9265d7f7ee11b827a36d20e5bcce7
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="faq-for-the-azure-active-directory-sso-plug-in"></a>Perguntas frequentes sobre o plug-in de SSO do Microsoft Azure Active Directory 

## <a name="what-does-the-plug-in-do"></a>O que o plug-in faz?

O plug-in oferece recurso de logon único (SSO) para Atlassian Jira (incluindo Jira Core, Jira Software, Jira Service Desk) e software local do Confluence. O plug-in funciona com o Microsoft Azure Active Directory (Azure AD) como provedor de identidade (IdP).

## <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Com quais produtos Atlassian o plug-in funciona?

O plug-in funciona apenas com as versões locais do Jira e Confluence.

## <a name="does-the-plug-in-work-on-cloud-versions"></a>O plug-in funciona em versões da nuvem?

Nº O plug-in é compatível apenas com as versões locais do Jira e Confluence.

## <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Com quais versões locais do Jira e do Confluence o plug-in é compatível?

O plug-in é compatível com estas versões:

* Jira Core e Software: 6.0 a 7.2.2 
* Jira Service Desk: 3.0 a 3.2 
* Confluence: 5.0 a 5.10

## <a name="is-the-plug-in-free-or-paid"></a>O plug-in é gratuito ou pago?

Trata-se de um complemento gratuito. Você pode instalá-lo pelo Atlassian Marketplace.

## <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>É necessário reiniciar o Jira ou Confluence depois de implantar o plug-in?

Uma reinicialização não é necessária. Você pode começar a usar o plug-in imediatamente.

## <a name="how-do-i-get-support-for-the-plug-in"></a>Como fazer para obter suporte para o plug-in?

Alcance a [Equipe de integração de SSO do Microsoft Azure Active Directory](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). A equipe responde em 24-48 horas de negócios. 

Também é possível criar um ticket de suporte com a Microsoft por meio do canal do Portal do Azure.

## <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Este plug-in funcionaria em uma instalação do Jira e Confluence em Mac ou Ubuntu?

Nós testamos este plug-in apenas em Jira e Confluence de instalações em servidores do Windows de 64 bits.

## <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>O plug-in funciona com IdPs diferente do Microsoft Azure Active Directory?

Nº Ele funciona apenas com o Microsoft Azure Active Directory.

## <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Com qual versão do SAML o plug-in funciona?

Funciona com SAML 2.0.

## <a name="does-the-plug-in-do-user-provisioning"></a>O plug-in realiza provisionamento de usuário?

Nº O plug-in fornece apenas SSO baseado em SAML 2.0. O usuário deve ser provisionado no aplicativo antes de entrar com SSO.

## <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>O plug-in é compartível com versões de cluster do Jira e do Confluence o plug-in é compatível?

Nº O plug-in funciona apenas com as versões locais do Jira e Confluence.

## <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>O plug-in funciona com versões HTTP do Jira e do Confluence?

Nº O plug-in funciona apenas com instalações habilitadas para HTTPS.