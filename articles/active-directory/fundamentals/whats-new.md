---
title: O que há de novo? Notas sobre a versão – Azure Active Directory | Microsoft Docs
description: Conheça as novidades do Azure Active Directory, como as últimas notas sobre a versão, problemas conhecidos, correções de bug, funcionalidades preteridas e alterações futuras.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.openlocfilehash: 0847096674d446940ed3fed3390457e3d2e894bf
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55248445"
---
# <a name="whats-new-in-azure-active-directory"></a>Novidades no Azure Active Directory

>Seja notificado sobre quando revisitar esta página para atualizações copiando e colando este URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` no seu ![ícone de RSS](./media/whats-new/feed-icon-16x16.png) leitor de feeds.

O Azure AD recebe melhorias de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de bug
- Funcionalidades preteridas
- Planos de alterações

Esta página é atualizada mensalmente; portanto, visite-a regularmente. Se você estiver procurando itens que têm mais de seis meses, poderá encontrá-los nos [Arquivos de O que há de novo no Azure Active Directory](whats-new-archive.md).

---
## <a name="january-2019"></a>Janeiro de 2019

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Novas configurações de cookies do Proxy de Aplicativo do Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Proxy do aplicativo  
**Funcionalidade do produto:** Controle de Acesso

Apresentamos três novas configurações de cookie, disponíveis para seus aplicativos publicados por meio do Proxy de Aplicativo:

- **Usar Cookie Somente HTTP.** Define o sinalizador **HTTPOnly** em seu acesso de Proxy de Aplicativo e cookies de sessão. Ativar essa configuração fornece benefícios de segurança adicionais, como ajudar a prevenir cópia ou modificação de cookies por meio de script do lado do cliente. É recomendável ativar esse sinalizador (escolher **Sim**) para os benefícios adicionais.

- **Usar um cookie seguro.** Define o sinalizador **Secure** em seu acesso de Proxy de Aplicativo e cookies de sessão. Ativar essa configuração proporciona benefícios de segurança adicionais ao garantir que os cookies só sejam transmitidos em canais seguros TLS, como HTTPS. É recomendável ativar esse sinalizador (escolher **Sim**) para os benefícios adicionais.

- **Usar cookie persistente.** Impede que cookies de acesso expirem quando o navegador da Web é fechado. Esses cookies duram todo o tempo de vida do token de acesso. No entanto, os cookies são redefinidos se o tempo de término é atingido ou se o usuário o exclui manualmente. Recomendamos que você mantenha a configuração padrão de **Não**, apenas ativando essa configuração para aplicativos mais antigos que não compartilham cookies entre processos.

Para obter mais informações sobre os novos cookies, confira [Configurações de cookie para acessar aplicativos locais no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD – janeiro de 2019

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Em janeiro de 2019, adicionamos esses 35 novos aplicativos com suporte para Federação à galeria de aplicativos:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [Talent Palette](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco Umbrella](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Internet Access Administrator](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [Expiration Reminder](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [Verb](https://app.verb.net/login), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO System](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARES for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 for Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/caf%c3%a9xportal%28meetings%29-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)        

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Novas melhorias do Azure AD Identity Protection (versão prévia pública)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Identity Protection  
**Funcionalidade do produto:** Segurança de identidade e proteção

Temos o prazer de anunciar que adicionamos os seguintes aprimoramentos à oferta de versão prévia pública do Azure AD Identity Protection, incluindo:

- Uma interface do usuário mais integrada e atualizada

- APIs adicionais

- Avaliação de risco aprimorada por meio de aprendizado de máquina

- Alinhamento de todo o produto entre usuários arriscados e entradas arriscadas

Para obter mais informações sobre os aprimoramentos, confira [O que é o Azure Active Directory Identity Protection (atualizado)?](https://aka.ms/IdentityProtectionDocs) Para saber mais e compartilhar suas ideias por meio dos prompts no produto.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Novo recurso de Bloqueio de Aplicativo para o aplicativo Microsoft Authenticator em dispositivos iOS e Android

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativo Microsoft Authenticator  
**Funcionalidade do produto:** Segurança de identidade e proteção

Para manter suas senhas únicas, informações do aplicativo e configurações de aplicativo mais seguras, você pode ativar o recurso de Bloqueio de Aplicativo no aplicativo Microsoft Authenticator. Ativar o Bloqueio de Aplicativo significa que você será solicitado a autenticar-se usando seu PIN ou biometria sempre que abrir o aplicativo Microsoft Authenticator.

Para obter mais informações, confira as [Perguntas frequentes do aplicativo Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Funcionalidades de exportação aprimoradas do Azure AD PIM (Privileged Identity Management)

**Tipo:** New recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management

Administradores do PIM (Privileged Identity Management) agora podem exportar todas as atribuições de função ativas e qualificadas para um recurso específico, incluindo atribuições de função para todos os recursos filho. Anteriormente, era difícil para os administradores obter uma lista completa das atribuições de função para uma assinatura e eles precisavam exportar as atribuições de função para cada recurso específico.

Para obter mais informações, confira [Exibir histórico de atividade e auditoria para funções de recurso do Azure no PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Novembro/dezembro de 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Os usuários removidos do escopo de sincronização não alternam mais para contas somente nuvem

**Tipo:** Correção  
**Categoria de serviço:** Gerenciamento de Usuários  
**Funcionalidade do produto:** Diretório

>[!Important]
>Nós ouvimos e entendemos sua frustração devido a essa correção. Portanto, revertemos essa alteração até o momento em que possamos facilitar a correção, para que você possa implementar em sua organização.

Corrigimos um bug no qual o sinalizador DirSyncEnabled de um usuário seria alternado erroneamente para **Falso** quando o objeto de AD DS (Active Directory Domain Services) fosse excluído do escopo de sincronização e, em seguida, movido para a Lixeira no Azure AD no ciclo de sincronização a seguir. Como resultado dessa correção, se o usuário for excluído do escopo de sincronização e, em seguida, restaurado da Lixeira do Azure AD, a conta do usuário permanecerá como sincronizada do AD local, conforme o esperado, e não poderá ser gerenciada na nuvem, já que a SoA (origem da autoridade) permanecerá como AD local.

Antes dessa correção, havia um problema quando o sinalizador DirSyncEnabled era alternado para Falso. Isso resultava na impressão errada de que essas contas eram convertidas em objetos somente nuvem e que as contas poderiam ser gerenciadas na nuvem. No entanto, as contas ainda retinham a SoA como propriedades locais e todas as propriedades sincronizadas (atributos de sombra) provenientes do AD local. Essa condição causou vários problemas no Azure AD e em outras cargas de trabalho de nuvem (como o Exchange Online) que esperavam tratar essas contas como sincronizadas do AD, mas agora se comportavam como contas somente nuvem.

No momento, a única maneira de converter verdadeiramente uma conta do AD de sincronizada para uma conta somente nuvem é desabilitando o DirSync no nível do locatário, que disparará uma operação de back-end para transferir a SoA. Esse tipo de alteração da SoA exige (mas não limita-se a) limpar todos os atributos relacionados locais (como LastDirSyncTime e atributos de sombra) e enviar um sinal para outras cargas de trabalho de nuvem para que o respectivo objeto também seja convertido em uma conta somente nuvem.

Essa correção, consequentemente, impede atualizações diretas no atributo ImmutableID de um usuário sincronizado do AD, que em alguns cenários no passado foram necessários. Por padrão, o ImmutableID de um objeto no Azure AD, como o nome indica, é imutável. Os novos recursos implementados no Azure AD Connect Health e no cliente de Sincronização do Azure AD Connect estão disponíveis para tratar esses cenários:

- **Atualização do ImmutableID em grande escala para muitos usuários em uma abordagem em etapas**
  
  Por exemplo, você precisa fazer uma migração longa entre florestas do AD DS. Solução: Use o Azure AD Connect para **Configurar Âncora de Origem** e, enquanto o usuário migra, copie os valores ImmutableID existentes do Azure AD para o atributo ms-DS-Consistency-Guid do usuário local do AD DS da nova floresta. Para obter mais informações, consulte [Usar ms-DS-ConsistencyGuid como sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Atualizações do ImmutableID em grande escala para muitos usuários em um único disparo**

  Por exemplo, ao implementar o Azure AD Connect, você comete um erro e agora precisa alterar o atributo SourceAnchor. Solução: Desabilite o DirSync no nível do locatário e limpe todos os valores ImmutableID inválidos. Para obter mais informações, consulte [Desativar a sincronização de diretório do Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Corresponder novamente o usuário local com um usuário existente no Azure AD** Por exemplo, um usuário que foi recriado no AD DS gera uma duplicata na conta do Azure AD, em vez de correspondê-la novamente com uma conta do Azure AD (objeto órfão) existente. Solução: Use o Azure AD Connect Health no portal do Azure para remapeamento da Âncora de Origem/ImmutableID. Para obter mais informações, consulte [Cenário de objeto órfão](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Alterações da falha: Atualizações no esquema de auditoria e de logs de entrada por meio do Azure Monitor

**Tipo:** Recurso alterado  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

No momento, estamos publicando os fluxos de log de Auditoria e Entrada por meio do Azure Monitor, para que você possa integrar os arquivos de log às suas ferramentas SIEM ou ao Log Analytics. Com base em seus comentários e em preparação para o comunicado de disponibilidade geral desse recurso, estamos fazendo as alterações a seguir em nosso esquema. Essas alterações de esquema e suas atualizações de documentação relacionadas ocorrerão até a primeira semana de janeiro.

#### <a name="new-fields-in-the-audit-schema"></a>Novos campos no esquema de Auditoria
Estamos adicionando um novo campo **Tipo de Operação**, para fornecer o tipo de operação executada no recurso. Por exemplo, **Adicionar**, **Atualizar** ou **Excluir**.

#### <a name="changed-fields-in-the-audit-schema"></a>Campos alterados no esquema de Auditoria
Os seguintes campos estão sendo alterados no esquema de Auditoria:

|Nome do campo|O que mudou|Valores antigos|Novos valores|
|----------|------------|----------|----------|
|Categoria|Esse era o campo **Nome do Serviço**. Agora ele passou a ser o campo **Categorias de Auditoria**. **Nome do Serviço** foi renomeado para o campo **loggedByService**.|<ul><li>Provisionamento de conta de usuário</li><li>Diretório principal</li><li>Redefinição de senha de autoatendimento</li></ul>|<ul><li>Gerenciamento de Usuários</li><li>Gerenciamento de grupos</li><li>Gerenciamento de aplicativos</li></ul>|
|targetResources|Inclui **TargetResourceType** no nível superior.|&nbsp;|<ul><li>Política</li><li>Aplicativo</li><li>Usuário</li><li>Agrupar</li></ul>|
|loggedByService|Fornece o nome do serviço que gerou o log de auditoria.|Nulo|<ul><li>Provisionamento de conta de usuário</li><li>Diretório principal</li><li>Redefinição de senha de autoatendimento</li></ul>|
|Result|Fornece o resultado dos logs de auditoria. Anteriormente, isso era enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li></ul>|<ul><li>Sucesso</li><li>Failure</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Campos alterados no esquema de Entrada
Os seguintes campos estão sendo alterados no esquema de Entrada:

|Nome do campo|O que mudou|Valores antigos|Novos valores|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Esse era o campo **conditionalaccessPolicies**. Agora ele passou a ser o campo **appliedConditionalAccessPolicies**.|Sem alteração|Sem alteração|
|conditionalAccessStatus|Fornece o resultado do Status da Política de Acesso Condicional na entrada. Anteriormente, isso era enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Sucesso</li><li>Failure</li><li>Não aplicado</li><li>Desabilitado</li></ul>|
|appliedConditionalAccessPolicies: resultado|Fornece o resultado do Status da Política de Acesso Condicional individual na entrada. Anteriormente, isso era enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Sucesso</li><li>Failure</li><li>Não aplicado</li><li>Desabilitado</li></ul>|

Para obter mais informações sobre o esquema, confira [Interpretar o esquema de logs de auditoria do Azure AD no Azure Monitor (versão prévia)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Melhorias de Proteção de Identidade no modelo de machine learning supervisionado e no mecanismo de pontuação de risco

**Tipo:** Recurso alterado  
**Categoria de serviço:** Identity Protection  
**Funcionalidade do produto:** Pontuações de risco

As melhorias no mecanismo e avaliação de risco de entrada e de usuário relacionadas à Proteção de Identidade podem ajudar a melhorar a cobertura e a precisão de risco de usuário. Os administradores talvez observem que o nível de risco de usuário deixou de estar vinculado ao nível de risco de detecções específicas e que há um aumento no número e no nível de eventos de entrada de risco.

As detecções de risco agora são avaliadas pelo modelo de machine learning supervisionado, que calcula o risco de usuário usando recursos adicionais de entradas do usuário e um padrão de detecções. Com base nesse modelo, o administrador poderá encontrar usuários com pontuações de alto risco, mesmo se as detecções associadas a esse usuário forem de risco baixo ou médio. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Os administradores podem redefinir suas próprias senhas usando o aplicativo Microsoft Authenticator (versão prévia pública)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Redefinição de senha por autoatendimento  
**Funcionalidade do produto:** Autenticação de usuário

Os administradores do Azure AD agora podem redefinir suas próprias senhas usando as notificações do aplicativo Microsoft Authenticator ou um código em qualquer aplicativo de autenticador móvel ou token de hardware. Para redefinir suas próprias senhas, os administradores agora poderão usar dois dos seguintes métodos:

- Notificação do aplicativo Microsoft Authenticator

- Outro aplicativo de autenticador móvel/código de token de hardware

- Email

- chamada telefônica

- mensagem de texto

Para obter mais informações sobre como usar o aplicativo Microsoft Authenticator para redefinir senhas, confira [Redefinição de senha de autoatendimento do Azure AD – aplicativo móvel e SSPR (versão prévia)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nova função Administrador de Dispositivo de Nuvem do Azure AD (versão prévia pública)

**Tipo:** New recurso  
**Categoria de serviço:** Registro e Gerenciamento de Dispositivos  
**Funcionalidade do produto:** Controle de acesso

Os administradores podem atribuir usuários à nova função Administrador de Dispositivo de Nuvem para executar tarefas de administrador de dispositivo de nuvem. Os usuários que recebem a função Administradores de Dispositivo de Nuvem podem habilitar, desabilitar e excluir dispositivos no Azure AD, além de poder ler as chaves do BitLocker do Windows 10 (se houver) no portal do Azure.

Para obter mais informações sobre funções e permissões, confira [Atribuindo funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Gerenciar seus dispositivos usando o novo carimbo de data/hora de atividade no Azure AD (versão prévia pública)

**Tipo:** New recurso  
**Categoria de serviço:** Registro e Gerenciamento de Dispositivos  
**Funcionalidade do produto:** Gerenciamento do Ciclo de Vida do Dispositivo

Percebemos que, ao longo do tempo, você precisa atualizar e desativar os dispositivos de suas organizações no Azure AD, para evitar a presença de dispositivos obsoletos no ambiente. Para ajudar com esse processo, o Azure AD agora atualiza seus dispositivos com um novo carimbo de data/hora de atividade, ajudando você a gerenciar o ciclo de vida do dispositivo.

Para obter mais informações sobre como obter e usar esse carimbo de data/hora, confira [Como: Gerenciar os dispositivos obsoletos no Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Os administradores podem exigir que os usuários aceitem os Termos de uso em cada dispositivo

**Tipo:** New recurso  
**Categoria de serviço:** Termos de Uso  
**Funcionalidade do produto:** Governança
 
Os administradores agora podem ativar a opção **Exigir o consentimento dos usuários em todos os dispositivos** para exigir que os usuários aceitem os Termos de uso em todos os dispositivos que estiverem usando em seu locatário.

Para obter mais informações, confira a [seção Termos de uso por dispositivo do recurso Termos de uso do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Os administradores podem configurar os Termos de uso para que expirem com base em um agendamento recorrente

**Tipo:** New recurso  
**Categoria de serviço:** Termos de Uso  
**Funcionalidade do produto:** Governança
 

Os administradores agora podem ativar a opção **Expirar consentimentos** para fazer com que os Termos de uso expirem para todos os usuários com base no agendamento recorrente especificado. O agendamento pode ser anual, semestral, trimestral ou mensalmente. Depois que os Termos de uso expirarem, os usuários precisarão aceitá-los novamente.

Para obter mais informações, confira a [seção Adicionar Termos de uso do recurso Termos de uso do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Os administradores podem configurar os Termos de uso para que expirem com base no agendamento de cada usuário

**Tipo:** New recurso  
**Categoria de serviço:** Termos de Uso  
**Funcionalidade do produto:** Governança

Os administradores agora podem especificar uma duração na qual o usuário precisam aceitar novamente os Termos de uso. Por exemplo, os administradores podem especificar que os usuários precisam aceitar novamente os Termos de uso a cada 90 dias.

Para obter mais informações, confira a [seção Adicionar Termos de uso do recurso Termos de uso do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Novos emails do Azure AD PIM (Privileged Identity Management) para funções do Azure Active Directory

**Tipo:** New recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Os clientes que usam o Azure AD PIM (Privileged Identity Management) agora podem receber um email de resumo semanal, incluindo as seguintes informações para os últimos sete dias:

- Visão geral sobre as principais atribuições de função qualificadas e permanentes

- Número de usuários que ativaram funções

- Número de usuários atribuídos a funções no PIM

- Número de usuários atribuídos a funções fora do PIM

- Número de usuários que passaram a ser "permanentes" no PIM

Para obter mais informações sobre o PIM e as notificações por email disponíveis, confira [Notificações por email no PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>O licenciamento baseado em grupo agora está disponível

**Tipo:** Recurso alterado  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Diretório

O licenciamento baseado em grupo está fora da versão prévia pública e agora está disponível. Como parte dessa versão geral, tornamos esse recurso mais escalonável e adicionamos a capacidade de reprocessar as atribuições de licenciamento baseado em grupo para um único usuário e a capacidade de usar o licenciamento baseado em grupo com licenças do Office 365 E3/A3.

Para obter mais informações sobre o licenciamento baseado em grupo, confira [O que é o licenciamento baseado em grupo no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Novos Aplicativos Federados disponíveis na galeria de aplicativos do Azure AD – novembro de 2018

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Em novembro de 2018, adicionamos estes 26 novos aplicativos com suporte de Federação à galeria de aplicativos:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://www.drivedollar.com/Account/Login), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps – Classic Test](https://test.plexonline.com/signon), [Plex Apps – Classic](https://www.plexonline.com/signon), [Plex Apps – UX Test](https://test.cloud.plex.com/sso), [Plex Apps – UX](https://cloud.plex.com/sso), [Plex Apps – IAM](https://accounts.plex.com/) e [CRAFTS – Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration) 

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Outubro de 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Os Logs do Azure AD agora funcionam com o Azure Log Analytics (visualização pública)

**Tipo:** New recurso  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

Temos o prazer de anunciar que agora você pode encaminhar seus logs do Azure AD ao Azure Log Analytics! Esse recurso mais solicitados ajuda dão acesso ainda melhor a análise para seu negócio, operações e segurança, bem como uma maneira de ajudar a monitorar sua infra-estrutura. Para obter mais informações, consulte o [logs de atividades de diretório do Azure Active Directory no Azure Log Analytics agora disponível](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD - outubro de 2018

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Em outubro de 2018, adicionamos esses 14 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Meus Pontos de Prêmio](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Teclado de discagem, [ON24 Ambiente Virtual](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Controle do Workspot](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Notificações por Email de serviços de domínio do AD do Azure

**Tipo:** New recurso  
**Categoria de serviço:** Azure AD Domain Services  
**Funcionalidade do produto:** Azure AD Domain Services

Os Serviços de Domínio do Azure AD fornecem alertas no portal do Azure sobre configurações incorretas ou problemas com o domínio gerenciado. Esses alertas incluem guias passo a passo para que você possa tentar corrigir os problemas sem precisar entrar em contato com o suporte.

A partir de outubro, você poderá personalizar as configurações de notificação do seu domínio gerenciado para que, quando novos alertas ocorrerem, um e-mail seja enviado a um grupo designado de pessoas, eliminando a necessidade de verificar constantemente o portal em busca de atualizações.

Para obter mais informações, consulte [Configurações de notificação nos Serviços de Domínio do AD do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD portal dá suporte ao uso do domínio ForceDelete API para excluir os domínios personalizados 

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerenciamento de diretórios  
**Funcionalidade do produto:** Diretório

Temos o prazer de anunciar que agora você pode usar o domínio ForceDelete API para excluir nomes de domínios personalizados renomeando referências, como usuários, grupos e aplicativos de seu nome de domínio personalizado (contoso.com) volta para o (de nome de domínio padrão inicial de forma assíncrona Contoso.onmicrosoft.com).

Essa alteração ajuda você a excluir mais rapidamente seus nomes de domínio personalizados se sua organização não usar mais o nome ou se você precisar usar o nome de domínio com outro AD do Azure.

Para obter mais informações, consulte [Excluir um nome de domínio personalizado](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Setembro de 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Permissões de função de administrador atualizadas para grupos dinâmicos

**Tipo:** Correção  
**Categoria de serviço:** Gerenciamento de grupos  
**Funcionalidade do produto:** Colaboração

Corrigimos um problema para que funções de administrador específicas agora possam criar e atualizar regras de associação dinâmica, sem a necessidade de ser o proprietário do grupo.

As funções são:

- Administrador global ou Escritor da empresa

- Administrador de serviços do Intune

- Administrador da conta de usuário

Para obter mais informações, consulte [Criar um grupo dinâmico e verificar o status](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Definições de configuração simplificada de SSO (logon único) para alguns aplicativos de terceiros

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Sabemos que configurar SSO (logon único) para aplicativos SaaS (software como serviço) pode ser um desafio devido à natureza específica de cada configuração de aplicativo. Criamos uma experiência de configuração simplificada para preencher automaticamente as definições de configuração de SSO para os seguintes aplicativos SaaS de terceiros:

- Zendesk

- ArcGIS Online

- Jamf Pro

Para começar a usar essa experiência de um único clique, acesse a página do **portal do Azure** > **configuração de SSO** do aplicativo. Para obter mais informações, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Página: Azure Active Directory – Onde seus dados estão localizados?

**Tipo:** New recurso  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** GoLocal

Selecione a região da sua empresa na página **Azure Active Directory – Onde seus dados estão localizados** para exibir qual datacenter do Azure armazena os dados em repouso do Azure AD de todos os seus serviços do Azure AD. Você pode filtrar as informações por serviços específicos do Azure AD da região da sua empresa.

Para acessar esse recurso e obter mais informações, consulte [Azure Active Directory – Onde seus dados estão localizados](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Novo plano de implantação disponível para o painel de acesso Meus Aplicativos

**Tipo:** New recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** SSO

Confira o novo plano de implantação que está disponível para o painel de acesso Meus Aplicativos (https://aka.ms/deploymentplans).
O painel de acesso Meus Aplicativos fornece aos usuários um único lugar para localizar e acessar os aplicativos. Esse portal também fornece aos usuários oportunidades de autoatendimento, como solicitar acesso a aplicativos e grupos ou gerenciamento de acesso a esses recursos em nome de outros.

Para obter mais informações, consulte [O que é o portal Meus Aplicativos?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nova guia de Solução de problemas e Suporte na página de entradas de Logs do portal do Azure

**Tipo:** New recurso  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

A nova guia **Solução de problemas e Suporte** na página **Entradas** do portal do Azure serve para ajudar administradores e engenheiros de suporte a solucionar problemas relacionados a entradas do Azure AD. Esta nova guia oferece o código de erro, a mensagem de erro e as recomendações de correção (caso haja alguma) para ajudar a resolver o problema. Se você não conseguir resolver o problema, também oferecemos uma nova maneira de criar um tíquete de suporte usando a experiência de **Copiar para a área de transferência**, que preenche os campos **ID da Solicitação** e **Data (UTC)** do arquivo de log em seu tíquete de suporte.  

![Logs de entrada com a nova guia](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Suporte aprimorado para propriedades de extensão personalizadas usadas para criar regras de associação dinâmica

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerenciamento de grupos  
**Funcionalidade do produto:** Colaboração

Com essa atualização, agora você pode clicar no link **Obter propriedades de extensão personalizadas** no construtor de regra dinâmico de grupo dos usuários, inserir sua ID do aplicativo exclusiva e receber a lista completa das propriedades de extensão personalizadas para usar ao criar uma regra de associação dinâmica para usuários. Essa lista também pode ser atualizada para obter as novas propriedades de extensão personalizadas do aplicativo.

Para obter mais informações sobre como usar propriedades de extensão personalizadas para regras de associação dinâmica, consulte [Propriedades de extensão e propriedades de extensão personalizadas](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novos aplicativos de cliente aprovados para acesso condicional com base em aplicativos do Microsoft Azure Active Directory

**Tipo:** Plano para alteração  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** Segurança de identidade e proteção

Os seguintes aplicativos estão na lista de [aplicativos do cliente aprovados](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

Para obter mais informações, consulte:

- [Acesso condicional com base no aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Novo suporte para redefinição de senha de autoatendimento da tela de bloqueio do Windows 7/8/8.1

**Tipo:** New recurso  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** Autenticação de usuário

Depois de configurar esse novo recurso, os usuários verão um link para redefinir a senha da tela de **bloqueio** de um dispositivo que executa o Windows 7, Windows 8 ou Windows 8.1. Ao clicar nesse link, o usuário é guiado pelo mesmo fluxo de redefinição de senha que no navegador da Web.

Para obter mais informações, consulte [Como habilitar a redefinição de senha do Windows 7, 8 e 8.1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Aviso de alteração: Os códigos de autorização não ficarão mais disponíveis para reutilização 

**Tipo:** Plano para alteração  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de usuário

A partir de 15 de novembro de 2018, o Azure AD deixará de aceitar códigos de autenticação usados anteriormente para aplicativos. Essa alteração de segurança ajuda a deixar o Azure AD em conformidade com a especificação do OAuth e será aplicada aos pontos de extremidade v1 e v2.

Se seu aplicativo reutiliza códigos de autorização para obter tokens para vários recursos, recomendamos que você use o código para obter um token de atualização e, em seguida, utilize esse token de atualização para adquirir tokens adicionais para outros recursos. Os códigos de autorização só podem ser usados uma vez; porém, os códigos de atualização podem ser usados várias vezes em diferentes recursos. Um aplicativo que tente reutilizar um código de autenticação durante o fluxo de código OAuth receberá um erro invalid_grant.

Para isso e outras alterações relacionadas a protocolos, consulte [a lista completa das novidades em autenticação](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Novos Aplicativos Federados disponíveis na galeria de Aplicativos do Azure AD – setembro de 2018

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Em setembro de 2018, adicionamos esses 16 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Software de Recrutamento Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO for Azure, SurveyMonkey, [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Suporte para métodos de transformações de declarações adicionais

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Apresentamos os novos métodos de transformação de declaração, ToLower () e ToUpper (), que podem ser aplicados aos tokens SAML por meio da página baseada em SAML **Configuração de logon único**.

Para obter mais informações, consulte [Como personalizar declarações emitidas no token SAML para aplicativos empresariais no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Interface do usuário atualizada da configuração de aplicativo baseado em SAML (versão prévia)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Como parte da nossa interface do usuário atualizada de configuração de aplicativo baseado em SAML, você obterá:

- Uma experiência atualizada de explicação passo a passo para configurar seus aplicativos baseados em SAML.

- Maior visibilidade sobre o que está ausente ou incorreto em sua configuração.

- A capacidade de adicionar vários endereços de email para notificação de expiração de certificado.

- Novos métodos de transformação de declaração, ToLower () e ToUpper () e muito mais.

- Uma forma de carregar seu próprio token de assinatura de certificado para aplicativos empresariais.

- Uma maneira de definir o formato NameID para aplicativos SAML e uma maneira de definir o valor NameID como Extensões de diretório.

Para ativar este modo de exibição atualizado, clique no link **Experimentar nossa nova experiência** na parte superior da página **Logon único**. Para obter mais informações, confira [Tutorial: Configurar o logon único baseado em SAML para um aplicativo com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).

---

## <a name="august-2018"></a>Agosto de 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Alterações em intervalos de endereços IP do Azure Active Directory

**Tipo:** Plano para alteração  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Plataforma

Estamos introduzindo intervalos de IP maiores no Azure AD, o que significa que, se você tiver configurado intervalos de endereços IP do Azure AD para firewalls, roteadores ou grupos de segurança de rede, será necessário atualizá-los. Estamos fazendo essa atualização para que você não tenha que alterar as configurações do intervalo IP, do roteador ou do grupo de segurança de rede novamente quando o Azure AD adicionar novos pontos de extremidade. 

O tráfego de rede está se movendo para esses novos intervalos nos próximos dois meses. Para continuar com o serviço ininterrupto, você deve adicionar esses valores atualizados aos seus endereços IP antes de 10 de setembro de 2018:

- 20.190.128.0/18 

- 40.126.0.0/18 

É altamente recomendável não remover os intervalos de endereços IP antigos até que todo o tráfego da rede seja movido para os novos intervalos. Para obter atualizações sobre a movimentação e saber quando você pode remover os intervalos antigos, consulte [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Aviso de alteração: Os códigos de autorização não ficarão mais disponíveis para reutilização 

**Tipo:** Plano para alteração  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de usuário

A partir de 15 de novembro de 2018, o Azure AD deixará de aceitar códigos de autenticação usados anteriormente para aplicativos. Essa alteração de segurança ajuda a deixar o Azure AD em conformidade com a especificação do OAuth e será aplicada aos pontos de extremidade v1 e v2.

Se seu aplicativo reutiliza códigos de autorização para obter tokens para vários recursos, recomendamos que você use o código para obter um token de atualização e, em seguida, utilize esse token de atualização para adquirir tokens adicionais para outros recursos. Os códigos de autorização só podem ser usados uma vez; porém, os códigos de atualização podem ser usados várias vezes em diferentes recursos. Um aplicativo que tente reutilizar um código de autenticação durante o fluxo de código OAuth receberá um erro invalid_grant.

Para isso e outras alterações relacionadas a protocolos, consulte [a lista completa das novidades em autenticação](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Gerenciamento de informações de segurança convergida para SSPR (senha de autoatendimento) e MFA (Autenticação Multifator do Microsoft Azure)

**Tipo:** New recurso  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** Autenticação de usuário
 
Esse novo recurso ajuda as pessoas a gerenciar suas informações de segurança (por exemplo, número de telefone, aplicativo móvel e assim por diante) para SSPR e MFA em um único local e a experiência; anteriormente, isso era feito em dois locais diferentes.

Essa experiência convergida também funciona para pessoas que usam SSPR ou MFA. Além disso, se sua organização não impõe registro de MFA ou SSPR, as pessoas ainda podem registrar métodos de informações de segurança de MFA ou SSPR permitidos pela sua organização no portal Meus aplicativos.

Esta é uma visualização pública opt-in. Os administradores podem ativar a nova experiência (se desejado) para um grupo selecionado ou para todos os usuários em um locatário. Para obter mais informações sobre a experiência convergida, consulte o [Blog da experiência convergida](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nova configuração de cookies somente HTTP em aplicativos do proxy de aplicativo do Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Proxy do aplicativo  
**Funcionalidade do produto:** Controle de Acesso

Há uma nova configuração chamada **Cookies somente HTTP** em seus aplicativos de proxy de aplicativo. Essa configuração ajuda a fornecer segurança adicional, pois inclui o sinalizador HTTPOnly no cabeçalho de resposta HTTP para os cookies de sessão e acesso de proxy de aplicativo, impede o acesso ao cookie de um script do lado do cliente e também impede ações como copiar ou modificar o cookie. Embora esse sinalizador não tenha sido usado anteriormente, os cookies sempre foram criptografados e transmitidos usando uma conexão SSL para ajudar a proteger contra modificações inadequadas.

Essa configuração não é compatível com aplicativos que usam os controles ActiveX, como Área de Trabalho Remota. Caso esteja nessa situação, é recomendável desativar essa configuração.

Para obter mais informações sobre a configuração Cookies somente HTTP, consulte [Publicar aplicativos usando o Proxy de Aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>O PIM (Privileged Identity Management) para recursos do Azure dá suporte aos tipos de recurso do grupo de gerenciamento

**Tipo:** New recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Agora, as configurações de ativação e atribuição Just-In-Time podem ser aplicadas aos tipos de recursos de Grupo de Gerenciamento, como você já faz para Assinaturas, Grupos de Recursos e Recursos (tais como VMs, Serviços de Aplicativos e muito mais). Além disso, qualquer pessoa com uma função que conceda acesso de administrador para um Grupo de Gerenciamento pode descobrir e gerenciar tal recurso no PIM.

Para obter mais informações sobre o PIM e os recursos do Azure, consulte [Descobrir e gerenciar recursos do Azure usando o Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>O acesso do aplicativo (versão prévia) fornece um acesso mais rápido ao portal do Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Hoje, ao ativar uma função usando o PIM, as permissões podem levar mais de 10 minutos para entrar em vigor. Se você optar por usar o acesso do aplicativo, que está em visualização pública no momento, os administradores poderão acessar o portal do Azure AD assim que a solicitação de ativação for concluída.

Atualmente, o acesso do aplicativo dá suporte apenas à experiência do portal do Azure AD e aos recursos do Azure. Para obter mais informações sobre o PIM e o acesso do Aplicativo, confira [O que é o Privileged Identity Management do Azure AD?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Novos Aplicativos Federados disponíveis na galeria de Aplicativos do Azure AD – agosto de 2018

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Em agosto de 2018, adicionamos esses 16 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Sauce Labs - Mobile and Web Testing](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [Way We Do](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (por Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [Dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - Expense reports](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>O suporte nativo ao Tableau já está disponível no Proxy de Aplicativo do Azure AD

**Tipo:** Recurso alterado  
**Categoria de serviço:** Proxy do aplicativo  
**Funcionalidade do produto:** Controle de Acesso

Com nossa atualização do OpenID Connect para o protocolo OAuth 2.0 Code Grant para nosso protocolo de pré-autenticação, você não precisa mais fazer nenhuma configuração adicional para usar o Tableau com Proxy de Aplicativo. Essa alteração de protocolo também ajuda o Proxy de Aplicativo a oferecer um suporte melhor a aplicativos mais modernos usando apenas redirecionamentos de HTTP, que normalmente recebem suporte em marcações HTML e JavaScript.

Para obter mais informações sobre o suporte nativo para Tableau, consulte [Proxy de Aplicativo do Azure AD agora com suporte nativo para Tableau](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Novo suporte para adicionar o Google como provedor de identidade para usuários convidados B2B no Azure Active Directory (versão prévia)

**Tipo:** New recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C

Ao configurar a federação com o Google em sua organização, você pode permitir que os usuários convidados do Gmail entrem em seus aplicativos e recursos compartilhados usando as respectivas contas do Google existentes, sem precisar criar uma MSA (Conta Microsoft) pessoal ou uma conta do Azure AD.

Esta é uma visualização pública opt-in. Para obter mais informações sobre a federação do Google, consulte [Adicionar o Google como provedor de identidade para usuários convidados B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Julho de 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Melhorias nas notificações por email do Azure Active Directory

**Tipo:** Recurso alterado  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidades
 
Os emails do Active Directory do Azure (Azure AD) agora apresentam um design atualizado, bem como alterações no endereço de email do remetente e no nome de exibição do remetente, quando enviados dos seguintes serviços:
 
- Revisões de acesso do Azure AD
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Gerenciamento de identidades com privilégios do AD do Azure
- Notificação de certificado expirando do Aplicativo Enterprise
- Notificações do serviço de provisionamento do Aplicativo Enterprise
 
As notificações por email serão enviadas a partir do seguinte endereço de email e nome de exibição:

- Endereço de email: azure-noreply@microsoft.com
- Nome de exibição: Microsoft Azure
 
Para obter um exemplo de alguns dos novos designs de email e mais informações, consulte [Notificações por email no PIM do Azure AD](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Os Logs de atividade do Azure AD agora estão disponíveis por meio do Azure Monitor

**Tipo:** New recurso  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

Os Logs de atividade do Azure AD agora estão disponíveis na visualização pública do Monitor do Azure (serviço de monitoramento da plataforma do Azure). O Monitor do Azure oferece retenção a longo prazo e integração perfeita, além dessas melhorias:

- Retenção de longo prazo roteando seus arquivos de log para sua própria conta de armazenamento do Azure.

- Integração perfeita do SIEM, sem exigir que você escreva ou mantenha scripts personalizados.

- Integração perfeita com suas próprias soluções personalizadas, ferramentas de análise ou soluções de gerenciamento de incidentes.

Para obter mais informações sobre esses novos recursos, consulte o [log de atividades do Azure AD no diagnóstico do Monitor do Azure agora na visualização pública](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) e nossa documentação, [logs de atividades do Azure Active Directory no Azure Monitor (visualização)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informações de acesso condicional adicionadas ao relatório de logins do Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Segurança de identidade e proteção
 
Esta atualização permite ver quais políticas são avaliadas quando um usuário faz login junto com o resultado da política. Além disso, o relatório agora inclui o tipo de aplicativo cliente usado pelo usuário, para que você possa identificar o tráfego de protocolo herdado. Agora, as entradas de relatório também podem ser pesquisadas em busca de um ID de correlação, que pode ser encontrado na mensagem de erro do usuário, e pode ser usado para identificar e solucionar problemas da solicitação de entrada correspondente.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Veja autenticações herdadas por meio dos logs de atividades de Entradas

**Tipo:** New recurso  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório
 
Com a introdução do campo **Client App** nos logs de atividades de login, os clientes agora podem ver usuários que estão usando autenticações herdadas. Os clientes poderão acessar essas informações usando a API do MS Graph de entradas ou por meio dos logs de atividades de entrada no portal do Azure AD, onde você pode usar o controle **Aplicativo Cliente** para filtrar as autenticações herdadas. Confira a documentação para obter mais detalhes.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD - julho de 2018

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Em julho de 2018, adicionamos esses 16 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [Certos SSO de administrador](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC Staging, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial) , PowerSchool Unified Classroom, [Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Suporte Remoto Bomgar](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [ImagineVisualizar Web](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [Base de Habilidades](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Novas integrações de aplicativos SaaS para provisionamento de usuários - julho de 2018

**Tipo:** New recurso  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** Integração de terceiros
 
O Azure AD permite que você automatize a criação, a manutenção e a remoção de identidades de usuário em aplicativos SaaS, como Dropbox, Salesforce, ServiceNow e muito mais. Para julho de 2018, adicionamos o suporte ao aprovisionamento de usuários aos seguintes aplicativos na galeria de aplicativos do Azure AD:

- [Cisco Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial)

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Para obter uma lista de todos os aplicativos que dão suporte ao provisionamento de usuário na Galeria do Azure AD, consulte [integração de aplicativos SaaS ao Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Conecte o Health for Sync - Uma maneira mais fácil de corrigir erros de sincronização de atributos órfãos e duplicados

**Tipo:** New recurso  
**Categoria de serviço:** AD Connect  
**Funcionalidade do produto:** Monitoramento e relatório
 
O Azure AD Connect Health apresenta a correção de autoatendimento para ajudá-lo a destacar e corrigir erros de sincronização. Esse recurso soluciona erros de sincronização de atributos duplicados e corrige objetos que são órfãos do AD do Azure. Este diagnóstico tem os seguintes benefícios:

- Diminui os erros de sincronização de atributos duplicados, fornecendo correções específicas

- Aplica uma correção para cenários dedicados do AD do Azure, resolvendo erros em uma única etapa

- Nenhuma atualização ou configuração é necessária para ativar e usar esse recurso

Para obter mais informações, consulte [Diagnosticar e corrigir erros de sincronização de atributos duplicados](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Atualizações visuais nas experiências de entrada do Azure AD e MSA

**Tipo:** Recurso alterado  
**Categoria de serviço:** AD do Azure  
**Funcionalidade do produto:** Autenticação de usuário

Atualizamos a interface do usuário para a experiência de login de serviços on-line da Microsoft, como no Office 365 e no Azure. Essa alteração torna as telas menos confusas e mais simples. Para obter mais informações sobre essa alteração, consulte as [próximas melhorias no blog de experiência de logon do Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/).

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nova versão do Azure AD Connect - julho de 2018

**Tipo:** Recurso alterado  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** Gerenciamento do ciclo de vida de identidades

A versão mais recente do Azure AD Connect inclui: 

- Correções de bugs e atualizações de capacidade de suporte 

- Disponibilidade geral da integração federada de Ping

- Atualizações para o cliente mais recente do SQL 2012 

Para obter mais informações sobre essa atualização, confira [Azure AD Connect: Histórico de lançamento de versões](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-tou-end-user-ui"></a>Atualizações da interface do usuário final dos Termos de Uso (ToU)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Termos de Uso  
**Funcionalidade do produto:** Governança

Estamos atualizando a sequência de aceitação na interface do usuário final do TOU.

**Texto atual.** Para acessar os recursos do [tenantName], você deve aceitar os termos de uso.<br>**Novo texto.** Para acessar o recurso [tenantName], você deve ler os termos de uso.

**Texto atual:** A escolha de aceitação significa que você concorda com todos os termos de uso acima.<br>**Novo texto:** Clique em Aceitar para confirmar que leu e compreendeu os termos de uso.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>A autenticação de passagem dá suporte a aplicativos e protocolos herdados

**Tipo:** Recurso alterado  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de usuário
 
A autenticação de passagem agora suporta protocolos e aplicativos legados. As limitações a seguir agora são totalmente suportadas:

- Logins de usuário para aplicativos cliente legados do Office, Office 2010 e Office 2013, sem exigir autenticação moderna.

- Acesso ao compartilhamento de calendário e informações livres/ocupadas nos ambientes híbridos do Exchange somente no Office 2010.

- Entradas de usuário para aplicativos cliente do Skype for Business sem a necessidade de autenticação moderna.

- Entradas de usuário no PowerShell versão 1.0.

- O Programa de Inscrição de Dispositivos da Apple (Apple DEP), usando o Assistente de Configuração do iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Gerenciamento de informações de segurança convergida para redefinição de senha de autoatendimento e a autenticação multifator

**Tipo:** New recurso  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** Autenticação de usuário

Esse novo recurso permite que os usuários gerenciem suas informações de segurança (por exemplo, número de telefone, endereço de email, aplicativo móvel etc.) para redefinição de senha de autoatendimento (SSPR) e autenticação de vários fatores (MFA) em uma única experiência. Os usuários não precisarão mais registrar as mesmas informações de segurança para SSPR e MFA em duas experiências diferentes. Essa nova experiência também se aplica a usuários que possuem SSPR ou MFA.

Se uma organização não estiver aplicando o registro de MFA ou SSPR, os usuários poderão registrar suas informações de segurança no portal **Meus aplicativos**. A partir daí, os usuários podem registrar qualquer método habilitado para MFA ou SSPR. 

Esta é uma visualização pública opt-in. Os administradores podem ativar a nova experiência (se desejado) para um grupo selecionado de usuários ou todos os usuários em um locatário.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Use o aplicativo Microsoft Authenticator para verificar sua identidade quando você redefinir sua senha

**Tipo:** Recurso alterado  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** Autenticação de usuário

Esse recurso permite que não administradores verifiquem sua identidade ao redefinir uma senha usando uma notificação ou código do Microsoft Authenticator (ou qualquer outro aplicativo autenticador). Depois que os administradores ativarem esse método de redefinição de senha de autoatendimento, os usuários que tiverem registrado um aplicativo móvel por meio de aka.ms/mfasetup ou aka.ms/setupsecurityinfo poderão usar o aplicativo móvel como um método de verificação ao redefinir a senha.

A notificação de aplicativos para dispositivos móveis só pode ser ativada como parte de uma política que exige dois métodos para redefinir sua senha.

---
