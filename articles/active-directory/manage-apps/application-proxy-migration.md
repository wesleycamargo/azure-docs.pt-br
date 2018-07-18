---
title: Atualizar para o Proxy de Aplicativo do Azure AD | Microsoft Docs
description: Escolha qual solução de proxy será a melhor se você estiver atualizando do Microsoft Forefront ou do Unified Access Gateway.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: cf1c39d0270dcb7de3ae8ff487b21874b23db74a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
---
# <a name="compare-remote-access-solutions"></a>Comparar as soluções de acesso remoto

O Azure Active Directory Application Proxy é uma das duas soluções de acesso remoto que a Microsoft oferece. O outro é o Proxy de Aplicativo Web, a versão local. Essas duas soluções substituem os produtos anteriores oferecidos pela Microsoft: Microsoft Forefront Threat Management Gateway (TMG) e Unified Access Gateway (UAG). Use este artigo para ver uma comparação entre estas quatro soluções. Para aqueles que ainda utilizam as soluções TMG ou UAG preteridas, use este artigo para ajudar a planejar a migração para um Proxy de Aplicativo. 


## <a name="feature-comparison"></a>Comparação de recursos

Use essa tabela para entender a comparação entre o Threat Management Gateway (TMG), o Unified Access Gateway (UAG), o Proxy de Aplicativo da Web (WAP) e o Proxy de Aplicativo do Azure AD (AP).

| Recurso | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Autenticação de certificado | sim | sim | - | - |
| Publicar seletivamente os aplicativos de navegador | sim | sim | sim | sim |
| Pré-autenticação e logon único | sim | sim | sim | sim | 
| Firewall de Camada 2/3 | sim | sim | - | - |
| Recursos de proxy de encaminhamento | sim | - | - | - |
| Funcionalidades de VPN | sim | sim | - | - |
| Suporte avançado a protocolo | - | sim | Sim, se estiver em execução sobre HTTP | Sim, se a execução for feita via HTTP ou por meio do Gateway de Área de Trabalho Remota |
| Atua como um servidor proxy do ADFS | - | sim | sim | - |
| Um portal para acesso de aplicativo | - | sim | - | sim |
| Conversão de link de corpo de resposta | sim | sim | - | sim | 
| Autenticação com cabeçalhos | - | sim | - | Sim, com PingAccess | 
| Segurança em escala de nuvem | - | - | - | sim | 
| Acesso condicional | - | sim | - | sim |
| Nenhum componente na zona desmilitarizada (DMZ) | - | - | - | sim |
| Nenhuma conexão de entrada | - | - | - | sim |

Para a maioria dos cenários, é recomendável usar o Aplicativo do Azure AD como a solução moderna. P Proxy de Aplicativo Web é preferencial apenas em cenários que exigem um servidor proxy para o AD FS e não pode usar domínios personalizados no Azure Active Directory. 

O Proxy de Aplicativo do Azure AD oferece benefícios exclusivos quando comparados a produtos semelhantes, incluindo:

- Extensão do AD do Azure para recursos locais
   - Proteção e segurança em escala de nuvem
   - Os recursos como acesso condicional e a Autenticação Multifator são fáceis de habilitar
- Nenhum componente na zona desmilitarizada
- Nenhuma conexão de entrada necessária
- Um painel de acesso em que os usuários poderão procurar todos os aplicativos, incluindo o O365, os aplicativos SaaS integrados do Azure AD e seus aplicativos Web locais. 


## <a name="next-steps"></a>Próximas etapas

- [Usar o Aplicativo do Azure AD para fornecer acesso remoto seguro a aplicativos locais](application-proxy.md)
- [Transição do Forefront TMG e UAG para o Proxy de Aplicativo](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).
