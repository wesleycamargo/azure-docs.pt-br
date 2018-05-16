---
title: Comparação da colaboração B2B e B2C no Azure Active Directory | Microsoft Docs
description: Qual é a diferença entre colaboração B2B e a B2C do Azure Active Directory?
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 03/15/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 0e8781fcf2710b00ba352bff29a370cbb73e0648
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Comparação da colaboração B2B e B2C no Azure Active Directory

As duas colaborações B2B e B2C do Azure Active Directory (AD do Azure) permitem que você trabalhe com usuários externos no AD do Azure. Mas como eles se comparam?


Recursos de colaboração B2B |     Oferta autônoma do B2C do AD do Azure
-------- | --------
Destinado a: organizações que desejam poder autenticar usuários de uma organização de parceiros, independentemente do provedor de identidade. | Destinado a: convidar clientes dos aplicativos Web e móveis, sejam indivíduos, clientes institucionais ou organizacionais, para o Azure AD.
Identidades suportadas: funcionários com contas corporativas ou escolares, parceiros com contas corporativas ou escolares ou qualquer email. Em breve com suporte à federação direta.  | Identidades suportadas: os usuários do consumidor com contas de aplicativo local (qualquer nome de usuário ou email) ou identidades sociais suportadas com federação direta.
Qual diretório são os usuários do parceiro encontram-se: usuários de parceiros da organização externa são gerenciados no mesmo diretório que os funcionários, mas anotados especialmente. Eles podem ser gerenciados da mesma forma que os funcionários, podem ser adicionados aos mesmos grupos e assim por diante  | Qual diretório as entidades de usuário do cliente estão: no diretório do aplicativo. Gerenciado separadamente do diretório de parceiros e funcionários da organização (se houver).
Há suporte para SSO (logon único) em todos os aplicativos conectados ao Azure AD. Por exemplo, é possível fornecer acesso ao Office 365 ou a aplicativos locais, além de outros aplicativos SaaS, como o Salesforce ou Workday.  |  Há suporte para SSO para aplicativos de clientes dentro dos locatários do B2C do AD do Azure. Não há suporte para SSO para Office 365 nem para outros aplicativos SaaS da Microsoft SaaS que não sejam da Microsoft.
Ciclo de vida do parceiro: gerenciado pela organização convidada/host.  | Ciclo de vida do cliente: autoatendido ou gerenciado pelo aplicativo.
Política de segurança e conformidade: gerenciada pela organização convidada/host.  | Política de segurança e conformidade: gerenciada pelo aplicativo.
Identidade visual: uso da marca da organização convidada/host.  |    Identidade visual: gerenciada pelo aplicativo. Geralmente, tende a ser da marca do produto com o esmaecimento da organização na tela de fundo.
Mais informações em: [Postagem de Blog](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Documentação](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | Mais informações em: [Página](https://azure.microsoft.com/services/active-directory-b2c/), [Documentação](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Próximas etapas

- [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Propriedades de usuário de colaboração B2B](active-directory-b2b-user-properties.md)

