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
#### [Portal do Azure](fundamentals/active-directory-groups-create-azure-portal.md)
#### [PowerShell do Azure AD para Graph (v2)](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
#### [Azure AD PowerShell MSOnline](active-directory-accessmanagement-groups-settings-cmdlets.md)
### [Gerenciar membros do grupo](fundamentals/active-directory-groups-members-azure-portal.md)
### [Gerenciar proprietários do grupo](fundamentals/active-directory-accessmanagement-managing-group-owners.md)
### [Gerenciar associação ao grupo](fundamentals/active-directory-groups-membership-azure-portal.md)
### [Atribuir licenças usando os grupos](fundamentals/active-directory-licensing-whatis-azure-portal.md)
#### [Atribuir licenças a um grupo](active-directory-licensing-group-assignment-azure-portal.md)
#### [Identificar e resolver problemas de licença em um grupo](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Migrar usuários individuais licenciados para licenciamento baseado em grupo](active-directory-licensing-group-migration-azure-portal.md)
#### [Migrar usuários entre as licenças do produto](active-directory-licensing-group-product-migration.md)
#### [Cenários adicionais para licenciamento baseado em grupos](active-directory-licensing-group-advanced.md)
#### [Exemplos do Azure PowerShell para licenciamento baseado em grupo](active-directory-licensing-ps-examples.md)
#### [Referência de produtos e planos de serviço no Azure AD](active-directory-licensing-product-and-service-plan-reference.md)
### [Configurar a expiração de grupos do Office 365](active-directory-groups-lifecycle-azure-portal.md)
### [Impor uma política de nomenclatura para grupos](groups-naming-policy.md)
### [Exibir todos os grupos](fundamentals/active-directory-groups-view-azure-portal.md)
### [Adicionar grupo de acesso a aplicativos SaaS](active-directory-accessmanagement-group-saasapps.md)
### [Restaurar um grupo excluído do Office 365](fundamentals/active-directory-groups-restore-azure-portal.md)
### [Gerenciar configurações de grupo](fundamentals/active-directory-groups-settings-azure-portal.md) 
### Criar regras avançadas
#### [Portal do Azure](active-directory-groups-dynamic-membership-azure-portal.md)
### [Configurar grupos de autoatendimento](active-directory-accessmanagement-self-service-group-management.md)
### [Solucionar problemas](active-directory-accessmanagement-troubleshooting.md)

## [Gerenciar relatórios](active-directory-reporting-azure-portal.md)
### [Atividade de entrada](active-directory-reporting-activity-sign-ins.md)
### [Atividade de auditoria](active-directory-reporting-activity-audit-logs.md)
### [Usuários em risco](active-directory-reporting-security-user-at-risk.md)
### [Entradas de risco](active-directory-reporting-security-risky-sign-ins.md)
### [Eventos de risco](active-directory-reporting-risk-events.md)
### [Perguntas frequentes](active-directory-reporting-faq.md)
### Tarefas
#### [Configurar localizações nomeadas](active-directory-named-locations.md)
#### [Localizar relatórios de atividades](active-directory-reporting-migration.md)
#### [Usar o Pacote de Conteúdo do Power BI do Active Directory do Azure](active-directory-reporting-power-bi-content-pack-how-to.md)
#### [Remediar usuários sinalizados como em risco](active-directory-report-security-user-at-risk-remediation.md)
### Referência
#### [Retenção](active-directory-reporting-retention.md)
#### [Latências](active-directory-reporting-latencies-azure-portal.md)
#### [Notificações](active-directory-reporting-notifications.md)
#### [Referência das atividades de auditoria](active-directory-reporting-activity-audit-reference.md)
#### [Códigos de erro da atividade de entrada](active-directory-reporting-activity-sign-ins-errors.md)
#### [Autenticação multifator](active-directory-reporting-activity-sign-ins-mfa.md)



### Solucionar problemas
#### [Dados de auditoria ausentes](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Dados ausentes nos downloads](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Erros de pacote de conteúdo de logs de Atividade do Azure Active Directory](active-directory-reporting-troubleshoot-content-pack.md)
### [Acesso Programático](active-directory-reporting-api-getting-started-azure-portal.md)
#### [Pré-requisitos](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [Exemplos de auditoria](active-directory-reporting-api-audit-samples.md)
#### [Exemplos de entrada](active-directory-reporting-api-sign-in-activity-samples.md)
#### [Usando certificados](active-directory-reporting-api-with-certificates.md)

## Gerenciar senhas
### [Visão geral de senhas](authentication/active-directory-passwords-overview.md)
### Documentos do usuário
#### [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md)
#### [Práticas recomendadas de senha](active-directory-secure-passwords.md)
#### [Registro de redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md)
### [Como funciona o SSPR](authentication/concept-sspr-howitworks.md)
### [Guia de Implantação de SSPR](authentication/howto-sspr-deployment.md)
### [SSPR e Windows 10](authentication/tutorial-sspr-windows.md)
### [Políticas de SSPR ](authentication/concept-sspr-policy.md)
### [Personalização de SSPR](authentication/concept-sspr-customization.md)
### [Requisitos de dados de SSPR](authentication/howto-sspr-authenticationdata.md)
### [Relatórios de SSPR](authentication/howto-sspr-reporting.md)
### Administradores de TI: redefinir senhas
#### [Portal do Azure](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [Licença SSPR](authentication/concept-sspr-licensing.md)
### [Write-back de senha](authentication/howto-sspr-writeback.md)
### [Solucionar problemas](authentication/active-directory-passwords-troubleshoot.md)
### [Perguntas frequentes](authentication/active-directory-passwords-faq.md)


## Gerenciar dispositivos
### [Introdução](device-management-introduction.md)
### [Usando o portal do Azure](device-management-azure-portal.md)
### [Planejar a Associação do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
### [Perguntas frequentes](device-management-faq.md)
### Tarefas
#### [Configurar dispositivos do Windows 10 registrados pelo Azure AD](device-management-azuread-registered-devices-windows10-setup.md)
#### [Configurar dispositivos adicionados ao Azure AD](device-management-azuread-joined-devices-setup.md)
#### [Configurar dispositivos adicionados ao Azure AD](device-management-hybrid-azuread-joined-devices-setup.md)
#### [Implantar no local](active-directory-device-registration-on-premises-setup.md)
#### [Associação do Azure AD durante a experiência de primeira execução do Windows 10](device-management-azuread-joined-devices-frx.md)
### Solucionar problemas
#### [Dispositivos híbridos do Windows 10 e Windows Server 2016 adicionados ao Azure AD](device-management-troubleshoot-hybrid-join-windows-current.md)
#### [Dispositivos híbridos do Windows de herança adicionados ao Azure AD](device-management-troubleshoot-hybrid-join-windows-legacy.md)

## Gerenciar aplicativos
### [Visão geral](manage-apps/what-is-application-management.md)
### [Guia de Introdução](manage-apps/plan-an-application-integration.md)
### [Tutoriais de integração de aplicativos SaaS](active-directory-saas-tutorial-list.md)
### [Cloud App Discovery](manage-apps/cloud-app-discovery.md)
#### [Criar relatórios de instantâneo](manage-apps/cloud-app-discovery-create-snapshot-reports.md)
#### [Configurar relatório contínuo](https://docs.microsoft.com/cloud-app-security/discovery-docker)
#### [Usar um analisador de log personalizado](https://docs.microsoft.com/cloud-app-security/custom-log-parser)

### [Provisionar e desprovisionar usuário para aplicativos SaaS](active-directory-saas-app-provisioning.md) 
#### [Tutoriais de integração do aplicativo](active-directory-saas-tutorial-list.md) 
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
#### [Atribuir usuários](manage-apps/assign-user-or-group-access-portal.md)
#### [Personalizar identidade visual](manage-apps/change-name-or-logo-portal.md)
#### [Desabilitar entradas de usuário](manage-apps/disable-user-sign-in-portal.md)
#### [Remover usuários](manage-apps/remove-user-or-group-access-portal.md)
#### [Exibir todos os meus aplicativos](manage-apps/view-applications-portal.md)
#### [Gerenciar o provisionamento de conta de usuário](manage-apps/configure-automatic-user-provisioning-portal.md)
#### [Gerenciar logon único para aplicativos corporativos](manage-apps/configure-single-sign-on-portal.md)
#### [Assinatura de certificados avançada para aplicativos SAML](manage-apps/certificate-signing-options.md)
#### [Como remover um aplicativo da experiência de um usuário](manage-apps/hide-application-from-user-portal.md)
### [Configurar a aceleração automática da entrada usando a Política de HRD](manage-apps/configure-authentication-for-federated-users-portal.md)
### [Migrar aplicativos AD FS para Azure AD](migrate-adfs-apps-to-azure.md) 
### [Gerenciar o acesso aos aplicativos](manage-apps/what-is-access-management.md)
#### [Acesso SSO](manage-apps/what-is-single-sign-on.md)
#### [Certificados para SSO](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [Restrições de locatário](manage-apps/tenant-restrictions.md)
#### [Usar usuários de provisionamento de SCIM](manage-apps/use-scim-to-provision-users-and-groups.md)

### [Solucionar problemas](active-directory-application-troubleshoot-content-map.md)
#### [Desenvolvimento de Aplicativos](active-directory-application-dev-troubleshoot-content-map.md)
##### [Configuração e Registro](active-directory-application-dev-config-content-map.md)
##### [Desenvolvimento](active-directory-application-dev-development-content-map.md)
#### [Gerenciamento de Aplicativos](active-directory-application-management-troubleshoot-content-map.md)
##### [Configuração](active-directory-application-config-content-map.md)
##### [Entrar](active-directory-application-sign-in-content-map.md)
##### [Provisionamento](active-directory-application-provisioning-content-map.md)

###### [Verificar um usuário provisionado](application-provisioning-when-will-provisioning-finish-specific-user.md) 
###### [Provisionamento muito demorado](application-provisioning-when-will-provisioning-finish.md) 
###### [Como configurar provisionamento do usuário](application-provisioning-config-how-to.md) 
###### [Problema na configuração do provisionamento](application-provisioning-config-problem.md) 
###### [Problema ao salvar credenciais do administrador](application-provisioning-config-problem-storage-limit.md) 
###### [Nenhum usuário sendo provisionado](application-provisioning-config-problem-no-users-provisioned.md) 
###### [Usuário errados sendo provisionados](application-provisioning-config-problem-wrong-users-provisioned.md) 

##### [Gerenciamento do Acesso](active-directory-application-access-content-map.md)
##### [Painel de Acesso](active-directory-application-access-panel-content-map.md)
##### [Proxy de Aplicativo](active-directory-application-proxy-content-map.md)
##### [Acesso condicional](active-directory-application-conditional-access-content-map.md)
### [Desenvolver aplicativos](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Biblioteca de documentos](active-directory-apps-index.md)

## Gerenciar seu diretório
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Nomes de domínio personalizados
#### [Início rápido](fundamentals/add-custom-domain.md)
#### [Adicionar nomes de domínio personalizados](active-directory-domains-manage-azure-portal.md)
### [Administrar seu diretório](fundamentals/active-directory-administer.md)
### [Excluir um diretório](directory-delete-howto.md)
### [Vários diretórios](active-directory-licensing-directory-independence.md)
### [Inscrição por autoatendimento](active-directory-self-service-signup.md)
### [Assumir um diretório não gerenciado](domains-admin-takeover.md)
### [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Habilitar](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Configurações de política de grupo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Configurações do Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Perguntas frequentes](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Solucionar problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Integrar identidades locais usando o Azure AD Connect](./connect/active-directory-aadconnect.md)

## Delegar acesso aos recursos
### [Funções de administrador](active-directory-assign-admin-roles-azure-portal.md)
#### [Atribuir função de administrador a um usuário](fundamentals/active-directory-users-assign-role-azure-portal.md) 
#### [Comparar permissões de usuários membro e convidado](fundamentals/users-default-permissions.md) 
### [Protegendo o acesso privilegiado](admin-roles-best-practices.md)  
### [Criar contas administrativas de acesso de emergência](active-directory-admin-manage-emergency-access-accounts.md) 


#### [Permissões de usuário padrão](fundamentals/users-default-permissions.md)
### [Unidades administrativas](active-directory-administrative-units-management.md)
### [Configurar tempo de vida de tokens](active-directory-configurable-token-lifetimes.md)
### [Protegendo funções privilegiadas](admin-roles-best-practices.md)

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
#### Tutoriais
##### [Migrar política MFA clássica](active-directory-conditional-access-migration-mfa.md)
#### Conceitos
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
### [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)

## Integrar outros serviços no Azure AD 
### [Integrar LinkedIn no Azure AD](linkedin-integration.md)

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
## [Restrições e limites de serviço](active-directory-service-limits-restrictions.md)

# Relacionados
## [Autenticação Multifator](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure AD para desenvolvedores](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# Recursos
## [Fórum de comentários do Azure](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Roteiro do Azure](https://azure.microsoft.com/roadmap/?category=security-identity)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Preços](https://azure.microsoft.com/pricing/details/active-directory/)
## [Calculadora de preço](https://azure.microsoft.com/pricing/calculator/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
