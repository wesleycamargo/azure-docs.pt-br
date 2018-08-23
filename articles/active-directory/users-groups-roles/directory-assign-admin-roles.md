---
title: Atribuindo funções de administrador no Azure Active Directory | Microsoft Docs
description: Uma função de administrador pode adicionar usuários, atribuir funções administrativas, redefinir senhas de usuário, gerenciar licenças de usuário ou gerenciar domínios. Um usuário que tem uma função de administrador atribuída tem as mesmas permissões em todos os serviços de nuvem ao quais sua organização tenha assinado.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/21/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 6c97d7c2f901110421f9fc5d0a1d4468d832c472
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2018
ms.locfileid: "42141224"
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Atribuindo funções de administrador no Azure Active Directory

Usando o Azure Active Directory (Azure AD), você pode designar administradores separados para atender a diferentes funções. Os administradores podem ser designados no portal do Azure AD para realizar tarefas como adicionar ou alterar os usuários, atribuir funções administrativas, redefinir senhas de usuário, gerenciar licenças de usuário e gerenciando nomes de domínio.

## <a name="details-about-the-global-administrator-role"></a>Detalhes sobre a função de administrador global

O administrador global tem acesso a todos os recursos administrativos. Por padrão, a pessoa que se inscreve para uma assinatura do Azure recebe a função de administrador global para o diretório. Somente os administradores globais podem atribuir outras funções de administrador.

## <a name="assign-or-remove-administrator-roles"></a>Atribuir ou remover funções de administrador

Para saber como atribuir funções administrativas a um usuário no Azure Active Directory, veja [Atribuir um usuário a funções de administrador no Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="available-roles"></a>Funções disponíveis

As seguintes funções de administrador estão disponíveis:

* **[Administrador de Aplicativos](#application-administrator)**: usuários nesta função podem criar e gerenciar todos os aspectos de aplicativos corporativos, registros de aplicativo e as configurações de proxy de aplicativo. Essa função também concede a capacidade de consentimento para permissões delegadas e permissões do aplicativo excluindo o Microsoft Graph e o Microsoft Azure AD Graph. Os membros dessa função não são adicionados como proprietários, ao criar novos registros de aplicativo ou aplicativos empresariais.

* **[Desenvolvedor de Aplicativos](#application-developer)**: os usuários nesta função podem criar registros do aplicativo quando a configuração "Os usuários podem registrar aplicativos" estiver definida como Não. Essa função também permite que os membros façam consentimento em seu próprio nome quando a configuração"Os usuários podem dar consentimento para aplicativos que acessam dados da empresa em seu nome" estiver definida como Não. Os membros dessa função são adicionados como proprietários, ao criar novos registros de aplicativo ou aplicativos empresariais.

* **[Administrador de Cobrança](#billing-administrator)**: faz compras, gerencia as assinaturas, gerencia tíquetes de suporte e monitora a integridade do serviço.

* **[Administrador de Aplicativos de Nuvem](#cloud-application-administrator)**: os usuários nesta função têm as mesmas permissões que a função de administrador do aplicativo, exceto a capacidade de gerenciar o proxy de aplicativo. Essa função concede a capacidade de criar e gerenciar todos os aspectos de aplicativos corporativos e os registros do aplicativo. Essa função também concede a capacidade de consentimento para permissões delegadas e permissões do aplicativo excluindo o Microsoft Graph e o Microsoft Azure AD Graph. Os membros dessa função não são adicionados como proprietários, ao criar novos registros de aplicativo ou aplicativos empresariais.

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

* **[Leitor do Centro de Mensagens](#message-center-reader)**: os usuários que têm essa função podem monitorar as notificações e atualizações de consultoria de integridade no [Centro de Mensagens do Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) da sua organização sobre serviços configurados como o Exchange, o Intune e o Microsoft Teams. Os Leitores do Centro de Mensagens recebem por email resumos semanais de postagens, atualizações e podem compartilhar postagens do Centro de Mensagens no Office 365. No Azure AD, os usuários atribuídos a essa função terão acesso somente leitura aos serviços do Azure AD como usuários e grupos. 

* **[Suporte ao parceiro de Nível 1](#partner-tier1-support)**: não use. Essa função foi substituída e será removida do Azure AD no futuro. Essa função é destinada a um pequeno número de parceiros de revenda da Microsoft e não se destina ao uso geral.

* **[Suporte ao parceiro de Nível 2](#partner-tier2-support)**: não use. Essa função foi substituída e será removida do Azure AD no futuro. Essa função é destinada a um pequeno número de parceiros de revenda da Microsoft e não se destina ao uso geral.

* **[Administrador de Senhas/Administrador de Assistência Técnica](#helpdesk-administrator)**: usuários com essa função podem alterar senhas, gerenciar solicitações de serviço e monitorar a integridade do serviço. Os administradores de Assistência Técnica podem alterar senhas apenas para usuários e outros administradores de Assistência Técnica. 

  > [!NOTE]
  > Na API do Graph da Microsoft, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Administrador da Assistência Técnica". Ele é um "Administrador de Senha" no [Portal do Azure](https://portal.azure.com/).
  >
  >
  
* **[Administrador de Serviços do Power BI](#power-bi-service-administrator)**: os usuários com essa função têm permissões globais no Microsoft Power BI quando o serviço está presente, além da capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [Noções básicas sobre a função de administrador do Power BI](https://docs.microsoft.com/power-bi/service-admin-role).

* **[Administrador da função com privilégios](#privileged-role-administrator)**: os usuários com essa função podem gerenciar atribuições de função no Azure Active Directory, bem como dentro do Azure AD Privileged Identity Management. Além disso, essa função permite o gerenciamento de todos os aspectos do Privileged Identity Management.

* **[Leitor de Relatórios](#reports-reader)**: usuários com essa função podem exibir dados de relatórios de uso e o painel de relatórios no centro de administração do Office 365 e o pacote do contexto de adoção no Power BI. Além disso, a função fornece acesso a relatórios e atividades de logon no Azure AD e a dados retornados pela API de relatórios do Microsoft Graph. Um usuário atribuído à função Leitor de Relatórios pode acessar somente o uso relevante e as métricas de adoção. Ele não tem permissões de administrador para definir configurações ou acessar os centros de administração específico ao produto como o Exchange. 

* **[Administrador de Segurança](#security-administrator)**: os usuários com essa função têm todas as permissões somente leitura da função de Leitor de segurança, mais a capacidade de gerenciamento de configuração de serviços relacionados à segurança: Azure Active Directory Identity Protection, Proteção de Informações do Azure, Privileged Identity Management e Centro de Segurança e Conformidade do Office 365. Mais informações sobre permissões do Office 365 estão disponíveis em [Permissões no Centro de Conformidade de Segurança do Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
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

* **[Administrador de Serviços do Lync/Skype for Business](#lync-service-administrator)**: os usuários com essa função têm permissões globais no Microsoft Skype for Business quando o serviço está presente, além de gerenciar atributos de usuário específicos do Skype no Azure Active Directory. Além disso, essa função concede a capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [sobre o Skype para função de administrador de negócios](https://support.office.com/en-us/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5).

  > [!NOTE]
  > Na API do Graph da Microsoft, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Administrador de Serviços do Lync". Ela equivale a "Administrador de Serviços do Skype for Business" no [Portal do Azure](https://portal.azure.com/).
  >
  >

* **[Administrador da Conta de Usuário](#user-account-administrator)**: os usuários com essa função podem criar e gerenciar todos os aspectos de usuários e grupos. Além disso, essa função inclui a capacidade de gerenciar tíquetes de suporte e monitora a integridade do serviço. Algumas restrições se aplicam. Por exemplo, essa função não permite a exclusão de um administrador global. Os administradores de Contas de Usuário podem alterar senhas somente de usuários, administradores da Assistência Técnica e outros administradores de Contas de Usuário.

| O que ele pode fazer | O que não pode fazer |
| --- | --- |
| <p>Exibir informações da empresa e do usuário</p><p>Gerenciar tíquetes de suporte do Office</p><p>Alterar senhas somente de usuários, administradores de Assistência Técnica e outros administradores de Contas de Usuário</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Criar, editar e excluir usuários e grupos e gerenciar licenças de usuário, com limitações. Eles não podem excluir um administrador global ou criar outros administradores.</p> |<p>Executar operações de cobrança e compra de produtos do Office</p><p>Gerenciar domínios</p><p>Editar informações da empresa</p><p>Delegar funções administrativas a outros</p><p>Usar sincronização de diretório</p><p>Habilitar ou desabilitar autenticação multifator</p><p>Exibir logs de auditoria</p> |

## <a name="deprecated-roles"></a>Funções preteridas

As seguintes funções não devem ser usadas. Elas foram preteridas e serão removidas do Azure AD no futuro.

* Administrador de Licenças AdHoc
* Ingresso de Dispositivo
* Gerenciadores de Dispositivo
* Usuários de Dispositivo
* Criador de Usuário Verificado por Email
* Administrador de caixa de correio
* Ingresso no Dispositivo no Local de Trabalho


### <a name="to-add-a-colleague-as-a-global-administrator"></a>Para adicionar um colega como um administrador global

1. Entre no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com uma conta que seja um administrador global ou administrador de função privilegiado do diretório de locatário.

   ![Como abrir o Centro de administração do Azure AD](./media/directory-assign-admin-roles/active-directory-admin-center.png)

2. Selecione **Usuários**.

3. Encontre o usuário que você deseja designar como administrador global e abra a folha para esse usuário.

4. Na folha do usuário, selecione a **função Diretório**.
 
5. Na folha da função de diretório, selecione a função **Administrador global** e salve.

## <a name="detailed-azure-active-directory-permissions"></a>Permissões do Azure Active Directory detalhadas
As tabelas a seguir descrevem as permissões específicas no Azure Active Directory fornecidas a cada função. Algumas funções, como o Administrador Global, podem ter permissões adicionais no Microsoft outide de serviços do Azure Active Directory.


### <a name="application-administrator"></a>Administrador de aplicativos
Pode criar e gerenciar todos os aspectos de registros de aplicativo e aplicativos empresariais.

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Criar Aplicativos no Azure Active Directory. |
| microsoft.aad.directory/Application/Delete | Excluir Aplicativos no Azure Active Directory. |
| microsoft.aad.directory/Application/Update | Atualize as propriedades padrão em Aplicativos no Azure Active Directory. |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Atualizar a propriedade Applications.DefaultPolicy no Azure Active Directory. |
| microsoft.aad.directory/Application/Update/Owners | Atualizar a propriedade Applications.Owners no Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Create | Criar AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Excluir AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Atualizar propriedades padrão em AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Criar Políticas no Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Excluir Políticas no Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Atualizar propriedades padrão em Políticas no Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Atualizar a propriedade Policies.Owners no Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | É possível consentir em nome de todos os usuários a todos os recursos, exceto o Azure Active Directory (Azure AD Graph & Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/Create | Criar ServicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Delete | Excluir ServicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Atualize as propriedades padrão em ServicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Atualizar a propriedade ServicePrincipals.AppRoleAssignedTo no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Atualizar a propriedade ServicePrincipals.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Atualizar a propriedade ServicePrincipals.DefaultPolicy no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Atualizar a propriedade ServicePrincipals.Owners no Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Gerenciar licenças em Usuários no Azure Active Directory. |
| microsoft.aad.reports/AllEntities/Read | Ler Relatórios do Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="application-developer"></a>Desenvolvedor de aplicativos
É possível criar registros de aplicativo independentemente da configuração **Usuários podem registrar aplicativos**.

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/Application/CreateAsOwner | Criar Aplicativos no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.aad.directory/AppRoleAssignment/CreateAsOwner | Criar AppRoleAssignments no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.aad.directory/OAuth2PermissionGrant/CreateAsOwner | Criar OAuth2PermissionGrants no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.aad.directory/ServicePrincipal/CreateAsOwner | Criar ServicePrincipals no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |

### <a name="billing-administrator"></a>Administrador de cobrança
Pode executar tarefas comuns de relacionadas à cobrança, como atualizar informações de pagamento.

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Leia a propriedade Organizations.TrustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.directory/Organization/Update | Atualize as propriedades padrão em Organizações no Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Atualize a propriedade Organizations.TrustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar propriedades padrão no Controle de Acesso do Azure. |
| microsoft.aad.billing/AllEntities/AllActions | Gerencie todos os aspectos de cobrança do Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="cloud-application-administrator"></a>Administrador de Aplicativos de Nuvem
Pode criar e gerenciar todos os aspectos de registros de aplicativo e aplicativos empresariais, exceto o Proxy de Aplicativo.

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Criar Aplicativos no Azure Active Directory. |
| microsoft.aad.directory/Application/Delete | Excluir Aplicativos no Azure Active Directory. |
| microsoft.aad.directory/Application/Update | Atualize as propriedades padrão em Aplicativos no Azure Active Directory. |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Atualizar a propriedade Applications.DefaultPolicy no Azure Active Directory. |
| microsoft.aad.directory/Application/Update/Owners | Atualizar a propriedade Applications.Owners no Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Create | Criar AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Excluir AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Atualizar propriedades padrão em AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Criar Políticas no Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Excluir Políticas no Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Atualizar propriedades padrão em Políticas no Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Atualizar a propriedade Policies.Owners no Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | É possível consentir em nome de todos os usuários a todos os recursos, exceto o Azure Active Directory (Azure AD Graph & Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/Create | Criar ServicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Delete | Excluir ServicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Atualize as propriedades padrão em ServicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Atualizar a propriedade ServicePrincipals.AppRoleAssignedTo no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Atualizar a propriedade ServicePrincipals.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Atualizar a propriedade ServicePrincipals.DefaultPolicy no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Atualizar a propriedade ServicePrincipals.Owners no Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Gerenciar licenças em Usuários no Azure Active Directory. |
| microsoft.aad.reports/AllEntities/Read | Ler Relatórios do Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="company-administrator"></a>Administradores de Empresa
Pode gerenciar todos os aspectos do Azure AD e dos serviços da Microsoft que usam identidades do Azure AD. Na API do Graph da Microsoft, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Administrador da Empresa". É "Administrador Global" no [portal do Azure](https://portal.azure.com).

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/AllActions/AllProperties | Criar e excluir AdministrativeUnits, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/Application/AllActions/AllProperties | Criar e excluir Aplicativos, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/AllActions/AllProperties | Criar e excluir AppRoleAssignments, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/AllActions/AllProperties | Criar e excluir CollaborationSpaces, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/Contact/AllActions/AllProperties | Criar e excluir Contatos, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/Device/AllActions/AllProperties | Criar e excluir Dispositivos, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/AllActions/AllProperties | Criar e excluir DirectoryRoles, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/DirectoryRoleTemplate/AllActions/AllProperties | Criar e excluir DirectoryRoleTemplates, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/AllActions/AllProperties | Criar e excluir DirectorySettings, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/DirectorySettingTemplate/AllActions/AllProperties | Criar e excluir DirectorySettingTemplates, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/Domain/AllActions/AllProperties | Criar e excluir Domínios, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/Group/AllActions/AllProperties | Criar e excluir Grupos, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/LoginTenantBranding/AllActions/AllProperties | Criar e excluir LoginTenantBrandings, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/OAuth2PermissionGrant/AllActions/AllProperties | Criar e excluir OAuth2PermissionGrants, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/Policy/AllActions/AllProperties | Criar e excluir Políticas, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllWithDirectory | É possível consentir em nome de todos os usuários a todos os recursos, incluindo o Azure Active Directory (Azure AD Graph & Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/AllActions/AllProperties | Criar e excluir ServicePrincipals, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/Organization/AllActions/AllProperties | Criar e excluir Organizações, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/User/AllActions/AllProperties | Criar e excluir Usuários, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.aadconnect/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar as propriedades padrão no microsoft.aad.aadconnect. |
| microsoft.aad.accessservice/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar propriedades padrão no Controle de Acesso do Azure. |
| microsoft.aad.billing/AllEntities/AllActions | Gerencie todos os aspectos de cobrança do Office 365. |
| microsoft.aad.compliance/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar propriedades padrão no Centro de conformidade. |
| microsoft.aad.directorysync/AllEntities/AllActions | Executar todas as ações no Azure AD Connect. |
| microsoft.aad.lockbox/AllEntities/AllActions | Gerencie todos os aspectos do serviço do Sistema de Proteção de Dados. |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | Gerencie todos os aspectos do serviço de Gerenciamento de Funções Privilegiadas. |
| microsoft.aad.reports/AllEntities/AllActions | Ler e configurar Relatórios do Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.crm/AllEntities/AllActions | Gerencie todos os aspectos do Dynamics 365. |
| microsoft.exchange/AllEntities/AllActions | Gerencie todos os aspectos do Exchange Online. |
| microsoft.aad.informationprotection/AllEntities/AllActions | Gerencie todos os aspectos de Proteção de Informações. |
| microsoft.intune/AllEntities/AllActions | Gerencie todos os aspectos do Intune. |
| microsoft.powerbi/AllEntities/AllActions | Gerencie todos os aspectos do Power BI. |
| microsoft.protectioncenter/AllEntities/AllActions | Gerenciar Centro de Proteção do Office 365. |
| microsoft.sharepoint/AllEntities/AllActions | Gerenciar SharePoint Online. |
| microsoft.skypeforbusiness/AllEntities/AllActions | Gerenciar Skype for Business Online. |

### <a name="compliance-administrator"></a>Administrador de conformidade
Pode ler e gerenciar a configuração de conformidade e relatórios no Azure AD e no Office 365.

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar propriedades padrão no Controle de Acesso do Azure. |
| microsoft.aad.compliance/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar propriedades padrão no Centro de conformidade. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.exchange/Compliance/AllActions | Gerencie a conformidade no Exchange Online. |
| microsoft.sharepoint/Compliance/AllActions | Gerencie a conformidade no SharePoint Online. |
| microsoft.skypeforbusiness/Compliance/AllActions | Gerencie a conformidade no Skype for Business Online. |

### <a name="conditional-access-administrator"></a>Administrador de acesso condicional
Pode gerenciar os recursos de acesso condicional.

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/ConditionalAccessPolicy/Create | Criar ConditionalAccessPolicys no Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Delete | Excluir ConditionalAccessPolicys no Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read | Leia as propriedades padrão em ConditionalAccessPolicys no Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/Owners | Leia a propriedade ConditionalAccessPolicys.Owners no Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/PolicyAppliedTo | Leia a propriedade ConditionalAccessPolicys.PolicyAppliedTo no Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Update | Atualize as propriedades padrão em ConditionalAccessPolicys no Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Update/Owners | Atualize a propriedade ConditionalAccessPolicys.Owners no Azure Active Directory. |

### <a name="device-administrators"></a>Administradores de Dispositivo

Os usuários com essa função se tornam administradores de computador local em todos os dispositivos Windows 10 associados ao Azure Active Directory. Eles não têm a capacidade de gerenciar objetos de dispositivos no Azure Active Directory.

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

### <a name="directory-readers"></a>Leitores de Diretório
Pode ler informações básicas do diretório. Solucionar problemas de acesso a aplicativos.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/Read | Ler propriedades padrão em AdministrativeUnits no Azure Active Directory. |
| microsoft.aad.directory/AdministrativeUnit/Read/Members | Ler a propriedade AdministrativeUnits.Members no Azure Active Directory. |
| microsoft.aad.directory/Application/Read | Leia as propriedades padrão em Aplicativos no Azure Active Directory. |
| microsoft.aad.directory/Application/Read/Owners | Ler a propriedade Applications.Owners no Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/Read | Ler propriedades padrão em CollaborationSpaces no Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/Read/Owners | Ler a propriedade CollaborationSpaces.Owners no Azure Active Directory. |
| microsoft.aad.directory/Contact/Read | Ler propriedades padrão em Contatos no Azure Active Directory. |
| microsoft.aad.directory/Contact/Read/MemberOf | Ler a propriedade Contacts.MemberOf no Azure Active Directory. |
| microsoft.aad.directory/Device/Read | Ler propriedades padrão em Dispositivos no Azure Active Directory. |
| microsoft.aad.directory/Device/Read/MemberOf | Ler a propriedade Devices.MemberOf no Azure Active Directory. |
| microsoft.aad.directory/Device/Read/RegisteredOwners | Ler a propriedade Devices.RegisteredOwners no Azure Active Directory. |
| microsoft.aad.directory/Device/Read/RegisteredUsers | Ler a propriedade Devices.RegisteredUsers no Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read | Leia as propriedades padrão em DirectoryRoles no Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read/EligibleMembers | Ler a propriedade DirectoryRoles.EligibleMembers no Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read/Members | Ler a propriedade DirectoryRoles.Members no Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Read | Ler propriedades padrão em DirectorySettings no Azure Active Directory. |
| microsoft.aad.directory/DirectorySettingTemplate/Read | Ler propriedades padrão em DirectorySettingTemplates no Azure Active Directory. |
| microsoft.aad.directory/Domain/Read | Ler propriedades padrão em Domínios no Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Leia as propriedades padrão em Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/Read/AppRoleAssignments | Ler a propriedade Groups.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/Group/Read/MemberOf | Ler a propriedade Groups.MemberOf no Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Members | Ler a propriedade Groups.Members no Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Owners | Ler a propriedade Groups.Owners no Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Settings | Ler a propriedade Groups.Settings no Azure Active Directory. |
| microsoft.aad.directory/OAuth2PermissionGrant/Read | Ler propriedades padrão em OAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read | Leia as propriedades padrão em ServicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | Ler a propriedade ServicePrincipals.AppRoleAssignedTo no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | Ler a propriedade ServicePrincipals.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | Ler a propriedade ServicePrincipals.DefaultPolicy no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | Ler a propriedade ServicePrincipals.MemberOf no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | Ler a propriedade ServicePrincipals.OAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | Ler a propriedade ServicePrincipals.Owners no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | Ler a propriedade ServicePrincipals.OwnedObjects no Azure Active Directory. |
| microsoft.aad.directory/Organization/Read | Leia as propriedades padrão em Organizações no Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Leia a propriedade Organizations.TrustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.directory/User/Read | Leia as propriedades padrão em Usuários no Azure Active Directory. |
| microsoft.aad.directory/User/Read/AppRoleAssignments | Ler a propriedade Users.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/User/Read/DirectReports | Ler a propriedade Users.DirectReports no Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedBy | Ler a propriedade Users.InvitedBy no Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedUsers | Ler a propriedade Users.InvitedUsers no Azure Active Directory. |
| microsoft.aad.directory/User/Read/Manager | Ler a propriedade Users.Manager no Azure Active Directory. |
| microsoft.aad.directory/User/Read/MemberOf | Ler a propriedade Users.MemberOf no Azure Active Directory. |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | Ler a propriedade Users.OAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedDevices | Ler a propriedade Users.OwnedDevices no Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedObjects | Ler a propriedade Users.OwnedObjects no Azure Active Directory. |
| microsoft.aad.directory/User/Read/RegisteredDevices | Ler a propriedade Users.RegisteredDevices no Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Contas de sincronização de diretório
Apenas usado pelo serviço do Azure AD Connect.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/Policy/Create | Criar Políticas no Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Excluir Políticas no Azure Active Directory. |
| microsoft.aad.directory/Policy/Read | Ler propriedades padrão em Políticas no Azure Active Directory. |
| microsoft.aad.directory/Policy/Read/Owners | Ler a propriedade Policies.Owners no Azure Active Directory. |
| microsoft.aad.directory/Policy/Read/PolicyAppliedTo | Ler a propriedade Policies.PolicyAppliedTo no Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Atualizar propriedades padrão em Políticas no Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Atualizar a propriedade Policies.Owners no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Create | Criar ServicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read | Leia as propriedades padrão em ServicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | Ler a propriedade ServicePrincipals.AppRoleAssignedTo no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | Ler a propriedade ServicePrincipals.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | Ler a propriedade ServicePrincipals.DefaultPolicy no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | Ler a propriedade ServicePrincipals.MemberOf no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | Ler a propriedade ServicePrincipals.OAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | Ler a propriedade ServicePrincipals.Owners no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | Ler a propriedade ServicePrincipals.OwnedObjects no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Atualize as propriedades padrão em ServicePrincipals no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Atualizar a propriedade ServicePrincipals.AppRoleAssignedTo no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Atualizar a propriedade ServicePrincipals.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Atualizar a propriedade ServicePrincipals.DefaultPolicy no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Atualizar a propriedade ServicePrincipals.Owners no Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/DirSync | Atualize a propriedade Organizations.DirSync no Azure Active Directory. |
| microsoft.aad.directorysync/AllEntities/AllActions | Executar todas as ações no Azure AD Connect. |

### <a name="directory-writer"></a>Gravador de diretório
Pode ler e gravar informações básicas do diretório. Solucionar problemas de acesso a aplicativos

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/DirectorySetting/Create | Criar DirectorySettings no Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Delete | Delete DirectorySettings in Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Update | Atualizar propriedades padrão em DirectorySettings no Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Criar Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Criar Grupos no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.aad.directory/Group/Read | Leia as propriedades padrão em Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Atualize as propriedades padrão em Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Atualizar a propriedade Groups.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Atualizar a propriedade Groups.Members no Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Atualizar a propriedade Groups.Owners no Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Atualizar a propriedade Groups.Settings no Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Leia a propriedade Organizations.TrustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Gerenciar licenças em Usuários no Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.aad.directory/User/Update | Atualize as propriedades padrão em Usuários no Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Atualizar a propriedade Users.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Atualizar a propriedade Users.Manager no Azure Active Directory. |

### <a name="dynamics-365-service-administrator"></a>Administrador de serviços do Dynamics 365
Pode gerenciar todos os aspectos do produto Dynamics 365.

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Leia a propriedade Organizations.TrustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar propriedades padrão no Controle de Acesso do Azure. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.crm/AllEntities/AllActions | Gerencie todos os aspectos do Dynamics 365. |

### <a name="exchange-service-administrator"></a>Administrador de serviços do Exchange
Pode gerenciar todos os aspectos do produto Exchange.

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar propriedades padrão no Controle de Acesso do Azure. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.exchange/AllEntities/AllActions | Gerencie todos os aspectos do Exchange Online. |

### <a name="guest-inviter"></a>Emissor do Convite ao Convidado
Pode convidar usuários convidados independentemente da configuração de **Membros podem convidar os convidados**.

  > [!NOTE]
  > Essa função herda permissões adicionais da função de Convidado.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/User/InviteGuest | Convidar usuários convidados no Azure Active Directory. |
| microsoft.aad.directory/User/Read | Leia as propriedades padrão em Usuários no Azure Active Directory. |
| microsoft.aad.directory/User/Read/AppRoleAssignments | Ler a propriedade Users.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/User/Read/DirectReports | Ler a propriedade Users.DirectReports no Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedBy | Ler a propriedade Users.InvitedBy no Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedUsers | Ler a propriedade Users.InvitedUsers no Azure Active Directory. |
| microsoft.aad.directory/User/Read/Manager | Ler a propriedade Users.Manager no Azure Active Directory. |
| microsoft.aad.directory/User/Read/MemberOf | Ler a propriedade Users.MemberOf no Azure Active Directory. |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | Ler a propriedade Users.OAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedDevices | Ler a propriedade Users.OwnedDevices no Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedObjects | Ler a propriedade Users.OwnedObjects no Azure Active Directory. |
| microsoft.aad.directory/User/Read/RegisteredDevices | Ler a propriedade Users.RegisteredDevices no Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Administrador de assistência técnica
Pode redefinir senhas para não administradores e Administradores de Assistência Técnica.

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Leia a propriedade Organizations.TrustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordHelpdeskScope | Atualize as senhas para os administradores limitados e outros administradores de Suporte Técnico no Azure Active Directory. Consulte a documentação online para obter mais detalhes. |
| microsoft.aad.accessservice/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar propriedades padrão no Controle de Acesso do Azure. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |

### <a name="information-protection-administrator"></a>Administrador da Proteção de Informações
Pode gerenciar todos os aspectos do produto de Proteção de Informações do Azure.

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/Group/Read | Leia as propriedades padrão em Grupos no Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Leia a propriedade Organizations.TrustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.informationprotection/AllEntities/AllActions | Gerencie todos os aspectos de Proteção de Informações. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="intune-service-administrator"></a>Administrador de serviços do Intune
Pode gerenciar todos os aspectos do produto Intune.

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/Contact/Create | Criar Contatos no Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Excluir Contatos no Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Atualizar propriedades padrão em Contatos no Azure Active Directory. |
| microsoft.aad.directory/Device/Create | Criar Dispositivos no Azure Active Directory. |
| microsoft.aad.directory/Device/Delete | Excluir Dispositivos no Azure Active Directory. |
| microsoft.aad.directory/Device/Update | Atualizar propriedades padrão em Dispositivos no Azure Active Directory. |
| microsoft.aad.directory/Device/Update/RegisteredOwners | Atualizar a propriedade Devices.RegisteredOwners no Azure Active Directory. |
| microsoft.aad.directory/Device/Update/RegisteredUsers | Atualizar a propriedade Devices.RegisteredUsers no Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Criar Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Criar Grupos no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.aad.directory/Group/Delete | Excluir Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Leia as propriedades padrão em Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/Read/HiddenMembers | Ler a propriedade Groups.HiddenMembers no Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Restaurar Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Atualize as propriedades padrão em Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Atualizar a propriedade Groups.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Atualizar a propriedade Groups.Members no Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Atualizar a propriedade Groups.Owners no Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Atualizar a propriedade Groups.Settings no Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Leia a propriedade Organizations.TrustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.directory/User/Update | Atualize as propriedades padrão em Usuários no Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Atualizar a propriedade Users.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Atualizar a propriedade Users.Manager no Azure Active Directory. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.intune/AllEntities/AllActions | Gerencie todos os aspectos do Intune. |

### <a name="lync-service-administrator"></a>Administrador de serviços do Lync
Pode gerenciar todos os aspectos do produto Skype for Business.

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar propriedades padrão no Controle de Acesso do Azure. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.skypeforbusiness/AllEntities/AllActions | Gerenciar Skype for Business Online. |

### <a name="message-center-reader"></a>Leitor do Centro de Mensagens
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
| microsoft.aad.directory/Group/Read | Leia as propriedades padrão em Grupos no Azure Active Directory. |
| microsoft.aad.accessmessagecenter/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar as propriedades padrão no Centro de Mensagens. |
| microsoft.aad.accessservice/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar propriedades padrão no Controle de Acesso do Azure. |

### <a name="partner-tier1-support"></a>Suporte de camada 1 do parceiro
Não use – não se destina para uso geral.

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar propriedades padrão no Controle de Acesso do Azure. |
| microsoft.aad.directory/Contact/Create | Criar Contatos no Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Excluir Contatos no Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Atualizar propriedades padrão em Contatos no Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Criar Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Criar Grupos no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.aad.directory/Group/Read | Leia as propriedades padrão em Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Atualizar a propriedade Groups.Members no Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Atualizar a propriedade Groups.Owners no Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Leia a propriedade Organizations.TrustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Gerenciar licenças em Usuários no Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Excluir Usuários no Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Restaurar usuários excluídos no Azure Active Directory. |
| microsoft.aad.directory/User/Update | Atualize as propriedades padrão em Usuários no Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Atualizar a propriedade Users.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Atualizar a propriedade Users.Manager no Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordUserScope | Atualize senhas para não administradores no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="partner-tier2-support"></a>Suporte de camada 2 do parceiro
Não use – não se destina para uso geral.

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar propriedades padrão no Controle de Acesso do Azure. |
| microsoft.aad.directory/Contact/Create | Criar Contatos no Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Excluir Contatos no Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Atualizar propriedades padrão em Contatos no Azure Active Directory. |
| microsoft.aad.directory/Domain/AllActions | Criar e excluir Domínios, e ler e atualizar propriedades padrão no Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Criar Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/Delete | Excluir Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Leia as propriedades padrão em Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Restaurar Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Atualizar a propriedade Groups.Members no Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Leia a propriedade Organizations.TrustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.directory/Organization/Update | Atualize as propriedades padrão em Organizações no Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Atualize a propriedade Organizations.TrustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Gerenciar licenças em Usuários no Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Excluir Usuários no Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Restaurar usuários excluídos no Azure Active Directory. |
| microsoft.aad.directory/User/Update | Atualize as propriedades padrão em Usuários no Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Atualizar a propriedade Users.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Atualizar a propriedade Users.Manager no Azure Active Directory. |
| microsoft.aad.directory/User/Update/Password | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="power-bi-service-administrator"></a>Administrador de serviços do Power BI
Pode gerenciar todos os aspectos do produto Power BI.

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Leia a propriedade Organizations.TrustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar propriedades padrão no Controle de Acesso do Azure. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.powerbi/AllEntities/AllActions | Gerencie todos os aspectos do Power BI. |

### <a name="privileged-role-administrator"></a>Administrador de função com privilégios
Pode gerenciar atribuições de função do AD do Azure

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/DirectoryRole/Update | Atualize as propriedades padrão em DirectoryRoles no Azure Active Directory. |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | Gerencie todos os aspectos do serviço de Gerenciamento de Funções Privilegiadas. |

### <a name="reports-reader"></a>Leitor de Relatórios
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
| microsoft.aad.reports/AllEntities/Read | Ler Relatórios do Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.usagereports/AllEntities/Read | Leia os relatórios de uso do Office 365. |

### <a name="security-administrator"></a>Administrador de segurança
Pode ler relatórios e informações de segurança

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Atualizar a propriedade Applications.DefaultPolicy no Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Criar Políticas no Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Excluir Políticas no Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Atualizar propriedades padrão em Políticas no Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Atualizar a propriedade Policies.Owners no Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Atualizar a propriedade ServicePrincipals.DefaultPolicy no Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Leia a propriedade Organizations.TrustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar propriedades padrão no Controle de Acesso do Azure. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.aad.privilegedrolemanagement/AllEntities/Read | Leia todos os aspectos de Privileged Identity Management. |
| microsoft.protectioncenter/AllEntities/Read | Ler todos os aspectos do Centro de Proteção do Office 365. |
| microsoft.protectioncenter/AllEntities/Update | Gerenciar Centro de Proteção do Office 365. |

### <a name="security-reader"></a>Leitor de segurança
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
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Leia a propriedade Organizations.TrustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar propriedades padrão no Controle de Acesso do Azure. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.privilegedidentitymanagement/AllEntities/Read | Leia todos os aspectos de Privileged Identity Management. |
| microsoft.protectioncenter/AllEntities/Read | Ler todos os aspectos do Centro de Proteção do Office 365. |

### <a name="service-support-administrator"></a>Administrador de suporte a serviço
Pode ler informações de integridade do serviço e gerenciar os tíquetes de suporte.

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Leia a propriedade Organizations.TrustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar propriedades padrão no Controle de Acesso do Azure. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |

### <a name="sharepoint-service-administrator"></a>Administrador de serviços do SharePoint
Pode gerenciar todos os aspectos do serviço SharePoint.

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Consulte a descrição da função acima para obter mais informações.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar propriedades padrão no Controle de Acesso do Azure. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.sharepoint/AllEntities/AllActions | Gerenciar SharePoint Online. |

### <a name="user-account-administrator"></a>Administrador da conta de usuário
Pode gerenciar todos os aspectos de usuários e grupos

  > [!NOTE]
  > Essa função herda permissões adicionais da [função de usuário](https://docs.microsoft.com/azure/active-directory/users-default-permissions).
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/AppRoleAssignment/Create | Criar AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Excluir AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Atualizar propriedades padrão em AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/Contact/Create | Criar Contatos no Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Excluir Contatos no Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Atualizar propriedades padrão em Contatos no Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Criar Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Criar Grupos no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.aad.directory/Group/Delete | Excluir Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Leia as propriedades padrão em Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/Read/HiddenMembers | Ler a propriedade Groups.HiddenMembers no Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Restaurar Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Atualize as propriedades padrão em Grupos no Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Atualizar a propriedade Groups.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Atualizar a propriedade Groups.Members no Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Atualizar a propriedade Groups.Owners no Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Atualizar a propriedade Groups.Settings no Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Leia a propriedade Organizations.TrustedCAsForPasswordlessAuth no Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Gerenciar licenças em Usuários no Azure Active Directory. |
| microsoft.aad.directory/User/Create | Criar Usuários no Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Excluir Usuários no Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Restaurar usuários excluídos no Azure Active Directory. |
| microsoft.aad.directory/User/Update | Atualize as propriedades padrão em Usuários no Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Atualizar a propriedade Users.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Atualizar a propriedade Users.Manager no Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordUserAcctAdminScope | Atualize as senhas para os administradores limitados, administradores Helpdesk, e outros administradores de Conta de Usuário no Azure Active Directory. Consulte a documentação online para obter mais detalhes. |
| microsoft.aad.accessservice/AllEntities/AllActions | Criar e excluir todos os recursos, e ler e atualizar propriedades padrão no Controle de Acesso do Azure. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Ler e configurar a Integridade de Serviço do Office 365. |

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como alterar administradores para uma assinatura do Azure, veja [Como adicionar ou alterar as funções de administrador do Azure](../../billing/billing-add-change-azure-subscription-administrator.md)
* Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, confira [Noções básicas sobre o acesso a recursos no Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Para saber mais sobre como o Azure Active Directory está relacionado à sua assinatura do Azure, consulte [Como as assinaturas do Azure estão associadas ao Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
