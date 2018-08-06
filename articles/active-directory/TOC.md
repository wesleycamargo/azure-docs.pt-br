# [Documentação do Azure Active Directory](index.md)

# Visão geral
## [O que é o Active Directory do Azure?](fundamentals/active-directory-whatis.md)
## [Sobre o gerenciamento de identidade do Azure](fundamentals/identity-fundamentals.md)
## [Entender as soluções de identidade do Azure](fundamentals/understand-azure-identity-solutions.md)
## [Escolher uma solução de identidade híbrida](choose-hybrid-identity-solution.md)
## [Gerenciar assinaturas do Azure](fundamentals/active-directory-how-subscriptions-associated-directory.md)
## [Considerações de dados e de residência](fundamentals/active-directory-data-storage-eu.md)
## [Perguntas frequentes](fundamentals/active-directory-faq.md)
## [Novidades](fundamentals/whats-new.md)


# Introdução
## [Introdução ao Microsoft Azure Active Directory](fundamentals/get-started-azure-ad.md)
## [Inscrever-se no Azure AD Premium](fundamentals/active-directory-get-started-premium.md)
## [Como adicionar um nome de domínio personalizado](fundamentals/add-custom-domain.md)
## [Configurar a identidade visual da empresa](fundamentals/customize-branding.md)
## [Adicionar usuários ao Azure AD](fundamentals/add-users-azure-active-directory.md)
## [Atribuir licenças aos usuários](fundamentals/license-users-groups.md)
## [Como configurar a redefinição de senha de autoatendimento](authentication/quickstart-sspr.md)
## [Adicionar informações de privacidade da sua organização no Azure AD](active-directory-properties-area.md)


# Como
## Planejar e projetar
### [Compreenda a arquitetura do Azure AD](fundamentals/active-directory-architecture.md)
### [Mapeamento de declarações no Azure Active Directory](active-directory-claims-mapping.md)
### [Implantar uma Solução de Identidade Híbrida](active-directory-hybrid-identity-design-considerations-overview.md)
#### Determinar os requisitos
##### [Identidade](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [Sincronização de diretórios](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Autenticação multifator](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Identificar estratégia de ciclo de vida](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Planejar segurança de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Proteção de dados](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Gerenciamento de conteúdo](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Controle de acesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Resposta a incidentes](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Planejar o ciclo de vida de identidade
##### [Tarefas](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Estratégia de adoção](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Próximas etapas](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [Comparação de ferramentas](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## Gerenciar usuários
### [Adicionar novos usuários ao Azure AD](fundamentals/add-users-azure-active-directory.md)
### [Gerenciar perfis de usuário](fundamentals/active-directory-users-profile-azure-portal.md)
### [Redefinir senhas de usuário](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [Compartilhar contas](active-directory-sharing-accounts.md)
### [Atribuir usuários a funções de administrador](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [Restaurar um usuário excluído](fundamentals/active-directory-users-restore.md)
### [Adicionar usuários convidados de outro diretório (B2B)](b2b/what-is-b2b.md)
#### [Admins adicionando usuários B2B](b2b/add-users-administrator.md)
#### [Trabalhos de informação adicionando usuários B2B](b2b/add-users-information-worker.md)
#### [API e personalização](b2b/customize-invitation-api.md)
#### [Exemplos de código e do Azure PowerShell](b2b/code-samples.md)
#### [Exemplo de portal para iniciar sessão com autoatendimento](b2b/self-service-portal.md)
#### [Email de convite](b2b/invitation-email-elements.md)
#### [Resgate do convite](b2b/redemption-experience.md)
#### [Como adicionar usuários de B2B sem um convite](b2b/add-user-without-invite.md)
#### [Permitir ou bloquear convites](b2b/allow-deny-list.md)
#### [Acesso condicional para B2B](b2b/conditional-access.md)
#### [Políticas de compartilhamento B2B](b2b/delegate-invitations.md)
#### [Adicionar um usuário B2B a uma função](b2b/add-guest-to-role.md)
#### [Grupos dinâmicos e usuários B2B](b2b/use-dynamic-groups.md)
#### [Sair da organização](b2b/leave-the-organization.md)
#### [Auditoria e relatórios](b2b/auditing-and-reporting.md)
#### [B2B para organizações híbridas](b2b/hybrid-organizations.md)
##### [Conceder aos usuários B2B acesso aos aplicativos locais](b2b/hybrid-cloud-to-on-premises.md)
##### [Conceder aos usuários locais acesso aos aplicativos de nuvem](b2b/hybrid-on-premises-to-cloud.md)
#### [B2B e compartilhamento externo do Office 365](b2b/o365-external-user.md)
#### [Licenciamento B2B](b2b/licensing-guidance.md)
#### [Limitações atuais](b2b/current-limitations.md)
#### [Perguntas frequentes](b2b/faq.md)
#### [Solucionando problemas do B2B](b2b/troubleshoot.md)
#### [Entender o usuário B2B](b2b/user-properties.md)
#### [Token do usuário B2B](b2b/user-token.md)
#### [B2B para aplicativos integrados do Azure AD](b2b/configure-saas-apps.md)
#### [Mapeamento de declarações do usuário B2B](b2b/claims-mapping.md)
#### [Comparar a colaboração B2B com B2C](b2b/compare-with-b2c.md)
#### [Obtendo suporte para B2B](b2b/get-support.md)

## [Gerenciar grupos e membros](fundamentals/active-directory-manage-groups.md)
### Gerenciar grupos
#### [portal do Azure](fundamentals/active-directory-groups-create-azure-portal.md)
#### [PowerShell do Azure AD para Graph (v2)](users-groups-roles/groups-settings-v2-cmdlets.md)
#### [Azure AD PowerShell MSOnline](users-groups-roles/groups-settings-cmdlets.md)
### [Gerenciar membros do grupo](fundamentals/active-directory-groups-members-azure-portal.md)
### [Gerenciar proprietários do grupo](fundamentals/active-directory-accessmanagement-managing-group-owners.md)
### [Gerenciar associação ao grupo](fundamentals/active-directory-groups-membership-azure-portal.md)
### [Atribuir licenças usando os grupos](fundamentals/active-directory-licensing-whatis-azure-portal.md)
#### [Atribuir licenças a um grupo](users-groups-roles/licensing-groups-assign.md)
#### [Identificar e resolver problemas de licença em um grupo](users-groups-roles/licensing-groups-resolve-problems.md)
#### [Migrar usuários individuais licenciados para licenciamento baseado em grupo](users-groups-roles/licensing-groups-migrate-users.md)
#### [Migrar usuários entre as licenças do produto](users-groups-roles/licensing-groups-change-licenses.md)
#### [Cenários adicionais para licenciamento baseado em grupos](users-groups-roles/licensing-group-advanced.md)
#### [Exemplos do Azure PowerShell para licenciamento baseado em grupo](users-groups-roles/licensing-ps-examples.md)
#### [Referência de produtos e planos de serviço no Azure AD](users-groups-roles/licensing-service-plan-reference.md)
### [Configurar a expiração de grupos do Office 365](users-groups-roles/groups-lifecycle.md)
### [Impor uma política de nomenclatura para grupos](users-groups-roles/groups-naming-policy.md)
### [Exibir todos os grupos](fundamentals/active-directory-groups-view-azure-portal.md)
### [Adicionar grupo de acesso a aplicativos SaaS](users-groups-roles/groups-saasapps.md)
### [Restaurar um grupo excluído do Office 365](fundamentals/active-directory-groups-restore-azure-portal.md)
### [Gerenciar configurações de grupo](fundamentals/active-directory-groups-settings-azure-portal.md)
### [Configurar grupos de autoatendimento](users-groups-roles/groups-self-service-management.md)
### Grupos dinâmicos
#### [Criar um grupo dinâmico](users-groups-roles/groups-create-rule.md)
#### [Sintaxe da regra e propriedades](users-groups-roles/groups-dynamic-membership.md)
#### [Alterar tipo de associação ao grupo](users-groups-roles/groups-change-type.md)
#### [Solucionar problemas](users-groups-roles/groups-troubleshooting.md)

## [Gerenciar relatórios](active-directory-reporting-azure-portal.md)
### [Atividade de entrada](active-directory-reporting-activity-sign-ins.md)
### [Atividade de auditoria](active-directory-reporting-activity-audit-logs.md)
### [Usuários em risco](active-directory-reporting-security-user-at-risk.md)
### [Entradas de risco](active-directory-reporting-security-risky-sign-ins.md)
### [Eventos de risco](active-directory-reporting-risk-events.md)
### [Monitorizando os logs usando o Azure Monitor](reporting-azure-monitor-diagnostics-overview.md)
### [Perguntas frequentes](active-directory-reporting-faq.md)

### Tarefas
#### [Configurar localizações nomeadas](active-directory-named-locations.md)
#### [Localizar relatórios de atividades](active-directory-reporting-migration.md)
#### [Usar o Pacote de Conteúdo do Azure AD Power BI](active-directory-reporting-power-bi-content-pack-how-to.md)
#### [Remediar usuários sinalizados como em risco](active-directory-report-security-user-at-risk-remediation.md)
#### [Rotear os logs de atividade para um hub de eventos do Azure](reporting-azure-monitor-diagnostics-azure-event-hub.md)
#### [Arquivar os logs de atividade para uma conta de armazenamento do Azure](reporting-azure-monitor-diagnostics-azure-storage-account.md)
#### [Integrar os logs de atividade no Splunk usando o Azure Monitor](reporting-azure-monitor-diagnostics-splunk-integration.md)

### Referência
#### [Retenção](active-directory-reporting-retention.md)
#### [Latências](active-directory-reporting-latencies-azure-portal.md)
#### [Referência das atividades de auditoria](active-directory-reporting-activity-audit-reference.md)
#### [Códigos de erro da atividade de entrada](active-directory-reporting-activity-sign-ins-errors.md)
#### [Interpretar o esquema do log de auditoria no Azure Monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
#### [Interpretar o esquema do log de logon no Azure Monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)

### Solucionar problemas
#### [Dados de auditoria ausentes](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Dados ausentes nos downloads](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Erros do pacote de conteúdo de logs de Atividade do Azure AD](active-directory-reporting-troubleshoot-content-pack.md)
#### [Erros na API de Relatórios do Azure AD](active-directory-reporting-troubleshoot-graph-api.md)

### [Acesso Programático](active-directory-reporting-api-getting-started-azure-portal.md)
#### [Pré-requisitos](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [Exemplos de auditoria](active-directory-reporting-api-audit-samples.md)
#### [Exemplos de entrada](active-directory-reporting-api-sign-in-activity-samples.md)
#### [Usando certificados](active-directory-reporting-api-with-certificates.md)

## [Gerenciar senhas](authentication/concept-sspr-howitworks.md)
### Documentos do usuário
#### [Redefinir ou alterar sua senha](user-help/active-directory-passwords-update-your-own-password.md)
#### [Registro de redefinição de senha de autoatendimento](user-help/active-directory-passwords-reset-register.md)


## Gerenciar dispositivos
### [Visão geral](devices/overview.md)

### Guia de início rápido
#### [Configurar dispositivos do Windows 10 registrados pelo Azure AD](user-help/device-management-azuread-registered-devices-windows10-setup.md)
#### [Configurar dispositivos adicionados ao Azure AD](user-help/device-management-azuread-joined-devices-setup.md)

### Tutoriais
#### [Configurar ingresso no Azure AD híbrido para os domínios gerenciados](devices/hybrid-azuread-join-managed-domains.md)
#### [Configurar ingresso no Azure AD híbrido para os domínios federados](devices/hybrid-azuread-join-federated-domains.md)
#### [Configurar manualmente ingresso no Azure AD híbrido](devices/hybrid-azuread-join-manual-steps.md)
#### [Configurar ingresso no Azure AD durante a experiência de primeira execução do Windows 10](devices/azuread-joined-devices-frx.md)

### Guias de instruções
#### [Planejar ingresso no Azure AD](devices/azureadjoin-plan.md)
#### [Planejar sua implementação de ingresso no Azure AD híbrido](devices/hybrid-azuread-join-plan.md)
#### [Controlar ingresso no Azure AD híbrido de seus dispositivos](devices/hybrid-azuread-join-control.md)
#### [Solucionar problemas dos dispositivos atuais do Windows ingressados no Azure AD híbrido](devices/troubleshoot-hybrid-join-windows-current.md)
#### [Solucionar problemas dos dispositivos do Windows de herança ingressados no Azure AD híbrido](devices/troubleshoot-hybrid-join-windows-legacy.md)

### Conceitos
### [Gerenciar os dispositivos usando o portal do Azure](devices/device-management-azure-portal.md)
### [Perguntas frequentes](devices/faq.md)

## Gerenciar aplicativos
### [Visão geral](manage-apps/what-is-application-management.md)
### [Guia de Introdução](manage-apps/plan-an-application-integration.md)
### [Tutoriais de integração de aplicativos SaaS](saas-apps/tutorial-list.md)

### [Provisionar e desprovisionar usuário para aplicativos SaaS](active-directory-saas-app-provisioning.md) 
#### [Tutoriais de integração do aplicativo](saas-apps/tutorial-list.md) 
#### [Automatizar provisionamento para aplicativos habilitados para SCIM](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [Personalizar mapeamentos do atributo](active-directory-saas-customizing-attribute-mappings.md) 
#### [Escrever expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md) 
#### [Usar filtros de escopo](active-directory-saas-scoping-filters.md) 
#### [Relatório sobre provisionamento automático do usuário](active-directory-saas-provisioning-reporting.md) 
#### [Solucionar problemas de provisionamento do usuário](active-directory-application-provisioning-content-map.md) 

### [Acesse aplicativos remotamente com o Proxy de aplicativo](manage-apps/application-proxy.md)
#### Introdução
##### [Habilitar Proxy de aplicativo](manage-apps/application-proxy-enable.md)
##### [Publicar aplicativos](manage-apps/application-proxy-publish-azure-portal.md)
##### [Domínios personalizados](manage-apps/application-proxy-configure-custom-domain.md)
#### [Logon Único](manage-apps/application-proxy-single-sign-on.md)
##### [SSO com KCD](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
##### [SSO com cabeçalhos](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md)
##### [SSO com compartimentação de senha](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)
#### Conceitos
##### [Conectores](manage-apps/application-proxy-connectors.md)
##### [Segurança](manage-apps/application-proxy-security.md)
##### [Redes](manage-apps/application-proxy-network-topology.md)


##### [Atualização do TMG ou UAG](manage-apps/application-proxy-migration.md)

#### Configurações avançadas
##### [Publicar em redes separadas](manage-apps/application-proxy-connector-groups.md)
##### [Servidores proxy](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
##### [Aplicativos com reconhecimento de declarações](manage-apps/application-proxy-configure-for-claims-aware-applications.md)
##### [Aplicativos cliente nativos](manage-apps/application-proxy-configure-native-client-application.md)
##### [Instalação silenciosa](manage-apps/application-proxy-register-connector-powershell.md)
##### [Home page personalizada](manage-apps/application-proxy-configure-custom-home-page.md)
##### [Converter links embutidos](manage-apps/application-proxy-configure-hard-coded-link-translation.md)
##### [Curingas](active-directory-application-proxy-wildcard.md)
##### [Remover dados pessoais](manage-apps/application-proxy-remove-personal-data.md)


#### Orientações de publicação
##### [Área de Trabalho Remota](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Equipes da Microsoft](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](active-directory-application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [Solucionar problemas](manage-apps/application-proxy-troubleshoot.md)

### Gerenciar aplicativos empresariais
#### [Adicionar um aplicativo](manage-apps/add-application-portal.md)
#### [Exibir aplicativos de locatário](manage-apps/view-applications-portal.md)
#### [Atribuir usuários](manage-apps/assign-user-or-group-access-portal.md)
#### [Personalizar identidade visual](manage-apps/change-name-or-logo-portal.md)
#### [Desabilitar entradas de usuário](manage-apps/disable-user-sign-in-portal.md)
#### [Remover usuários](manage-apps/remove-user-or-group-access-portal.md)

#### [Gerenciar o provisionamento de conta de usuário](manage-apps/configure-automatic-user-provisioning-portal.md)
#### [Gerenciar logon único para aplicativos corporativos](manage-apps/configure-single-sign-on-portal.md)
#### [Assinatura de certificados avançada para aplicativos SAML](manage-apps/certificate-signing-options.md)
#### [Como remover um aplicativo da experiência de um usuário](manage-apps/hide-application-from-user-portal.md)
### [Configurar a aceleração automática da entrada usando a Política de HRD](manage-apps/configure-authentication-for-federated-users-portal.md)
### [Migrar aplicativos AD FS para Azure AD](manage-apps/migrate-adfs-apps-to-azure.md) 
### [Gerenciar o acesso aos aplicativos](manage-apps/what-is-access-management.md)
#### [Acesso SSO](manage-apps/what-is-single-sign-on.md)
#### [Certificados para SSO](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [Restrições de locatário](manage-apps/tenant-restrictions.md)
#### [Usar usuários de provisionamento de SCIM](manage-apps/use-scim-to-provision-users-and-groups.md)


### Solucionar problemas



#### Painel de acesso
##### [Aplicativo não aparece](application-access-panel-unexpected-application-not-appearing.md)
##### [Aplicativo inesperado aparece](application-access-panel-unexpected-application-appears.md)
##### [Não é possível entrar](application-access-panel-web-sign-in-problem.md)
##### [Erro ao instalar extensão do navegador](application-access-panel-extension-problem-installing.md)
##### [Como usar o acesso ao aplicativo de autoatendimento](application-access-panel-self-service-applications-how-to.md)
##### [Erro ao usar o acesso ao aplicativo de autoatendimento](application-access-panel-self-service-applications-problem.md)

#### Adicionando um aplicativo
##### [Escolher o tipo de aplicativo](application-config-add-app-problem-how-to-choose-application-type.md)
##### [Problemas comuns - aplicativos da galeria](application-config-add-app-problem-problem-adding-gallery-app.md)
##### [Problemas comuns - aplicativos não da galeria](application-config-add-app-problem-problem-adding-non-gallery-app.md)

#### Proxy de Aplicativo
##### [Problema ao exibir página do aplicativo](application-proxy-page-appearance-broken-problem.md)
##### [O carregamento do aplicativo é muito longo](application-proxy-page-load-speed-problem.md)
##### [Os links na página de aplicativo não funcionam](application-proxy-page-links-broken-problem.md)
##### [Quais portas abrir para meu aplicativo](application-proxy-connectivity-ports-how-to.md)
##### [Nenhum conector útil em um grupo de conectores para meu aplicativo](application-proxy-connectivity-no-working-connector.md)
##### [Configurar no portal de administração](application-proxy-config-how-to.md)
##### [Configurar logon único para meu aplicativo](application-proxy-config-sso-how-to.md)
##### [Problema ao criar um aplicativo no portal de administração](application-proxy-config-problem.md)
##### [Configurar a Delegação Restrita do Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [Configurar com PingAccess](application-proxy-back-end-ping-access-how-to.md)
##### [Erro "Não é Possível Acessar este Aplicativo Corporativo"](application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [Problema ao instalar o Conector de Agente de Proxy do Aplicativo](application-proxy-connector-installation-problem.md)


#### Registro de aplicativo
##### [Inserir campos para o objeto de aplicativo](application-dev-registration-config-specific-application-property-how-to.md)
##### [Alterar os padrões de vida útil do token](application-dev-registration-config-change-token-lifetime-how-to.md)

#### Autenticação
##### [Configurar pontos de extremidade](application-dev-registration-config-how-to.md)

#### Acesso Condicional
##### [O cliente não atendeu aos pré-requisitos de Registro do Dispositivo](active-directory-conditional-access.md)
##### [O locatário está sendo bloqueado devido à configuração incorreta de políticas de Acesso Condicional](active-directory-conditional-access-device-remediation.md)
##### [Como e quando as regras de fora da rede corporativa entram vigor?](https://aka.ms/calocation)
##### [Como aumentar o número de dispositivos que o usuário tem permissão para registrar no Azure AD?](active-directory-azureadjoin-setup.md)
##### [Como configurar o Acesso Condicional para o Exchange Online?](https://aka.ms/csforexchange)
##### [Como configurar o Acesso Condicional para dispositivos do Windows 7?](active-directory-conditional-access.md#device-based-conditional-access)
##### [Quais aplicativos têm suporte com o acesso condicional?](active-directory-conditional-access-supported-apps.md)

#### Localizar uma API
##### [Localizar uma API](application-dev-api-find-an-api-how-to.md)

#### Gerenciando o acesso
##### [Atribuir usuários e grupos a um aplicativo](application-access-assignment-how-to-add-assignment.md)
##### [Remover um acesso de usuários a um aplicativo](application-access-assignment-how-to-remove-assignment.md)
##### [Configurar atribuição de aplicativos com autoatendimento](application-access-self-service-how-to.md)
##### [Usuário inesperado atribuído](application-access-unexpected-user-assignment.md)
##### [Aplicativo inesperado na lista de aplicativos](application-access-unexpected-application.md)

#### Aplicativos multilocatário
##### [Configurar um novo aplicativo](application-dev-setup-multi-tenant-app.md)
##### [Adicionar à galeria de aplicativos](application-dev-registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### Permissões
##### [Escolher permissões para uma API](application-dev-perms-for-given-api.md)
##### [Conceder permissões a meu aplicativo](application-dev-registration-config-grant-permissions-how-to.md)
##### [Permissões do aplicativo versus delegadas](application-dev-delegated-and-app-perms.md)
##### [Consentimento de aplicativos](application-dev-consent-framework.md)

#### Provisionamento
##### [Quanto tempo demora](application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [Levando horas - aplicativo da galeria](application-provisioning-when-will-provisioning-finish.md)
##### [Configurar provisionamento de usuários - aplicativo da galeria](application-provisioning-config-how-to.md)
##### [Problema ao configurar provisionamento de usuários - aplicativo da galeria](application-provisioning-config-problem.md)
##### [Problema para salvar as credenciais de administrador ao configurar o aplicativo da galeria para o provisionamento do usuário](application-provisioning-config-problem-storage-limit.md)
##### [Os usuários não são provisionados - aplicativo da galeria](application-provisioning-config-problem-no-users-provisioned.md)
##### [Usuários errados provisionados - aplicativo da galeria](application-provisioning-config-problem-wrong-users-provisioned.md)

#### Logon único
##### [Escolher um método](application-config-sso-how-to-choose-sign-on-method.md)
##### [Configurar](application-dev-registration-config-sso-how-to.md)
##### [Configurar federado - aplicativos da galeria](application-config-sso-how-to-configure-federated-sso-gallery.md)
##### [Problemas comuns ao configurar federados - aplicativos da galeria](application-config-sso-problem-configure-federated-sso-gallery.md)
##### [Configurar federado - aplicativos não da galeria](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
##### [Problemas comuns ao configurar federados - aplicativos não da galeria](application-config-sso-problem-configure-federated-sso-non-gallery.md)
##### [Configurar senha - aplicativos da galeria](application-config-sso-how-to-configure-password-sso-gallery.md)
##### [Problemas comuns ao configurar senha - aplicativos da galeria](application-config-sso-problem-configure-password-sso-gallery.md)
##### [Configurar senha - aplicativos não da galeria](application-config-sso-how-to-configure-password-sso-non-gallery.md)
##### [Problemas comuns ao configurar senha - aplicativos não da galeria](application-config-sso-problem-configure-password-sso-non-gallery.md)

#### Problemas de entrada do usuário
##### [Solicitação de consentimento inesperada](application-sign-in-unexpected-user-consent-prompt.md)
##### [Erro de consentimento do usuário](application-sign-in-unexpected-user-consent-error.md)
##### [Problemas para entrar no portal personalizado](application-sign-in-other-problem-deeplink.md)
##### [Problemas para entrar no painel de acesso](application-sign-in-other-problem-access-panel.md)
##### [Erro na página de entrada do aplicativo](application-sign-in-problem-application-error.md)
##### [Problema de logon único com senha - aplicativo não da galeria](application-sign-in-problem-password-sso-non-gallery.md)
##### [Problema de logon único com senha - aplicativo da galeria](application-sign-in-problem-password-sso-gallery.md)
##### [Problema ao entrar em um aplicativo da Microsoft](application-sign-in-problem-first-party-microsoft.md)
##### [Problema com logon único federado - aplicativo não da galeria](application-sign-in-problem-federated-sso-non-gallery.md)
##### [Problema com logon único federado - aplicativo da galeria](application-sign-in-problem-federated-sso-gallery.md)
##### [Problema com aplicativo desenvolvido personalizado](application-sign-in-problem-custom-dev.md)
##### [Problema com aplicativo local - Proxy do Aplicativo](application-sign-in-problem-on-premises-application-proxy.md)

### [Desenvolver aplicativos](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Biblioteca de documentos](active-directory-apps-index.md)

## Gerenciar seu diretório
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Nomes de domínio personalizados
#### [Início rápido](fundamentals/add-custom-domain.md)
#### [Adicionar nomes de domínio personalizados](users-groups-roles/domains-manage.md)
### [Administrar seu diretório](fundamentals/active-directory-administer.md)
### [Excluir um diretório](users-groups-roles/directory-delete-howto.md)
### [Vários diretórios](users-groups-roles/licensing-directory-independence.md)
### [Inscrição por autoatendimento](users-groups-roles/directory-self-service-signup.md)
### [Assumir um diretório não gerenciado](users-groups-roles/domains-admin-takeover.md)
### [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Habilitar](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Configurações de política de grupo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Configurações do Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Perguntas frequentes](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Solucionar problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Integrar identidades locais usando o Azure AD Connect](./connect/active-directory-aadconnect.md)

## Delegar acesso aos recursos
### [Funções de administrador](users-groups-roles/directory-assign-admin-roles.md)
#### [Exibir membros de uma função de administrador](users-groups-roles//directory-manage-roles-portal.md)
#### [Atribuir função de administrador a um usuário](fundamentals/active-directory-users-assign-role-azure-portal.md)
#### [Comparar permissões de usuários membro e convidado](fundamentals/users-default-permissions.md)
### [Segurança da função de administrador](users-groups-roles/directory-admin-roles-secure.md)  
#### [Criar conta do administrador de acesso de emergência](users-groups-roles/directory-emergency-access.md)
### [Unidades administrativas](users-groups-roles/directory-administrative-units.md)
### [Configurar tempo de vida de tokens](active-directory-configurable-token-lifetimes.md)

## Análises de acesso
### [Visão geral de revisões de acesso](active-directory-azure-ad-controls-access-reviews-overview.md)
### [Concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md)
### [Criar uma revisão de acesso](active-directory-azure-ad-controls-create-access-review.md)
### [Como executar uma análise de acesso](active-directory-azure-ad-controls-perform-access-review.md)
### [Como analisar seu acesso](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [Acesso de convidado com análises de acesso](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [Gerenciando o acesso de usuário com análises](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [Gerenciamento de programas e controles](active-directory-azure-ad-controls-manage-programs-controls.md)
### [Recuperar resultados da análise do acesso](active-directory-azure-ad-controls-retrieve-access-review.md)

## Proteger suas identidades
### [Acesso condicional](active-directory-conditional-access-azure-portal.md)
#### [Introdução](active-directory-conditional-access-azure-portal-get-started.md)
#### Guia de início rápido
##### [Configurar cada aplicativo de nuvem MFA](active-directory-conditional-access-app-based-mfa.md)
##### [Exigir que os termos de uso sejam aceitos](active-directory-conditional-access-tou.md)
##### [Bloquear acesso quando é detectado um risco da sessão](active-directory-conditional-access-app-sign-in-risk.md)
#### Tutoriais
##### [Migrar política MFA clássica](active-directory-conditional-access-migration-mfa.md)
#### Conceitos
##### [Proteção da Linha de Base](active-directory-conditional-access-baseline-protection.md)
##### [Condições](active-directory-conditional-access-conditions.md)
##### [Condições de local](active-directory-conditional-access-locations.md)
##### [Controles](active-directory-conditional-access-controls.md)
##### [Ferramenta e-se](active-directory-conditional-access-whatif.md)
##### [Entender as políticas do dispositivo para os serviços do Office 365](active-directory-conditional-access-device-policies.md)
#### Guias de instruções
##### [práticas recomendadas](active-directory-conditional-access-best-practices.md)
##### [Configurar políticas de acesso condicional para tentativas de acesso de redes não confiáveis](active-directory-conditional-access-untrusted-networks.md)
##### [Configurar o acesso condicional com base no dispositivo](active-directory-conditional-access-policy-connected-applications.md)
##### [Configurar o acesso condicional com base no aplicativo](active-directory-conditional-access-mam.md)
##### [Forneça os termos de uso para usuários e aplicativos](active-directory-tou.md)
##### [Migrar políticas clássicas](active-directory-conditional-access-migration.md)
##### [Configurar a conectividade VPN](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/always-on-vpn-deploy)
##### [Configurar o SharePoint e o Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
##### [Correção](active-directory-conditional-access-device-remediation.md)
#### [Referência técnica](active-directory-conditional-access-technical-reference.md)
#### [Perguntas frequentes](active-directory-conditional-faqs.md)

### Autenticação baseada em certificado
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [Introdução](active-directory-certificate-based-authentication-get-started.md)

### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [Habilitar](active-directory-identityprotection-enable.md)
#### [Detectar vulnerabilidades](active-directory-identityprotection-vulnerabilities.md)
#### [Eventos de risco](active-directory-identity-protection-risk-events.md)
#### [Notificações](active-directory-identityprotection-notifications.md)
#### [Experiência de conexão](active-directory-identityprotection-flows.md)
#### [Simular eventos de risco](active-directory-identityprotection-playbook.md)
#### [Desbloquear usuários](active-directory-identityprotection-unblock-howto.md)
#### [Perguntas frequentes](active-directory-identity-protection-faqs.md)
#### [Glossário](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](privileged-identity-management/pim-configure.md?toc=%2fazure%2factive-directory%2ftoc.json)

## Integrar outros serviços no Azure AD 
### [Integrar LinkedIn no Azure AD](users-groups-roles/linkedin-integration.md)

## [Implantar o AD FS no Azure](active-directory-aadconnect-azure-adfs.md)
### [Alta disponibilidade](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Alterar o algoritmo de hash da assinatura](active-directory-federation-sha256-guidance.md)

## [Solucionar problemas](fundamentals/active-directory-troubleshooting-support-howto.md)

## Implantar a Prova de conceito (PoC) do Azure AD
### [Manual de PoC: introdução](active-directory-playbook-intro.md)
### [Manual de PoC: ingredientes](active-directory-playbook-ingredients.md)
### [Manual de PoC: implementação](active-directory-playbook-implementation.md)
### [Manual de PoC: blocos de construção](active-directory-playbook-building-blocks.md)


# Referência
## [Exemplos de código](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [Cmdlets do Azure PowerShell](/powershell/azure/overview)
## [Referência de API Java](/java/api)
## [API do .NET](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [Restrições e limites de serviço](users-groups-roles/directory-service-limits-restrictions.md)

# Relacionados
## [Autenticação Multifator](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure AD para desenvolvedores](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/pim-configure.md)

# Recursos
## [Planos de implantação do Azure AD](./fundamentals/active-directory-deployment-plans.md)
## [Fórum de comentários do Azure](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Roteiro do Azure](https://azure.microsoft.com/roadmap/?category=security-identity)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Preços](https://azure.microsoft.com/pricing/details/active-directory/)
## [Calculadora de preço](https://azure.microsoft.com/pricing/calculator/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
