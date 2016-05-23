<properties
	pageTitle="Migrar recursos de IaaS do Clássico para o Azure Resource Manager usando o Azure PowerShell"
	description="Este artigo apresenta as funcionalidades de serviço de migração com suporte da plataforma, do Gerenciamento de Serviços, para o Azure Resource Manager usando scripts do PowerShell"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Migrar recursos de IaaS do Clássico para o Azure Resource Manager usando o Azure PowerShell

Estas etapas mostram como usar os comandos do Azure PowerShell para migrar recursos de IaaS do Clássico para o Resource Manager. Estas etapas seguem uma abordagem de preencher lacunas para migrar seu ambiente personalizado. Você precisa apenas pegar os comandos e substituir seus próprios valores para as variáveis (as linhas que começam com "$")

## Etapa 1: Preparando para a migração

Veja a seguir algumas das práticas recomendadas durante a avaliação de migração dos recursos de IaaS do Clássico para o Resource Manager

- Leia a lista de recursos ou de configurações sem suporte [aqui](virtual-machines-windows-migration-classic-resource-manager.md). Se você tiver Máquinas Virtuais que usam recursos ou configurações sem suporte, será recomendável aguardar até que o suporte do recurso ou da configuração seja anunciado. Como alternativa, é possível remover esse recurso ou mudar a configuração para habilitar a migração, caso ela atenda às suas necessidades.
-	Se você tiver scripts automatizados, isso implantará sua infraestrutura e seus aplicativos hoje. Tente criar uma configuração de teste semelhante usando esses scripts para migração. Como alternativa, é possível configurar ambientes de exemplo usando o Portal do Azure também.
- Como o serviço está em Preview Pública, certifique-se de que o ambiente de teste para migração esteja isolado do ambiente de produção. Não combine contas de armazenamento, VNETs ou outros recursos entre os ambientes de teste e de produção.

## Etapa 2: Instalar o Azure PowerShell

Há duas opções principais para instalação: [Galeria do PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) e [WebPI](http://aka.ms/webpi-azps). A WebPI receberá atualizações mensais. A Galeria do PowerShell receberá atualizações continuamente.

Para obter mais informações, confira [Azure PowerShell 1.0](https://azure.microsoft.com//blog/azps-1-0/).

## Etapa 3: Definir sua assinatura e inscrever-se na Preview Pública da Migração

Primeiro, inicie um prompt do PowerShell. Para cenários de migração, é necessário definir seu ambiente tanto para o Clássico quanto para o Resource Manager.

Faça logon em sua conta para o modelo do Resource Manager.

	Login-AzureRmAccount

Obtenha as assinaturas disponíveis usando o comando a seguir.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Defina sua assinatura do Azure para a sessão atual. Substitua tudo que estiver entre aspas, inclusive os caracteres < and >, pelos nomes corretos.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Inscreva-se na Preview Pública com o comando a seguir.

	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Aguarde cinco minutos até que o registro seja concluído. É possível verificar o status da aprovação usando o comando a seguir. É necessário certificar-se de que o RegistrationState é “Registrado” antes de continuar.

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Agora faça logon em sua conta para o modelo clássico.

	Add-AzureAccount

Obtenha as assinaturas disponíveis usando o comando a seguir.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Defina sua assinatura do Azure para a sessão atual. Substitua tudo que estiver entre aspas, inclusive os caracteres < and >, pelos nomes corretos.

	$subscr="<subscription name>"
	Get-AzureSubscription –SubscriptionName $subscr | Select-AzureSubscription

## Etapa 4: Comandos para migrar os recursos de IaaS

>[AZURE.NOTE] Observe que todas as operações descritas abaixo são idempotentes. Caso encontre algo que não seja um recurso sem suporte ou um erro de configuração, é recomendável tentar novamente a operação de preparação, anulação ou confirmação e a plataforma tentará executar a ação novamente.

### Migrando máquinas virtuais em um serviço de nuvem (não em uma rede virtual)

Obtenha a lista de Serviços de Nuvem usando o comando a seguir e escolha o serviço de nuvem que deseja migrar. Observe que, se as VMs no Serviço de Nuvem estiverem em uma VNET ou se tiverem funções web/de trabalho, você receberá uma mensagem de erro.

	Get-AzureService | ft Servicename

Obtenha o nome da implantação do serviço de nuvem usando o comando a seguir

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

Prepare as máquinas virtuais no serviço de nuvem para a migração. Você tem duas opções para escolher.

Se você deseja migrar as VMs para uma Rede Virtual criada com a plataforma, use o comando abaixo.

	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

Se você deseja migrar para uma Rede Virtual existente no modelo de implantação do Resource Manager, use o comando abaixo.

	$existingVnetRGName = "<Existing VNET's Resource Group Name>"
	$vnetName = "<Virtual Network Name>"
	$subnetName = "<Subnet name>"
	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName 		-VirtualNetworkName $vnetName -SubnetName $subnetName

Quando a operação de preparação for bem-sucedida, você poderá consultar o estado da migração na VM e garantir que as VMs estão no estado “Preparado”.

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

Verifique a configuração dos recursos preparados usando o PowerShell ou o Portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando abaixo.

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando abaixo.

	Move-AzureService -Commit -ServiceName docmigtest1 -DeploymentName docmigtest1

### Migrando máquinas virtuais em uma rede virtual

Selecione a Rede Virtual que você deseja migrar. Observe que, se a Rede Virtual contiver funções web/de trabalho ou VMs com configurações sem suporte, você receberá uma mensagem de erro de validação.

Prepare a rede virtual para a migração usando o comando abaixo.

	$vnetName = "VNET-Name"
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

Verifique a configuração das máquinas virtuais preparadas usando o PowerShell ou o Portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando abaixo.

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando abaixo.

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

## Referências

- [Platform supported migration of IaaS resources from Classic to Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md) (Migração de recursos de IaaS com suporte da plataforma do Clássico para o Resource Manager)
- [Technical Deep Dive on Platform supported migration from Classic to Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md) (Análise técnica aprofundada sobre a migração com suporte da plataforma do Clássico para o Resource Manager)
- [Clonar uma máquina virtual clássica para o Azure Resource Manager usando scripts da comunidade do PowerShell](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0511_2016-->