# Visão geral
## [O que é o Azure Stack?](azure-stack-poc.md)
## [Novidades](azure-stack-whats-new.md)
## [Principais recursos e conceitos](azure-stack-key-features.md)
## [Arquitetura do POC](azure-stack-architecture.md)

# Avaliar e implantar
## Introdução
### [Pré-requisitos de implantação](azure-stack-deploy.md)
### [Implantar](azure-stack-run-powershell-script.md)
### [Registrar](azure-stack-register.md)
## Como
### [Conectar-se ao Azure Stack POC](azure-stack-connect-azure-stack.md)
### [Adicionar imagem padrão](azure-stack-add-default-image.md)
### [Provisionar uma máquina virtual](azure-stack-provision-vm.md)
### [Criar uma conta de armazenamento](azure-stack-provision-storage-account.md)
### [Adicionar um locatário do Azure Stack](azure-stack-add-new-user-aad.md)
### [Reimplantar o Azure Stack POC](azure-stack-redeploy.md)

# Gerenciar
## Visão geral
### [Gerenciamento da região](azure-stack-region-management.md)
### [Usando os portais](azure-stack-manage-portals.md)
## Introdução
### [Conectar-se ao Azure Stack](azure-stack-connect-azure-stack.md)
### Configurar ambiente de gerenciamento
#### [Instalar o PowerShell](azure-stack-powershell-install.md)
#### [Baixar ferramentas](azure-stack-powershell-download.md)
#### [Configurar o PowerShell](azure-stack-powershell-configure.md)
## Como
### [Gerenciar atualizações](azure-stack-updates.md)
### [Monitorar integridade e alertas](azure-stack-monitor-health.md)
### [Gerenciar recursos de rede](azure-stack-viewing-public-ip-address-consumption-in-tp2.md)
### [Gerenciar recursos de armazenamento](azure-stack-manage-storage-accounts.md)
### [Gerenciar VMs do Microsoft Azure Pack](azure-stack-manage-windows-azure-pack.md)

# Segurança e conformidade
## Como
### [Gerenciar permissões de usuário](azure-stack-manage-permissions.md)
### [Adicionar usuários do AD FS](azure-stack-add-users-adfs.md)

# Oferecer serviços
## Introdução
### Criar planos, ofertas e cotas
#### [Definir cotas](azure-stack-setting-quotas.md)
#### [Criar um plano](azure-stack-create-plan.md)
#### [Criar uma oferta](azure-stack-create-offer.md)
#### [Assinar uma oferta](azure-stack-subscribe-plan-provision-vm.md)
#### [Delegar ofertas no Azure Stack](azure-stack-delegated-provider.md)
## Como
### Oferta do SQL ou MySQL como PaaS
#### [Implantar provedor de recursos do MySQL](azure-stack-mysql-resource-provider-deploy.md)
#### [Implantar provedor de recursos do SQL](azure-stack-sql-resource-provider-deploy.md)
### Oferta de Serviço de Aplicativo como PaaS
#### [Serviço de aplicativo na visão geral do Azure Stack](azure-stack-app-service-overview.md)
#### [Antes de começar](azure-stack-app-service-before-you-get-started.md)
#### [Implantar provedor de recursos do Serviço de Aplicativo](azure-stack-app-service-deploy.md)
#### [Adicionar mais funções de trabalho da Web](azure-stack-app-service-add-worker-roles.md)
#### [Configurar fontes de implantação](azure-stack-app-service-configure-deployment-sources.md)
#### [Habilitar o FTP no Serviço de Aplicativo no Azure Stack](azure-stack-app-service-enable-ftp.md)
### Preencher o Marketplace
#### [Visão geral do Marketplace](azure-stack-marketplace.md)
#### [Baixar itens do Marketplace](azure-stack-download-azure-marketplace-item.md)
#### [Criar e publicar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md)
#### [Adicionar uma imagem de máquina virtual personalizada](azure-stack-add-vm-image.md)
#### [Implantar máquinas virtuais do Linux](azure-stack-linux.md)
### Cobrança e estorno
#### [Visão geral sobre cobrança e estorno](azure-stack-billing-and-chargeback.md)
#### [API de uso de recurso de provedor](azure-stack-provider-resource-api.md)
#### [API de uso de recurso de locatário](azure-stack-tenant-resource-usage-api.md)
#### [Perguntas frequentes sobre o uso](azure-stack-usage-related-faq.md)

# Usar os serviços
## Computação
### [Adicionar imagem da VM](azure-stack-add-vm-image.md)
### [Usar convidados com Linux](azure-stack-linux.md)
## Armazenamento
### [Visão geral](azure-stack-storage-overview.md)
### [Diferenças e considerações](azure-stack-acs-differences-tp2.md)
## Rede
### [iDNS para o Azure Stack](azure-stack-understanding-dns-in-tp2.md)
### [DNS no Azure Stack](azure-stack-dns.md)
### [Noções básicas sobre conexões VPN site a site](azure-stack-create-vpn-connection-one-node-tp2.md)
## Cofre de Chaves
### [Visão geral](azure-stack-kv-intro.md)
### Introdução
#### [Gerenciar com o portal](azure-stack-kv-manage-portal.md)
#### [Gerenciar com o PowerShell](azure-stack-kv-manage-powershell.md)
### Como
#### [Criar uma VM com um certificado](azure-stack-kv-push-secret-into-vm.md)
#### [Aplicativo de exemplo do Cofre de Chaves](azure-stack-kv-sample-app.md)

# Criar aplicativos
## Visão geral
### [Desenvolver para o Azure Stack](azure-stack-developer.md)
## Introdução
### [Conectar-se ao Azure Stack](azure-stack-connect-azure-stack.md)
### Configure o ambiente de desenvolvimento
#### [Instalar o PowerShell](azure-stack-powershell-install.md)
#### [Baixar ferramentas](azure-stack-powershell-download.md)
#### [Configurar o PowerShell](azure-stack-powershell-configure.md)
#### [Instalar Visual Studio](azure-stack-install-visual-studio.md)
## Como
### [Usar o módulo de política](azure-stack-policy-module.md)
### Usar modelos
#### [Visão geral de modelo](azure-stack-arm-templates.md)
#### [Desenvolver modelos](azure-stack-develop-templates.md)
#### [Verificar modelos](azure-stack-validate-templates.md)
#### [Implantar com o portal](azure-stack-deploy-template-portal.md)
#### [Implantar com o PowerShell](azure-stack-deploy-template-powershell.md)
#### [Implantar com o Visual Studio](azure-stack-deploy-template-visual-studio.md)

# Solucionar problemas
## [Problemas conhecidos](azure-stack-troubleshooting.md)
## [Diagnósticos no Azure Stack](azure-stack-diagnostics.md)

# Recursos
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStack)  
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-stack)

