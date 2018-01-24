---
title: "O que há de novo? Notas de versão do Azure Active Directory | Microsoft Docs"
description: "Saiba o que há de novo com o Azure AD (Azure Active Directory) incluindo as mais recentes notas de versão, problemas conhecidos, correções de bug, funcionalidades preteridas e alterações futuras."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
featureFlags: clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 6e1cf6e2ee717ef7629e1388d7bca2090eed46fa
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="whats-new-in-azure-active-directory"></a>Novidades no Azure Active Directory




> Mantenha-se atualizado com o que há de novo no Azure Active Directory assinando nosso [![feed](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [RSS](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us).



Estamos constantemente aperfeiçoando o Azure Active Directory. Para que você esteja a par das últimas novidades do desenvolvimento, este tópico fornece informações sobre:

-   As versões mais recentes 
-   Problemas conhecidos 
-   Correções de bug 
-   Funcionalidades preteridas 
-   Planos de alterações 

Revisite esta página regularmente, pois estamos realizando atualizações mensalmente.


## <a name="december-2017"></a>Dezembro de 2017
 

### <a name="terms-of-use-in-the-access-panel-for-end-users"></a>Termos de uso no painel de acesso para usuários finais

**Tipo:** novo recurso  
**Categoria de serviço:** termos de uso  
**Funcionalidade do produto:** governança/conformidade
 
Os usuários finais agora podem ir ao painel de acesso e visualizar os termos de uso que eles aceitaram anteriormente.

Os usuários podem examinar e ver os termos de uso que eles tiverem aceitado. Isso pode ser feito usando qualquer uma dos procedimentos a seguir:

1. Navegue e acesse o [portal MyApps](https://myapps.microsoft.com).

2. No canto superior direito, clique em seu nome e selecione **Perfil** no menu suspenso. 

3. Em seu perfil, clique em **Revisar os termos de uso**. 

4. A partir daí, você pode revisar os termos de uso que você aceitou. 

Para obter mais informações, consulte os [Termos de Uso do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>Nova experiência de entrada do Azure AD.

**Tipo:** novo recurso  
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** autenticação de usuário
 
Como parte da jornada de convergir o Azure AD e os sistemas de identidade de conta da Microsoft, a interface do usuário foi reformulada nos dois sistemas para que eles tenham uma aparência consistente. Além disso, a página de entrada do Azure AD foi paginada para que o nome de usuário seja coletado primeiro, seguido da credencial em uma segunda tela.

Para obter mais informações, consulte [A nova experiência de entrada do Azure AD agora está em visualização pública](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)
 
---
 

### <a name="fewer-login-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-login"></a>Menos prompts de logon: Uma nova experiência "Manter-me conectado" para o logon do Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** autenticação de usuário
 
Nós substituímos a caixa de seleção **Manter-me conectado** na página de logon no Azure AD por um novo prompt que aparece depois que o usuário for autenticado com êxito. 

Se um usuário responde **Sim** a esse prompt, o serviço fornece um token de atualização persistente. Este comportamento é o mesmo que o usuário tem ao verificar a caixa de seleção **Manter-me conectado** na experiência antiga. Para locatários federados, esse prompt aparecerá depois que o usuário for autenticado com êxito com o serviço federado.

Para obter mais informações, consulte [Menos prompts de logon: A nova experiência "Manter-me conectado" do Azure AD está em versão prévia](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/) 

---
 

### <a name="add-configuration-to-require-the-tou-to-be-expanded-prior-to-accepting"></a>Adicionar configuração para solicitar que os Termos de Uso sejam expandidos antes de aceitar.

**Tipo:** novo recurso  
**Categoria de serviço:** termos de uso  
**Funcionalidade do produto:** governança
 
Agora, adicionamos uma opção para que os administradores solicitem que os usuários finais expandam os Termos de Uso antes de aceitá-los.

Selecione ativar ou desativar para Solicitar que os usuários expandam os termos de uso. Se for ativado, os usuários finais precisarão ler os termos de uso antes de aceitá-los.

Para obter mais informações, consulte [Recurso Termos de Uso do Azure Active Directory (Versão prévia)](active-directory-tou.md).
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>Ativação com escopo para atribuições de função qualificadas

**Tipo:** novo recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
A ativação com escopo permite que você ative atribuições de função qualificadas em recursos do Azure com menos autonomia do que nos padrões de atribuição originais. Por exemplo, suponha que a você foi atribuída a função de Proprietário em uma assinatura em seu locatário. Com a ativação com escopo, é possível ativar a função de Proprietário para até cinco recursos contidos na assinatura (Grupos de Recursos, Máquinas Virtuais, etc...). A ativação com escopo pode reduzir a possibilidade de executar alterações indesejadas em recursos críticos do Azure.

Para obter mais informações, confira [O que é o Privileged Identity Management do Azure AD?](active-directory-privileged-identity-management-configure.md).
 
---
 

### <a name="new-federated-apps-in-azure-ad-app-gallery"></a>Novos aplicativos federados na galeria de aplicativos do Azure AD

**Tipo:** novo recurso  
**Categoria de Serviço:** aplicativos empresariais  
**Funcionalidade do Produto:** Integração de Terceiros
 
Em dezembro de 2017, adicionamos os seguintes aplicativos novos em nossa Galeria de aplicativos com suporte da Federação:

|NOME|Tipo de Integração|DESCRIÇÃO|
|:-- |----------------|:----------|
|EFI Digital StoreFront|SAML 2.0|[Aplicativo Web 2 Print](https://go.microsoft.com/fwlink/?linkid=861685)|
|Vodeclic|SAML 2.0|[Use o Microsoft Azure AD para gerenciar o acesso do usuário e habilitar o logon único com o Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522).  Requer uma conta existente no Vodeclic.|
|Accredible|SAML 2.0|[Crie, gerencie e entregue certificados, notificações e credenciais de blockchain](https://go.microsoft.com/fwlink/?linkid=863523)|
|FactSet|SAML 2.0|[Logon único no aplicativo de FDSWeb do FactSet](https://go.microsoft.com/fwlink/?linkid=863525)|
|Integração do AD Azure MobileIron|SAML 2.0|A missão do [MobileIron](https://go.microsoft.com/fwlink/?linkid=858027) é permitir que as empresas modernas protejam e gerenciem informações ao moverem-se ao celular e à nuvem, ao mesmo tempo em que preservam a privacidade e a confiança do usuário final.|
|IMAGE WORKS|SAML 2.0|Use o Microsoft Azure AD para gerenciar o acesso de usuários, provisionar contas de usuários e habilitar o logon único com o [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517). Requer uma assinatura existente no IMAGE WORKS.|
|SSO de SAML para o Bitbucket por resolução GmbH|SAML 2.0|[SSO Bitbucket](https://go.microsoft.com/fwlink/?linkid=863519) delega a autenticação ao Azure AD e os usuários já registrados no Azure AD podem acessar diretamente o Bitbucket. Os usuários podem ser criados e atualizados imediatamente com dados de atributos de SAML.|
|SSO de SAML para Bamboo por resolução GmbH|SAML 2.0|[SSO Bamboo](https://go.microsoft.com/fwlink/?linkid=863520) delega a autenticação ao Azure AD e os usuários já registrados no Azure AD podem acessar diretamente o Bamboo.|
|Communifire|SAML 2.0|[Communifire](https://go.microsoft.com/fwlink/?linkid=861676) é seu software de intranet social moderno, repleto de recursos, que dá suporte a seus funcionários e seus negócios.|
|MOBI|SAML 2.0|[Centralize, compreenda e controle todo o ecossistema de seu dispositivo](https://go.microsoft.com/fwlink/?linkid=863521).|
|Reflektive|SAML 2.0|[Reflektive](https://go.microsoft.com/fwlink/?linkid=863518) é uma plataforma moderna para o gerenciamento de desempenho, comentários em tempo real e definição de metas. Nós capacitamos funcionários para que conduzam seu próprio desenvolvimento, para que você possa ser mais estratégico.|
|CybSafe|OAuth e OpenID Connect|CybSafe é uma plataforma de reconhecimento cibernético certificada pela GCHQ. Ela usa tecnologia avançada e a análise de dados para comprovadamente reduzir o elemento humano na segurança cibernética e o risco de proteção de dados.|
|WebHR|OAuth e OpenID Connect|O Software Social Multifuncional Preferido por Todos. Usado com confiança por mais de 20.000 empresas em 197 países|
 |Zenegy Azure AD Integration|OAuth e OpenID Connect|Com este aplicativo, você pode usar as credenciais do Azure Active Directory da sua empresa para fazer logon no Zenegy.|
|Adobe Experience Manager|SAML 2.0|O Adobe Experience Manager (AEM) é uma solução abrangente de plataforma de gerenciamento de conteúdo para a criação de sites, aplicativos móveis e formulários, tornando mais fácil gerenciar os seus ativos e conteúdos de marketing.|

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Fluxos de trabalho de aprovação para as funções de diretório do Azure AD

**Tipo:** recurso alterado  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
O fluxo de trabalho de aprovação para as funções de diretório do Azure AD está geralmente disponível.

Com o fluxo de trabalho de aprovação, os administradores de função privilegiada podem exigir que membros de função qualificada solicitem a ativação de função antes que eles possam usar a função privilegiada.
Vários usuários e grupos podem ter delegadas responsabilidades de aprovação Membros de função qualificada recebem notificações quando a aprovação é concluída e sua função está ativa

---
 

### <a name="pass-through-authentication---skype-for-business-support"></a>Autenticação de passagem - Skype para suporte de negócios

**Tipo:** recurso alterado  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário


Entradas do usuário para o Skype for Business que suportam autenticação moderna, incluindo topologias online e híbrida. 

Para obter mais informações, consulte [Topologias do Skype for Business com suporte da Autenticação Moderna](https://technet.microsoft.com/library/mt803262.aspx).
 
---
 

### <a name="updates-to-azure-active-directory-privileged-identity-management-pim-for-azure-rbac-preview"></a>Atualizações para o Privileged Identity Management (PIM) do Azure Active Directory para o Azure RBAC (versão prévia)

**Tipo:** recurso alterado  
**Categoria de serviço:** PIM  
**Funcionalidade do produto:** Privileged Identity Management
 
Com a nossa Atualização da Visualização Pública do Privileged Identity Management (PIM) do Azure Active Directory para o Azure RBAC, agora você pode:

Usar a aprovação Requer Apenas Administração Suficiente para ativar funções de recursos Agendar uma ativação futura de uma função que requer aprovação do AAD e do Azure RBAC Roles

 
Para obter mais informações, consulte [Privileged Identity Management para recursos do Azure (Versão Prévia)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)

 
---
 
## <a name="november-2017"></a>Novembro de 2017
 
### <a name="retiring-acs"></a>Desativando o ACS



**Tipo:** plano de alteração  
**Categoria de serviço:** ACS  
**Funcionalidade de produto:** Serviço de Controle de Acesso 


O Serviço de Controle de Acesso do Microsoft Azure Active Directory (também conhecido como Serviço de Controle de Acesso ou ACS) será desativado no final de 2018.  Serão fornecidas mais informações, incluindo diretrizes sobre migração de alto nível e agenda detalhada, nas próximas semanas. Por enquanto, deixe comentários nesta página com dúvidas sobre o ACS e um membro da nossa equipe entrará em contato para ajudar a responder.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Restringir o acesso do navegador ao Intune Managed Browser 


**Tipo:** plano de alteração  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** Identity Security & Protection




Com esse comportamento, você poderá restringir o acesso do navegador ao Office 365 e a outros aplicativos de nuvem conectados com o Azure AD usando o Intune Managed Browser como um aplicativo aprovado. 

Essa alteração permite que você configure a seguinte condição de acesso condicional com base em aplicativos:

**Aplicativos de cliente:** Browser

**Qual é o efeito da alteração?**

Atualmente, o acesso está bloqueado ao usar essa condição. Quando a versão prévia desse comportamento estiver disponível, todo o acesso exigirá o uso do aplicativo de navegador gerenciado. 

Procure por essa funcionalidade e mais nas notas de versão e em blogs futuros. 

Para saber mais, confira [Acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md).

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novos aplicativos de cliente aprovados para acesso condicional com base em aplicativos do Microsoft Azure Active Directory

 
**Tipo:** plano de alteração  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** Identity Security & Protection




Os aplicativos a seguir foram planejados para serem adicionados à lista de [aplicativos de cliente aprovados](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)

- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


Para obter mais informações, confira:

- [Requisito de aplicativo cliente aprovado](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Acesso condicional baseado em aplicativo do Azure Active Directory](active-directory-conditional-access-mam.md)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>Suporte dos termos de uso para diversos idiomas



**Tipo:** novo recurso    
**Categoria de serviço:** termos de uso  
**Funcionalidade do produto:** governança/conformidade





Os administradores agora podem criar novos termos de uso (TOU) que contêm vários documentos em PDF. Você pode marcar esses documentos em PDF com um idioma correspondente. Os usuários que estiverem no escopo receberão o PDF com o idioma correspondente com base em suas preferências. Se não houver nenhuma correspondência, o idioma padrão será mostrado.


---
 

### <a name="realtime-password-writeback-client-status"></a>Status de cliente de write-back de senha em tempo real



**Tipo:** novo recurso  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** autenticação de usuário


 

Agora você pode examinar o status do seu cliente de write-back de senha local. Essa opção está disponível na seção **Integração local** da página **[Redefinição de senha](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)**. 

Se houver problemas com sua conexão com seu cliente de write-back local, você verá uma mensagem de erro exibindo:

- Informações sobre o motivo pelo qual você não pode se conectar ao seu cliente de write-back local 
- Um link para a documentação que auxilia na resolução do problema. 


Para obter mais informações, consulte [Integração local](active-directory-passwords-how-it-works.md#on-premises-integration).

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Acesso condicional com base no aplicativo do Microsoft Azure Active Directory 



 
**Tipo:** novo recurso  
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** Identity Security & Protection





Agora você pode restringir o acesso ao Office 365 e a outros aplicativos de nuvem conectados do Azure AD para [aplicativos cliente aprovados](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) que dão suporte a políticas da Proteção de Aplicativo do Intune usando o [acesso condicional com base no aplicativo Azure AD](active-directory-conditional-access-mam.md). Políticas da Proteção de Aplicativo do Intune são usadas para configurar e proteger os dados da empresa nesses aplicativos cliente.

Ao combinar as políticas de acesso condicional [baseado em aplicativo](active-directory-conditional-access-mam.md) com [baseado em dispositivo](active-directory-conditional-access-policy-connected-applications.md), você tem a flexibilidade para proteger dados pessoais e dispositivos da empresa.

As condições e os controles a seguir agora estão disponíveis para uso com acesso condicional com base no aplicativo:

**Condição de plataforma com suporte**

- iOS
- Android

**Condição de aplicativos cliente**

- Aplicativos móveis e clientes de desktop

**Controle de acesso**

- Exigir um aplicativo cliente aprovado


Para saber mais, confira [Acesso condicional baseado em aplicativo do Azure Active Directory](active-directory-conditional-access-mam.md).

 
---

### <a name="managing-azure-ad-devices-in-the-azure-portal"></a>Gerenciamento de dispositivos do Microsoft Azure Active Directory no portal do Azure



**Tipo:** novo recurso  
**Categoria de serviço:** registro e gerenciamento de dispositivos  
**Funcionalidade do produto:** Identity Security & Protection

 



Agora você pode encontrar todos os dispositivos conectados ao Azure AD e as atividades relacionadas ao dispositivo em um único local. Há uma nova experiência de administração para gerenciar todas as suas identidades e configurações de dispositivos no portal do Azure. Nesta versão, você pode:

- Exibir todos os dispositivos que estão disponíveis para acesso condicional no Azure AD

- Exibir propriedades, incluindo dispositivos vinculados do Azure AD híbrido

- Localize chaves do BitLocker para os dispositivos vinculados do Azure AD, gerencie seu dispositivo com o Intune e muito mais.

- Gerenciar configurações relacionadas ao dispositivo do Azure AD


Para obter mais informações, consulte [Gerenciar dispositivos usando o portal do Azure](device-management-azure-portal.md).



 
---

### <a name="support-for-macos-as-device-platform-for-azure-ad-conditional-access"></a>Suporte para macOS como plataforma de dispositivo para acesso condicional do Microsoft Azure Active Directory 



**Tipo:** novo recurso    
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** Identity Security & Protection 
 

Agora você pode incluir (ou excluir) macOS como condição de plataforma de dispositivo em sua política de acesso condicional do Azure AD. Com a adição do macOS às plataformas de dispositivos com suporte, você pode:

- **Registrar e gerenciar dispositivos macOS usando o Intune** -semelhante a outras plataformas como iOS e Android, um aplicativo de portal da empresa está disponível para macOS para realizar registros unificados. O novo aplicativo de portal da empresa para macOS permite que você registre um dispositivo com o Intune e registre-o com o Azure AD.
 
- **Certifique-se de que dispositivos macOS sigam as políticas de conformidade da sua organização definidas no Intune** -no Intune no portal do Azure, agora você pode definir políticas de conformidade para dispositivos macOS. 
  
- **Restringir o acesso a aplicativos no Azure AD apenas a dispositivos compatíveis macOS** -a criação de política de acesso condicional tem macOS como uma opção de plataforma de dispositivo separada. Essa opção permite criar políticas de acesso condicional específicas do macOS para o conjunto de aplicativos destinados no Azure.

Para obter mais informações, confira:

- [Criar uma política de conformidade do dispositivo para dispositivos macOS com o Intune](https://aka.ms/macoscompliancepolicy)
- [Acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md)


 
---

### <a name="nps-extension-for-azure-mfa"></a>Extensão NPS para o Azure MFA 


**Tipo:** novo recurso    
**Categoria de serviço:** MFA  
**Funcionalidade do produto:** autenticação de usuário




A extensão do Servidor de Políticas de Rede (NPS) para o Azure MFA adiciona recursos MFA baseados em nuvem à sua infraestrutura de autenticação usando os seus servidores existentes. Com a extensão do NPS, você pode adicionar verificação por chamada telefônica, mensagem de texto ou aplicativo ao fluxo de autenticação existente sem a necessidade de instalar, configurar e manter novos servidores. 

Esta extensão foi criada para organizações que desejam proteger conexões VPN sem implantar o Servidor do Azure MFA. A extensão NPS atua como um adaptador entre RADIUS e Azure MFA baseado em nuvem para fornecer um segundo fator de autenticação para usuários federados ou sincronizados.


Para obter mais informações, consulte [Integrar sua infraestrutura existente do NPS à Autenticação Multifator do Azure](../multi-factor-authentication/multi-factor-authentication-nps-extension.md)

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Restaurar ou remover permanentemente os usuários excluídos


**Tipo:** novo recurso    
**Categoria de serviço:** gerenciamento de usuário  
**Funcionalidade do produto:** diretório 



No centro de administração do Azure AD, agora você pode:

- Restaurar um usuário excluído 
- Excluir permanentemente um usuário 


**Para experimentar:**

1. No centro de administração do Azure AD, selecione [**Todos os usuários**](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) na seção **Gerenciar**. 

2. Na lista **Mostrar**, selecione **Usuários excluídos recentemente**. 

4. Selecione um ou mais usuários excluídos recentemente e, em seguida, restaure-os ou exclua-os permanentemente.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novos aplicativos de cliente aprovados para acesso condicional com base em aplicativos do Microsoft Azure Active Directory

 
**Tipo:** recurso alterado  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** Identity Security & Protection


Os aplicativos a seguir foram adicionados à lista de [aplicativos cliente aprovados](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement):

- Microsoft Planner

- Proteção de Informações do Microsoft Azure 


Para obter mais informações, confira:

- [Requisito de aplicativo cliente aprovado](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Acesso condicional baseado em aplicativo do Azure Active Directory](active-directory-conditional-access-mam.md)


---

### <a name="ability-to-or-between-controls-in-a-conditional-access-policy"></a>Capacidade de 'OR' entre os controles em uma política de acesso condicional 


**Tipo:** recurso alterado    
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** Identity Security & Protection

 
A capacidade de controles de acesso condicional 'OR' (Exigir um dos controles selecionados) foi lançada. Esse recurso permite que você crie políticas com um **OR** entre os controles de acesso. Por exemplo, você pode usar esse recurso para criar uma política que exige que um usuário entre usando a autenticação multifator **OR** (ou) esteja em um dispositivo compatível.

Para saber mais, confira [Controles de acesso condicional no Azure Active Directory](active-directory-conditional-access-controls.md).

 
---

### <a name="aggregation-of-realtime-risk-events"></a>Agregação de eventos de risco em tempo real


**Tipo:** recurso alterado    
**Categoria de serviço:** proteção de identidade  
**Funcionalidade do produto:** Identity Security & Protection


Para melhorar sua experiência de administração, no Azure AD Identity Protection, todos os eventos de risco em tempo real que foram originados no mesmo endereço IP em um determinado dia agora são agregados por cada tipo de evento de risco. Essa alteração limita o volume de eventos de risco mostrados sem qualquer alteração na segurança de usuário.

A detecção em tempo real subjacente funciona sempre que o usuário fizer logon. Se você tiver uma configuração de política de segurança de risco de conexão para MFA ou para bloquear acesso, ela ainda será disparada durante cada conexão arriscada.

 
---
 




## <a name="october-2017"></a>Outubro de 2017


### <a name="deprecating-azure-ad-reports"></a>Reprovar relatórios do Microsoft Azure Active Directory


**Tipo:** plano de alteração  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto**: gerenciamento de ciclo de vida de identidade  



O Portal do Azure oferece:

- Um novo console de administração do Azure Active Directory 
- Novas APIs para gerar relatórios de atividades e de segurança
 
Devido a essas novas funcionalidades, as APIs de relatórios sob o ponto de extremidade **/reports** serão desativadas em 10 de dezembro de 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Detecção automática de campo de conexão


**Tipo:** corrigido   
**Categoria de serviço:** Meus aplicativos  
**Funcionalidade do produto:** SSO  



O Azure Active Directory dá suporte à detecção automática campo de entrada para os aplicativos que renderizam um campo de nome de usuário e senha HTML.  Essas etapas são documentadas em [Como capturar automaticamente os campos de entrada para um aplicativo](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application). Você pode encontrar essa funcionalidade adicionando um aplicativo *Inexistente na galeria* na página **Aplicativos empresariais** do [Portal do Azure](http://aad.portal.azure.com). Além disso, você pode configurar o modo de **Logon único** neste novo aplicativo para **Logon único baseado em senha** digitando uma URL da Web e salvando a página.
 
Devido a um problema de serviço, essa funcionalidade ficou temporariamente desabilitada por um período. O problema foi resolvido e a detecção automática do campo de entrada está disponível novamente.

---

### <a name="new-mfa-features"></a>Novos recursos de MFA


**Tipo:** novo recurso  
**Categoria de serviço:** MFA  
**Funcionalidade do produto:** Identity Security & Protection  



A MFA (Autenticação Multifator) é uma parte essencial da proteção da sua organização. Para tornar as credenciais mais adaptável e a experiência perfeita, os seguintes recursos foram adicionados: 

- A integração do desafio multifator resulta diretamente no relatório de logon único do Azure AD, incluindo o acesso programático aos resultados da MFA

- Integração mais profunda da configuração de MFA com a experiência de configuração do Azure AD no portal do Azure

Com essa versão prévia pública, o gerenciamento e os relatórios de MFA são parte integrante da experiência de configuração central do Azure AD. Agregar os dois recursos permite que você gerencie a funcionalidade de portal de gerenciamento de MFA dentro da experiência do Azure AD.

Para saber mais, veja [Referência para relatórios de autenticação multifator no portal do Azure](active-directory-reporting-activity-sign-ins-mfa.md) 


---

### <a name="introducing-terms-of-use"></a>Apresentar os termos de uso



**Tipo:** novo recurso  
**Categoria de serviço:** termos de uso  
**Funcionalidade do produto:** governança  



Os termos de uso do Azure AD fornecem um método simples para apresentar as informações aos usuários finais. Isso garante que os usuários vejam os avisos de isenção de responsabilidade relevantes para os requisitos legais ou de conformidade.

Use os termos de uso do Azure AD nos seguintes cenários:

- Termos gerais de uso para todos os usuários em sua organização. 

- Termos específicos de uso baseados nos atributos de um usuário (por exemplo, médicos vs enfermeiras ou funcionários locais vs internacionais, realizado por grupos dinâmicos). 

- Termos específicos de uso para acessar aplicativos de alto impacto nos negócios, como o Salesforce.

Para obter mais informações, consulte [Termos de Uso do Azure Active Directory](active-directory-tou.md).


---

### <a name="enhancements-to-privileged-identity-management"></a>Aprimoramentos no Privileged Identity Management


**Tipo:** novo recurso  
**Categoria de serviço:** PIM  
**Funcionalidade do produto:** Privileged Identity Management  


Com o Azure Active Directory Privileged Identity Management (PIM), você pode gerenciar, controlar e monitorar o acesso aos Recursos do Azure (Versão prévia) em sua organização para:

- Assinaturas
- Grupos de recursos
- Máquinas virtuais. 

Todos os recursos do Portal do Azure que aproveitam a funcionalidade de RBAC (Controle de Acesso Baseado em Volume) do Azure podem tirar proveito de todas as capacidades de segurança e gerenciamento de ciclo de vida que o Azure AD PIM tem a oferecer.

Para saber mais, consulte [PIM para recursos do Azure](privileged-identity-management/azure-pim-resource-rbac.md).


---

### <a name="introducing-access-reviews"></a>Apresentar revisões de acesso


**Tipo:** novo recurso  
**Categoria de serviço:** revisões de acesso  
**Funcionalidade do produto:** governança  



As revisões de acesso (versão prévia) permitem que as organizações gerenciem com eficiência as associações de grupos e o acesso a aplicativos corporativos: 

- Você pode renovar o acesso do usuário convidado usando as revisões de acesso do acesso deles a aplicativos e a associações de grupos. As informações fornecidas pelas revisões de acesso permitem aos revisores decidir com eficiência se o acesso dos convidados deve continuar.

- Você pode renovar o certificado de acesso de funcionários a aplicativos e associações de grupos com as revisões de acesso.

Você pode coletar os controles de revisão de acesso em programas relevantes para que sua organização rastreie as análises de conformidade ou aplicativos suscetíveis a riscos.

Para obter mais informações, consulte [Revisões de acesso do Azure AD](active-directory-azure-ad-controls-access-reviews-overview.md).


---

### <a name="hiding-third-party-applications-from-my-apps-and-the-office-365-launcher"></a>Ocultar aplicativos de terceiros dos Meus aplicativos e do inicializador do Office 365



**Tipo:** novo recurso  
**Categoria de serviço:** Meus aplicativos  
**Funcionalidade do produto:** SSO  



Agora é possível gerenciar melhor os aplicativos que aparecem em seus portais de usuário por meio de uma nova propriedade **ocultar aplicativo**. Ocultar aplicativos ajuda nos casos em que os blocos de aplicativos estão aparecendo para serviços de back-end ou há blocos duplicados que acabam por encher os inicializadores de aplicativos do usuário. O botão de alternância encontra-se na seção de propriedades do aplicativo de terceiros e é chamado **Visível para usuário?** Também é possível ocultar um aplicativo programaticamente por meio do PowerShell. 

Para obter mais informações, consulte [Ocultar um aplicativo de terceiros da experiência do usuário no Azure Active Directory](active-directory-coreapps-hide-third-party-app.md). 


**O que está disponível?**

 Como parte da transição para o novo console de administrador, 2 novas APIs para recuperar os Logs de atividades do Azure AD estão disponíveis. O novo conjunto de APIs fornece funcionalidades de filtragem e classificação mais sofisticadas, bem como atividades mais avançadas de auditoria e de conexão. Os dados disponíveis anteriormente por meio de relatórios de segurança agora podem ser acessados por meio da API de eventos de risco do Identity Protection do Microsoft Graph.


## <a name="september-2017"></a>Setembro de 2017

### <a name="hotfix-for-microsoft-identity-manager"></a>Hotfix para o Microsoft Identity Manager


**Tipo:** recurso alterado  
**Categoria de serviço:** Microsoft Identity Manager  
**Funcionalidade do produto**: gerenciamento de ciclo de vida de identidade  



Um pacote cumulativo de atualizações de hotfix (build 4.4.1642.0) está disponível desde 25 de setembro de 2017 para o MIM (Microsoft Identity Manager) 2016 2016 Service Pack 1 (SP1). Esse pacote cumulativo:

- Resolve problemas e adiciona melhorias
- Ele é uma atualização cumulativa que substitui todas as atualizações do MIM 2016 SP1 até o build 4.4.1459.0 para o Microsoft Identity Manager 2016. 
- Você precisa ter o **Microsoft Identity Manager 2016 build 4.4.1302.0.** 

Para obter mais informações, consulte [Pacote cumulativo de atualizações de hotfix (build 4.4.1642.0) disponível para o Microsoft Identity Manager 2016 SP1](https://support.microsoft.com/help/4021562). 

---
