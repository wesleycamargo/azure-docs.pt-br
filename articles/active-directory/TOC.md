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
## [Inscrever-se no Azure AD Premium](fundamentals/active-directory-get-started-premium.md)
## [Como adicionar um nome de domínio personalizado](fundamentals/add-custom-domain.md)
## [Configurar a identidade visual da empresa](fundamentals/customize-branding.md)
## [Adicionar usuários ao Azure AD](fundamentals/add-users-azure-active-directory.md)
## [Atribuir licenças aos usuários](fundamentals/license-users-groups.md)
## [Como configurar a redefinição de senha de autoatendimento](authentication/quickstart-sspr.md)
## [Adicionar informações de privacidade da sua organização no Azure AD](active-directory-properties-area.md)
## [Acessar o Azure Active Directory para criar um novo locatário](fundamentals/active-directory-access-create-new-tenant.md)


# Como
## Planejar e projetar
### [Compreenda a arquitetura do Azure AD](fundamentals/active-directory-architecture.md)
### [Mapeamento de declarações no Azure Active Directory](develop/active-directory-claims-mapping.md)
### [Implantar uma Solução de Identidade Híbrida](hybrid/plan-hybrid-identity-design-considerations-overview.md)
#### Determinar os requisitos
##### [Identidade](hybrid/plan-hybrid-identity-design-considerations-business-needs.md)
##### [Sincronização de diretórios](hybrid/plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Autenticação multifator](hybrid/plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Identificar estratégia de ciclo de vida](hybrid/plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Planejar segurança de dados](hybrid/plan-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Proteção de dados](hybrid/plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Gerenciamento de conteúdo](hybrid/plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Controle de acesso](hybrid/plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Resposta a incidentes](hybrid/plan-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Planejar o ciclo de vida de identidade
##### [Tarefas](hybrid/plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Estratégia de adoção](hybrid/plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Próximas etapas](hybrid/plan-hybrid-identity-design-considerations-nextsteps.md)
#### [Comparação de ferramentas](hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md)

## Gerenciar usuários
### [Adicionar novos usuários ao Azure AD](fundamentals/add-users-azure-active-directory.md)
### [Gerenciar perfis de usuário](fundamentals/active-directory-users-profile-azure-portal.md)
### [Redefinir senhas de usuário](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [Compartilhar contas](active-directory-sharing-accounts.md)
### [Atribuir usuários a funções de administrador](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [Adicionar usuários convidados de outro diretório (B2B)](b2b/what-is-b2b.md)
#### [Admins adicionando usuários B2B](b2b/add-users-administrator.md)
#### [Trabalhos de informação adicionando usuários B2B](b2b/add-users-information-worker.md)
#### [API e personalização](b2b/customize-invitation-api.md)
#### [Federação do Google](b2b/google-federation.md)
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
### [Gerenciar grupos](fundamentals/active-directory-groups-create-azure-portal.md)
### [Excluir um grupo e seus membros](fundamentals/active-directory-groups-delete-group.md)
### [Gerenciar configurações de grupo](fundamentals/active-directory-groups-settings-azure-portal.md)
## [Gerenciar relatórios](reports-monitoring/overview-reports.md)
### [Atividade de entrada](reports-monitoring/concept-sign-ins.md)
### [Atividade de auditoria](reports-monitoring/concept-audit-logs.md)
### [Usuários em risco](reports-monitoring/concept-user-at-risk.md)
### [Entradas de risco](reports-monitoring/concept-risky-sign-ins.md)
### [Eventos de risco](reports-monitoring/concept-risk-events.md)
### [Monitorizando os logs usando o Azure Monitor](reports-monitoring/concept-activity-logs-in-azure-monitor.md)
### [Perguntas frequentes](reports-monitoring/reports-faq.md)

### Tarefas
#### [Baixar um relatório de entrada](reports-monitoring/quickstart-download-sign-in-report.md)
#### [Baixar um relatório de auditoria](reports-monitoring/quickstart-download-audit-report.md)
#### [Configurar localizações nomeadas](reports-monitoring/quickstart-configure-named-locations.md)
#### [Localizar relatórios de atividades](reports-monitoring/howto-find-activity-reports.md)
#### [Usar o Pacote de Conteúdo do Azure AD Power BI](reports-monitoring/howto-power-bi-content-pack.md)
#### [Remediar usuários sinalizados como em risco](reports-monitoring/howto-remediate-users-flagged-for-risk.md)
#### [Rotear os logs de atividade para um hub de eventos do Azure](reports-monitoring/quickstart-azure-monitor-stream-logs-to-event-hub.md)
#### [Arquivar os logs de atividade para uma conta de armazenamento do Azure](reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
#### [Integrar os logs de atividade no Splunk usando o Azure Monitor](reports-monitoring/tutorial-integrate-activity-logs-with-splunk.md)
#### [Integrar os logs de atividade no SumoLogic usando o Azure Monitor](reports-monitoring/howto-integrate-activity-logs-with-sumologic.md)
#### [Integrar logs de atividade no Log Analytics usando o Azure Monitor](reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

### Referência
#### [Retenção](reports-monitoring/reference-reports-data-retention.md)
#### [Latências](reports-monitoring/reference-reports-latencies.md)
#### [Referência das atividades de auditoria](reports-monitoring/reference-audit-activities.md)
#### [Códigos de erro da atividade de entrada](reports-monitoring/reference-sign-ins-error-codes.md)
#### [Interpretar o esquema do log de auditoria no Azure Monitor](reports-monitoring/reference-azure-monitor-audit-log-schema.md)
#### [Interpretar o esquema do log de logon no Azure Monitor](reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md)

### Solucionar problemas
#### [Dados ausentes nos logs de atividade do Azure AD](reports-monitoring/troubleshoot-missing-audit-data.md)
#### [Dados ausentes nos downloads](reports-monitoring/troubleshoot-missing-data-download.md)
#### [Erros no pacote de conteúdo dos logs de atividade do Azure AD](reports-monitoring/troubleshoot-content-pack.md)
#### [Erros na API de Relatórios do Azure AD](reports-monitoring/troubleshoot-graph-api.md)

### [Acesso Programático](reports-monitoring/concept-reporting-api.md)
#### [Pré-requisitos](reports-monitoring/howto-configure-prerequisites-for-reporting-api.md)
#### [Usando certificados](reports-monitoring/tutorial-access-api-with-certificates.md)

## [Gerenciar senhas](authentication/concept-sspr-howitworks.md)

## Gerenciar aplicativos
### [Visão geral](manage-apps/what-is-application-management.md)
### [Guia de Introdução](manage-apps/plan-an-application-integration.md)
### [Tutoriais de integração de aplicativos SaaS](saas-apps/tutorial-list.md)

### [Provisionar e desprovisionar usuário para aplicativos SaaS](manage-apps/user-provisioning.md) 
#### [Tutoriais de integração do aplicativo](saas-apps/tutorial-list.md) 
#### [Automatizar provisionamento para aplicativos habilitados para SCIM](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [Personalizar mapeamentos do atributo](manage-apps/customize-application-attributes.md) 
#### [Escrever expressões para mapeamentos de atributo](manage-apps/functions-for-customizing-application-data.md) 
#### [Usar filtros de escopo](manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) 
#### [Relatório sobre provisionamento automático do usuário](manage-apps/check-status-user-account-provisioning.md) 
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
##### [Curingas](manage-apps/application-proxy-wildcard.md)
##### [Remover dados pessoais](manage-apps/application-proxy-remove-personal-data.md)


#### Orientações de publicação
##### [Área de Trabalho Remota](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Equipes da Microsoft](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](manage-apps/application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [Solucionar problemas](manage-apps/application-proxy-troubleshoot.md)

### Gerenciar aplicativos empresariais
#### [Adicionar um aplicativo](manage-apps/add-application-portal.md)
#### [Exibir aplicativos de locatário](manage-apps/view-applications-portal.md)
#### [Configurar Logon Único](manage-apps/configure-single-sign-on-portal.md)
#### [Atribuir usuários](manage-apps/assign-user-or-group-access-portal.md)
#### [Personalizar identidade visual](manage-apps/change-name-or-logo-portal.md)
#### [Desabilitar entradas de usuário](manage-apps/disable-user-sign-in-portal.md)
#### [Remover usuários](manage-apps/remove-user-or-group-access-portal.md)

#### [Gerenciar o provisionamento de conta de usuário](manage-apps/configure-automatic-user-provisioning-portal.md)

#### [Assinatura de certificados avançada para aplicativos SAML](manage-apps/certificate-signing-options.md)
#### [Como remover um aplicativo da experiência de um usuário](manage-apps/hide-application-from-user-portal.md)
### [Configurar a aceleração automática da entrada usando a Política de HRD](manage-apps/configure-authentication-for-federated-users-portal.md)
### [Migrar aplicativos AD FS para Azure AD](manage-apps/migrate-adfs-apps-to-azure.md) 
### [Gerenciar o acesso aos aplicativos](manage-apps/what-is-access-management.md)
#### [Acesso SSO](manage-apps/what-is-single-sign-on.md)
#### [Certificados para SSO](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [Restrições de locatário](manage-apps/tenant-restrictions.md)
#### [Usar usuários de provisionamento de SCIM](manage-apps/use-scim-to-provision-users-and-groups.md)

### [Noções básicas sobre experiências de consentimento de aplicativo do Azure AD](develop/application-consent-experience.md)

### Solucionar problemas



#### Painel de acesso
##### [Aplicativo não aparece](manage-apps/access-panel-troubleshoot-application-not-appearing.md)
##### [Aplicativo inesperado aparece](manage-apps/access-panel-troubleshoot-unexpected-application.md)
##### [Não é possível entrar](manage-apps/access-panel-troubleshoot-web-sign-in-problem.md)
##### [Erro ao instalar extensão do navegador](manage-apps/access-panel-extension-problem-installing.md)
##### [Como usar o acesso ao aplicativo de autoatendimento](manage-apps/access-panel-manage-self-service-access.md)
##### [Erro ao usar o acesso ao aplicativo de autoatendimento](manage-apps/access-panel-troubleshoot-self-service-access.md)

#### Adicionando um aplicativo
##### [Escolher o tipo de aplicativo](manage-apps/choose-application-type.md)
##### [Problemas comuns - aplicativos da galeria](manage-apps/adding-gallery-app-common-problems.md)
##### [Problemas comuns - aplicativos não da galeria](manage-apps/adding-non-gallery-app-common-problems.md)

#### Proxy de Aplicativo
##### [Problema ao exibir página do aplicativo](manage-apps/application-proxy-page-appearance-broken-problem.md)
##### [O carregamento do aplicativo é muito longo](manage-apps/application-proxy-page-load-speed-problem.md)
##### [Os links na página de aplicativo não funcionam](manage-apps/application-proxy-page-links-broken-problem.md)
##### [Quais portas abrir para meu aplicativo](manage-apps/application-proxy-connectivity-ports-how-to.md)
##### [Nenhum conector útil em um grupo de conectores para meu aplicativo](manage-apps/application-proxy-connectivity-no-working-connector.md)
##### [Configurar no portal de administração](manage-apps/application-proxy-config-how-to.md)
##### [Configurar logon único para meu aplicativo](manage-apps/application-proxy-config-sso-how-to.md)
##### [Problema ao criar um aplicativo no portal de administração](manage-apps/application-proxy-config-problem.md)
##### [Configurar a Delegação Restrita do Kerberos](manage-apps/application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [Configurar com PingAccess](manage-apps/application-proxy-back-end-ping-access-how-to.md)
##### [Erro "Não é Possível Acessar este Aplicativo Corporativo"](manage-apps/application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [Problema ao instalar o Conector de Agente de Proxy do Aplicativo](manage-apps/application-proxy-connector-installation-problem.md)


#### Registro de aplicativo
##### [Inserir campos para o objeto de aplicativo](develop/registration-config-specific-application-property-how-to.md)
##### [Alterar os padrões de vida útil do token](develop/registration-config-change-token-lifetime-how-to.md)

#### Autenticação
##### [Configurar pontos de extremidade](develop/registration-config-how-to.md)

#### Acesso Condicional
##### [O cliente não atendeu aos pré-requisitos de Registro do Dispositivo](active-directory-conditional-access.md)
##### [Como e quando as regras de fora da rede corporativa entram vigor?](https://aka.ms/calocation)
##### [Como aumentar o número de dispositivos que o usuário tem permissão para registrar no Azure AD?](active-directory-azureadjoin-setup.md)
##### [Como configurar o Acesso Condicional para o Exchange Online?](https://aka.ms/csforexchange)
##### [Como configurar o Acesso Condicional para dispositivos do Windows 7?](active-directory-conditional-access.md#device-based-conditional-access)
##### [Quais aplicativos têm suporte com o acesso condicional?](active-directory-conditional-access-supported-apps.md)

#### Localizar uma API
##### [Localizar uma API](develop/api-find-an-api-how-to.md)

#### Gerenciando o acesso
##### [Atribuir usuários e grupos a um aplicativo](manage-apps/methods-for-assigning-users-and-groups.md)
##### [Remover um acesso de usuários a um aplicativo](manage-apps/methods-for-removing-user-access.md)
##### [Configurar atribuição de aplicativos com autoatendimento](manage-apps/manage-self-service-access.md)
##### [Usuário inesperado atribuído](manage-apps/ways-users-get-assigned-to-applications.md)
##### [Aplicativo inesperado na lista de aplicativos](manage-apps/application-types.md)

#### Aplicativos multilocatário
##### [Configurar um novo aplicativo](develop/setup-multi-tenant-app.md)
##### [Adicionar à galeria de aplicativos](develop/registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### Permissões
##### [Escolher permissões para uma API](develop/perms-for-given-api.md)
##### [Permissões do aplicativo versus delegadas](develop/delegated-and-app-perms.md)
##### [Consentimento de aplicativos](develop/consent-framework.md)

#### Provisionamento
##### [Quanto tempo demora](manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [Levando horas - aplicativo da galeria](manage-apps/application-provisioning-when-will-provisioning-finish.md)
##### [Configurar provisionamento de usuários - aplicativo da galeria](manage-apps/application-provisioning-config-how-to.md)
##### [Problema ao configurar provisionamento de usuários - aplicativo da galeria](manage-apps/application-provisioning-config-problem.md)
##### [Problema para salvar as credenciais de administrador ao configurar o aplicativo da galeria para o provisionamento do usuário](manage-apps/application-provisioning-config-problem-storage-limit.md)
##### [Os usuários não são provisionados - aplicativo da galeria](manage-apps/application-provisioning-config-problem-no-users-provisioned.md)
##### [Usuários errados provisionados - aplicativo da galeria](manage-apps/application-provisioning-config-problem-wrong-users-provisioned.md)

#### Logon único
##### [Escolher um método](manage-apps/single-sign-on-modes.md)
##### [Configurar](develop/registration-config-sso-how-to.md)
##### [Configurar federado - aplicativos da galeria](manage-apps/configure-federated-single-sign-on-gallery-applications.md)
##### [Problemas comuns ao configurar federados - aplicativos da galeria](manage-apps/configure-federated-single-sign-on-gallery-applications-problems.md)
##### [Configurar federado - aplicativos não da galeria](manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
##### [Problemas comuns ao configurar federados - aplicativos não da galeria](manage-apps/configure-federated-single-sign-on-non-gallery-applications-problems.md)
##### [Configurar senha - aplicativos da galeria](manage-apps/configure-password-single-sign-on-gallery-applications.md)
##### [Problemas comuns ao configurar senha - aplicativos da galeria](manage-apps/configure-password-single-sign-on-gallery-applications-problems.md)
##### [Configurar senha - aplicativos não da galeria](manage-apps/configure-password-single-sign-on-non-gallery-applications.md)
##### [Problemas comuns ao configurar senha - aplicativos não da galeria](manage-apps/configure-password-single-sign-on-non-gallery-applications-problems.md)

#### Problemas de entrada do usuário
##### [Solicitação de consentimento inesperada](manage-apps/application-sign-in-unexpected-user-consent-prompt.md)
##### [Erro de consentimento do usuário](manage-apps/application-sign-in-unexpected-user-consent-error.md)
##### [Problemas para entrar no portal personalizado](manage-apps/application-sign-in-other-problem-deeplink.md)
##### [Problemas para entrar no painel de acesso](manage-apps/application-sign-in-other-problem-access-panel.md)
##### [Erro na página de entrada do aplicativo](manage-apps/application-sign-in-problem-application-error.md)
##### [Problema de logon único com senha - aplicativo não da galeria](manage-apps/application-sign-in-problem-password-sso-non-gallery.md)
##### [Problema de logon único com senha - aplicativo da galeria](manage-apps/application-sign-in-problem-password-sso-gallery.md)
##### [Problema ao entrar em um aplicativo da Microsoft](manage-apps/application-sign-in-problem-first-party-microsoft.md)
##### [Problema com logon único federado - aplicativo não da galeria](manage-apps/application-sign-in-problem-federated-sso-non-gallery.md)
##### [Problema com logon único federado - aplicativo da galeria](manage-apps/application-sign-in-problem-federated-sso-gallery.md)
##### [Problema com aplicativo desenvolvido personalizado](manage-apps/application-sign-in-problem-custom-dev.md)
##### [Problema com aplicativo local - Proxy do Aplicativo](manage-apps/application-sign-in-problem-on-premises-application-proxy.md)

### [Desenvolver aplicativos](active-directory-applications-guiding-developers-for-lob-applications.md)


## Gerenciar seu diretório
### [Azure AD Connect](hybrid/whatis-hybrid-identity.md)
### Nomes de domínio personalizados
#### [Início rápido](fundamentals/add-custom-domain.md)
### [Administrar seu diretório](fundamentals/active-directory-administer.md)
### [Integrar identidades locais usando o Azure AD Connect](hybrid/whatis-hybrid-identity.md)

### [Configurar tempo de vida de tokens](develop/active-directory-configurable-token-lifetimes.md)

## Proteger suas identidades

### [Privileged Identity Management](privileged-identity-management/pim-configure.md?toc=%2fazure%2factive-directory%2ftoc.json)

## [Implantar o AD FS no Azure](hybrid/how-to-connect-fed-azure-adfs.md)
### [Alta disponibilidade](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Alterar o algoritmo de hash da assinatura](active-directory-federation-sha256-guidance.md)

## [Solucionar problemas](fundamentals/active-directory-troubleshooting-support-howto.md)

# Referência
## [Exemplos de código](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [Cmdlets do Azure PowerShell](/powershell/azure/overview)
## [Referência de API Java](/java/api)
## [API do .NET](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)

# Relacionados
## [Autenticação Multifator](/azure/multi-factor-authentication/)
## [Azure AD Connect](hybrid/whatis-hybrid-identity.md)
## [Azure AD Connect Health](hybrid/whatis-hybrid-identity-health.md)
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
