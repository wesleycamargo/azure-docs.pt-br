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
ms.topic: conceptual
ms.date: 07/27/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: c4ecb812156eae7402065cff4dc4bae3aef1554b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365168"
---
# <a name="compare-remote-access-solutions"></a>Comparar as soluções de acesso remoto

O Azure Active Directory Application Proxy é uma das duas soluções de acesso remoto que a Microsoft oferece. O outro é o Proxy de Aplicativo Web, a versão local. Essas duas soluções substituem os produtos anteriores oferecidos pela Microsoft: Microsoft Forefront Threat Management Gateway (TMG) e Unified Access Gateway (UAG). Use este artigo para ver uma comparação entre estas quatro soluções. Para aqueles que ainda utilizam as soluções TMG ou UAG preteridas, use este artigo para ajudar a planejar a migração para um Proxy de Aplicativo. 


## <a name="feature-comparison"></a>Comparação de recursos

Use essa tabela para entender a comparação entre o Threat Management Gateway (TMG), o Unified Access Gateway (UAG), o Proxy de Aplicativo da Web (WAP) e o Proxy de Aplicativo do Azure AD (AP).

| Recurso | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Autenticação de certificado | SIM | SIM | - | - |
| Publicar seletivamente os aplicativos de navegador | SIM | sim | sim | SIM |
| Pré-autenticação e logon único | SIM | sim | sim | SIM | 
| Firewall de Camada 2/3 | SIM | SIM | - | - |
| Recursos de proxy de encaminhamento | SIM | - | - | - |
| Funcionalidades de VPN | SIM | SIM | - | - |
| Suporte avançado a protocolo | - | SIM | Sim, se estiver em execução sobre HTTP | Sim, se a execução for feita via HTTP ou por meio do Gateway de Área de Trabalho Remota |
| Atua como um servidor proxy do ADFS | - | SIM | SIM | - |
| Um portal para acesso de aplicativo | - | SIM | - | SIM |
| Conversão de link de corpo de resposta | SIM | sim | - | SIM | 
| Autenticação com cabeçalhos | - | SIM | - | Sim, com PingAccess | 
| Segurança em escala de nuvem | - | - | - | SIM | 
| Acesso condicional | - | SIM | - | SIM |
| Nenhum componente na zona desmilitarizada (DMZ) | - | - | - | SIM |
| Nenhuma conexão de entrada | - | - | - | SIM |

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
