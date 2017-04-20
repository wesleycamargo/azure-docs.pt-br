---
title: "Edições do Azure Active Directory | Microsoft Docs"
description: "Este artigo explica as opções para as edições gratuita e paga do Azure Active Directory. O Azure Active Directory Basic, o Azure Active Directory Premium P1 e o Azure Active Directory Premium P2 são edições pagas."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: dcaf8939-7633-40a8-bd76-27dedbb6083a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: f29c3ed47c772609cfeca0ad2b5078001b6d0737
ms.lasthandoff: 04/13/2017


---
# <a name="azure-active-directory-editions"></a>Edições do Active Directory do Azure
Todos os serviços comerciais do Microsoft Online dependem do Azure Active Directory (Azure AD) para entrada e outras necessidades de identidade. Se você assinar qualquer um dos serviços comerciais do Microsoft Online (por exemplo, Office 365 ou Microsoft Azure), obterá o Azure AD com acesso a todos os recursos Gratuitos, descritos abaixo.  

O Azure Active Directory é uma solução de nuvem de gerenciamento de acesso e identidade abrangente e altamente disponível que combina os serviços principais de diretório, governança avançada de identidades e gerenciamento do acesso de aplicativos. O Azure Active Directory também oferece uma plataforma avançada, baseada em padrões, que permite que os desenvolvedores ofereçam controle de acesso aos aplicativos, com base em regras e políticas centralizadas. Com a edição Gratuita do Active Directory do Azure, você pode gerenciar usuários e grupos, sincronizar com diretórios locais, obter logon único no Azure, Office 365 e em milhares de aplicativos SaaS populares como Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox e muito mais. Para saber mais sobre o Azure Active Directory, leia [O que é o Azure AD?](active-directory-whatis.md)

Para aprimorar seu Active Directory do Azure, é possível adicionar recursos pagos usando as edições do Azure Active Directory Basic, Premium P1 e Premium P2. As edições pagas do Active Directory do Azure são criadas em seu diretório gratuito existente, fornecendo recursos corporativos que abrangem autoatendimento, monitoramento avançado, relatórios de segurança, MFA (Multi-Factor Authentication) e acesso seguro para a sua força de trabalho móvel.

As assinaturas do Office 365 incluem recursos adicionais do Active Directory do Azure descritos na tabela de comparação abaixo.

> [!NOTE]
> Para obter as opções de preço dessas edições, confira [Preço do Active Directory do Azure](https://azure.microsoft.com/pricing/details/active-directory/). O Azure Active Directory Premium P1, Premium P2 e Azure Active Directory Basic não têm suporte atualmente na China. Entre em contato conosco no Fórum do Azure Active Directory para obter mais informações.
>
>

* **Azure Active Directory Basic** - Projetada para profissionais de tarefas com necessidades que priorizam a nuvem, esta edição fornece soluções de gerenciamento de identidades por autoatendimento e de acesso a aplicativos centrados na nuvem. Com a Basic edition do Active Directory do Azure, você obtém recursos que aumentam sua produtividade e reduzem os custos, como o gerenciamento de acesso baseado em grupo, redefinição de senha por autoatendimento para aplicativos em nuvem e o Proxy de Aplicativo do Active Directory do Azure (para publicar aplicativos web locais usando o Active Directory do Azure), tudo com o apoio de um SLA de nível empresarial de 99,9% de tempo de atividade.
* **Azure Active Directory Premium P1** – Projetado para capacitar as organizações com necessidades mais exigentes de gerenciamento de identidades e acesso, a Active Directory Premium edition do Azure adiciona funcionalidades de gerenciamento de identidades de nível empresarial rica em recursos e permite que os usuários híbridos acessem diretamente as funcionalidades locais e na nuvem. Esta edição inclui tudo o que necessário para o profissional de informações e os administradores de identidades em ambientes híbridos no acesso a aplicativos, IAM (gerenciamento de identidades e acesso) por autoatendimento, proteção de identidade e segurança na nuvem. Ela dá suporte a recursos avançados de administração e delegação, como grupos dinâmicos e gerenciamento de grupos por autoatendimento. Inclui o Microsoft Identity Manager (um pacote de gerenciamento de identidades e acesso local) e fornece recursos de write-back na nuvem, possibilitando soluções como a redefinição de senha por autoatendimento para seus usuários locais.
* **Azure Active Directory Premium P2** – Projetado com proteção avançada para todos os usuários e administradores, essa nova oferta inclui todos os recursos no Azure AD Premium P1, bem como nossa nova Identity Protection e Privileged Identity Management. O Azure Active Directory Identity Protection utiliza bilhões de sinais para fornecer acesso condicional com base em risco aos seus aplicativos e dados importantes da empresa. Podemos também ajudar a gerenciar e proteger contas privilegiadas com o Azure Active Directory Privileged Identity Management para descobrir, restringir e monitorar os administradores e o acesso a recursos e fornecer acesso just-in-time quando necessário.  

Para inscrever-se e começar a usar o Active Directory Premium hoje, consulte [Introdução ao Active Directory Premium do Azure](active-directory-get-started-premium.md).

> [!NOTE]
> Uma série de recursos do Active Directory do Azure está disponível por meio de edições “pré-pagas”:
>
> * O Active Directory B2C é a solução de gerenciamento de identidades e acesso para seus aplicativos voltados para o consumidor. Para obter mais detalhes, veja [Active Directory B2C do Azure](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * O Azure Multi-Factor Authentication pode ser usado por usuário ou por provedores de autenticação. Para obter mais detalhes, veja [O que é o Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)
>
>

## <a name="comparing-generally-available-features"></a>Comparação de recursos geralmente disponíveis
> [!NOTE]
> Para obter uma exibição diferente dos dados, confira os [Recursos do Azure Active Directory](https://www.microsoft.com/en/server-cloud/products/azure-active-directory/features.aspx).
>
>

**Recursos comuns**

* [Objetos do diretório](#directory-objects)
* [Gerenciamento de usuário/grupo (adicionar/atualizar/excluir)/Provisionamento baseado em usuário, Registro de dispositivo](#usergroup-management-addupdatedelete-user-based-provisioning-device-registration)
* [SSO (Logon único)](#single-sign-on-sso)
* [Alteração de senhas por autoatendimento para usuários de nuvem](#self-service-password-change-for-cloud-users)
* [Connect (Mecanismo de sincronização que amplia os diretórios locais ao Azure Active Directory)](#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory)
* [Segurança/Relatórios de uso](#securityusage-reports)

**Recursos Básicos**

* [Gerenciamento de acesso baseado em grupos/provisionamento](#group-based-access-managementprovisioning)
* [Redefinição de senha por autoatendimento para usuários de nuvem](#self-service-password-reset-for-cloud-users)
* [Identidade visual da empresa (Páginas de logon/Personalização do painel de acesso)](#company-branding-logon-pagesaccess-panel-customization)
* [Proxy de Aplicativo](#application-proxy)
* [SLA de 99,9%](#sla-999)

**Recursos do Premium P1**

* [Gerenciamento de autoatendimento de aplicativos e de grupos/Adições de autoatendimento de aplicativos/Grupos dinâmicos](#self-service-group)
* [Redefinição/Alteração/Desbloqueio de senha de autoatendimento com write-back local](#self-service-password-resetchangeunlock-with-on-premises-write-back)
* [Multi-Factor Authentication (nuvem e local [Servidor MFA])](#multi-factor-authentication-cloud-and-on-premises-mfa-server)
* [CAL MIM + Servidor MIM](#mim-cal-mim-server)
* [Cloud App Discovery](#cloud-app-discovery)
* [Connect Health](#connect-health)
* [Substituição automática de senha para contas de grupo](#automatic-password-rollover-for-group-accounts)

**Recursos do Premium P2**

* [Identity Protection](active-directory-identityprotection.md)
* [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)

**Ingresso do Active Directory do Azure - apenas para recursos relacionados ao Windows 10**

* [Adicione um dispositivo para AD do Azure, Desktop SSO, Microsoft Passport para AD do Azure, recuperação do Administrator Bitlocker](#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery)
* [Registro automático do MDM, Recuperação de autoatendimento do Bitlocker, Administradores locais adicionais aos dispositivos do Windows 10 através de Ingresso do Azure AD](#mdm-auto-enrollment)

## <a name="common-features"></a>Recursos comuns
#### <a name="directory-objects"></a>Objetos do diretório
**Tipo:** recursos comuns

A cota de uso padrão é de 150 mil objetos. Um objeto é uma entrada no serviço do diretório, representado por seu nome diferenciado exclusivo. Um exemplo de objeto é uma entrada de usuário para propósitos de autenticação. Se você precisa ultrapassar essa cota padrão, entre em contato com o suporte. O limite de objeto de 500 mil não se aplica ao Office 365, ao Microsoft Intune ou a qualquer outro serviço online pago da Microsoft que dependa do Active Directory do Azure para serviços de diretório.

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| Até 500 mil objetos |Não há limite de objeto |Não há limite de objeto |Nenhum limite de objeto para contas de usuário do Office 365 |

#### <a name="usergroup-management-addupdatedelete-user-based-provisioning-device-registration"></a>Gerenciamento de usuário/grupo (adicionar/atualizar/excluir), Provisionamento baseado em usuário, Registro de dispositivo
**Tipo:** recursos comuns

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| ![Verificação][12] |![Verificação][12] |![Verificação][12] |![Verificação][12] |

**Mais detalhes:**

* [Administrar seu diretório do Azure AD](active-directory-administer.md)
* [Visão geral do registro de dispositivos do Active Directory do Azure](active-directory-conditional-access-device-registration-overview.md)

#### <a name="single-sign-on-sso"></a>SSO (Logon único)
**Tipo:** recursos comuns

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| 10 aplicativos por usuário (1) |10 aplicativos por usuário (1) |Sem limite (2) |10 aplicativos por usuário (1) |

1. Com o Azure AD Gratuito e o Azure AD Básico, os usuários finais têm direto a obter acesso com logon único a até 10 aplicativos.
2. Integração de autoatendimento de qualquer aplicativo que dê suporte à autenticação baseada em formulários, a SCIM ou a SAML usando modelos fornecidos no menu da galeria de aplicativos. Para obter mais detalhes, confira [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Azure Active Directory](active-directory-saas-custom-apps.md).

**Mais detalhes:**

* [Gerenciando aplicativos com o AD do Azure (Active Directory do Azure)](active-directory-enable-sso-scenario.md)

#### <a name="self-service-password-change-for-cloud-users"></a>Alteração de senhas por autoatendimento para usuários de nuvem
**Tipo:** recursos comuns

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| ![Verificação][12] |![Verificação][12] |![Verificação][12] |![Verificação][12] |

**Mais detalhes:**

* [Como atualizar sua própria senha](active-directory-passwords-update-your-own-password.md#reset-my-password)

#### <a name="connect--sync-engine-that-extends-on-premises-directories-to-azure-active-directory"></a>Connect (Mecanismo de sincronização que amplia os diretórios locais ao Azure Active Directory)
**Tipo:** recursos comuns

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| ![Verificação][12] |![Verificação][12] |![Verificação][12] |![Verificação][12] |

**Mais detalhes:**

* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

#### <a name="securityusage-reports"></a>Relatórios de segurança/uso
**Tipo:** recursos comuns

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| Três relatórios básicos |Três relatórios básicos |Relatórios avançados |Três relatórios básicos |

**Mais detalhes:**

* [Exibir relatórios de acesso e uso](active-directory-view-access-usage-reports.md)

## <a name="premium-and-basic-features"></a>Recursos básicos e Premium
#### <a name="group-based-access-managementprovisioning"></a>Gerenciamento/provisionamento de acesso baseado em grupos
**Tipo:** recursos básicos

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![Verificação][12] | ![Verificação][12] | &nbsp; |

**Mais detalhes:**

* [Uso de um grupo para gerenciar o acesso a aplicativos SaaS](active-directory-accessmanagement-group-saasapps.md)

#### <a name="self-service-password-reset-for-cloud-users"></a>Redefinição de senha por autoatendimento para usuários de nuvem
**Tipo:** recursos básicos

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![Verificação][12] |![Verificação][12] | ![Verificação][12] |

**Mais detalhes:**

* [Redefinição de senha do AD do Azure para usuários e administradores](active-directory-passwords.md)

#### <a name="company-branding-logon-pagesaccess-panel-customization"></a>Identidade visual da empresa (Páginas de logon/Personalização do painel de acesso)
**Tipo:** recursos básicos

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![Verificação][12] |![Verificação][12] | ![Verificação][12] |

**Mais detalhes:**

* [Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso](active-directory-add-company-branding.md)

#### <a name="application-proxy"></a>Proxy de Aplicativo
**Tipo:** recursos básicos

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![Verificação][12] | ![Verificação][12] | &nbsp; |

**Mais detalhes:**

* [Como fornecer acesso remoto seguro a aplicativos locais](active-directory-application-proxy-get-started.md)

#### <a name="sla-999"></a>SLA de 99,9%
**Tipo:** recursos básicos

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; |![Verificação][12] |![Verificação][12] | ![Verificação][12] |

**Mais detalhes:**

* [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/)

## <a name="premium-features"></a>Recursos Premium


#### <a name="self-service-group"></a>Gerenciamento de autoatendimento de aplicativos e grupos/Adições de autoatendimento de aplicativos/Grupos dinâmicos
**Tipo:** recursos premium

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Verificação][12]| &nbsp; |

#### <a name="self-service-password-resetchangeunlock-with-on-premises-write-back"></a>Redefinição/Alteração/Desbloqueio de senha de autoatendimento com write-back local
**Tipo:** recursos premium

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Verificação][12] | &nbsp; |

#### <a name="multi-factor-authentication-cloud-and-on-premises-mfa-server"></a>Multi-Factor Authentication (nuvem e local [Servidor MFA])
**Tipo:** recursos premium

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; |![Verificação][12] |Limitado à nuvem somente para aplicativos do Office 365 |

**Mais detalhes:**

* [O que é a Autenticação Multifator do Azure?](../multi-factor-authentication/multi-factor-authentication.md)


#### <a name="mim-cal-mim-server"></a>CAL MIM + Servidor MIM
Direitos de software de servidor Microsoft Identity Manager são concedidos com licenças do Windows Server (qualquer edição). Como o Microsoft Identity Manager é executado no sistema operacional Windows Server, se o servidor estiver executando uma cópia licenciada válida do Windows Server, o Microsoft Identity Manager pode ser instalado e usado no servidor. Nenhuma outra licença separada é necessária para o servidor do Microsoft Identity Manager.

**Tipo:** recursos premium

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; |![Verificação][12] | &nbsp; |

#### <a name="cloud-app-discovery"></a>Cloud App Discovery
**Tipo:** recursos premium

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Verificação][12] | &nbsp; |

**Mais detalhes:**

* [Encontrando aplicativos em nuvem não gerenciados com o Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)

#### <a name="azure-ad-connect-health"></a>Azure AD Connect Health
**Tipo:** recursos premium

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Verificação][12] | &nbsp; |

**Mais detalhes:**

* [Monitorar infraestrutura de identidade local e serviços de sincronização na nuvem](active-directory-aadconnect-health.md)

#### <a name="automatic-password-rollover-for-group-accounts"></a>Substituição automática de senha para contas de grupo
**Tipo:** recursos premium

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Verificação][12] | &nbsp; |

#### <a name="identity-protection"></a>Identity Protection
**Tipo:** recursos premium

| Edição Gratuita | Edição Básica | Edição Premium P2 | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Verificação][12] | &nbsp; |

#### <a name="privileged-identity-management"></a>Privileged Identity Management
**Tipo:** recursos premium

| Edição Gratuita | Edição Básica | Edição Premium P2 | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Verificação][12] | &nbsp; |

## <a name="azure-active-directory-join--windows-10-only--related-features"></a>Ingresso do Azure Active Directory - apenas recursos relacionados ao Windows 10
#### <a name="join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery"></a>Adicione um dispositivo para AD do Azure, Desktop SSO, Microsoft Passport para AD do Azure, recuperação do Administrator Bitlocker
**Tipo:** ingresso do Azure Active Directory – apenas recursos relacionados ao Windows 10

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| ![Verificação][12] |![Verificação][12] |![Verificação][12] |![Verificação][12] |


#### <a name="mdm-auto-enrollment"></a>Registro automático do MDM, Recuperação de autoatendimento do Bitlocker, Administradores locais adicionais aos dispositivos do Windows 10 através de Ingresso do Azure AD
**Tipo:** ingresso do Azure Active Directory – apenas recursos relacionados ao Windows 10

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Verificação][12] | &nbsp; |

#### <a name="enterprise-state-roaming"></a>Enterprise State Roaming
**Tipo:** ingresso do Azure Active Directory – apenas recursos relacionados ao Windows 10

**Disponibilidade:**

| Edição Gratuita | Edição Básica | Edições Premium (P1 e P2) | Somente aplicativos do Office 365 |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Verificação][12] | &nbsp; |

**Mais detalhes:**

* [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)

## <a name="azure-ad-preview-features"></a>Recursos de visualização do AD do Azure
Além dos recursos disponíveis das edições Gratuita, Básica e Premium (P1 e P2), o Azure AD também fornece um conjunto de recursos de visualização. Você pode usar os recursos de visualização para obter uma impressão das novidades que serão lançadas no futuro próximo e para determinar se esses recursos podem ajudar a melhorar seu ambiente.

**Recursos de visualização disponíveis:**

* [Colaboração B2B](active-directory-b2b-collaboration-overview.md)
* [Unidades Administrativas](active-directory-administrative-units-management.md)
* [Integração de aplicativo de RH](active-directory-saas-workday-inbound-tutorial.md)
* [Autenticação baseada em certificado no iOS](active-directory-certificate-based-authentication-ios.md)
* [Autenticação baseada em certificado no Android](active-directory-certificate-based-authentication-android.md)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md)
* [Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso](active-directory-add-company-branding.md)
* [Exibir relatórios de acesso e de uso](active-directory-view-access-usage-reports.md)

<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png

