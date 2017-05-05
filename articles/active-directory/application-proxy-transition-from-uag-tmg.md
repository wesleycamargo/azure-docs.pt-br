---
title: "Fazer a transição do Microsoft Forefront para proxies de aplicativo do Azure AD | Microsoft Docs"
description: "Aborda os conceitos básicos sobre como mudar das soluções Microsoft Forefront TMG e UAG para proxies de aplicativo do Azure Active Directory."
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
ms.date: 04/17/2017
ms.author: kgremban
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 6e95e9abac988ae54a401927a92bdb397dd63eed
ms.lasthandoff: 04/21/2017

---
# <a name="transition-to-azure-ad-application-proxies-from-microsoft-forefront"></a>Fazer a transição do Microsoft Forefront para proxies de aplicativo do Azure AD

Este artigo descreve como fazer a transição das soluções Microsoft Forefront TMG (Threat Management Gateway) e UAG (Unified Access Gateway) para o Proxy de Aplicativo do Azure AD.

Para obter informações detalhadas sobre a transição do Forefront TMG e UAG para o Proxy de Aplicativo, você pode [ler um white paper relacionado da Microsoft](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).

## <a name="functionality-details-for-the-conversion"></a>Detalhes de funcionalidade da conversão

|**Funcionalidade do TMG/UAG**|**Proxy de Aplicativo Web/Proxy de Aplicativo do Azure AD**|
|:-----|:-----|
|Publicação seletiva de HTTP para aplicativos de navegador|Disponível no Proxy de Aplicativo Web no Windows Server 2012 R2. Disponível hoje no Proxy de Aplicativo do Azure AD.|
|Integração do AD FS (Serviços de Federação do Active Directory)|Disponível no Proxy de Aplicativo Web no Windows Server 2012 R2. Disponível hoje no Proxy de Aplicativo do Azure AD.|
|Publicação avançada de protocolos (por exemplo, Citrix, Lync, RDG)|Disponível no Proxy de Aplicativo Web no Windows Server 2012 R2. Disponível hoje no Proxy de Aplicativo do Azure AD.|
|Pré-autenticação do ActiveSync (HTTP Básico) e RDG|Atualmente, não está disponível no Proxy de Aplicativo Web ou no Proxy de Aplicativo do Azure AD.|
|Portal|Use o Intune ou System Center para o Proxy de Aplicativo Web. Use o Painel de Acesso do Azure AD ou o Lançador de Aplicativo do Office 365 para o Proxy de Aplicativo do Azure AD.|
|Detecção de integridade do ponto de extremidade|Use o Intune ou System Center.|
|Túnel SSL|Use o recurso SSL ou VPN do Windows.|
|Firewall de Camada 2/3|Use os recursos do Windows Server.|
|Firewall do aplicativo Web|Nenhuma solução atual da Microsoft.|
|Gateway da Web seguro (proxy de encaminhamento)|Nenhuma solução atual da Microsoft.|


## <a name="next-steps"></a>Próximas etapas

[Blog: Web Application Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy) (Proxy de Aplicativo Web)<br>
[Blog: Proxy de Aplicativo do Azure AD](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)

