<properties
	pageTitle="Edições do Active Directory do Azure | Microsoft Azure"
	description="Um tópico que explica as opções das edições gratuita e paga do Active Directory. O Active Directory Basic do Azure é a edição gratuita e o Active Directory Premium do Azure é a edição paga."
	services="active-directory"
	documentationCenter=""
	authors="MarkusVi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/12/2016"
	ms.author="markvi"/>

# Edições do Active Directory do Azure

Todos os serviços comerciais do Microsoft Online dependem do Active Directory do Azure para logon e outras necessidades de identidade. Se você assinar qualquer um dos serviços comerciais do Microsoft Online (por exemplo, Office 365, Microsoft Azure, etc.), você obterá o Active Directory do Azure (AD do Azure) com acesso a todos os recursos Gratuitos, descritos abaixo.


O Active Directory do Azure é um serviço que fornece recursos abrangentes de gerenciamento de acesso e de identidade na nuvem para seus funcionários, parceiros e clientes. Ele combina serviços de diretório, governança avançada de identidade, uma plataforma sofisticada baseada em padrões para desenvolvedores e gerenciamento de acesso de aplicativo para seu próprio aplicativo ou qualquer um dos milhares de aplicativos pré-integrados. Com a edição Gratuita do Active Directory do Azure, você pode gerenciar usuários e grupos, sincronizar com diretórios locais, obter logon único no Azure, Office 365 e em milhares de aplicativos SaaS populares como Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox e muito mais. Para saber mais sobre o Active Directory do Azure, leia [O que é o AD do Azure?](active-directory-whatis.md)



Para aprimorar seu Active Directory do Azure, é possível adicionar recursos pagos usando as Active Directory Basic e Premium editions do Azure. As edições pagas do Active Directory do Azure são criadas em seu diretório gratuito existente, fornecendo recursos corporativos que abrangem autoatendimento, monitoramento avançado, relatórios de segurança, MFA (Multi-Factor Authentication) e acesso seguro para a sua força de trabalho móvel.

As assinaturas do Office 365 incluem recursos adicionais do Active Directory do Azure descritos na tabela de comparação abaixo.


> [AZURE.NOTE] Para obter as opções de preço dessas edições, confira [Preço do Active Directory do Azure](https://azure.microsoft.com/pricing/details/active-directory/). O Azure Active Directory Premium e o Azure Active Directory Basic não têm suporte atualmente na China. Entre em contato conosco no Fórum do Active Directory do Azure para obter mais informações


- **Azure Active Directory Basic** - Projetada para profissionais de tarefas com necessidades que priorizam a nuvem, esta edição fornece soluções de gerenciamento de identidades por autoatendimento e de acesso a aplicativos centrados na nuvem. Com a Basic edition do Active Directory do Azure, você obtém recursos que aumentam sua produtividade e reduzem os custos, como o gerenciamento de acesso baseado em grupo, redefinição de senha por autoatendimento para aplicativos em nuvem e o Proxy de Aplicativo do Active Directory do Azure (para publicar aplicativos web locais usando o Active Directory do Azure), tudo com o apoio de um SLA de nível empresarial de 99,9% de tempo de atividade.
 
- **Azure Active Directory Premium** - Projetada para capacitar as organizações com necessidades mais exigentes de gerenciamento de identidades e acesso, a Active Directory Premium edition do Azure adiciona funcionalidades de gerenciamento de identidades de nível empresarial rica em recursos e permite que os usuários híbridos acessem diretamente as funcionalidades locais e na nuvem. Esta edição inclui tudo o que necessário para o profissional de informações e os administradores de identidades em ambientes híbridos no acesso a aplicativos, IAM (gerenciamento de identidades e acesso) por autoatendimento, proteção de identidade e segurança na nuvem. Ela dá suporte a recursos avançados de administração e delegação, como grupos dinâmicos e gerenciamento de grupos por autoatendimento. Inclui o Microsoft Identity Manager (um pacote de gerenciamento de identidades e acesso local) e fornece recursos de write-back na nuvem, possibilitando soluções como a redefinição de senha por autoatendimento para seus usuários locais.

Para inscrever-se e começar a usar o Active Directory Premium hoje, consulte [Introdução ao Active Directory Premium do Azure](active-directory-get-started-premium.md).


> [AZURE.NOTE] 
Uma série de recursos do Active Directory do Azure está disponível por meio de edições “pré-pagas”:
>
>- O Active Directory B2C é a solução de gerenciamento de identidades e acesso para seus aplicativos voltados para o consumidor. Para obter mais detalhes, veja [Active Directory B2C do Azure](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
 
>-	O Azure Multi-Factor Authentication pode ser usado por usuário ou por provedores de autenticação. Para obter mais detalhes, veja [O que é o Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)


##Comparação de recursos geralmente disponíveis

> [AZURE.NOTE] Para obter uma exibição diferente dos dados, confira os [Recursos do Azure Active Directory](https://www.microsoft.com/en/server-cloud/products/azure-active-directory/features.aspx).

| | AD do Azure Gratuito | AD Basic do Azure | AD Premium do Azure |
| ---                      | :-:           | :-:            | :-:              |
| Recursos comuns | ![Verificação][12] | ![Verificação][12] | ![Verificação][12] |
| Recursos Básicos | | ![Verificação][12] | ![Verificação][12] |
| Recursos Premium | | | ![Verificação][12] |





**Recursos comuns**

- [Objetos do diretório](#directory-objects) 

- [Gerenciamento de usuário/Grupo (adicionar/atualizar/excluir)/Provisionamento baseado em usuário, Registro de dispositivo](#usergroup-management-addupdatedelete-user-based-provisioning-device-registration)

- [SSO (Logon único)](#single-sign-on-sso)

- [Alteração de senhas por autoatendimento para usuários de nuvem](#self-service-password-change-for-cloud-users)

- [Connect (Mecanismo de sincronização que amplia os diretórios locais para o Active Directory do Azure)](#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory)

- [Segurança/Relatórios de uso](#securityusage-reports)



**Recursos Básicos**

- [Gerenciamento de acesso baseado em grupos/provisionamento](#group-based-access-managementprovisioning)

- [Redefinição de senha por autoatendimento para usuários de nuvem](#self-service-password-reset-for-cloud-users)

- [Identidade visual da empresa (Páginas de logon/Personalização do painel de acesso)](#company-branding-logon-pagesaccess-panel-customization)

- [Proxy de Aplicativo](#application-proxy)

- [SLA de 99,9%](#sla-999)


**Recursos Premium**

- [Gerenciamento de grupo de autoatendimento/Adições de aplicativo de autoatendimento/ Grupos dinâmicos](#self-service-group-and-app-managementself-service-application-additions-dynamic-groups)

- [Redefinição/Alteração/Desbloqueio de senha de autoatendimento com write-back local](#self-service-password-resetchangeunlock-with-on-premises-write-back)

- [Multi-Factor Authentication (nuvem e local [Servidor MFA])](#multi-factor-authentication-cloud-and-on-premises-mfa-server)

- [CAL MIM + Servidor MIM](#mim-cal-mim-server)

- [Cloud App Discovery](#cloud-app-discovery)

- [Connect Health](#connect-health)

- [Substituição automática de senha para contas de grupo](#automatic-password-rollover-for-group-accounts)


**Ingresso do Active Directory do Azure - apenas para recursos relacionados ao Windows 10**

- [Adicione um dispositivo para AD do Azure, Desktop SSO, Microsoft Passport para AD do Azure, recuperação do Administrator Bitlocker](#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery)

- [Registro automático do MDM, recuperação de autoatendimento do Bitlocker, administração local extra para dispositivos do Windows 10 via Ingresso no AD do Azure](#mdm-auto-enrolment-self-service-bitlocker-recovery-additional-local-administrators-to-windows-10-devices-via-azure-ad-join)






## Recursos comuns
#### Objetos do diretório 

**Tipo:** recursos comuns

A cota de uso padrão é de 150 mil objetos. Um objeto é uma entrada no serviço do diretório, representado por seu nome diferenciado exclusivo. Um exemplo de objeto é uma entrada de usuário para propósitos de autenticação. Se você precisa ultrapassar essa cota padrão, entre em contato com o suporte. O limite de objeto de 500 mil não se aplica ao Office 365, ao Microsoft Intune ou a qualquer outro serviço online pago da Microsoft que dependa do Active Directory do Azure para serviços de diretório.


**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| Até 500 mil objetos| Não há limite de objeto| Não há limite de objeto| Nenhum limite de objeto para contas de usuário do Office 365|



#### Gerenciamento de usuário/Grupo (adicionar/atualizar/excluir)/Provisionamento baseado em usuário, Registro de dispositivo

**Tipo:** recursos comuns

**Disponibilidade:**


| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| ![Verificação][12]| ![Verificação][12]| ![Verificação][12]| ![Verificação][12]|

**Mais detalhes:**

- [Administrar seu diretório do Azure AD](active-directory-administer.md)
- [Visão geral do registro de dispositivos do Active Directory do Azure](active-directory-conditional-access-device-registration-overview.md)




#### SSO (Logon único)

**Tipo:** recursos comuns


**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| 10 aplicativos por usuário [1] (aplicativos SaaS pré-integrados e integrados pelo desenvolvedor)| 10 aplicativos por usuário [1] (camada gratuita + aplicativos de proxy de aplicativo) | Sem limite [2] (gratuito, camadas Básicas + modelos da Integração de Aplicativos de Autoatendimento)| 10 aplicativos por usuário [1] (aplicativos SaaS pré-integrados e integrados pelo desenvolvedor)|

[1] Com o AD do Azure Gratuito e o AD do Azure Básico, os usuários finais que receberam acesso a aplicativos SaaS podem ver até dez aplicativos em seu Painel de Acesso e ter acesso com SSO a eles. Os administradores podem configurar SSO e atribuir acesso do usuário a tantos aplicativos SaaS quantos quiserem com Gratuito e Básico; no entanto, os usuários finais só verão 10 aplicativos em seu Painel de Acesso de cada vez.

[2] Integração de autoatendimento de qualquer aplicativo que dê suporte a autenticação baseada em formulários, a SCIM ou a SAML usando modelos fornecidos no menu da galeria de aplicativos. Para mais detalhes, confira [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Active Directory do Azure](active-directory-saas-custom-apps.md)

**Mais detalhes:**

- [Gerenciando aplicativos com o AD do Azure (Active Directory do Azure)](active-directory-enable-sso-scenario.md)



#### Alteração de senhas por autoatendimento para usuários de nuvem

**Tipo:** recursos comuns

**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| ![Verificação][12]| ![Verificação][12]| ![Verificação][12]| ![Verificação][12]|

**Mais detalhes:**

- [Como atualizar sua própria senha](active-directory-passwords-update-your-own-password.md)




#### Connect (Mecanismo de sincronização que amplia os diretórios locais para o Active Directory do Azure) 

**Tipo:** recursos comuns


**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| ![Verificação][12]| ![Verificação][12]| ![Verificação][12]| ![Verificação][12]|

**Mais detalhes:**

- [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)



#### Relatórios de segurança/uso

**Tipo:** recursos comuns


**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| Três relatórios básicos| Três relatórios básicos| Relatórios avançados| Três relatórios básicos|

**Mais detalhes:**

- [Exibir relatórios de acesso e uso](active-directory-view-access-usage-reports.md)




## Recursos básicos e Premium
#### Gerenciamento/provisionamento de acesso baseado em grupos

**Tipo:** recursos básicos


**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| | ![Verificação][12]| ![Verificação][12]| |

**Mais detalhes:**

- [Uso de um grupo para gerenciar o acesso a aplicativos SaaS](active-directory-accessmanagement-group-saasapps.md)



#### Redefinição de Senha por Autoatendimento para usuários de nuvem

**Tipo:** recursos básicos


**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| | ![Verificação][12]| ![Verificação][12]| ![Verificação][12]|

**Mais detalhes:**

- [Redefinição de senha do AD do Azure para usuários e administradores](active-directory-passwords.md)



#### Identidade visual da empresa (Páginas de logon/Personalização do painel de acesso)

**Tipo:** recursos básicos


**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| | ![Verificação][12]| ![Verificação][12]| ![Verificação][12]|

**Mais detalhes:**

- [Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso](active-directory-add-company-branding.md)



#### Proxy de Aplicativo

**Tipo:** recursos básicos


**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| | ![Verificação][12]| ![Verificação][12]| |

**Mais detalhes:**

- [Como fornecer acesso remoto seguro a aplicativos locais](active-directory-application-proxy-get-started.md)



#### SLA de 99,9%

**Tipo:** recursos básicos


**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| | ![Verificação][12]| ![Verificação][12]| ![Verificação][12]|

**Mais detalhes:**

- [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/)




## Recursos premium
#### Gerenciamento de grupo de autoatendimento/Adições de aplicativo de autoatendimento/ Grupos dinâmicos

**Tipo:** recursos premium


**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| | | ![Verificação][12]| |




         
#### Redefinição/Alteração/Desbloqueio de senha de autoatendimento com write-back local

**Tipo:** recursos premium


**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| | | ![Verificação][12]| |





#### Multi-Factor Authentication (nuvem e local [Servidor MFA])

**Tipo:** recursos premium


**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| | | ![Verificação][12]| Limitado à nuvem somente para aplicativos do Office 365|

**Mais detalhes:**

- [O que é o Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)



#### CAL MIM + Servidor MIM 

Direitos de software de servidor Microsoft Identity Manager são concedidos com licenças do Windows Server (qualquer edição). Como o Microsoft Identity Manager é executado no Sistema Operacional do Windows Server, desde que o servidor esteja executando uma cópia válida e licenciada do Windows Server, o Microsoft Identity Manager pode ser instalado e utilizado nesse servidor. Nenhuma outra licença separada é necessária para o servidor do Microsoft Identity Manager.

**Tipo:** recursos premium


**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| | | ![Verificação][12]| |





#### Cloud App Discovery 

**Tipo:** recursos premium


**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| | | ![Verificação][12]| |

**Mais detalhes:**

- [Encontrando aplicativos em nuvem não gerenciados com o Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)



#### Connect Health

**Tipo:** recursos premium


**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| | | ![Verificação][12]| |

**Mais detalhes:**

- [Monitorar infraestrutura de identidade local e serviços de sincronização na nuvem](active-directory-aadconnect-health.md)



#### Substituição automática de senha para contas de grupo

**Tipo:** recursos premium


**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| | | ![Verificação][12]| |




## Ingresso do Active Directory do Azure - apenas para recursos relacionados ao Windows 10
#### Adicione um dispositivo para AD do Azure, Desktop SSO, Microsoft Passport para AD do Azure, recuperação do Administrator Bitlocker

**Tipo:** Ingresso do Active Directory do Azure – apenas para recursos relacionados ao Windows 10


**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| ![Verificação][12]| ![Verificação][12]| ![Verificação][12]| ![Verificação][12]|




#### Registro automático do MDM, recuperação de autoatendimento do Bitlocker, administração local extra para dispositivos do Windows 10 via Ingresso no AD do Azure

**Tipo:** Ingresso do Active Directory do Azure – apenas para recursos relacionados ao Windows 10


**Disponibilidade:**

| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| :-: | :-: | :-: | :-: |
| | | ![Verificação][12]| |




## Recursos de visualização do AD do Azure
Além dos recursos disponíveis das edições Gratuita, Básico e Premium, o AD do Azure também fornece um conjunto de recursos de visualização. Você pode usar os recursos de visualização para obter uma impressão das novidades que serão lançadas no futuro próximo e para determinar se esses recursos podem ajudar a melhorar seu ambiente.

**Recursos de visualização disponíveis:**

- [Colaboração B2B](active-directory-b2b-collaboration-overview.md)
- [Unidades Administrativas](active-directory-administrative-units-management.md)
- Privileged Identity Management
- [Integração de aplicativo de RH](active-directory-saas-workday-inbound-tutorial.md)
- [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)





## O que vem a seguir

- [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso](active-directory-add-company-branding.md)
- [Exibir relatórios de acesso e de uso](active-directory-view-access-usage-reports.md)


<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png

<!---HONumber=AcomDC_0518_2016-->