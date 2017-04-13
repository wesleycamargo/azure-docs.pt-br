# Visão geral
## [O que é o Azure AD Connect?](active-directory-aadconnect.md)
## [O que é a Sincronização do Azure AD Connect?](active-directory-aadconnectsync-whatis.md)
### [Usuários e contatos](active-directory-aadconnectsync-understanding-users-and-contacts.md)
### [Arquitetura](active-directory-aadconnectsync-understanding-architecture.md)
### [Provisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md)
#### [Expressões de provisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
### [Configuração padrão](active-directory-aadconnectsync-understanding-default-configuration.md)
## [O que é o Azure AD Connect e Federação?](active-directory-aadconnectfed-whatis.md)


# Introdução
## [Pré-requisitos](active-directory-aadconnect-prerequisites.md)
## [Instalar o Azure AD Connect](active-directory-aadconnect-select-installation.md)
### [Configurações Expressas](active-directory-aadconnect-get-started-express.md)
### [Configurações personalizadas](active-directory-aadconnect-get-started-custom.md)
### [Atualização do DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
### [Atualizar de uma versão anterior](active-directory-aadconnect-upgrade-previous-version.md)


# Como
## Planejar e projetar
### [Conceitos de design](active-directory-aadconnect-design-concepts.md)
### [Topologias para o Azure AD Connect](active-directory-aadconnect-topologies.md)
### [Logon único](active-directory-aadconnect-sso.md)
### [Suporte para os Serviços de Federação do Active Directory no Azure](active-directory-aadconnect-azure-adfs.md)
### [Considerações especiais para instâncias](active-directory-aadconnect-instances.md)
### [Você já tem o Azure AD](active-directory-aadconnect-existing-tenant.md)
## [Gerenciar o Azure AD Connect](active-directory-aadconnect-whats-next.md)
### [Renovar certificados para O365 e Azure AD](active-directory-aadconnect-o365-certs.md)
### [Habilitar write-back de dispositivo](active-directory-aadconnect-feature-device-writeback.md)
### [Opções de entrada do usuário](active-directory-aadconnect-user-signin.md)
### [Suporte a vários domínios para federação](active-directory-aadconnect-multiple-domains.md)
### [Atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md)



## Gerenciar a sincronização do Azure AD Connect
### [Impedir exclusões acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
### [Sincronização de senha](active-directory-aadconnectsync-implement-password-synchronization.md)
### [Autenticação de Passagem](active-directory-aadconnect-pass-through-authentication.md)
### [Conta de serviço do Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md)
### [Assistente de instalação](active-directory-aadconnectsync-installation-wizard.md)
### [Alterar a configuração padrão](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)
### [Configurar a filtragem](active-directory-aadconnectsync-configure-filtering.md)
### [Agendador](active-directory-aadconnectsync-feature-scheduler.md)
### [Extensões de diretório](active-directory-aadconnectsync-feature-directory-extensions.md)

### [Alteração da senha da conta de serviço do Azure AD Sync](active-directory-aadconnectsync-change-serviceacct-pass.md)
### [Alterando a senha da conta do AD DS](active-directory-aadconnectsync-change-addsacct-pass.md)
### [Habilitar a lixeira do AD](active-directory-aadconnectsync-recycle-bin.md)

### [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)
#### [Operações](active-directory-aadconnectsync-service-manager-ui-operations.md)
#### [Conectores](active-directory-aadconnectsync-service-manager-ui-connectors.md)
#### [Designer de metaverso](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)
#### [Pesquisa de metaverso](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)


## Gerenciar Serviços de Federação
### [Gerenciar e personalizar](active-directory-aadconnect-federation-management.md)
### [Federar várias instâncias do Azure AD com uma instância única do AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)


## Solucionar problemas
### [Conectividade](active-directory-aadconnect-troubleshoot-connectivity.md)
### [Erros durante a sincronização](active-directory-aadconnect-troubleshoot-sync-errors.md)
### [O objeto não está sincronizado](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)
### [Sincronização de senha](active-directory-aadconnectsync-troubleshoot-password-synchronization.md)
### [Como se recuperar de um limite de 10 GB LocalDB](active-directory-aadconnect-recover-from-localdb-10gb-limit.md)

# Referência
## [Sincronização de identidades e resiliência do atributo duplicada](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
## [Portas e Protocolos Requeridos para Identidade Híbrida](active-directory-aadconnect-ports.md)
## [Recursos na visualização](active-directory-aadconnect-feature-preview.md)
## [Histórico de versão](active-directory-aadconnect-version-history.md)
## [Contas e permissões](active-directory-aadconnect-accounts-permissions.md)

## Sincronização do Azure AD Connect
### [Atributos sincronizados com o Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md)
### [Histórico de lançamento de versão do conector](active-directory-aadconnectsync-connector-version-history.md)
### [Referência de funções](active-directory-aadconnectsync-functions-reference.md)
### [Considerações e tarefas operacionais](active-directory-aadconnectsync-operations.md)
### [Lista de compatibilidade de federação do AD do Azure](active-directory-aadconnect-federation-compatibility.md)
### [Conceitos técnicos](active-directory-aadconnectsync-technical-concepts.md)
### [Recursos de serviço](active-directory-aadconnectsyncservice-features.md)




# Relacionados
## [Monitorar infraestrutura de identidade local e serviços de sincronização na nuvem](../connect-health/active-directory-aadconnect-health.md)
## [Guia de design de identidade híbrida](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/)


# Recursos
##[Perguntas frequentes do Azure AD Connect](active-directory-aadconnect-faq.md)
##[Reprovação do DirSync](active-directory-aadconnect-dirsync-deprecated.md)
