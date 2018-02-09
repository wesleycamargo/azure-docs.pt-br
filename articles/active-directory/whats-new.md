---
title: "O que há de novo? Notas de versão do Azure Active Directory | Microsoft Docs"
description: "Conheça as novidades do Azure AD (Azure Active Directory), incluindo as últimas notas de versão, problemas conhecidos, correções de bug, funcionalidades preteridas e alterações futuras."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 34826332db2e63d442d47ada74fcbad4cad600ae
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="whats-new-in-azure-active-directory"></a>Novidades no Azure Active Directory




> Mantenha-se atualizado com as novidades do Azure AD (Azure Active Directory) assinando nosso [![feed](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [RSS](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us).



O Azure AD recebe melhorias de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

-   As versões mais recentes
-   Problemas conhecidos
-   Correções de bug
-   Funcionalidades preteridas
-   Planos de alterações

Esta página é atualizada mensalmente; portanto, visite-a regularmente.


## <a name="january-2018"></a>Janeiro de 2018
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Novos Aplicativos Federados disponíveis na Galeria de aplicativos Azure AD 

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 

Em janeiro de 2018, os seguintes novos aplicativos com suporte à federação foram adicionados à Galeria de aplicativos:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Privacy Management Software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk Federated Directory](https://go.microsoft.com/fwlink/?linkid=864699) e [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Para obter uma visão geral completa de todos os tutoriais disponíveis, consulte [Integração de aplicativos SaaS ao Azure Active Directory](https://aka.ms/appstutorial).
 

---
 


### <a name="sign-in-with-additional-risk-detected"></a>Entrada com risco adicional detectado

**Tipo:** novo recurso  
**Categoria de serviço:** proteção da identidade  
**Funcionalidade do produto:** segurança e proteção da identidade
 

O insight obtido para um evento de risco detectado está vinculado à sua assinatura do Microsoft Azure AD. Com a edição Azure AD Premium P2, você obtém as informações mais detalhadas sobre todas as detecções subjacentes.

Com a edição Azure AD Premium P1, detecções não cobertas por sua licença aparecem como o evento de risco Entrada com risco adicional detectado.

Para saber mais, veja [Eventos de risco do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Ocultar aplicativos do Office 365 dos painéis de acesso do usuário final

**Tipo:** novo recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** SSO
 

Agora você pode gerenciar melhor como os aplicativos do Office 365 são exibidos nos painéis de acesso do usuário por meio de uma nova configuração de usuário. Essa opção é útil para reduzir a quantidade de aplicativos nos painéis de acesso de um usuário, caso prefira mostrar apenas os aplicativos do Office no portal do Office. A configuração está localizada nas **Configurações de Usuário** e é rotulada **Os usuários só poderão ver os aplicativos do Office 365 no portal do Office 365**.
 

Para obter mais informações, consulte [Ocultar um aplicativo da experiência de um usuário no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 


### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Entrada direta em aplicativos habilitados para SSO de Senha diretamente na URL do aplicativo 

**Tipo:** novo recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** SSO
 

A extensão do navegador Meus Aplicativos agora está disponível por meio de uma ferramenta conveniente que fornece a funcionalidade de logon único de Meus Aplicativos como um atalho no navegador. Após a instalação, os usuários verão um ícone de waffle no navegador que fornece a eles o acesso rápido aos aplicativos. Os usuários agora podem usufruir dos seguintes benefícios:

- A capacidade de entrar diretamente em aplicativos baseados em SSO de senha na página de logon do aplicativo
- Iniciar um aplicativo usando o recurso de pesquisa rápida
- Atalhos para os aplicativos usados recentemente da extensão
- A extensão está disponível para o Edge, Chrome e Firefox.
 
Para obter mais informações, consulte [Extensão de Entrada Segura dos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction#my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>A experiência de administração do Azure AD no portal clássico do Azure foi desativada

**Tipo:** preterido   
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** diretório
 

A partir de 8 de janeiro de 2018, a experiência de administração do Azure AD no portal clássico do Azure foi desativada. Isso ocorreu em conjunto com a desativação do próprio portal clássico do Azure. No futuro, você deve usar o [Centro de administração do Azure AD](https://aad.portal.azure.com) para toda a administração baseada no portal do Azure AD.
 
---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>A experiência de administração do Azure AD no portal clássico do Azure foi desativada

**Tipo:** preterido  
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** diretório
 

A partir de 8 de janeiro de 2018, o portal da Web do PhoneFactor foi desativado. Este portal foi usado para a administração do servidor MFA, mas essas funções foram movidas para o portal do Azure em portal.azure.com. 

A configuração do MFA está localizada em: **Azure Active Directory \> Servidor MFA**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Preterir relatórios do Azure AD


**Tipo:** preterido  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto**: Gerenciamento do Ciclo de Vida da Identidade  


Com a disponibilidade geral do novo Azure Active Directory, um console de Administração e novas APIs agora estão disponíveis para relatórios de atividade e de segurança, as APIs de relatório no ponto de extremidade “/reports” foram desativadas em 31 de dezembro de 2017.


**O que está disponível?**

Como parte da transição para o novo console de administrador, disponibilizamos duas novas APIs para recuperar os Logs de Atividades do Azure AD. O novo conjunto de APIs fornecem funcionalidades de filtragem e classificação mais sofisticadas, bem como atividades mais avançadas de auditoria e de entrada. Os dados disponíveis anteriormente por meio de relatórios de segurança agora podem ser acessados por meio da API de eventos de risco do Identity Protection do Microsoft Graph.

Para obter mais informações, confira:

- [Introdução à API de relatórios do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Introdução ao Azure Active Directory Identity Protection e ao Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)


---


## <a name="december-2017"></a>Dezembro de 2017
 

### <a name="terms-of-use-in-the-access-panel"></a>Termos de uso no Painel de Acesso

**Tipo:** novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** governança/conformidade
 
Agora você pode acessar o Painel de Acesso e exibir os termos de uso que aceitou anteriormente.

Siga estas etapas:

1. Acesse o [portal do MyApps](https://myapps.microsoft.com) e entre.

2. No canto superior direito, selecione seu nome e, em seguida, selecione **Perfil** na lista. 

3. Em seu **Perfil**, selecione **Examinar termos de uso**. 

4. Agora, você pode ler os termos de uso que você aceitou. 

Para obter mais informações, consulte o [Recurso de termos de uso do Azure AD (versão prévia)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>Nova experiência de entrada do Azure AD.

**Tipo:** novo recurso  
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** autenticação de usuário
 
As interfaces do usuário do Azure AD e do sistema de identidade da conta da Microsoft foram reformuladas para que tenham uma aparência consistente. Além disso, a página de entrada do Azure AD coleta o nome de usuário primeiro, seguido da credencial em uma segunda tela.

Para obter mais informações, consulte [A nova experiência de entrada do Azure AD agora está em visualização pública](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Menos prompts de entrada: uma nova experiência “Manter-me conectado” para a entrada do Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** autenticação de usuário
 
A caixa de seleção **Manter-me conectado** na página de entrada do Azure AD foi substituída por um novo prompt exibido depois que você é autenticado com êxito. 

Se você responder **Sim** a esse prompt, o serviço fornecerá um token de atualização persistente. Esse comportamento é o mesmo exibido quando você selecionava a caixa de seleção **Manter-me conectado** na experiência antiga. Para locatários federados, esse prompt é mostrado após a autenticação bem-sucedida no serviço federado.

Para obter mais informações, consulte [Menos prompts de entrada: a nova experiência “Manter-me conectado” do Azure AD está em versão prévia](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---
 

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Adicionar uma configuração para exigir que os termos de uso sejam expandidos antes da aceitação

**Tipo:** novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** governança
 
Uma opção para os administradores exige que seus usuários expandam os termos de uso antes de aceitar os termos.

Selecione **Ativado** ou **Desativado** para solicitar que os usuários expandam os termos de uso. A configuração **Ativado** exige que os usuários visualizem os termos de uso antes de aceitá-los.

Para obter mais informações, consulte o [Recurso de termos de uso do Azure AD (versão prévia)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>Ativação com escopo para atribuições de função qualificadas

**Tipo:** novo recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Use a ativação com escopo para ativar atribuições de função qualificadas em recursos do Azure com menos autonomia do que nos padrões de atribuição originais. Um exemplo é quando você está atribuído como o proprietário de uma assinatura em seu locatário. Com a ativação com escopo, é possível ativar a função de proprietário para até cinco recursos contidos na assinatura (como grupos de recursos e máquinas virtuais). A ativação com escopo pode reduzir a possibilidade de executar alterações indesejadas em recursos críticos do Azure.

Para obter mais informações, confira [O que é o Privileged Identity Management do Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Novos aplicativos federados na galeria de aplicativos Azure AD

**Tipo:** novo recurso  
**Categoria de serviço:** aplicativos empresariais  
**Funcionalidade do produto:** integração de terceiros
 
Em dezembro de 2017, os seguintes novos aplicativos com suporte à federação foram adicionados à galeria de aplicativos:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for Bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integration.

Para obter uma visão geral completa de todos os tutoriais disponíveis, consulte [Integração de aplicativos SaaS ao Azure Active Directory](https://aka.ms/appstutorial).

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Fluxos de trabalho de aprovação para as funções de diretório do Azure AD

**Tipo:** recurso alterado  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
O fluxo de trabalho de aprovação para as funções de diretório do Azure AD está geralmente disponível.

Com o fluxo de trabalho de aprovação, os administradores de função com privilégios podem exigir que membros de função qualificada solicitem a ativação de função antes que possam usar a função com privilégios. Vários usuários e grupos podem receber responsabilidades de aprovação. Os membros de função qualificados recebem notificações quando a aprovação é concluída e suas funções estão ativas.

---
 

### <a name="pass-through-authentication-skype-for-business-support"></a>Autenticação de passagem: Suporte para o Skype for Business

**Tipo:** recurso alterado  
**Categoria de serviço:** autenticações (logons)  
**Funcionalidade do produto:** autenticação de usuário


A autenticação de passagem agora dá suporte a entradas do usuário em aplicativos cliente do Skype for Business que dão suporte à autenticação moderna, que incluem topologias online e híbridas. 

Para obter mais informações, consulte [Topologias do Skype for Business compatíveis com a autenticação moderna](https://technet.microsoft.com/library/mt803262.aspx).
 
---
 

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Atualizações no Azure AD Privileged Identity Management para o RBAC do Azure (versão prévia)

**Tipo:** recurso alterado  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Com a atualização da visualização pública do Azure AD PIM (Privileged Identity Management) para o RBAC (Controle de Acesso Baseado em Função), agora você pode:

* Usar a Administração Just Enough.
* Exigir aprovação para ativar as funções de recurso.
* Agendar uma ativação futura de uma função que exige aprovação para funções do Azure AD e de RBAC do Azure.

 
Para obter mais informações, consulte [Privileged Identity Management para recursos do Azure (versão prévia)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

 
---
 
## <a name="november-2017"></a>Novembro de 2017
 
### <a name="access-control-service-retirement"></a>Desativação do serviço de Controle de Acesso



**Tipo:** plano de alteração  
**Categoria de serviço:** serviço de Controle de Acesso  
**Funcionalidade do produto:** serviço de Controle de Acesso 


 O Controle de Acesso do Azure Active Directory (também conhecido como serviço de Controle de Acesso) será desativado no final de 2018. Mais informações que incluem um cronograma detalhado e diretrizes de migração de alto nível serão fornecidas nas próximas semanas. Você pode deixar comentários nesta página com dúvidas sobre o serviço de Controle de Acesso e um membro da equipe responderá a suas dúvidas.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Restringir o acesso do navegador ao Intune Managed Browser 


**Tipo:** plano de alteração  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** segurança e proteção de identidade




Você pode restringir o acesso do navegador ao Office 365 e a outros aplicativos de nuvem conectados ao Azure AD usando o Intune Managed Browser como um aplicativo aprovado. 

Agora você pode configurar a seguinte condição para o acesso condicional baseado em aplicativo:

**Aplicativos cliente:** Browser

**Qual é o efeito da alteração?**

Atualmente, o acesso está bloqueado quando essa condição é usada. Quando a versão prévia estiver disponível, todo o acesso exigirá o uso do aplicativo de navegador gerenciado. 

Procure essa funcionalidade e mais informações em notas de versão e blogs futuros. 

Para obter mais informações, consulte [Acesso condicional no Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novos aplicativos de cliente aprovados para acesso condicional com base em aplicativos do Microsoft Azure Active Directory

 
**Tipo:** plano de alteração  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** segurança e proteção de identidade




Os aplicativos a seguir foram planejados para serem adicionados à lista de [aplicativos de cliente aprovados](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


Para obter mais informações, confira:

- [Requisito de aplicativo cliente aprovado](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Acesso condicional com base no aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>Suporte dos termos de uso para diversos idiomas



**Tipo:** novo recurso    
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** governança/conformidade





Os administradores agora podem criar novos termos de uso que contêm vários documentos PDF. Você pode marcar esses documentos em PDF com um idioma correspondente. Os usuários recebem o PDF com o idioma correspondente de acordo com suas preferências. Se não houver nenhuma correspondência, o idioma padrão será mostrado.


---
 

### <a name="real-time-password-writeback-client-status"></a>Status do cliente de write-back de senha em tempo real



**Tipo:** novo recurso  
**Categoria de serviço:** redefinição de senha de autoatendimento  
**Funcionalidade do produto:** autenticação de usuário


 

Agora você pode examinar o status do cliente de write-back de senha local. Essa opção está disponível na seção **Integração local** da página [Redefinição de senha](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset). 

Se houver problemas com sua conexão com o cliente de write-back local, você verá uma mensagem de erro que exibirá:

- Informações sobre o motivo pelo qual não é possível se conectar ao cliente de write-back local.
- Um link para a documentação que auxilia na resolução do problema. 


Para obter mais informações, consulte [Integração local](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Acesso condicional com base no aplicativo do Microsoft Azure Active Directory 



 
**Tipo:** novo recurso  
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** segurança e proteção de identidade





Agora você pode restringir o acesso ao Office 365 e a outros aplicativos de nuvem conectados ao Azure AD aos [aplicativos cliente aprovados](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) que dão suporte a políticas de proteção de aplicativo do Intune usando o [acesso condicional baseado no aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam). Políticas da Proteção de Aplicativo do Intune são usadas para configurar e proteger os dados da empresa nesses aplicativos cliente.

Ao combinar as políticas de acesso condicional [baseado em aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) com [baseado em dispositivo](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications), você tem a flexibilidade para proteger dados pessoais e dispositivos da empresa.

As condições e os controles a seguir agora estão disponíveis para uso com acesso condicional com base no aplicativo:

**Condição de plataforma com suporte**

- iOS
- Android

**Condição de aplicativos cliente**

- Aplicativos móveis e clientes de desktop

**Controle de acesso**

- Exigir um aplicativo cliente aprovado


Para obter mais informações, consulte [Acesso condicional baseado em aplicativo do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam).

 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Gerenciar dispositivos do Azure AD no portal do Azure



**Tipo:** novo recurso  
**Categoria de serviço:** registro e gerenciamento de dispositivos  
**Funcionalidade do produto:** segurança e proteção de identidade

 



Agora você pode encontrar todos os dispositivos conectados ao Azure AD e as atividades relacionadas ao dispositivo em um único local. Há uma nova experiência de administração para gerenciar todas as suas identidades e configurações de dispositivos no portal do Azure. Nesta versão, você pode:

- Exibir todos os dispositivos que estão disponíveis para acesso condicional no Azure AD.
- Exibir propriedades, incluindo dispositivos vinculados do Azure AD híbrido.
- Encontrar chaves do BitLocker para os dispositivos ingressados no Azure AD, gerencie seu dispositivo com o Intune e muito mais.
- Gerenciar configurações relacionadas ao dispositivo do Azure AD.

Para obter mais informações, consulte [Gerenciar dispositivos usando o portal do Azure](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).



 
---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Suporte para macOS como plataforma de dispositivo para acesso condicional do Azure AD 



**Tipo:** novo recurso    
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** segurança e proteção de identidade 
 

Agora você pode incluir (ou excluir) o macOS como uma condição de plataforma de dispositivo em sua política de acesso condicional do Azure AD. Com a adição do macOS às plataformas de dispositivos com suporte, você pode:

- **Registrar e gerenciar dispositivos macOS usando o Intune.** Semelhante a outras plataformas como iOS e Android, um aplicativo do portal da empresa está disponível para o macOS para realizar registros unificados. Use o novo aplicativo do portal da empresa para o macOS para registrar um dispositivo no Intune e registrá-lo no Azure AD.
- **Garantir que os dispositivos macOS seguem as políticas de conformidade de sua organização definidas no Intune.** No Intune no portal do Azure, agora você pode configurar políticas de conformidade para dispositivos macOS. 
- **Restringir o acesso a aplicativos no Azure AD apenas a dispositivos macOS em conformidade.** A criação de política de acesso condicional traz o macOS como uma opção de plataforma de dispositivo separada. Agora, você pode criar políticas de acesso condicional específicas ao macOS para o conjunto de aplicativos direcionados no Azure.

Para obter mais informações, confira:

- [Criar uma política de conformidade do dispositivo para dispositivos macOS com o Intune](https://aka.ms/macoscompliancepolicy)
- [Acesso condicional no Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)


 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Extensão Servidor de Políticas de Rede para a Autenticação Multifator do Azure 


**Tipo:** novo recurso    
**Categoria de serviço:** autenticação multifator  
**Funcionalidade do produto:** autenticação de usuário




A extensão Servidor de Políticas de Rede para a Autenticação Multifator do Azure adiciona funcionalidades de autenticação multifator baseado em nuvem à sua infraestrutura de autenticação usando os servidores existentes. Com a extensão Servidor de Políticas de Rede, você pode adicionar uma chamada telefônica, mensagem de texto ou verificação de aplicativo de telefone a seu fluxo de autenticação existente. Você não precisa instalar, configurar nem manter novos servidores. 

Essa extensão foi criada para organizações que desejam proteger as conexões de rede virtual privada sem implantar o Servidor de Autenticação Multifator do Azure. A extensão Servidor de Políticas de Rede atua como um adaptador entre o RADIUS e a Autenticação Multifator do Azure baseada em nuvem para fornecer um segundo fator de autenticação para usuários federados ou sincronizados.


Para obter mais informações, consulte [Integrar sua infraestrutura existente do Servidor de Políticas de Rede à Autenticação Multifator do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Restaurar ou remover permanentemente os usuários excluídos


**Tipo:** novo recurso    
**Categoria de serviço:** gerenciamento de usuário  
**Funcionalidade do produto:** diretório 



No centro de administração do Azure AD, agora você pode:

- Restaurar um usuário excluído. 
- Excluir permanentemente um usuário.


**Para experimentar:**

1. No centro de administração do Azure AD, selecione [Todos os usuários](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) na seção **Gerenciar**. 

2. Na lista **Mostrar**, selecione **Usuários excluídos recentemente**. 

3. Selecione um ou mais usuários excluídos recentemente e, em seguida, restaure-os ou exclua-os permanentemente.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novos aplicativos de cliente aprovados para acesso condicional com base em aplicativos do Microsoft Azure Active Directory

 
**Tipo:** recurso alterado  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** segurança e proteção de identidade


Os seguintes aplicativos foram adicionados à lista de [aplicativos cliente aprovados](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Microsoft Planner
- Proteção de Informações do Azure 


Para obter mais informações, confira:

- [Requisito de aplicativo cliente aprovado](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Acesso condicional com base no aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Usar “OR” entre controles em uma política de acesso condicional 


**Tipo:** recurso alterado    
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** segurança e proteção de identidade

 
Agora você pode usar “OR” (exige um dos controles selecionados) para controles de acesso condicional. Use esse recurso para criar políticas com “OR” entre os controles de acesso. Por exemplo, você pode usar esse recurso para criar uma política que exige que um usuário entre usando a autenticação multifator OU que ele esteja em um dispositivo em conformidade.

Para obter mais informações, consulte [Controles no acesso condicional do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).

 
---

### <a name="aggregation-of-real-time-risk-events"></a>Agregação de eventos de risco em tempo real


**Tipo:** recurso alterado    
**Categoria de serviço:** proteção da identidade  
**Funcionalidade do produto:** segurança e proteção de identidade


No Azure AD Identity Protection, todos os eventos de risco em tempo real que foram originados no mesmo endereço IP em determinado dia agora são agregados por tipo de evento de risco. Essa alteração limita o volume de eventos de risco mostrados sem nenhuma alteração na segurança do usuário.

A detecção em tempo real subjacente funciona sempre que o usuário se conecta. Se você tiver uma política de segurança de risco de entrada configurada para a autenticação multifator ou para bloquear o acesso, ela ainda será disparada durante cada entrada de risco.

 
---
 




## <a name="october-2017"></a>Outubro de 2017


### <a name="deprecate-azure-ad-reports"></a>Preterir relatórios do Azure AD


**Tipo:** plano de alteração  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto**: Gerenciamento do Ciclo de Vida da Identidade  



O Portal do Azure oferece:

- Um novo console de administração do Azure AD.
- Novas APIs para relatórios de atividade e segurança.
 
Devido a essas novas funcionalidades, as APIs de relatórios no ponto de extremidade /reports foram desativadas em 10 de dezembro de 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Detecção automática de campo de conexão


**Tipo:** corrigido   
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** logon único  



O Azure AD é compatível com a detecção automática de campo de entrada para os aplicativos que renderizam um campo de nome de usuário e senha HTML. Essas etapas são documentadas em [Como capturar automaticamente os campos de entrada para um aplicativo](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application). Você pode encontrar essa funcionalidade adicionando um aplicativo *Inexistente na galeria* na página **Aplicativos empresariais** do [Portal do Azure](http://aad.portal.azure.com). Além disso, você pode configurar o modo **Logon Único** neste novo aplicativo como **Logon Único Baseado em Senha**, inserir uma URL da Web e, em seguida, salvar a página.
 
Devido a um problema do serviço, essa funcionalidade ficou temporariamente desabilitada. O problema foi resolvido e a detecção automática do campo de entrada está disponível novamente.

---

### <a name="new-multi-factor-authentication-features"></a>Novos recursos de autenticação multifator


**Tipo:** novo recurso  
**Categoria de serviço:** autenticação multifator  
**Funcionalidade do produto:** segurança e proteção de identidade  



O MFA (Autenticação multifator) é uma parte essencial da proteção de sua organização. Para tornar as credenciais mais adaptáveis e a experiência mais direta, os seguintes recursos foram adicionados: 

- Os resultados do desafio multifator são diretamente integrados ao relatório de entrada do Azure AD, que inclui o acesso programático aos resultados do MFA.
- A configuração do MFA está mais profundamente integrada à experiência de configuração do Azure AD no portal do Azure.

Com essa versão prévia pública, o gerenciamento e os relatórios de MFA são parte integrante da experiência de configuração central do Azure AD. Agora, você pode gerenciar a funcionalidade do portal de gerenciamento do MFA na experiência do Azure AD.

Para obter mais informações, consulte [Referência de relatórios do MFA no portal do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 


---

### <a name="terms-of-use"></a>Termos de uso



**Tipo:** novo recurso  
**Categoria de serviço:** Termos de uso  
**Funcionalidade do produto:** governança  



Use os termos de uso do Azure AD para apresentar informações como avisos de isenção de responsabilidade relevantes para requisitos legais ou de conformidade para os usuários.

Use os termos de uso do Azure AD nos seguintes cenários:

- Termos de uso gerais para todos os usuários em sua organização
- Termos de uso específicos com base nos atributos de um usuário (por exemplo, médicos versus enfermeiras ou funcionários nacionais vs. internacionais, feito por grupos dinâmicos)
- Termos de uso específicos para acessar aplicativos de negócios de alto impacto, como o Salesforce

Para obter mais informações, consulte [Termos de uso do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-tou).


---

### <a name="enhancements-to-privileged-identity-management"></a>Melhorias no Privileged Identity Management


**Tipo:** novo recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management  


Com o Azure AD Privileged Identity Management, você pode gerenciar, controlar e monitorar o acesso aos recursos do Azure (versão prévia) em sua organização para:

- Assinaturas
- Grupos de recursos
- Máquinas virtuais 

Todos os recursos do portal do Azure que usam a funcionalidade de RBAC do Azure podem usufruir todas as funcionalidades de segurança e gerenciamento do ciclo de vida que o Azure AD Privileged Identity Management tem a oferecer.

Para obter mais informações, consulte [Privileged Identity Management para recursos do Azure](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).


---

### <a name="access-reviews"></a>Análises de acesso


**Tipo:** novo recurso  
**Categoria de serviço:** revisões de acesso  
**Funcionalidade do produto:** governança  



As organizações podem usar revisões de acesso (versão prévia) para gerenciar com eficiência as associações a um grupo e o acesso a aplicativos corporativos: 

- Você pode reconfirmar o acesso do usuário convidado usando as revisões do acesso deles a aplicativos e a associações de grupos. Os revisores podem decidir com eficiência se permitirão o acesso contínuo aos convidados de acordo com as informações fornecidas pelas revisões de acesso.
- Você pode reconfirmar o acesso de funcionários a aplicativos e associações de grupo com as revisões de acesso.

Você pode coletar os controles de revisão de acesso em programas relevantes para que sua organização rastreie as análises de conformidade ou aplicativos suscetíveis a riscos.

Para obter mais informações, consulte [Revisões de acesso do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).


---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Ocultar aplicativos de terceiros de Meus Aplicativos e do inicializador de aplicativos do Office 365



**Tipo:** novo recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** logon único  



Agora é possível gerenciar melhor os aplicativos que são mostrados nos portais dos usuários por meio de uma nova propriedade **ocultar aplicativo**. Você pode ocultar aplicativos para ajudar em casos em que os blocos de aplicativos são mostrados para serviços de back-end ou blocos duplicados e emails secundários de iniciadores de aplicativos dos usuários. A alternância está localizada na seção **Propriedades** do aplicativo de terceiros e é rotulada **Visível para o usuário?** Também é possível ocultar um aplicativo de forma programática por meio do PowerShell. 

Para obter mais informações, consulte [Ocultar um aplicativo de terceiros da experiência de um usuário no Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**O que está disponível?**

 Como parte da transição para o novo console de administrador, duas novas APIs para recuperar os logs de atividades do Azure AD estão disponíveis. O novo conjunto de APIs fornece funcionalidades de filtragem e classificação mais sofisticadas, bem como atividades mais avançadas de auditoria e de conexão. Os dados disponíveis anteriormente por meio de relatórios de segurança agora podem ser acessados por meio da API de Eventos de Risco do Identity Protection no Microsoft Graph.


## <a name="september-2017"></a>Setembro de 2017

### <a name="hotfix-for-identity-manager"></a>Hotfix para o Identity Manager


**Tipo:** recurso alterado  
**Categoria de serviço:** Identity Manager  
**Funcionalidade do produto**: gerenciamento do ciclo de vida da identidade  



Um pacote cumulativo de atualizações do hotfix (build 4.4.1642.0) está disponível a partir de 25 de setembro de 2017 para o Identity Manager 2016 Service Pack 1. Esse pacote cumulativo:

- Resolve problemas e adiciona melhorias.
- É uma atualização cumulativa que substitui todas as atualizações do Identity Manager 2016 Service Pack 1 até o build 4.4.1459.0 para o Identity Manager 2016. 
- Exige o Identity Manager 2016 build 4.4.1302.0. 

Para obter mais informações, consulte [Pacote cumulativo de atualizações do hotfix (build 4.4.1642.0) disponível para o Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
