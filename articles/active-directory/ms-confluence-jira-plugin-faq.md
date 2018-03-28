---
title: Perguntas frequentes sobre o Plug-in de logon único do Microsoft Azure Active Directory | Microsoft Docs
description: Saiba como configurar o logon único entre o Azure Active Directory e logon único do Microsoft Azure Active Directory para JIRA.
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
ms.openlocfilehash: 571fbd5078f66375f6e81cba2a790121366f9d60
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-faq"></a>Perguntas frequentes sobre o plugin de logon único do Microsoft Azure Active Directory 

## <a name="1-whats-the-microsoft-sso-add-on"></a>1. O que é o complemento Microsoft SSO?

Este complemento oferece recurso de logon único para JIRA da Atlassian (incluindo JIRA Core, JIRA Software, JIRA Service Desk) e software local do Confluence. O complemento funciona com o Microsoft Azure Active Directory como IdP.

## <a name="2-add-on-works-with-which-atlassian-products"></a>2. O complemento funciona com quais produtos Atlassian?

No momento, o complemento funciona apenas com as versões locais do JIRA e Confluence.

## <a name="3-does-this-add-on-work-on-cloud-version"></a>3. Este complemento funciona na versão da nuvem?

Nº Somente as versões locais do JIRA e do Confluence têm suporte.

## <a name="4-which-versions-of-jira-and-confluence-are-supported"></a>4. Quais versões locais do JIRA e do Confluence têm suporte?

Abaixo está a lista de versões que têm suporte:

* JIRA Core e Software: 6.0 a 7.2.2 
* JIRA Service Desk: 3.0 a 3.2 
* Confluence: 5.0 a 5.10

## <a name="5-is-this-add-on-free-or-paid"></a>5. Esse complemento é gratuito ou pago?

Trata-se de um complemento gratuito que pode ser instalado pelo marketplace da Atlassian.

## <a name="6-do-i-need-to-restart-jiraconfluence-once-i-deploy-the-add-on"></a>6. É necessário reiniciar o JIRA/Confluence depois de implantar o complemento?

Não é necessária reiniciar após a implantação de complemento. Você pode começar a usar o complemento imediatamente após a implantação.

## <a name="7-how-do-i-get-support-for-the-add-on"></a>7. Como fazer para obter suporte para o complemento?

Entre em contato em: <email>. Responderemos dentro de <> horas. Também é possível criar um ticket de suporte com a Microsoft por meio do canal do Portal do Azure. Você também pode entrar em contato conosco em: <Number> entre <> e <> nos dias úteis.

## <a name="8-would-this-add-on-work-on-mac-or-ubuntu-installation-of-jira-and-confluence"></a>8. Este complemento funcionaria na instalação do JIRA e Confluence em Mac ou Ubuntu?

Nós testamos este complemento apenas em JIRA e Confluence de instalações em servidores do Windows de 64 bits.

## <a name="9-does-this-add-on-work-with-other-idps-than-azure-ad"></a>9. Este complemento funciona com outros IdPs que além do Microsoft Azure Active Directory?

Nº O complemento funciona apenas com o Microsoft Azure Active Directory.

## <a name="10-what-version-of-saml-does-the-add-on-work-with"></a>10. Com qual versão do SAML o complemento funciona?

O complemento funciona com SAML 2.0.

## <a name="11-does-the-add-on-do-use-provisioning-as-well"></a>11. O complemento também usa provisionamento?

Nº No momento, o complemento fornece apenas SSO baseado em SAML 2.0. O usuário deve ser provisionado no aplicativo antes do logon SSO.

## <a name="12-are-cluster-versions-of-jira-and-confluence-supported-by-add-on"></a>12. As versões de cluster do JIRA e Confluence são compatíveis com o complemento?

Nº O complemento funciona apenas com as versões locais do JIRA e Confluence.

## <a name="13-would-this-plugin-work-with-http-version-of-jira-and-confluence"></a>13. Este plug-in funcionaria com a versão HTTP do JIRA e Confluence?

Nº O complemento funciona apenas com instalações habilitadas para HTTPS.

## <a name="14-do-i-need-to-buy-license-of-the-add-on"></a>14. É necessário adquirir uma licença do complemento?

Trata-se de um complemento gratuito.