# Visão geral
## [O que é o Azure Active Directory?](active-directory-whatis.md)
## [Escolher edição](active-directory-editions.md)
## [Sobre o gerenciamento de identidade do Azure](identity-fundamentals.md)
## [Entender as soluções de identidade do Azure](understand-azure-identity-solutions.md)
## [Visualizar a experiência do Portal do Azure](active-directory-preview-explainer.md)

# Introdução
## [Obter um locatário do AD do Azure](active-directory-howto-tenant.md)
## [Inscrever-se no Azure AD Premium](active-directory-get-started-premium.md)
## [Gerenciar assinaturas do Azure](active-directory-how-subscriptions-associated-directory.md)
## Gerenciar o licenciamento do Azure AD
### [Portal do Azure](active-directory-licensing-get-started-azure-portal.md)
### [Portal clássico](active-directory-licensing-what-is.md)
## [Obter o Azure para sua organização](sign-up-organization.md)
## [Perguntas frequentes](active-directory-faq.md)
## [Tutoriais de aplicativo SaaS](active-directory-saas-tutorial-list.md)

# Como
## Planejar e projetar
### [Compreenda a arquitetura do Azure AD](active-directory-architecture.md)
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
### Adicionar usuários
#### [Portal do Azure](active-directory-users-create-azure-portal.md)
#### [Portal clássico](active-directory-create-users.md)

### [Adicionar usuários de outros diretórios (portal clássico)](active-directory-create-users-external.md)
### [Excluir usuários](active-directory-users-delete-user-azure-portal.md)
### [Gerenciar perfis de usuário](active-directory-users-profile-azure-portal.md)
### [Redefinir uma senha](active-directory-users-reset-password-azure-portal.md)
### [Gerenciar informações de trabalho do usuário](active-directory-users-work-info-azure-portal.md)
### [Compartilhar contas](active-directory-sharing-accounts.md)

## [Gerenciar grupos e membros](active-directory-manage-groups.md)
### Gerenciar grupos
#### [portal do Azure](active-directory-groups-create-azure-portal.md)
#### [Portal clássico](active-directory-accessmanagement-manage-groups.md)
#### [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
### [Gerenciar membros do grupo](active-directory-groups-members-azure-portal.md)
### [Gerenciar proprietários do grupo](active-directory-accessmanagement-managing-group-owners.md)
### [Gerenciar associação ao grupo](active-directory-groups-membership-azure-portal.md)
### [Exibir todos os grupos](active-directory-groups-view-azure-portal.md)
### [Habilitar grupos dedicados](active-directory-accessmanagement-dedicated-groups.md)
### [Adicionar grupo de acesso a aplicativos SaaS](active-directory-accessmanagement-group-saasapps.md)
### [Restaurar um grupo excluído do Office 365](active-directory-groups-restore-azure-portal.md)
### Gerenciar configurações de grupo
#### [Portal do Azure](active-directory-groups-settings-azure-portal.md)
#### [Cmdlets](active-directory-accessmanagement-groups-settings-cmdlets.md)
### Criar regras avançadas
#### [Portal do Azure](active-directory-groups-dynamic-membership-azure-portal.md)
#### [Portal clássico](active-directory-accessmanagement-groups-with-advanced-rules.md)
### [Licenciamento baseado em grupo](active-directory-licensing-whatis-azure-portal.md)
#### [Atribuir licenças a um grupo](active-directory-licensing-group-assignment-azure-portal.md)
#### [Identificar e resolver problemas de licença para um grupo](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Migrar usuários individuais licenciados para licenciamento baseado em grupo](active-directory-licensing-group-migration-azure-portal.md)
#### [Cenários adicionais para licenciamento baseado em grupos](active-directory-licensing-group-advanced.md)
#### [Exemplos do PowerShell para licenciamento baseado em grupo](active-directory-licensing-ps-examples.md)
### [Configurar grupos de autoatendimento](active-directory-accessmanagement-self-service-group-management.md)
### [Solucionar problemas](active-directory-accessmanagement-troubleshooting.md)

## [Gerenciar relatórios](active-directory-reporting-azure-portal.md)
### [Atividade de entrada](active-directory-reporting-activity-sign-ins.md)
### [Atividade de auditoria](active-directory-reporting-activity-audit-logs.md)
### [Usuários em risco](active-directory-reporting-security-user-at-risk.md)
### [Entradas de risco](active-directory-reporting-security-risky-sign-ins.md)
### [Eventos de risco](active-directory-reporting-risk-events.md)
### [Locais nomeados](active-directory-named-locations.md)
### [Relatório de migração](active-directory-reporting-migration.md)
### [Retenção](active-directory-reporting-retention.md)
### [Latências](active-directory-reporting-latencies-azure-portal.md)
### [Perguntas frequentes](active-directory-reporting-faq.md)
### Solucionar problemas
#### [Dados de auditoria ausentes](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Dados ausentes nos downloads](active-directory-reporting-troubleshoot-missing-data-download.md)
###    [Acesso Programático](active-directory-reporting-api-getting-started-azure-portal.md)
#### [Referência de auditoria](active-directory-reporting-api-audit-reference.md)
#### [Referência de entrada](active-directory-reporting-api-sign-in-activity-reference.md)
#### [Pré-requisitos](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [Exemplos de auditoria](active-directory-reporting-api-audit-samples.md)
#### [Exemplos de entrada](active-directory-reporting-api-sign-in-activity-samples.md)
#### [Obter dados](active-directory-reporting-api-with-certificates.md)
### [Portal clássico](active-directory-view-access-usage-reports.md)
#### [Relatórios do Azure AD](active-directory-reporting-getting-started.md)
#### [Guia de relatórios](active-directory-reporting-guide.md)
#### [Redes conhecidas](active-directory-known-networks.md)
#### [API](active-directory-reporting-api-getting-started.md)
##### [Pré-requisitos](active-directory-reporting-api-prerequisites.md)
#### [Eventos de auditoria](active-directory-reporting-audit-events.md)
#### [Latências](active-directory-reporting-latencies.md)
#### [Notificações](active-directory-reporting-notifications.md)
#### Entenda os relatórios
##### [Entrada irregular](active-directory-reporting-irregular-sign-in-activity.md)
##### [Várias falhas](active-directory-reporting-sign-ins-after-multiple-failures.md)
##### [Entradas de endereços IP suspeitos](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
##### [Várias regiões geográficas](active-directory-reporting-sign-ins-from-multiple-geographies.md)
##### [Dispositivos possivelmente infectados](active-directory-reporting-sign-ins-from-possibly-infected-devices.md)
##### [Fontes desconhecidas](active-directory-reporting-sign-ins-from-unknown-sources.md)
##### [Entradas anômalas](active-directory-reporting-users-with-anomalous-sign-in-activity.md)

## [Gerenciar senhas](active-directory-passwords-overview.md)
### Documentos do usuário
#### [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md)
#### [Práticas recomendadas de senha](active-directory-secure-passwords.md)
#### [Registro de redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md)
### [Início rápido: redefinição de senha por autoatendimento](active-directory-passwords-getting-started.md)
### [Licença SSPR](active-directory-passwords-licensing.md)
### [Implantar SSPR](active-directory-passwords-best-practices.md)
### Administradores de TI: redefinir senhas
#### [Portal do Azure](active-directory-users-reset-password-azure-portal.md)
#### [Portal clássico do Azure](active-directory-create-users-reset-password.md)
### [Entender as políticas SSPR](active-directory-passwords-policy.md)
### [Compreender a redefinição de senha](active-directory-passwords-how-it-works.md)
### [Personalizar SSPR](active-directory-passwords-customize.md)
### [Dados usados pelo SSPR](active-directory-passwords-data.md)
### [Relatando no SSPR](active-directory-passwords-reporting.md)
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### [Write-back de senha](active-directory-passwords-writeback.md)
### [Sincronização de hash de senha](./connect/active-directory-aadconnectsync-implement-password-synchronization.md#how-password-synchronization-works)
### [Solucionar problemas](active-directory-passwords-troubleshoot.md)
### [PERGUNTAS FREQUENTES](active-directory-passwords-faq.md)


## Gerenciar dispositivos
### [Registrar dispositivos](active-directory-device-registration-overview.md)
#### [Configuração](active-directory-conditional-access-automatic-device-registration-setup.md)
#### [Implantar no local](active-directory-device-registration-on-premises-setup.md)
#### [Perguntas frequentes](active-directory-device-registration-faq.md)
#### Solucionar problemas
##### [Solução de problemas do Windows 10 e Windows Server 2016](active-directory-device-registration-troubleshoot-windows.md)
##### [Solução de problemas para clientes de nível inferior do Windows](active-directory-device-registration-troubleshoot-windows-legacy.md)
### [Ingresso no Azure AD](active-directory-azureadjoin-overview.md)
#### [Plano](active-directory-azureadjoin-deployment-aadjoindirect.md)
#### [Configure o registro de dispositivos](active-directory-azureadjoin-setup.md)
#### [Registrar novos dispositivos](active-directory-azureadjoin-user-frx.md)
#### [Implantar](active-directory-azureadjoin-devices-group-policy.md)
#### [Compreender a integração do Windows 10](active-directory-azureadjoin-windows10-devices-overview.md)
#### [Usar dispositivos com o Windows 10](active-directory-azureadjoin-windows10-devices.md)
#### [Associar seu dispositivo](active-directory-azureadjoin-personal-device.md)
#### [Associar um dispositivo com Windows 10](active-directory-azureadjoin-user-upgrade.md)

## Gerenciar aplicativos
### [Visão geral](active-directory-enable-sso-scenario.md)
### [Guia de Introdução](active-directory-integrating-applications-getting-started.md)

### [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)
#### [Atualizar configurações do Registro](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
#### [Compreender a segurança e a privacidade](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)

### [Acesse aplicativos remotamente com o Proxy de aplicativo](active-directory-application-proxy-get-started.md)
#### Introdução
##### [Habilitar Proxy de aplicativo](active-directory-application-proxy-enable.md)
##### [Publicar aplicativos](application-proxy-publish-azure-portal.md)
##### [Domínios personalizados](active-directory-application-proxy-custom-domains.md)
#### [Acesso SSO](active-directory-appssoaccess-whatis.md)
##### [SSO com KCD](active-directory-application-proxy-sso-using-kcd.md)
##### [SSO com cabeçalhos](application-proxy-ping-access.md)
##### [SSO com compartimentação de senha](application-proxy-sso-azure-portal.md)
#### Conceitos
##### [Conectores](application-proxy-understand-connectors.md)
##### [Segurança](application-proxy-security-considerations.md)
##### [Redes](application-proxy-network-topology-considerations.md)


##### [Atualização do TMG ou UAG](application-proxy-transition-from-uag-tmg.md)

#### Configurações avançadas
##### [Publicar em redes separadas](active-directory-application-proxy-connectors-azure-portal.md)
##### [Servidores proxy](application-proxy-working-with-proxy-servers.md)
##### [Aplicativos com reconhecimento de declarações](active-directory-application-proxy-claims-aware-apps.md)
##### [Aplicativos cliente nativos](active-directory-application-proxy-native-client.md)
##### [Instalação silenciosa](active-directory-application-proxy-silent-installation.md)
##### [Home page personalizada](application-proxy-office365-app-launcher.md)
#### Orientações de publicação
##### [Área de Trabalho Remota](application-proxy-publish-remote-desktop.md)
##### [SharePoint](application-proxy-enable-remote-access-sharepoint.md)
#### [Solucionar problemas](active-directory-application-proxy-troubleshoot.md)
#### Usar o portal clássico
##### [Baixar conectores](application-proxy-enable-classic-portal.md)
##### [Publicar aplicativos](active-directory-application-proxy-publish.md)
##### [Usar conectores](active-directory-application-proxy-connectors.md)
##### [Acesso condicional](active-directory-application-proxy-conditional-access.md)

### Gerenciar aplicativos empresariais
#### [Atribuir usuários](active-directory-coreapps-assign-user-azure-portal.md)
#### [Personalizar identidade visual](active-directory-coreapps-change-app-logo-user-azure-portal.md)
#### [Desabilitar entradas de usuário](active-directory-coreapps-disable-app-azure-portal.md)
#### [Remover usuários](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [Exibir todos os meus aplicativos](active-directory-coreapps-view-azure-portal.md)
#### [Gerenciar o provisionamento de conta de usuário](active-directory-enterprise-apps-manage-provisioning.md)

### [Gerenciar o acesso aos aplicativos](active-directory-managing-access-to-apps.md)
#### [Acesso de autoatendimento](active-directory-self-service-application-access.md)
#### [Certificados para SSO](active-directory-sso-certs.md)
#### [Restrições de locatário](active-directory-tenant-restrictions.md)
#### [Usar usuários de provisionamento de SCIM](active-directory-scim-provisioning.md)

### [Solucionar problemas](active-directory-application-troubleshoot-content-map.md)
#### [Desenvolvimento de Aplicativos](active-directory-application-dev-troubleshoot-content-map.md)
##### [Configuração e Registro](active-directory-application-dev-config-content-map.md)
##### [Desenvolvimento](active-directory-application-dev-development-content-map.md)
#### [Gerenciamento de Aplicativos](active-directory-application-management-troubleshoot-content-map.md)
##### [Configuração](active-directory-application-config-content-map.md)
##### [Entrar](active-directory-application-sign-in-content-map.md)
##### [Provisionamento](active-directory-application-provisioning-content-map.md)
##### [Gerenciamento do Acesso](active-directory-application-access-content-map.md)
##### [Painel de Acesso](active-directory-application-access-panel-content-map.md)
##### [Proxy de Aplicativo](active-directory-application-proxy-content-map.md)
##### [Acesso condicional](active-directory-application-conditional-access-content-map.md)
### [Desenvolver aplicativos](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Biblioteca de documentos](active-directory-apps-index.md)

## Gerenciar seu diretório
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Nomes de domínio personalizados
#### [Visão geral](active-directory-add-domain-concepts.md)
#### Adicionar seu nome de domínio
##### [Portal do Azure](active-directory-domains-add-azure-portal.md)
##### [Portal clássico](active-directory-add-domain.md)
##### [Com o AD FS](active-directory-add-domain-federated.md)
#### [Atribuir usuários](active-directory-add-domain-add-users.md)
#### Gerenciar nomes de domínio
##### [Portal do Azure](active-directory-domains-manage-azure-portal.md)
##### [Portal clássico](active-directory-add-manage-domain-names.md)
### Personalizar a página de entrada
#### [Portal do Azure](active-directory-branding-custom-signon-azure-portal.md)
#### [Específico do idioma](active-directory-branding-localize-azure-portal.md)
#### [Portal clássico](active-directory-add-company-branding.md)
### [Administrar seu diretório](active-directory-administer.md)
### [Vários diretórios](active-directory-licensing-directory-independence.md)
### [Diretórios de O365](active-directory-manage-o365-subscription.md)
### [Inscrição por autoatendimento](active-directory-self-service-signup.md)
### [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Habilitar](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Configurações de política de grupo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Configurações do Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Perguntas frequentes](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Solucionar problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

### [Integrar parceiros com o Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md)
#### [Admins adicionando usuários B2B](active-directory-b2b-admin-add-users.md)
#### [Trabalhos de informação adicionando usuários B2B](active-directory-b2b-iw-add-users.md)
#### [API e personalização](active-directory-b2b-api.md)
#### [Código e exemplos do PowerShell](active-directory-b2b-code-samples.md)
#### [Exemplo de portal para iniciar sessão com autoatendimento](active-directory-b2b-self-service-portal.md)
#### [Email de convite](active-directory-b2b-invitation-email.md)
#### [Resgate do convite](active-directory-b2b-redemption-experience.md)
#### [Acesso condicional para B2B](active-directory-b2b-mfa-instructions.md)
#### [Políticas de compartilhamento B2B](active-directory-b2b-delegate-invitations.md)
#### [Adicionar um usuário B2B a uma função](active-directory-b2b-add-guest-to-role.md)
#### [Grupos dinâmicos e usuários B2B](active-directory-b2b-dynamic-groups.md)
#### [Auditoria e relatórios](active-directory-b2b-auditing-and-reporting.md)
#### [B2B e compartilhamento externo do Office 365](active-directory-b2b-o365-external-user.md)
#### [Licenciamento](active-directory-b2b-licensing.md)
#### [Limitações atuais](active-directory-b2b-current-limitations.md)
#### [Perguntas frequentes](active-directory-b2b-faq.md)
#### [Solucionando problemas do B2B](active-directory-b2b-troubleshooting.md)
#### [Entender o usuário B2B](active-directory-b2b-user-properties.md)
#### [Token do usuário B2B](active-directory-b2b-user-token.md)
#### [B2B para aplicativos integrados do Azure AD](active-directory-b2b-configure-saas-apps.md)
#### [Mapeamento de declarações do usuário B2B](active-directory-b2b-claims-mapping.md)
#### [Comparar a colaboração B2B com B2C](active-directory-b2b-compare-b2c.md)
#### [Obtendo suporte para B2B](active-directory-b2b-support.md)

### [Integrar identidades locais usando o Azure AD Connect](./connect/active-directory-aadconnect.md)

## Delegar acesso aos recursos
### [Funções de administrador](active-directory-assign-admin-roles.md)
#### [Atribuir funções de administrador](active-directory-users-assign-role-azure-portal.md)
### [Unidades administrativas](active-directory-administrative-units-management.md)
### [Acesso a recursos no Azure](active-directory-understanding-resource-access.md)
### [Controle de acesso baseado em função](role-based-access-control-what-is.md)
#### Gerenciar atribuições de acesso
##### [Por usuário](role-based-access-control-manage-assignments.md)
##### [Por recurso](role-based-access-control-configure.md)
#### [Funções internas](role-based-access-built-in-roles.md)
#### [Funções personalizadas](role-based-access-control-custom-roles.md)
#### [Atribuir funções personalizadas para usuários internos e externos](role-based-access-control-create-custom-roles-for-internal-external-users.md)
#### [Relatórios](role-based-access-control-access-change-history-report.md)
#### Mais maneiras de gerenciar funções
##### [CLI do Azure](role-based-access-control-manage-access-azure-cli.md)
##### [PowerShell](role-based-access-control-manage-access-powershell.md)
##### [REST](role-based-access-control-manage-access-rest.md)
#### [Elevar o acesso de administrador de locatário](role-based-access-control-tenant-admin-access.md)
#### [Solucionar problemas](role-based-access-control-troubleshooting.md)
#### [Operações de provedor de recursos](role-based-access-control-resource-provider-operations.md)
### [Configurar tempo de vida de tokens](active-directory-configurable-token-lifetimes.md)

## Proteger suas identidades
### [Acesso condicional](active-directory-conditional-access-azure-portal.md)
#### [Introdução](active-directory-conditional-access-azure-portal-get-started.md)
#### [práticas recomendadas](active-directory-conditional-access-best-practices.md)
#### [Referência técnica](active-directory-conditional-access-technical-reference.md)
#### [Aplicativos com suporte](active-directory-conditional-access-supported-apps.md)
#### [Entender as políticas de dispositivo](active-directory-conditional-access-device-policies.md)
#### [Configurar o acesso a aplicativos conectados](active-directory-conditional-access-policy-connected-applications.md)
#### [Correção](active-directory-conditional-access-device-remediation.md)
#### [Perguntas frequentes](active-directory-conditional-faqs.md)
#### [Portal clássico](active-directory-conditional-access.md)
##### [Introdução](active-directory-conditional-access-azuread-connected-apps.md)


### Windows Hello
#### [Autenticar sem senhas](active-directory-azureadjoin-passport.md)
#### [Habilitar o Windows Hello for Business](active-directory-azureadjoin-passport-deployment.md)
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
### [Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

## [Implante o AD DS em máquinas virtuais do Azure](virtual-networks-windows-server-active-directory-virtual-machines.md)
### [Windows Server Active Directory nas VMs do Azure](active-directory-deploying-ws-ad-guidelines.md)
### [Controlador de domínio de réplica em uma rede virtual do Azure](active-directory-install-replica-active-directory-domain-controller.md)
### [Nova floresta em uma rede virtual do Azure](active-directory-new-forest-virtual-machine.md)



## [Implantar o AD FS no Azure](active-directory-aadconnect-azure-adfs.md)
### [Alta disponibilidade](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Alterar o algoritmo de hash da assinatura](active-directory-federation-sha256-guidance.md)

## [Solucionar problemas](active-directory-troubleshooting.md)

## Implantar a Prova de conceito (PoC) do Azure AD
### [Manual de PoC: introdução](active-directory-playbook-intro.md)
### [Manual de PoC: ingredientes](active-directory-playbook-ingredients.md)
### [Manual de PoC: implementação](active-directory-playbook-implementation.md)
### [Manual de PoC: blocos de construção](active-directory-playbook-building-blocks.md)


# Referência
## [Cmdlets do PowerShell](/powershell/azure/overview)
## [Referência de API Java](/java/api)
## [API do .NET](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [Restrições e limites de serviço](active-directory-service-limits-restrictions.md)

# Relacionados
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure AD para desenvolvedores](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# Recursos
## [Preços](https://azure.microsoft.com/pricing/details/active-directory/)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=active-directory)
## [Fórum de comentários do Azure](https://feedback.azure.com/forums/169401-azure-active-directory)
