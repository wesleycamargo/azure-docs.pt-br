# Visão geral
## [O que é o Resource Manager?](resource-group-overview.md)
## [Provedores e tipos de recursos](resource-manager-supported-services.md)
## [Resource Manager e a implantação clássica](resource-manager-deployment-model.md)
## [Governança de assinatura](resource-manager-subscription-governance.md)

# Introdução
## [Criar e implantar modelo](resource-manager-create-first-template.md)
## [Extensão do Código VS para modelos](resource-manager-vscode-extension.md)
## [Visual Studio com o Gerenciador de Recursos](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# Como
## Criar modelos
### [Seções do modelo](resource-group-authoring-templates.md)
#### [Parâmetros](resource-manager-templates-parameters.md)
#### [Variáveis](resource-manager-templates-variables.md)
#### [Recursos](resource-manager-templates-resources.md)
### [Práticas recomendadas para modelos](resource-manager-template-best-practices.md)
### [Vincular a outros modelos](resource-group-linked-templates.md)
### [Definir dependência entre recursos](resource-group-define-dependencies.md)
### [Criar múltiplas instâncias](resource-group-create-multiple.md)
### [Definir local](resource-manager-template-location.md)
### [Atribuir marcas](resource-manager-template-tags.md)
### [Definir o nome e tipo do recurso filho](resource-manager-template-child-resource.md)
### [Atualizar recurso](resource-manager-update.md)
### [Usar objetos de parâmetros](resource-manager-objects-as-parameters.md)
### [Compartilhar estado entre modelos vinculados](best-practices-resource-manager-state.md)
### [Padrões para projetar modelos](best-practices-resource-manager-design-templates.md)


## Implantar
### Azure PowerShell
#### [Implantar modelo](resource-group-template-deploy.md)
#### [Implantar modelo particular com o token SAS](resource-manager-powershell-sas-token.md)
#### [Exportar modelo e reimplantar](resource-manager-export-template-powershell.md)
### CLI do Azure
#### [Implantar modelo](resource-group-template-deploy-cli.md)
#### [Implantar modelo particular com o token SAS](resource-manager-cli-sas-token.md)
#### [Exportar modelo e reimplantar](resource-manager-export-template-cli.md)
### Portal do Azure
#### [Implantar recursos](resource-group-template-deploy-portal.md)
#### [Exportar modelo](resource-manager-export-template.md)
### [API REST](resource-group-template-deploy-rest.md)
### [Vários grupos de recursos ou assinaturas](resource-manager-cross-resource-group-deployment.md)
### [Integração contínua com o Visual Studio Team Services](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Transmitir valores seguros durante a implantação](resource-manager-keyvault-parameter.md)

## Gerenciar
### [PowerShell do Azure](powershell-azure-resource-manager.md)
### [CLI do Azure](xplat-cli-azure-resource-manager.md)
### [Portal do Azure](resource-group-portal.md)
### [API REST](resource-manager-rest-api.md)
### [Usar marcações para organizar recursos](resource-group-using-tags.md)
### [Mover recursos para um novo grupo ou assinatura](resource-group-move-resources.md)
### [Organizar assinaturas com grupos de gerenciamento](../billing/billing-enterprise-mgmt-group-overview.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Exemplos de governança](resource-manager-subscription-examples.md)
### [Aplicativos gerenciados](../managed-applications/overview.md)

## Controlar Acesso
### Criar uma entidade de serviço
#### [PowerShell do Azure](resource-group-authenticate-service-principal.md)
#### [CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Portal do Azure](resource-group-create-service-principal-portal.md)
### [API de autenticação para assinaturas de acesso](resource-manager-api-authentication.md)
### [Bloquear recursos](resource-group-lock-resources.md)

## Audit
### [Exibir logs de atividade](resource-group-audit.md)
### [Exibir operações de implantação](resource-manager-deployment-operations.md)

## Solucionar problemas
### [Erros de implantação comuns](resource-manager-common-deployment-errors.md)
#### [AccountNameInvalid](resource-manager-storage-account-name-errors.md)
#### [InvalidTemplate](resource-manager-invalid-template-errors.md)
#### [Problemas de implantação do Linux](../virtual-machines/linux/troubleshoot-deploy-vm.md)
#### [NoRegisteredProviderFound](resource-manager-register-provider-errors.md)
#### [NotFound](resource-manager-not-found-errors.md)
#### [ParentResourceNotFound](resource-manager-parent-resource-errors.md)
#### [Problemas de provisionamento e alocação do Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)
#### [Problemas de provisionamento e alocação do Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)
#### [RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
#### [ReservedResourceName](resource-manager-reserved-resource-name.md)
#### [ResourceQuotaExceeded](resource-manager-quota-errors.md)
#### [SkuNotAvailable](resource-manager-sku-not-available-errors.md)
#### [Problemas de implantação do Windows](../virtual-machines/windows/troubleshoot-deploy-vm.md)
### [Compreenda os erros de implantação](resource-manager-troubleshoot-tips.md)

# Referência
## [Formato do modelo](/azure/templates/)
## [Funções de modelo](resource-group-template-functions.md)
### [Funções de objeto e matriz](resource-group-template-functions-array.md)
### [Funções de comparação](resource-group-template-functions-comparison.md)
### [Funções de implantação](resource-group-template-functions-deployment.md)
### [Funções lógicas](resource-group-template-functions-logical.md)
### [Funções numéricas](resource-group-template-functions-numeric.md)
### [Funções de recurso](resource-group-template-functions-resource.md)
### [Funções de cadeia de caracteres](resource-group-template-functions-string.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [CLI do Azure](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# Recursos
## [Roteiro do Azure](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [Calculadora de preço](https://azure.microsoft.com/pricing/calculator/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=azure-resource-manager)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [Solicitações de limitação](resource-manager-request-limits.md)
## [Rastrear operações assíncronas](resource-manager-async-operations.md)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
