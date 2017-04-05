---
title: Fundamentos do gerenciamento de identidades do Azure | Microsoft Docs
description: 
keywords: 
author: jeffgilb
manager: femila
ms.date: 3/28/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.reviewer: jsnow
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 222259e1f7c5a8796fd9f652cf5e050e75d4ce49
ms.lasthandoff: 03/29/2017


---
# <a name="fundamentals-of-azure-identity-management"></a>Fundamentos do gerenciamento de identidades do Azure
Como mais dos recursos digitais da empresa ao vivo fora da rede corporativa, na nuvem e em dispositivos, uma ótima identidade baseado em nuvem e gerenciamento de acesso é a melhor maneira de manter o controle e visibilidade sobre como e quando os usuários acessam dados e aplicativos corporativos.

Microsoft tem sido protegendo identidades baseados em nuvem para mais de uma década e agora, com [Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), esses mesmos sistemas de proteção estão disponíveis para você. Com o Azure AD, os administradores de empresa podem garantir facilmente a responsabilidade do usuário e do administrador com mais segurança e controle do que nunca.

O Azure AD Premium é uma solução de gerenciamento de acesso e de identidades baseada em nuvem com recursos avançados de proteção que habilita uma identidade segura para todos os aplicativos, a proteção de identidades (aprimorada pelo [gráfico de segurança de inteligência da Microsoft](https://www.microsoft.com/en-us/security/intelligence)) e o Privileged Identity Management. Não é apenas outra monitoramento ou ferramenta de emissão de relatórios do Azure AD Premium pode proteger as identidades do usuário em tempo real e permitem que você crie políticas de acesso baseada em riscos, adaptável para proteger os dados da sua organização.

Assista a este vídeo curto para obter uma visão geral da proteção e do gerenciamento de identidades do Azure AD:
<iframe width="560" height="315" src="https://www.youtube.com/embed/9LGIJ2-FKIM" frameborder="0" allowfullscreen></iframe>

A Microsoft fornece não apenas a identidade que você usa em todos os lugares, mas também um conjunto de ferramentas para automatizar, ajudar a proteger e gerenciar TI em sua organização. Mesmo após o advento da computação em nuvem, ainda há demanda de gerenciar e controlar tarefas de TI como chamadas de assistência técnica para redefinir senhas de usuário, gerenciamento de grupo de usuários e solicitações do aplicativo. O que complica ainda mais as coisas, os funcionários estão agora levando seus dispositivos pessoais para o trabalho e usar aplicativos de SaaS prontamente disponíveis. Isso torna mantêm o controle sobre seus aplicativos em plataformas de nuvem pública e data centers corporativos um desafio significativo.

> [!Note]
> Os recursos descritos neste artigo exigem uma assinatura do Azure Active Directory P1 ou P2 seja adquirida separadamente ou como parte de um [Enterprise Mobility + Security E3 ou E5](https://docs.microsoft.com/enterprise-mobility-security/solutions/learn-about-ems) assinatura.

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>Conectar-se o Active Directory no local com o Azure AD e Office 365
As organizações que fizeram grandes investimentos no Active Directory local podem estender esses investimentos para a nuvem, integrar seus diretórios locais ao Azure AD em [gerenciamento de identidade híbrida](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Isso torna os usuários mais produtivos fornecendo uma identidade comum para acessar recursos, independentemente do local. Usuários e organizações podem usar o logon único (SSO) para acessar os recursos locais e serviços de nuvem como o Office 365.

[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) é a única ferramenta que você precisará obter a integração feita. O Azure AD Connect fornece os recursos mais recentes para dar suporte a suas necessidades de sincronização de identidades e substitui as versões mais antigas das ferramentas de integração de identidade como DirSync e sincronização do Azure AD. Com o Azure AD Connect, gerenciamento de identidade e a sincronização entre local e o Azure AD é habilitado por meio de:

- Sincronização - esse componente é responsável pela criação de usuários, grupos e outros objetos. Também é responsável por garantir que as informações de identidade dos usuários e grupos locais correspondam às da nuvem. Write-back de senha também pode ser habilitado para a manutenção da sincronização de diretórios locais quando um usuário atualiza sua senha no AD do Azure.
- AD FS - a federação é uma parte opcional do Azure AD Connect e pode ser usada para configurar um ambiente híbrido usando uma infraestrutura do AD FS local. Isto pode ser usado pelas organizações para endereçar as implantações complexas, como o SSO de ingresso no domínio, imposição da política de entrada no AD e cartão inteligente ou MFA de terceiros.
- Monitoramento de Integridade - o [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health) pode fornecer monitoramento robusto e fornecer um local central no portal do Azure para exibir essa atividade.

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>Aumentar a produtividade e reduzir os custos de assistência técnica com um únicos e autoatendimento experiências de logon

Os funcionários são mais produtivos quando têm um único nome de usuário e senha para se lembrar e uma experiência consistente de cada dispositivo. Também ganham tempo quando podem executar tarefas de autoatendimento como [redefinir uma senha esquecida](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) ou solicitar acesso a um aplicativo sem aguardar a assistência técnica.

O Azure AD [estende o Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) local para a nuvem, permitindo que os usuários usem suas contas organizacionais primárias nos dispositivos ingressados no domínio e nos recursos da empresa e todos os aplicativos SaaS e Web necessários para seu trabalho. Além de fazer com que não seja necessário lembrar de vários conjuntos de nomes de usuário e de senhas, o acesso a aplicativos dos usuários também pode ser automaticamente provisionado (ou desprovisionado) com base na associação ao grupo da organização e no status como funcionário. E você pode controlar o acesso para aplicativos da galeria ou para seus próprios aplicativos locais que você já desenvolveu e publicado por meio de [com o Proxy de aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

## <a name="manage-and-control-access-to-corporate-resources"></a>Gerenciar e controlar o acesso aos recursos corporativos
As soluções de gerenciamento de acesso e identidade da Microsoft ajudam a TI a proteger o acesso a aplicativos e recursos no datacenter corporativo e na nuvem, permitindo níveis de validação adicionais, como a [autenticação multifator](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) e as [políticas de acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). O monitoramento de atividade suspeita por meio de alertas, auditoria e relatórios de segurança avançados ajuda a reduzir potenciais problemas de segurança.

O sistema de acesso condicional no Azure AD Premium fornece a você, o administrador corporativo, a capacidade de criar regras de acesso baseado em políticas para qualquer aplicativo do Azure conectada AD (aplicativos SaaS, aplicativos personalizados em execução em aplicativos da web de nuvem ou local). O Azure AD avalia essas políticas em tempo real e as impõe sempre que um usuário tenta acessar um aplicativo. Políticas de proteção de identidade do Azure permitem que você entre em ação se a atividade suspeita é descoberta incluindo bloqueando o acesso a usuários em risco, impor a autenticação multifator e redefinir senhas se parece que credenciais foram comprometidas automaticamente.

## <a name="azure-active-directory-privileged-identify-management"></a>Gerenciamento de identidades privilegiadas do Azure Active Directory

[Com o Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started), incluído com a oferta do Azure Active Directory Premium P2, permite que você descubra, restringir e monitorar contas administrativas e o acesso a recursos no Active Directory do Azure e outros serviços online da Microsoft. Ele também ajuda a administrar o acesso administrativo sob demanda para o período exato que é necessário.

Privileged Identity Management pode impor os direitos de administrador sob demanda para que os administradores podem solicitar elevação de autenticado, temporária multifator de seus privilégios para pré-configurado períodos de tempo antes que suas contas retornam a um estado de usuário normal.

## <a name="benefits-of-azure-identity"></a>Benefícios da Identidade do Azure

Com o gerenciamento de identidades do Azure, você pode:

-   Criar e gerenciar uma identidade única para cada usuário em sua empresa híbrida, mantendo os usuários, grupos e dispositivos em sincronia com o [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

-   Fornecer acesso de logon único para seus aplicativos incluindo milhares de aplicativos SaaS pré-integrados ou fornecer acesso remoto seguro para aplicativos SaaS de locais usando o [com o Proxy de aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

-   Habilitar segurança de acesso do aplicativo por meio da aplicação da [autenticação multifator](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) com base em regras para aplicativos locais e na nuvem.

-   Melhorar a produtividade do usuário com [de redefinição de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)e o grupo e o aplicativo acessar solicitações usando o [portal MyApps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-user-help).

-   Aproveitar o [alta disponibilidade e confiabilidade](https://docs.microsoft.com/azure/architecture/resiliency/high-availability-azure-applications) de um empresariais em todo o mundo, solução de gerenciamento de identidades e acesso baseado em nuvem.

