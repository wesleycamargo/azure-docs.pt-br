---
title: Atualizar para o Proxy de Aplicativo do Azure AD | Microsoft Docs
description: "Escolha qual solução de proxy será a melhor se você estiver atualizando do Microsoft Forefront ou do Unified Access Gateway."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: e9d5caa4d11012744ce9f26648166371f3aa17ba
ms.contentlocale: pt-br
ms.lasthandoff: 05/12/2017

---
# <a name="upgrade-to-azure-ad-proxies-from-microsoft-forefront-or-unified-access-gateway"></a>Atualize para proxies do Azure AD do Microsoft Forefront ou do Unified Access Gateway

Este artigo descreve como fazer a transição das soluções Microsoft Forefront TMG (Threat Management Gateway) e UAG (Unified Access Gateway) para o Proxy de Aplicativo do Azure AD.

Para obter informações detalhadas sobre a transição do Forefront TMG e UAG para o Proxy de Aplicativo, você pode [ler um white paper relacionado da Microsoft](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).

## <a name="functionality-details-for-the-conversion"></a>Detalhes de funcionalidade da conversão

|**Funcionalidade do TMG/UAG**|**Solução moderna**|
|:-----|:-----|
|Publicação seletiva de HTTP para aplicativos de navegador|Proxy de Aplicativo do Azure AD|
|Integração do AD FS (Serviços de Federação do Active Directory)|Proxy de Aplicativo do Azure AD|
|Publicação avançada de protocolos (por exemplo, Citrix, Lync, RDG)|Proxy de Aplicativo do Azure AD|
|Portal|Painel de Acesso do Azure AD ou o Inicializador de Aplicativo do Office 365 para o Proxy de Aplicativo do Azure AD|
|Detecção de integridade do ponto de extremidade|Intune ou System Center|
|Túnel SSL|SSL ou VPN do Windows|
|Firewall de Camada 2/3|Windows Server|
|Pré-autenticação do ActiveSync (HTTP Básico) e RDG|Nenhuma solução atual da Microsoft|
|Firewall do aplicativo Web|Nenhuma solução atual da Microsoft.|
|Gateway da Web seguro (proxy de encaminhamento)|Nenhuma solução atual da Microsoft.|


## <a name="next-steps"></a>Próximas etapas

[Blog: Web Application Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy) (Proxy de Aplicativo Web)<br>
[Blog: Proxy de Aplicativo do Azure AD](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)

