---
title: "Fazer a transição do Microsoft Forefront para o Proxy de Aplicativo do Azure AD | Microsoft Docs"
description: "Cobre as noções básicas sobre os conectores do Proxy de Aplicativo do Azure AD."
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
ms.sourcegitcommit: 9945512d18d66c321c0d24ee1050497d4716fd47
ms.openlocfilehash: 699112846cc1e4e9fc6b04b1b8509152d7aecdef


---
#<a name="transition-to-azure-ad-app-proxy-from-microsoft-forefront"></a>Fazer a transição do Microsoft Forefront para o Proxy de Aplicativo do Azure AD

Este artigo descreve como fazer a transição das soluções UAG (United Access Gateway) e TMG (Threat Management Gateway) do Microsoft Forefront para estes proxies do Azure AD: WAP (Proxy de Aplicativo Web) e AADAP (Proxy de Aplicativo do Azure AD). 

> [!NOTE]
> O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).
> 
 
Muitos clientes estão nos perguntando como fazer a transição do UAG e do TMG do Forefront para os novos Proxies de Aplicativo. Para saber mais, confira este [white paper](http://download.microsoft.com/download/3/E/3/3E335D93-6DB8-4834-90A8-B86105419F05/Microsoft%20TMG%20and%20UAG%20EOL%20and%20transitioning%20to%20WAP%20and%20AADAP.docx), que descreve a transição em detalhes. 
 
## <a name="tmguag-conversion-to-azure-ad-application-proxy-table"></a>Tabela de conversão do TMG/UAG para o Proxy de Aplicativo do Azure AD
 
|**Funcionalidade do TMG/UAG**|**WAP (Proxy de Aplicativo Web)/AADAP (Proxy de Aplicativo do Azure AD)**|
|:-----|:-----|
|Publicação seletiva de HTTP para aplicativos de navegador|Disponível no WAP, no Windows Server 2012 R2 (hoje, disponível no AADAP)|
|Integração do ADFS|Disponível no WAP, no Windows Server 2012 R2 (hoje, disponível no AADAP)|
|Publicação avançada de protocolos (por exemplo, Citrix, Lync, RDG)|Disponível no WAP, no Windows Server 2012 R2 (hoje, disponível no AADAP)|
|Pré-autenticação do ActiveSync (HTTP Básico) e RDG|Estará disponível no WAP, no Windows Server vNext (será lançada no AADAP)|
|Portal|Use o Intune/System Center para WAP (Use o Painel de Acesso do AAD ou o Inicializador de Aplicativos do Office 365 disponível para AADAP)|
|Detecção de Integridade do Ponto de Extremidade|Usar o Intune/System Center|
|Túnel SSL|Usar o recurso VPN/SSL do Windows|
|Firewall de Camada 2/3|Usar os recursos do Windows Server|
|Firewall do Aplicativo Web|Nenhuma solução atual da Microsoft|
|Gateway Web Seguro (Proxy de Encaminhamento)|Nenhuma solução atual da Microsoft|


## <a name="next-steps"></a>Próximas etapas

[Blog: Web Application Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy) (Proxy de Aplicativo Web)<br>
[Blog: Application Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap) (Proxy de Aplicativo)



<!--HONumber=Feb17_HO1-->


