---
title: "Estendendo os recursos de nuvem para os dispositivos Windows 10 por meio da Associação do Azure Active Directory | Microsoft Docs"
description: "Fornece uma visão geral detalhada de como os dispositivos Windows 10 podem utilizar o Ingresso no Azure AD para se registrar no Active Directory do Azure."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: 0cd4942f-7d54-474e-bd12-8e6764b0d42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 31574a82d190b9b157f8df3308fac298924eada5


---
# <a name="extending-cloud-capabilities-to-windows-10-devices-through-azure-active-directory-join"></a>Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure
## <a name="what-is-azure-active-directory-join"></a>O que é o Ingresso no Active Directory do Azure?
O Ingresso no Active Directory do Azure (Azure AD) é a funcionalidade que registra um dispositivo corporativo no Active Directory do Azure para habilitar seu gerenciamento centralizado. Ele permite que os usuários, como funcionários e alunos, se conectem à nuvem da empresa por meio do Active Directory do Azure. Isso habilita implantações simplificadas do Windows e acesso a aplicativos e recursos organizacionais por meio de qualquer dispositivo Windows, tanto dispositivos corporativos quanto pessoais (BYOD).

O Ingresso no Azure AD destina-se a empresas que priorizam a nuvem/usam somente a nuvem (normalmente, pequenas e médias empresas), que não têm uma infraestrutura do Active Directory do Windows Server local. Dito isto, o Ingresso no Azure AD pode ser e também será usado por organizações de grande porte em dispositivos que são incapazes de realizar um ingresso no domínio tradicional (dispositivos móveis, por exemplo) ou para usuários que precisam acessar principalmente o Office 365 ou outros aplicativos SaaS do Azure AD.

Embora o ingresso no domínio tradicional ainda ofereça a melhor local experiência local em dispositivos que são capazes de ingressar no domínio, o Ingresso no Azure AD é adequado para dispositivos que não podem ingressar no domínio. O Ingresso no Azure AD também é adequado para gerenciar usuários na nuvem. Ele faz isso usando recursos de gerenciamento de dispositivos móveis em vez de ferramentas de gerenciamento de domínio tradicionais, como a Política de Grupo e o SCCM (System Center Configuration Manager).

![Visão geral de dispositivos corporativos e pessoais com o Active Directory local e o Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)

## <a name="why-should-enterprises-adopt-azure-ad-join"></a>Por que as empresas devem adotar o Ingresso no Azure AD?
* **Empresas que estão principalmente na nuvem**: se você tiver migrado ou estiver migrando para um modelo no qual esteja reduzindo seu volume local e deseje operar mais na nuvem, o Ingresso no Azure AD poderá beneficiá-lo. Você pode ter criado contas do Azure AD manualmente ou sincronizando seu Active Directory local. De qualquer forma, você tem uma conta no Azure AD e pode usá-la para entrar no Windows 10. Os usuários podem fazer o ingresso de seus computadores no Azure AD por meio da OOBE (configuração inicial pelo usuário) ou do menu Configurações. Após o ingresso, os usuários desfrutarão das vantagens do SSO (logon único) para recursos de nuvem como o Office 365, no navegador ou em aplicativos do Office.
* **Instituições educacionais**: um dos cenários sobre os quais ouvimos falar com frequência é que as instituições de ensino têm dois tipos de usuário: professores e alunos. Os membros do corpo docente são considerados integrantes de longo prazo da organização. Portanto, é desejável criar contas locais para eles. Porém, os alunos são membros temporários da organização e, assim, podem ser gerenciados no Azure AD. Isso significa que a escala do diretório pode ser enviada para a nuvem em vez de ser armazenada localmente. Isso também significa que os alunos podem entrar no Windows com suas contas do Azure AD e obter acesso aos recursos do Office 365, no navegador ou em aplicativos do Office.
* **Empresas de varejo**: outro cenário que os clientes mencionam é seu desejo de gerenciar funcionários sazonais mais facilmente.  Novamente, as contas para funcionários em tempo integral de longo prazo geralmente são criadas como contas locais em computadores que ingressaram no domínio. Porém, os trabalhadores sazonais são membros de curto prazo da organização. Portanto, é desejável gerenciá-los onde as licenças de usuário possam ser mais facilmente movidas. A criação dessas contas de usuários na nuvem com licenças do Office 365 permite que os usuários tenham os benefícios de entrar em aplicativos do Office e no Windows com uma conta do Azure AD. Enquanto isso, você mantém mais flexibilidade com suas licenças depois que eles partem.
* **Outras empresas**: embora você mantenha os usuários no Active Directory local, ainda poderia se beneficiar se os usuários ingressassem no Azure AD. Isso ocorre porque o Azure AD oferece uma experiência simplificada de ingresso, gerenciamento de dispositivos eficiente, registro de gerenciamento automático de dispositivos móveis e capacidade de logon único para o Azure AD e recursos locais.  

## <a name="what-capabilities-does-azure-ad-join-offer"></a>Quais recursos o Ingresso no Azure AD oferece?
Com a Junção do Azure AD, você obtém o seguinte:

* **Autoprovisionamento de dispositivos corporativos**: com o Windows 10, os usuários podem configurar um dispositivo totalmente novo na experiência de configuração inicial pelo usuário, sem o envolvimento da TI.
* **Suporte para fatores forma modernos**: o Ingresso no Azure AD funciona em dispositivos que não têm recursos tradicionais de ingresso no domínio.  
* **Suporte para contas organizacionais existentes**: os usuários não precisam mais criar e manter uma conta da Microsoft pessoal para obter a melhor experiência em dispositivos fornecidos pela empresa, como ocorria com o Windows 8. Em vez disso, eles podem usar suas contas corporativas existentes no Azure AD. Para muitas organizações, isso basicamente significa que os usuários podem configurar e entrar no Windows com as mesmas credenciais que usam para acessar o Office 365.
* **Registro automático de gerenciamento de dispositivos móveis**: os dispositivos podem ser registrados automaticamente no gerenciamento de dispositivos móveis quando conectados ao Azure AD. Esse processo funciona com o Microsoft Intune e as soluções de gerenciamento de dispositivos móveis de parceiros. Quando o gerenciamento de dispositivos é feito com o Intune, os administradores de TI podem monitorar/gerenciar dispositivos com o Ingresso no Azure AD junto com dispositivos com ingresso no domínio no console de gerenciamento do SCCM.
* **Logon único para recursos corporativos**: os usuários aproveitam o logon único na área de trabalho do Windows para aplicativos e recursos na nuvem, como o Office 365 e milhares de aplicativos de negócios que dependem do Azure AD para autenticação por meio do [Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md). Dispositivos corporativos que ingressaram no Azure AD também aproveitam o SSO em recursos locais quando o dispositivo está em uma rede corporativa, e em qualquer lugar quando esses recursos são expostos por meio do [Proxy de Aplicativo do Azure AD](https://msdn.microsoft.com/library/azure/Dn768219.aspx).
* **Roaming de estado do sistema operacional**: configurações de acessibilidade, sites, senhas de Wi-Fi e outras configurações são sincronizados entre dispositivos corporativos sem a necessidade de uma conta da Microsoft pessoal.
* **Windows Store pronta para a empresa**: a Windows Store dá suporte à aquisição e ao licenciamento de aplicativos com contas do Azure AD. As organizações podem licenciar aplicativos por volume e disponibilizá-los para os usuários em sua organização.

## <a name="how-do-different-devices-work-with-azure-ad-join"></a>Como diferentes dispositivos funcionam com o Ingresso no Azure AD?
| Dispositivo corporativo (que ingressou em um domínio local) | Dispositivo corporativo (que ingressou na nuvem) | Dispositivo pessoal |
| --- | --- | --- |
| Os usuários podem entrar no Windows com credenciais corporativas (como fazem hoje) |Os usuários podem entrar no Windows com as credenciais de trabalho que são gerenciadas no Azure AD. Isso é relevante para dispositivos corporativos em três casos: 1) A organização não tem o Active Directory local (por exemplo, uma pequena empresa). 2) A organização não cria todas as contas de usuário no Active Directory (por exemplo, contas para alunos, consultores ou funcionários sazonais não são criadas no Active Directory). 3) A organização tem dispositivos corporativos que não podem ingressar em um domínio (local), como telefones ou tablets que executam uma SKU Móvel (por exemplo, um dispositivo secundário levado ao chão de fábrica/varejo). O Ingresso do Azure AD dá suporte ao ingresso de dispositivos corporativos para organizações gerenciadas e federadas. |Os usuários entram no Windows com suas credenciais pessoais da conta da Microsoft (sem alteração) |
| Os usuários têm acesso às configurações de roaming e à Windows Store corporativa. Esses serviços funcionam com contas corporativas e não exigem uma conta da Microsoft pessoal. Isso requer que as empresas conectem seu Active Directory local ao Azure AD. |Os usuários podem fazer a instalação de autoatendimento. Eles podem percorrer a FRX (tela de apresentação) por meio de sua conta corporativa como uma alternativa ao provisionamento dos dispositivos pela TI, embora ambos os métodos tenham suporte. |Os usuários podem adicionar facilmente uma conta corporativa que é gerenciada no Active Directory ou no Azure AD. |
| Os usuários têm a capacidade de SSO na área de trabalho para aplicativos, sites e recursos corporativos, incluindo recursos locais e aplicativos na nuvem que usam o Azure AD para autenticação. |Os dispositivos são registrados automaticamente no diretório corporativo (Azure AD) e registrados automaticamente no gerenciamento de dispositivos móveis. (Recurso do Azure AD Premium). |Os usuários têm a capacidade de SSO entre aplicativos e para sites/recursos com essa conta corporativa. |
| Os usuários podem adicionar suas contas da Microsoft pessoais para acessar seus arquivos e imagens pessoais sem afetar os dados corporativos. (As configurações de roaming continuam a funcionar com suas contas corporativas.) A conta da Microsoft habilita o SSO e não exige mais o roaming de configurações. |Os usuários podem executar uma SSPR (redefinição de senha de autoatendimento) no winlogon, o que significa que podem redefinir uma senha esquecida. (Recurso do Azure AD Premium). |Os usuários têm acesso à Windows Store corporativa, assim, podem adquirir e usar aplicativos de linha de negócios em seus dispositivos pessoais. |

## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Autenticando identidades sem senhas com o Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)




<!--HONumber=Feb17_HO2-->


