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
ms.date: 01/27/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: c887adaae811972efd7fcea86eaa67a899f3f4c2
ms.openlocfilehash: 542dd7df7e0b887298522f29cb597f1df73709cb
ms.lasthandoff: 02/27/2017


---
# <a name="transition-to-azure-ad-application-proxies-from-microsoft-forefront"></a>Fazer a transição do Microsoft Forefront para proxies de aplicativo do Azure AD

Este artigo descreve como fazer a transição das soluções TMG (Threat Management Gateway) e UAG (Unified Access Gateway) do Microsoft Forefront para estes proxies de aplicativo do Azure AD (Azure Active Directory): Proxy de Aplicativo Web e Proxy de Aplicativo do Azure AD.

> [!NOTE]
> O proxy de aplicativo do Azure AD é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Basic do Azure AD. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).


Para obter informações detalhadas sobre a transição do Forefront TMG e UAG para os novos proxies de aplicativo, [baixe um white paper relacionado da Microsoft](http://download.microsoft.com/download/3/E/3/3E335D93-6DB8-4834-90A8-B86105419F05/Microsoft%20TMG%20and%20UAG%20EOL%20and%20transitioning%20to%20WAP%20and%20AADAP.docx).

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

