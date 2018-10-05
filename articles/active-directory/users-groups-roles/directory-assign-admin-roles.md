---
title: Permissões da função de administrador do Azure Active Directory | Microsoft Docs
description: Uma função de administrador pode adicionar usuários, atribuir funções administrativas, redefinir senhas de usuário, gerenciar licenças de usuário ou gerenciar domínios.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/25/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 293d8376d83d729588aab0aeaa1040d9b3e5e0b5
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182273"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Permissões da função de administrador no Azure Active Directory

Usando o Azure Active Directory (Azure AD), você pode designar administradores separados para atender a diferentes funções. Os administradores podem ser designados no portal do Azure AD para realizar tarefas como adicionar ou alterar os usuários, atribuir funções administrativas, redefinir senhas de usuário, gerenciar licenças de usuário e gerenciando nomes de domínio.

O administrador global tem acesso a todos os recursos administrativos. Por padrão, a pessoa que se inscreve para uma assinatura do Azure recebe a função de Administrador Global para o diretório. Somente Administradores Globais podem delegar funções de administrador.

## <a name="assign-or-remove-administrator-roles"></a>Atribuir ou remover funções de administrador

Para saber como atribuir funções administrativas a um usuário no Azure Active Directory, veja [Exibir e atribuir funções de administrador no Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Funções disponíveis

As seguintes funções de administrador estão disponíveis:

* **[Administrador de Aplicativos](#application-administrator)**: usuários nesta função podem criar e gerenciar todos os aspectos de aplicativos corporativos, registros de aplicativo e as configurações de proxy de aplicativo. Essa função também concede a capacidade de consentimento para permissões delegadas e permissões do aplicativo excluindo o Microsoft Graph e o Microsoft Azure AD Graph. Os membros dessa função não são adicionados como proprietários, ao criar novos registros de aplicativo ou aplicativos empresariais.

* **[Desenvolvedor de aplicativos](#application-developer)**: os usuários nessa função podem criar registros de aplicativos quando a configuração "Usuários podem registrar aplicativos" estiver definida como Não. Essa função também permite que os membros consintam em nome próprio quando a configuração "Usuários podem consentir em aplicativos acessando dados da empresa em seu nome" está definida como Não. Os membros dessa função são adicionados como proprietários, ao criar novos registros de aplicativo ou aplicativos empresariais.

* **[Administrador de Cobrança](#billing-administrator)**: faz compras, gerencia as assinaturas, gerencia tíquetes de suporte e monitora a integridade do serviço.

* **[Administrador de Aplicativos de Nuvem](#cloud-application-administrator)**: os usuários nesta função têm as mesmas permissões que a função de administrador do aplicativo, exceto a capacidade de gerenciar o proxy de aplicativo. Essa função concede a capacidade de criar e gerenciar todos os aspectos de aplicativos corporativos e os registros do aplicativo. Essa função também concede a capacidade de consentimento para permissões delegadas e permissões do aplicativo excluindo o Microsoft Graph e o Microsoft Azure AD Graph. Os membros dessa função não são adicionados como proprietários, ao criar novos registros de aplicativo ou aplicativos empresariais.

* **[Administrador do Dispositivo de Nuvem](#cloud-device-administrator)**: os usuários nessa função podem habilitar, desabilitar e excluir dispositivos no Azure AD e ler as chaves do BitLocker do Windows 10 (se houver) no portal do Azure. A função não concede permissões para gerenciar nenhuma outra propriedade no dispositivo.

* **[Administrador de Conformidade](#compliance-administrator)**: os usuários com essa função têm permissões de gerenciamento no Centro de Segurança e Conformidade do Office 365 e no Centro de Administração do Exchange. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Administrador de acesso condicional](#conditional-access-administrator)**: os usuários com essa função têm a capacidade de gerenciar configurações de acesso condicional do Azure Active Directory.
  > [!NOTE]
  > Para implantar a política de acesso condicional do Exchange ActiveSync no Azure, o usuário também deve ser um Administrador Global.
  
* **[Administradores de dispositivo](#device-administrators)**: essa função está disponível para atribuição apenas como um administrador local adicional nas [Configurações do dispositivo](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Os usuários com essa função se tornam administradores de computador local em todos os dispositivos Windows 10 associados ao Azure Active Directory. Eles não têm a capacidade de gerenciar objetos de dispositivos no Azure Active Directory. 

* **[Leitores de Diretório](#directory-readers)**: esta é uma função herdada que deve ser atribuída aos aplicativos que não dão suporte a [Estrutura de Consentimento](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Ele não deve ser atribuído a nenhum usuário.

* **[Contas de Sincronização de Diretório](#directory-synchronization-accounts)**: não use. Essa função é automaticamente atribuída ao serviço do Azure AD Connect e não tem intenção ou suporte para outros usos.

* **[Gravadores de Diretório](#directory-writers)**: esta é uma função herdada que deve ser atribuída aos aplicativos que não dão suporte a [Estrutura de Consentimento](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Ele não deve ser atribuído a nenhum usuário.

* **[ Administrador de serviço do Dynamics 365 / CRM Service Administrator](#dynamics-365-service-administrator)**: os usuários com essa função têm permissões globais no Microsoft Dynamics 365 Online, quando o serviço está presente, bem como a capacidade de gerenciar tickets de suporte e monitorar a saúde do serviço. Mais informações em [usar a função de administrador de serviço para gerenciar seu locatário](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

* **[Administrador de serviços do Exchange](#exchange-service-administrator)**: os usuários com essa função têm permissões globais no Microsoft Exchange Online quando o serviço está presente. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Administrador Global/Administrador da Empresa](#company-administrator)**: os usuários com essa função têm acesso a todos os recursos administrativos do Azure Active Directory, bem como serviços que usam as identidades do Azure Active Directory assim como o Exchange Online, o SharePoint Online e o Skype for Business Online. A pessoa que se inscreve no locatário do Azure Active Directory torna-se um administrador global. Somente os administradores globais podem atribuir outras funções de administrador. Pode haver mais de um administrador global na sua empresa. Administradores globais podem redefinir a senha para qualquer usuário e todos os outros administradores.

  > [!NOTE]
  > Na API do Graph da Microsoft, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Administrador da Empresa". É "Administrador Global" no [portal do Azure](https://portal.azure.com).
  >
  >

* **[Emissor do Convite ao Convidado](#guest-inviter)**: os usuários nessa função podem gerenciar convites para usuários convidados do Azure Active Directory B2B quando a configuração de usuário **Membros podem convidar** está definida como Não. Mais informações sobre a colaboração B2B em [Sobre a colaboração B2B do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Ela não inclui nenhuma outra permissão.

* **[Administrador da Proteção de Informações](#information-protection-administrator)**: Usuários com esta função têm todas as permissões no serviço de Proteção de Informações do Azure. Esta função pode configurar rótulos para a política da Proteção de Informações do Azure, gerenciar modelos de proteção e ativar a proteção. Esta função não garante permissões de usuário no Identity Protection Center, Privileged Identity Management, Monitorar Integridade de Serviço do Office 365 ou Centro de Segurança e Conformidade do Office 365.

* **[Administrador de Serviços do Intune](#intune-service-administrator)**: os usuários com essa função têm permissões globais no Microsoft Intune Online quando o serviço está presente. Além disso, essa função contém a capacidade de gerenciar usuários e dispositivos para associar a política, bem como criar e gerenciar grupos. Mais informações em [Controle de administração baseada em função (RBAC) com o Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)

* **[Administrador de licenças](#license-administrator)**: os usuários nesta função podem adicionar, remover e atualizar as atribuições de licença em usuários e grupos (usando o licenciamento baseado em grupo), além de gerenciar o local de uso nos usuários. A função não concede a capacidade de comprar ou gerenciar assinaturas, criar ou gerenciar grupos, ou criar ou gerenciar usuários além do local de uso.

* **[Leitor do Centro de Mensagens](#message-center-reader)**: os usuários que têm essa função podem monitorar as notificações e atualizações de consultoria de integridade no [Centro de Mensagens do Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) da sua organização sobre serviços configurados como o Exchange, o Intune e o Microsoft Teams. Os Leitores do Centro de Mensagens recebem por email resumos semanais de postagens, atualizações e podem compartilhar postagens do Centro de Mensagens no Office 365. No Azure AD, os usuários atribuídos a essa função terão acesso somente leitura aos serviços do Azure AD como usuários e grupos. 

* **[Suporte ao parceiro de Nível 1](#partner-tier1-support)**: não use. Essa função foi substituída e será removida do Azure AD no futuro. Essa função é destinada a um pequeno número de parceiros de revenda da Microsoft e não se destina ao uso geral.

* **[Suporte ao parceiro de Nível 2](#partner-tier2-support)**: não use. Essa função foi substituída e será removida do Azure AD no futuro. Essa função é destinada a um pequeno número de parceiros de revenda da Microsoft e não se destina ao uso geral.

* **[Administrador de Senhas/Administrador de Suporte Técnico](#helpdesk-administrator)**: usuários com essa função podem alterar senhas, gerenciar solicitações de serviço e monitorar a integridade do serviço. Os administradores de Suporte Técnico podem alterar senhas e invalidar tokens de atualização apenas para usuários e outros administradores de Suporte Técnico. Invalidar um token de atualização força o usuário a entrar novamente.

  > [!NOTE]
  > Na API do Graph da Microsoft, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Administrador da Assistência Técnica". Ele é um "Administrador de Senha" no [Portal do Azure](https://portal.azure.com/).
  >
  >
  
* **[Administrador de Serviços do Power BI](#power-bi-service-administrator)**: os usuários com essa função têm permissões globais no Microsoft Power BI quando o serviço está presente, além da capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [Noções básicas sobre a função de administrador do Power BI](https://docs.microsoft.com/power-bi/service-admin-role).

* **[Administrador da função com privilégios](#privileged-role-administrator)**: os usuários com essa função podem gerenciar atribuições de função no Azure Active Directory, bem como dentro do Azure AD Privileged Identity Management. Além disso, essa função permite o gerenciamento de todos os aspectos do Privileged Identity Management.

* **[Leitor de Relatórios](#reports-reader)**: usuários com essa função podem exibir dados de relatórios de uso e o painel de relatórios no centro de administração do Office 365 e o pacote do contexto de adoção no Power BI. Além disso, a função fornece acesso a relatórios e atividades de logon no Azure AD e a dados retornados pela API de relatórios do Microsoft Graph. Um usuário atribuído à função Leitor de Relatórios pode acessar somente o uso relevante e as métricas de adoção. Ele não tem permissões de administrador para definir configurações ou acessar os centros de administração específico ao produto como o Exchange. 

* **[Administrador de Segurança](#security-administrator)**: os usuários com essa função têm todas as permissões somente leitura da função de Leitor de segurança, mais a capacidade de gerenciamento de configuração de serviços relacionados à segurança: Azure Active Directory Identity Protection, Proteção de Informações do Azure e Centro de Segurança e Conformidade do Office 365. Mais informações sobre permissões do Office 365 estão disponíveis em [Permissões no Centro de Conformidade de Segurança do Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  | No | O que ele pode fazer |
  | --- | --- |
  | Identity Protection Center |<ul><li>Todas as permissões da função Leitor de Segurança.<li>Além disso, a capacidade de executar todas as operações de IPC, exceto para a redefinição de senhas. |
  | Privileged Identity Management |<ul><li>Todas as permissões da função Leitor de Segurança.<li>**Não é possível** gerenciar associações de função ou configurações do Azure AD. |
  | <p>Monitorar a integridade do serviço Office 365</p><p>Centro de Conformidade e Segurança do Office 365 |<ul><li>Todas as permissões da função Leitor de Segurança.<li>Pode configurar todas as configurações no recurso de Proteção avançada contra ameaças (proteção contra malware e vírus, configuração de URL mal-intencionado, rastreamento de URL, etc). |
  
* **[Leitor de Segurança](#security-reader)**: os usuários com essa função têm acesso somente leitura global, incluindo todas as informações no Azure Active Directory, Identity Protection e Privileged Identity Management, bem como a capacidade de ler relatórios de entrada do Azure Active Directory e logs de auditoria. A função também concede permissão somente leitura no Centro de Conformidade e Segurança do Office 365. Mais informações sobre permissões do Office 365 estão disponíveis em [Permissões no Centro de Conformidade de Segurança do Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  | No | O que ele pode fazer |
  | --- | --- |
  | Identity Protection Center |Ler todos os relatórios de segurança e informações de configurações para recursos de segurança<ul><li>Anti-spam<li>Criptografia<li>Prevenção de perda de dados<li>Antimalware<li>Proteção avançada contra ameaças<li>Antiphishing<li>Regras de fluxo de mensagens |
  | Privileged Identity Management |<p>Tem acesso somente leitura a todas as informações exibidas no Azure AD PIM: políticas e relatórios de atribuições de função do Azure AD, análises de segurança e, no futuro, acesso de leitura aos dados de política e relatórios para cenários além da atribuição de função do Azure AD.<p>**Não pode** se inscrever no Azure AD PIM nem fazer alterações nele. No portal do PIM ou por meio do PowerShell, alguém nesta função poderá ativar funções adicionais (por exemplo, administrador global ou administrador com função com privilégios) se o usuário for um candidato a elas. |
  | <p>Monitorar a integridade do serviço Office 365</p><p>Centro de Conformidade e Segurança do Office 365</p> |<ul><li>Ler e gerenciar alertas<li>Ler políticas de segurança<li>Ler informações sobre inteligência contra ameaças, Cloud App Discovery e quarentena em Pesquisar e Investigar<li>Ler todos os relatórios |

* **[Administrador de Suporte do Serviço](#service-support-administrator)**: os usuários com essa função podem abrir solicitações de suporte com a Microsoft para serviços do Azure e do Office 365, além de exibições de painel de serviço e do centro de mensagens no Portal do Azure e no portal de administração do Office 365. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Administrador de Serviços do SharePoint](#sharepoint-service-administrator)**: os usuários com essa função têm permissões globais no Microsoft SharePoint Online quando o serviço está presente, além da capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Administrador de Serviços do Lync/Skype for Business](#skype-for-business-administrator)**: os usuários com essa função têm permissões globais no Microsoft Skype for Business quando o serviço está presente, além de gerenciar atributos de usuário específicos do Skype no Azure Active Directory. Além disso, essa função concede a capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço, além de acessar o centro de administração do Skype for Business e do Teams. A conta também deve ser licenciada para o Teams ou não poderá executar os cmdlets do PowerShell do Teams. Mais informações em [Sobre a função de administrador do Skype for Business](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) e informações de licenciamento do Teams em [licenciamento de complemento do Skype for Business e Microsoft Teams](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > Na API do Graph da Microsoft, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Administrador de Serviços do Lync". Ela equivale a "Administrador de Serviços do Skype for Business" no [Portal do Azure](https://portal.azure.com/).
  >
  >

* **[Administrador de Comunicações do Teams](#teams-communications-administrator)**: os usuários nesta função podem gerenciar aspectos de cargas de trabalho do Microsoft Teams relacionadas a telefonia e voz. Isso inclui as ferramentas de gerenciamento para atribuição de número de telefone, políticas de reuniões e voz e acesso completo ao conjunto de ferramentas de análise de chamada.

* **[Engenheiro de Suporte de Comunicações do Teams](#teams-communications-support-engineer)**: os usuários nesta função podem solucionar problemas de comunicação no Microsoft Teams e no Skype for Business usando as ferramentas de solução de problemas de chamada do usuário no centro de administração do Microsoft Teams e no Skype for Business. Os usuários nesta função podem exibir informações do registro de chamadas completas para todos os participantes envolvidos.

* **[Especialista em Suporte de Comunicações do Teams](#teams-communications-support-specialist)**: os usuários nesta função podem solucionar problemas de comunicação no Microsoft Teams e no Skype for Business usando as ferramentas de solução de problemas de chamada do usuário no centro de administração do Microsoft Teams e no Skype for Business. Os usuários nessa função só podem exibir detalhes do usuário na chamada para o usuário específico que eles pesquisaram.

* **[Administrador de Serviços do Teams](#teams-service-administrator)**: os usuários nessa função podem gerenciar todos os aspectos da carga de trabalho do Microsoft Teams usando o centro de administração do Microsoft Teams e do Skype for Business e os respectivos módulos do PowerShell. Isso inclui, entre outras áreas, todas as ferramentas de gerenciamento relacionadas a telefonia, mensagens, reuniões e às próprias equipes. Essa função também permite gerenciar grupos do Office 365.

* **[Administrador da Conta de Usuário](#user-account-administrator)**: os usuários com essa função podem criar e gerenciar todos os aspectos de usuários e grupos. Além disso, essa função inclui a capacidade de gerenciar tíquetes de suporte e monitora a integridade do serviço. Algumas restrições se aplicam. Por exemplo, essa função não permite a exclusão de um administrador global. Os Administradores da conta de usuário podem alterar senhas e invalidar tokens de atualização para usuários, administradores de suporte técnico e outros Administradores de contas de usuários. Invalidar um token de atualização força o usuário a entrar novamente.

| O que ele pode fazer | O que não pode fazer |
| --- | --- |
| <p>Exibir informações da empresa e do usuário</p><p>Gerenciar tíquetes de suporte do Office</p><p>Alterar senhas somente de usuários, administradores de Assistência Técnica e outros administradores de Contas de Usuário</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Criar, editar e excluir usuários e grupos e gerenciar licenças de usuário, com limitações. Eles não podem excluir um administrador global ou criar outros administradores.</p> |<p>Executar operações de cobrança e compra de produtos do Office</p><p>Gerenciar domínios</p><p>Editar informações da empresa</p><p>Delegar funções administrativas a outros</p><p>Usar sincronização de diretório</p><p>Habilitar ou desabilitar autenticação multifator</p><p>Exibir logs de auditoria</p> |

As tabelas a seguir descrevem as permissões específicas no Azure Active Directory fornecidas a cada função. Algumas funções podem ter permissões adicionais nos serviços da Microsoft fora do Azure Active Directory.

## <a name="adhoc-license-administrator"></a>Administrador de Licenças AdHoc
Pode criar e gerenciar todos os aspectos de registros de aplicativo e aplicativos empresariais.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/domains/default/read | Leia as propriedades básicas em domínios no Active Directory do Azure. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Leia a propriedade groups.appRoleAssignments no Active Directory do Azure. |
| microsoft.aad.directory/groups/default/read | Leia as propriedades básicas em grupos no Active Directory do Azure. |
| microsoft.aad.directory/groups/memberOf/read | Leia a propriedade groups.memberOf no Active Directory do Azure. |
| microsoft.aad.directory/groups/members/read | Leia a propriedade groups.members no Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Leia a propriedade groups.owners no Active Directory do Azure. |
| microsoft.aad.directory/groups/settings/read | Leia a propriedade groups.settings no Active Directory do Azure. |
| microsoft.aad.directory/oAuth2PermissionGrants/default/read | Leia as propriedades básicas em oAuth2PermissionGrants no Active Directory do Azure. |
| microsoft.aad.directory/oAuth2PermissionGrants/update | Atualize o oAuth2PermissionGrants no Active Directory do Azure. |
| microsoft.aad.directory/organization/default/read | Leia as propriedades básicas da organização no Active Directory do Azure. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Leia a propriedade organization.trustedCAsForPasswordlessAuth no Active Directory do Azure. |
| microsoft.aad.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.aad.directory/umicrosoft.aad.directory/users/appRoleAssignments/readsers/appRoleAssignments/read | Leia a propriedade users.appRoleAssignments no Active Directory do Azure. |
| microsoft.aad.directory/users/default/read | Leia as propriedades básicas dos usuários no Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Leia a propriedade users.directReports no Active Directory do Azure. |
| microsoft.aad.directory/users/invitedBy/read | Leia a propriedade users.invitedBy no Active Directory do Azure. |
| microsoft.aad.directory/users/invitedUsers/read | Leia a propriedade users.invitedUsers no Active Directory do Azure. |
| microsoft.aad.directory/users/manager/read | Leia a propriedade users.manager no Active Directory do Azure. |
| microsoft.aad.directory/users/memberOf/read | Leia a propriedade users.memberOf no Active Directory do Azure. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Leia a propriedade users.oAuth2PermissionGrants no Active Directory do Azure. |
| microsoft.aad.directory/users/ownedDevices/read | Leia a propriedade users.ownedDevices no Active Directory do Azure. |
| microsoft.aad.directory/users/ownedObjects/read | Leia a propriedade users.ownedObjects no Active Directory do Azure. |
| microsoft.aad.directory/users/registeredDevices/read | Leia a propriedade users.registeredDevices no Active Directory do Azure. |

## <a name="application-administrator"></a>Administrador de aplicativos
Pode criar e gerenciar todos os aspectos de registros de aplicativo e aplicativos empresariais.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Atualize a propriedade applications.audience no Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Atualize a propriedade applications.authentication no Azure Active Directory. |
| microsoft.aad.directory/applications/default/update | Atualize as propriedades básicas dos aplicativos no Active Directory do Azure. |
| microsoft.aad.directory/applications/create | Crie aplicativos no Active Directory do Azure. |
| microsoft.aad.directory/applications/credentials/update | Atualize a propriedade applications.credentials no Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Excluir aplicativos no Active Directory do Azure. |
| microsoft.aad.directory/applications/owners/update | Atualize a propriedade applications.owners no Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Atualize a propriedade applications.permissions no Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Atualize a propriedade applications.policies no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Crie appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Leia appRoleAssignments no Active Directory do Azure. |
| microsoft.aad.directory/appRoleAssignments/update | Atualize o appRoleAssignments no Active Directory do Azure. |
| microsoft.aad.directory/appRoleAssignments/delete | Exclua appRoleAssignments em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/default/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/default/update | Atualize policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Crie políticas no Active Directory do Azure. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Exclua policies em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Atualize policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/update | Atualize as propriedades básicas em servicePrincipals no Active Directory do Azure. |
| microsoft.aad.directory/servicePrincipals/create | Criar servicePrincipals em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Excluir servicePrincipals em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Atualizar servicePrincipals.appRoleAssignments property em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Atualizar servicePrincipals.owners property em Azure Active Directory. |
| microsoftmicrosoft.aad.directory/servicePrincipals/policies/update.aad.directory/servicePrincipals/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.aad.reports/allEntities/read | Ler Relatórios do Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="application-developer"></a>Desenvolvedor de aplicativos
Pode criar registros de aplicativos independentemente da configuração "Usuários podem registrar aplicativos".

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Crie aplicativos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Crie appRoleAssignments no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Crie oAuth2PermissionGrants no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Criar servicePrincipals em Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |

## <a name="billing-administrator"></a>Administrador de cobrança
Pode executar tarefas comuns de relacionadas à cobrança, como atualizar informações de pagamento.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft.AAD.Directory/Organization/defMicrosoft.AAD.Directory/Organization/default/Updateault/Update | Atualize as propriedades básicas em organização no Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Atualize a propriedade organization.trustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gerencie todos os aspectos de cobrança do Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="cloud-application-administrator"></a>Administrador de Aplicativos de Nuvem
Pode criar e gerenciar todos os aspectos de registros de aplicativo e aplicativos empresariais, exceto o Proxy de Aplicativo.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Atualize a propriedade applications.audience no Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Atualize a propriedade applications.authentication no Azure Active Directory. |
| microsoft.aad.directory/applications/default/update | Atualize as propriedades básicas dos aplicativos no Active Directory do Azure. |
| microsoft.aad.directory/applications/create | Crie aplicativos no Active Directory do Azure. |
| microsoft.aad.directory/applications/credentials/update | Atualize a propriedade applications.credentials no Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Excluir aplicativos no Active Directory do Azure. |
| microsoft.aad.directory/applications/owners/update | Atualize a propriedade applications.owners no Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Atualize a propriedade applications.permissions no Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Atualize a propriedade applications.policies no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Crie appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Atualize o appRoleAssignments no Active Directory do Azure. |
| microsoft.aad.directory/appRoleAssignments/delete | Exclua appRoleAssignments em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Crie políticas no Active Directory do Azure. |
| microsoft.aad.directory/policies/applicationConfiguration/default/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/default/update | Atualize policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Exclua policies em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Atualize policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Atualizar servicePrincipals.appRoleAssignments property em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/update | Atualize as propriedades básicas em servicePrincipals no Active Directory do Azure. |
| microsoft.aad.directory/servicePrincipals/create | Criar servicePrincipals em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Excluir servicePrincipals em Azure Active Directory. |
| microsoft.aad.dimicrosoft.aad.directory/servicePrincipals/owners/updaterectory/servicePrincipals/owners/update | Atualizar servicePrincipals.owners property em Azure Active Directory. |
| microsoftmicrosoft.aad.directory/servicePrincipals/policies/update.aad.directory/servicePrincipals/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.aad.reports/allEntities/read | Ler Relatórios do Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="cloud-device-administrator"></a>Administrador de Dispositivo de Nuvem
Acesso completo para gerenciar os dispositivos no Azure AD.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft.AAD.Directory/Devices/Delete | Exclua dispositivos no Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/Disable | Desabilite dispositivos no Azure Active Directory. |
| microsoft.aad.directory/devices/enable | Habilite dispositivos no Azure Active Directory. |
| microsoft.aad.reports/allEntities/read | Ler Relatórios do Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

## <a name="company-administrator"></a>Administradores de Empresa
Pode gerenciar todos os aspectos do Azure AD e dos serviços da Microsoft que usam identidades do Azure AD.

  > [!NOTE]
  > Essa função herda permissões adicionais da função.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Criar e excluir administrativeUnits e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/applications/allProperties/allTasks | Criar e excluir aplicativos e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Criar e excluir appRoleAssignments e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Criar e excluir contatos e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Criar e excluir contratos e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/devices/allProperties/allTasks | Criar e excluir dispositivos e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Criar e excluir DirectoryRoles, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Criar e excluir DirectoryRoleTemplates, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/domains/allProperties/allTasks | Criar e excluir Domínios, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/groups/allProperties/allTasks | Criar e excluir Grupos, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Criar e excluir groupSettings e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Criar e excluir groupSettingTemplates e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Criar e excluir loginTenantBranding e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Criar e excluir oAuth2PermissionGrants e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/organization/allProperties/allTasks | Criar e excluir organização e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/policies/allProperties/allTasks | Criar e excluir políticas, ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Criar e excluir roleAssignments e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Criar e excluir roleDefinitions e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Criar e excluir scopedRoleMemberships e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/serviceAction/activateService | Pode executar a ação de serviço Activateservice no Azure Active Directory |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Pode executar a ação de serviço Disabledirectoryfeature no Azure Active Directory |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Pode executar a ação de serviço Enabledirectoryfeature no Azure Active Directory |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Pode executar a ação de serviço Getavailableextentionproperties no Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Criar e excluir servicePrincipals e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Criar e excluir subscribedSkus e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/users/allProperties/allTasks | Criar e excluir usuários e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Executar todas as ações no Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Ler todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.aad.reports/allEntities/allTasks | Ler e configurar Relatórios do Azure AD. |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerencie todos os aspectos da proteção de informações do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gerencie todos os aspectos de cobrança do Office 365. |
| microsoft.intune/allEntities/allTasks | Gerencie todos os aspectos do Intune. |
| Microsoft.office365.complianceManager/allEntities/allTasks | Gerenciar todos os aspectos do Gerenciador de conformidade do Office 365 |
| Microsoft.office365.Exchange/allEntities/allTasks | Gerencie todos os aspectos do Exchange Online. |
| Microsoft.office365.lockbox/allEntities/allTasks | Gerenciar todos os aspectos do Cofre de cliente do Office 365 |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gerencie todos os aspectos do Power BI. |
| Microsoft.office365.protectionCenter/allEntities/allTasks | Gerencie todos os aspectos do Centro de proteção do Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.office365.sharepoint. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspectos do Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gerencie todos os aspectos do Dynamics 365. |

## <a name="compliance-administrator"></a>Administrador de conformidade
Pode ler e gerenciar a configuração de conformidade e relatórios no Azure AD e no Office 365.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| Microsoft.office365.complianceManager/allEntities/allTasks | Gerenciar todos os aspectos do Gerenciador de conformidade do Office 365 |
| Microsoft.office365.Exchange/allEntities/allTasks | Gerencie todos os aspectos do Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.office365.sharepoint. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspectos do Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="conditional-access-administrator"></a>Administrador de acesso condicional
Pode gerenciar os recursos de acesso condicional.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/default/read | Ler a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/default/update | Atualize a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/create | Crie políticas no Active Directory do Azure. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Exclua policies em Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Ler a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Atualize a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Ler a propriedade policies.conditionalAccess no Azure Active Directory. |

## <a name="customer-lockbox-access-approver"></a>Aprovador de acesso do cofre do cliente
Pode aprovar solicitações de suporte da Microsoft para acessar dados organizacionais do cliente.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| Microsoft.office365.lockbox/allEntities/allTasks | Gerenciar todos os aspectos do Cofre de cliente do Office 365 |

## <a name="device-administrators"></a>Administradores de Dispositivo
Os membros dessa função são adicionados ao grupo Administradores local em dispositivos que ingressaram no AD do Azure.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/groupSettings/default/read | Ler as propriedades básicas no groupSettings no Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/default/read | Ler as propriedades básicas no groupSettingTemplates no Azure Active Directory. |

## <a name="device-managers"></a>Gerenciadores de Dispositivo
Pode aprovar solicitações de suporte da Microsoft para acessar dados organizacionais do cliente.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft.AAD.Directory/Devices/default/Read | Ler as propriedades básicas em dispositivos no Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/default/Update | Atualize as propriedades básicas em dispositivos no Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Ler a propriedade de memberOf no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Ler a propriedade registeredowners no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Atualize a propriedade registeredowners no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Ler a propriedade registeredusers no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Atualize a propriedade registeredusers no Azure Active Directory. |

## <a name="directory-readers"></a>Leitores de Diretório
Pode ler informações básicas do diretório. Solucionar problemas de acesso a aplicativos

  > [!NOTE]
  > Essa função herda permissões adicionais da função.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/default/read | Ler as propriedades básicas em administrativeUnits no Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/members/read | Ler a propriedade Administrativeunits no Azure Active Directory. |
Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/default/Read | Ler as propriedades básicas em aplicativos do Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/Owners/Read | Ler a propriedade Owners no Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/default/Read | Ler as propriedades básicas em contatos no Azure Active Directory. |
| microsoft.aad.directory/contacts/memberOf/read | Ler a propriedade Contacts no Azure Active Directory. |
| Microsoft.AAD.Directory/Contracts/default/Read | Ler as propriedades básicas sobre os contratos no Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/default/Read | Ler as propriedades básicas em dispositivos no Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Ler a propriedade de memberOf no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Ler a propriedade registeredowners no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Ler a propriedade registeredusers no Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/default/read | Ler as propriedades básicas no directoryRoles no Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Ler a propriedade Eligiblemembers no Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Ler a propriedade Directoryroles no Azure Active Directory. |
| microsoft.aad.directory/domains/default/read | Leia as propriedades básicas em domínios no Active Directory do Azure. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Leia a propriedade groups.appRoleAssignments no Active Directory do Azure. |
| microsoft.aad.directory/groups/default/read | Leia as propriedades básicas em grupos no Active Directory do Azure. |
| microsoft.aad.directory/groups/memberOf/read | Leia a propriedade groups.memberOf no Active Directory do Azure. |
| microsoft.aad.directory/groups/members/read | Leia a propriedade groups.members no Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Leia a propriedade groups.owners no Active Directory do Azure. |
| microsoft.aad.directory/groups/settings/read | Leia a propriedade groups.settings no Active Directory do Azure. |
| microsoft.aad.directory/groupSettings/default/read | Ler as propriedades básicas no groupSettings no Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/default/read | Ler as propriedades básicas no groupSettingTemplates no Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/default/read | Leia as propriedades básicas em oAuth2PermissionGrants no Active Directory do Azure. |
| microsoft.aad.directory/organization/default/read | Leia as propriedades básicas da organização no Active Directory do Azure. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Leia a propriedade organization.trustedCAsForPasswordlessAuth no Active Directory do Azure. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Ler a propriedade Approleassignedto no Ler a propriedade Approleassignedto no Azure Active Directory.Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Ler a propriedade ServicePrincipals.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/read | Ler as propriedades básicas em entidades de serviço no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/default/read | Ler a propriedade servicePrincipals.oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Ler a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/default/read | Ler as propriedades básicas no subscribedSkus no Azure Active Directory. |
| microsoft.aad.directory/umicrosoft.aad.directory/users/appRoleAssignments/readsers/appRoleAssignments/read | Leia a propriedade users.appRoleAssignments no Active Directory do Azure. |
| microsoft.aad.directory/users/default/read | Leia as propriedades básicas dos usuários no Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Leia a propriedade users.directReports no Active Directory do Azure. |
| microsoft.aad.directory/users/invitedBy/read | Leia a propriedade users.invitedBy no Active Directory do Azure. |
| microsoft.aad.directory/users/invitedUsers/read | Leia a propriedade users.invitedUsers no Active Directory do Azure. |
| microsoft.aad.directory/users/manager/read | Leia a propriedade users.manager no Active Directory do Azure. |
| microsoft.aad.directory/users/memberOf/read | Leia a propriedade users.memberOf no Active Directory do Azure. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Leia a propriedade users.oAuth2PermissionGrants no Active Directory do Azure. |
| microsoft.aad.directory/users/ownedDevices/read | Leia a propriedade users.ownedDevices no Active Directory do Azure. |
| microsoft.aad.directory/users/ownedObjects/read | Leia a propriedade users.ownedObjects no Active Directory do Azure. |
| microsoft.aad.directory/users/registeredDevices/read | Leia a propriedade users.registeredDevices no Active Directory do Azure. |

## <a name="directory-synchronization-accounts"></a>Contas de sincronização de diretório
Apenas usado pelo serviço do Azure AD Connect.

  > [!NOTE]
  > Essa função herda permissões adicionais da função.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Atualize a propriedade organization.dirSync no Azure Active Directory. |
| Microsoft.AAD.Directory/Policies/Create | Crie políticas no Active Directory do Azure. |
| Microsoft.AAD.Directory/Policies/Delete | Exclua policies em Azure Active Directory. |
| Microsoft.AAD.Directory/Policies/default/Read | Ler as propriedades básicas em políticas no Azure Active Directory. |
| Microsoft.AAD.Directory/Policies/default/Update | Atualize as propriedades básicas em políticas no Azure Active Directory. |
| Microsoft.AAD.Directory/Policies/Owners/Read | Ler a propriedade Owners no Azure Active Directory. |
| Microsoft.AAD.Directory/Policies/Owners/Update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Ler a propriedade policies.policiesAppliedTo no Azure Active Directory. |
| microsoft.aad.directormicrosoft.aad.directory/servicePrincipals/appRoleAssignedTo/ready/servicePrincipals/appRoleAssignedTo/read | Ler a propriedade Approleassignedto no Ler a propriedade Approleassignedto no Azure Active Directory.Azure Active Directory. |
| microsoftmicrosoft.aad.directory/servicePrincipals/appRoleAssignedTo/update.aad.directory/servicePrincipals/appRoleAssignedTo/update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Ler a propriedade ServicePrincipals.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Atualizar servicePrincipals.appRoleAssignments property em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/read | Ler as propriedades básicas em entidades de serviço no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/update | Atualize as propriedades básicas em servicePrincipals no Active Directory do Azure. |
| microsoft.aad.directory/servicePrincipals/create | Criar servicePrincipals em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/default/read | Ler a propriedade servicePrincipals.oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.dimicrosoft.aad.directory/servicePrincipals/owners/updaterectory/servicePrincipals/owners/update | Atualizar servicePrincipals.owners property em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Ler a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Executar todas as ações no Azure AD Connect. |

## <a name="directory-writers"></a>Gravadores de diretório
Pode ler e gravar informações básicas do diretório. Solucionar problemas de acesso a aplicativos

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft.AAD.Directory/Groups/Create | Crie grupos no Active Directory do Azure. |
| microsoft.aad.directory/groups/createAsOwner | Crie grupos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/default/Update | Atualize as propriedades básicas nos grupos do Active Directory do Azure. |
| Microsoft.AAD.Directory/Groups/Members/Update | Atualize a propriedade Groups no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Owners/Update | Atualize a propriedade Owners no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Settings/Update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.aad.directory/groupSettings/default/update | Atualize as propriedades básicas em groupSettings no Azure Active Directory. |
| microsoft.aad.directory/groupSettings/create | Crie groupSettings no Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Exclua groupSettings no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/default/Update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Manager/Update | Atualize a propriedade Users no Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |

## <a name="dynamics-365-administrator"></a>Administrador do Dynamics 365
Pode gerenciar todos os aspectos do produto Dynamics 365. Anteriormente conhecido como Administrador de Serviços do CRM.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gerencie todos os aspectos do Dynamics 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="exchange-service-administrator"></a>Administrador de serviços do Exchange
Pode gerenciar todos os aspectos do produto Exchange.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| Microsoft.office365.Exchange/allEntities/allTasks | Gerencie todos os aspectos do Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="guest"></a>Convidado
Função padrão para usuários convidados. Pode ler um conjunto limitado de informações de diretório.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Usuário.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft.AAD.Directory/Applications/default/Read | Ler as propriedades básicas em aplicativos do Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/Owners/Read | Ler a propriedade Owners no Azure Active Directory. |
| microsoft.aad.directory/domains/default/read | Leia as propriedades básicas em domínios no Active Directory do Azure. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Leia a propriedade groups.appRoleAssignments no Active Directory do Azure. |
| microsoft.aad.directory/groups/default/read | Leia as propriedades básicas em grupos no Active Directory do Azure. |
| microsoft.aad.directory/groups/memberOf/read | Leia a propriedade groups.memberOf no Active Directory do Azure. |
| microsoft.aad.directory/groups/members/read | Leia a propriedade groups.members no Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Leia a propriedade groups.owners no Active Directory do Azure. |
| microsoft.aad.directory/groups/settings/read | Leia a propriedade groups.settings no Active Directory do Azure. |
| microsoft.aad.directory/organization/basicProfile/read | Ler informações de perfil básico de organização no Azure Active Directory. |
| microsoft.aad.directormicrosoft.aad.directory/servicePrincipals/appRoleAssignedTo/ready/servicePrincipals/appRoleAssignedTo/read | Ler a propriedade Approleassignedto no Ler a propriedade Approleassignedto no Azure Active Directory.Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Ler a propriedade ServicePrincipals.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/read | Ler as propriedades básicas em entidades de serviço no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/members/read | Ler a propriedade servicePrincipals.members no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/default/read | Ler a propriedade servicePrincipals.oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Ler a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.aad.directory/users/basicProfile/read | Ler a propriedade BasicProfile no Azure Active Directory. |
| microsoft.aad.directory/umicrosoft.aad.directory/users/appRoleAssignments/readsers/appRoleAssignments/read | Leia a propriedade users.appRoleAssignments no Active Directory do Azure. |
| microsoft.aad.directory/users/default/read | Leia as propriedades básicas dos usuários no Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Leia a propriedade users.directReports no Active Directory do Azure. |
| microsoft.aad.directory/users/eligibleMemberOf/read | Ler a propriedade users.eligibleMemberOf no Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Leia a propriedade users.invitedBy no Active Directory do Azure. |
| microsoft.aad.directory/users/invitedUsers/read | Leia a propriedade users.invitedUsers no Active Directory do Azure. |
| microsoft.aad.directory/users/manager/read | Leia a propriedade users.manager no Active Directory do Azure. |
| microsoft.aad.directory/users/memberOf/read | Leia a propriedade users.memberOf no Active Directory do Azure. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Leia a propriedade users.oAuth2PermissionGrants no Active Directory do Azure. |
| microsoft.aad.directory/users/ownedDevices/read | Leia a propriedade users.ownedDevices no Active Directory do Azure. |
| microsoft.aad.directory/users/ownedObjects/read | Leia a propriedade users.ownedObjects no Active Directory do Azure. |
| Microsoft.AAD.Directory/Users/Password/Update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| microsoft.aad.directory/users/pendingMemberOf/read | Ler a propriedade users.pendingMemberOf no Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Leia a propriedade users.registeredDevices no Active Directory do Azure. |
| microsoft.aad.directory/users/scopedAdministratorOf/read | Ler a propriedade users.scopedAdministratorOf no Azure Active Directory. |

## <a name="guest-inviter"></a>Emissor do Convite ao Convidado
Pode convidar usuários convidados independentemente da configuração "membros podem convidar convidados �membros podem convidar convidados�.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Usuário.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/umicrosoft.aad.directory/users/appRoleAssignments/readsers/appRoleAssignments/read | Leia a propriedade users.appRoleAssignments no Active Directory do Azure. |
| microsoft.aad.directory/users/default/read | Leia as propriedades básicas dos usuários no Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Leia a propriedade users.directReports no Active Directory do Azure. |
| microsoft.aad.directory/users/invitedBy/read | Leia a propriedade users.invitedBy no Active Directory do Azure. |
| microsoft.aad.directory/users/inviteGuest | Convidar usuários convidados no Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Leia a propriedade users.invitedUsers no Active Directory do Azure. |
| microsoft.aad.directory/users/manager/read | Leia a propriedade users.manager no Active Directory do Azure. |
| microsoft.aad.directory/users/memberOf/read | Leia a propriedade users.memberOf no Active Directory do Azure. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Leia a propriedade users.oAuth2PermissionGrants no Active Directory do Azure. |
| microsoft.aad.directory/users/ownedDevices/read | Leia a propriedade users.ownedDevices no Active Directory do Azure. |
| microsoft.aad.directory/users/ownedObjects/read | Leia a propriedade users.ownedObjects no Active Directory do Azure. |
| microsoft.aad.directory/users/registeredDevices/read | Leia a propriedade users.registeredDevices no Active Directory do Azure. |

## <a name="helpdesk-administrator"></a>Administrador de assistência técnica
Pode redefinir senhas para não administradores e Administradores de Assistência Técnica.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Password/Update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="information-protection-administrator"></a>Administrador da Proteção de Informações
Pode gerenciar todos os aspectos do produto de Proteção de Informações do Azure.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerencie todos os aspectos da proteção de informações do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="intune-service-administrator"></a>Administrador de serviços do Intune
Pode gerenciar todos os aspectos do produto Intune.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft.AAD.Directory/Contacts/default/Update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Create | Crie contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Delete | Exclua contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/default/Microsoft.AAD.Directory/Devices/default/UpdateUpdate | Atualize as propriedades básicas em dispositivos no Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/Create | Crie dispositivos no Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/Delete | Exclua dispositivos no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Atualize a propriedade registeredowners no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Atualize a propriedade registeredusers no Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/default/Update | Atualize as propriedades básicas nos grupos do Active Directory do Azure. |
| Microsoft.AAD.Directory/Groups/Create | Crie grupos no Active Directory do Azure. |
| microsoft.aad.directory/groups/createAsOwner | Crie grupos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| Microsoft.AAD.Directory/Groups/Delete | Exclua grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Ler a propriedade hiddenmembers no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Members/Update | Atualize a propriedade Groups no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Owners/Update | Atualize a propriedade Owners no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Restore | Restaure grupos no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Settings/Update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/default/Update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Manager/Update | Atualize a propriedade Users no Azure Active Directory. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.intune/allEntities/allTasks | Gerencie todos os aspectos do Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="license-administrator"></a>Administrador de Licenças
Pode gerenciar licenças de produto em usuários e grupos.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Atualizar a propriedade users.usageLocation no Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

## <a name="message-center-reader"></a>Leitor do Centro de Mensagens
Pode ler as mensagens e as atualizações para sua organização somente no Centro de Mensagens do Office 365. 

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.accessmessagecenter/allEntities/allTasks | Criar e excluir todos os recursos, e ler e atualizar as propriedades padrão no Centro de Mensagens. |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |

## <a name="partner-tier1-support"></a>Suporte de camada 1 do parceiro
Não use – não se destina para uso geral.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft.AAD.Directory/Contacts/default/Update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Create | Crie contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Delete | Exclua contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Create | Crie grupos no Active Directory do Azure. |
| microsoft.aad.directory/groups/createAsOwner | Crie grupos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| Microsoft.AAD.Directory/Groups/Members/Update | Atualize a propriedade Groups no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Owners/Update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/default/Update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Delete | Exclua usuários no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Manager/Update | Atualize a propriedade Users no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Password/Update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| Microsoft.AAD.Directory/Users/Restore | Restaurar usuários excluídos no Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="partner-tier2-support"></a>Suporte de camada 2 do parceiro
Não use – não se destina para uso geral.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft.AAD.Directory/Contacts/default/Update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Create | Crie contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Delete | Exclua contatos no Azure Active Directory. |
| microsoft.aad.directory/domains/allTasks | Criar e excluir domínios e ler e atualizar propriedades padrão no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Create | Crie grupos no Active Directory do Azure. |
| Microsoft.AAD.Directory/Groups/Delete | Exclua grupos no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Members/Update | Atualize a propriedade Groups no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Restore | Restaure grupos no Azure Active Directory. |
| Microsoft.AAD.Directory/Organization/defMicrosoft.AAD.Directory/Organization/default/Updateault/Update | Atualize as propriedades básicas em organização no Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Atualize a propriedade organization.trustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/default/Update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Delete | Exclua usuários no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Manager/Update | Atualize a propriedade Users no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Password/Update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| Microsoft.AAD.Directory/Users/Restore | Restaurar usuários excluídos no Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="power-bi-service-administrator"></a>Administrador de serviços do Power BI
Pode gerenciar todos os aspectos do produto Power BI.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gerencie todos os aspectos do Power BI. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="privileged-role-administrator"></a>Administrador de função com privilégios
Pode gerenciar atribuições de função do AD do Azure

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/directoryRoles/update | Atualize directoryRoles no Azure Active Directory. |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.aad.privilegedIdentityManagement. |

## <a name="reports-reader"></a>Leitor de Relatórios
Pode ler relatórios de entrada e de auditoria.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.reports/allEntities/read | Ler Relatórios do Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| Microsoft.office365.usageReports/allEntities/Read | Leia os relatórios de uso do Office 365. |

## <a name="security-administrator"></a>Administrador de segurança
Pode ler relatórios e informações de segurança

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | Atualize a propriedade applications.policies no Azure Active Directory. |
| Microsoft.AAD.Directory/Policies/default/Update | Atualize as propriedades básicas em políticas no Azure Active Directory. |
| Microsoft.AAD.Directory/Policies/Create | Crie políticas no Active Directory do Azure. |
| Microsoft.AAD.Directory/Policies/Delete | Exclua policies em Azure Active Directory. |
| Microsoft.AAD.Directory/Policies/Owners/Update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoftmicrosoft.aad.directory/servicePrincipals/policies/update.aad.directory/servicePrincipals/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Ler todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Atualize todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityMmicrosoft.aad.privilegedIdentityManagement/allEntities/readanagement/allEntities/read | Ler todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| Microsoft.office365.protectionCenter/allEntities/Read | Ler todos os aspectos do Centro de Proteção do Office 365. |
| Microsoft.office365.protectionCenter/allEntities/Update | Atualize todos os recursos em microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

## <a name="security-reader"></a>Leitor de segurança
Pode ler relatórios e informações de segurança no Azure AD e no Office 365.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.identityProtection/allEntities/read | Ler todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityMmicrosoft.aad.privilegedIdentityManagement/allEntities/readanagement/allEntities/read | Ler todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| Microsoft.office365.protectionCenter/allEntities/Read | Ler todos os aspectos do Centro de Proteção do Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

## <a name="service-support-administrator"></a>Administrador de suporte a serviço
Pode ler informações de integridade do serviço e gerenciar os tíquetes de suporte.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="sharepoint-service-administrator"></a>Administrador de serviços do SharePoint
Pode gerenciar todos os aspectos do serviço SharePoint.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="skype-for-business-administrator"></a>Administrador do Skype for Business
Pode gerenciar todos os aspectos do produto Skype for Business. Anteriormente conhecido como administrador de Serviços do Lync.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspectos do Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="teams-communications-administrator"></a>Administrador de Comunicações do Teams
Pode gerenciar recursos de reuniões e chamadas no serviço do Microsoft Teams.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/policies/basic/read | Ler as propriedades básicas em políticas no Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| Microsoft.office365.usageReports/allEntities/Read | Leia os relatórios de uso do Office 365. |

## <a name="teams-communications-support-engineer"></a>Engenheiro de Suporte de Comunicações do Teams
Pode solucionar problemas de comunicação no Teams usando ferramentas avançadas.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/policies/basic/read | Ler as propriedades básicas em políticas no Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

## <a name="teams-communications-support-specialist"></a>Especialista em Suporte de Comunicações do Teams
Pode solucionar problemas de comunicação no Teams equipes usando ferramentas básicas.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/policies/basic/read | Ler as propriedades básicas em políticas no Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

## <a name="teams-service-administrator"></a>Administrador de Serviços do Teams
Pode gerenciar o serviço do Microsoft Teams.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Ler a propriedade hiddenmembers no Azure Active Directory. |
| microsoft.aad.directory/policies/basic/read | Ler as propriedades básicas em políticas no Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| Microsoft.office365.usageReports/allEntities/Read | Leia os relatórios de uso do Office 365. |

## <a name="user-account-administrator"></a>Administrador da conta de usuário
Pode gerenciar todos os aspectos de usuários e grupos

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Crie appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Exclua appRoleAssignments em Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Atualize o appRoleAssignments no Active Directory do Azure. |
| Microsoft.AAD.Directory/Contacts/default/Update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Create | Crie contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Delete | Exclua contatos no Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/default/Update | Atualize as propriedades básicas nos grupos do Active Directory do Azure. |
| Microsoft.AAD.Directory/Groups/Create | Crie grupos no Active Directory do Azure. |
| microsoft.aad.directory/groups/createAsOwner | Crie grupos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| Microsoft.AAD.Directory/Groups/Delete | Exclua grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Ler a propriedade hiddenmembers no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Members/Update | Atualize a propriedade Groups no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Owners/Update | Atualize a propriedade Owners no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Restore | Restaure grupos no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Settings/Update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/default/Update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Create | Crie usuários no Active Directory do Azure. |
| Microsoft.AAD.Directory/Users/Delete | Exclua usuários no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Manager/Update | Atualize a propriedade Users no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Password/Update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| Microsoft.AAD.Directory/Users/Restore | Restaurar usuários excluídos no Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Gerenciar todos os aspectos do serviço de Acesso do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="user"></a>Usuário
Função padrão para usuários membro. Pode ler todos e escrever um conjunto limitado de informações de diretório.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Leitores de Diretório.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Crie aplicativos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.aad.directory/groups/default/read | Leia as propriedades básicas em grupos no Active Directory do Azure. |
| microsoft.aad.directory/groups/createAsOwner | Crie grupos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.aad.directory/oAuth2PermissionGrants/create | Crie oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/delete | Exclua oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/update | Atualize o oAuth2PermissionGrants no Active Directory do Azure. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Criar servicePrincipals em Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.aad.directory/users/activateServicePlan | Activateserviceplan usuários no Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Convidar usuários convidados no Azure Active Directory. |
| microsoft.aad.directory/applications/default/update | Atualize as propriedades básicas dos aplicativos no Active Directory do Azure. |
| microsoft.aad.directory/applications/delete | Excluir aplicativos no Active Directory do Azure. |
| microsoft.aad.directory/applications/owners/update | Atualize a propriedade applications.owners no Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Atualize a propriedade applications.permissions no Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Atualize a propriedade applications.policies no Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/Restore | Restaure aplicativos no Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/Disable | Desabilite dispositivos no Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/default/Update | Atualize as propriedades básicas nos grupos do Active Directory do Azure. |
| Microsoft.AAD.Directory/Groups/Delete | Exclua grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/dynamicMembershipRule/update | Atualize a propriedade dynamicmembershiprule no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Members/Update | Atualize a propriedade Groups no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Owners/Update | Atualize a propriedade Owners no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Restore | Restaure grupos no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Settings/Update | Atualize a propriedade Groups no Azure Active Directory. |
| Microsoft.AAD.Directory/Policies/default/Update | Atualize as propriedades básicas em políticas no Azure Active Directory. |
| Microsoft.AAD.Directory/Policies/Delete | Exclua policies em Azure Active Directory. |
| Microsoft.AAD.Directory/Policies/Owners/Update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoftmicrosoft.aad.directory/servicePrincipals/appRoleAssignedTo/update.aad.directory/servicePrincipals/appRoleAssignedTo/update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Atualizar servicePrincipals.appRoleAssignments property em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/update | Atualize as propriedades básicas em servicePrincipals no Active Directory do Azure. |
| microsoft.aad.directory/servicePrincipals/delete | Excluir servicePrincipals em Azure Active Directory. |
| microsoft.aad.dimicrosoft.aad.directory/servicePrincipals/owners/updaterectory/servicePrincipals/owners/update | Atualizar servicePrincipals.owners property em Azure Active Directory. |
| microsoftmicrosoft.aad.directory/servicePrincipals/policies/update.aad.directory/servicePrincipals/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.aad.directory/users/changePassword | Alterar as senhas para todos os usuários no Azure Active Directory. Consulte a documentação online para obter mais detalhes. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.aad.directory/users/basicProfile/update | Atualize a propriedade BasicProfile no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Mobile/Update | Atualize a propriedade users.mobile no Azure Active Directory. |
| microsoft.aad.directory/users/searchableDeviceKey/update | Atualize a propriedade users.searchableDeviceKey no Azure Active Directory. |

## <a name="deprecated-roles"></a>Funções preteridas

As seguintes funções não devem ser usadas. Elas foram preteridas e serão removidas do Azure AD no futuro.

* Administrador de Licenças AdHoc
* Ingresso de Dispositivo
* Gerenciadores de Dispositivo
* Usuários de Dispositivo
* Criador de Usuário Verificado por Email
* Administrador de caixa de correio
* Ingresso no Dispositivo no Local de Trabalho

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como atribuir um usuário como um administrador de uma assinatura do Azure, veja [Gerenciar o acesso usando o portal do Azure e RBAC](../../role-based-access-control/role-assignments-portal.md)
* Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, confira [Noções básicas sobre o acesso a recursos no Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Para saber mais sobre como o Azure Active Directory está relacionado à sua assinatura do Azure, consulte [Como as assinaturas do Azure estão associadas ao Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
