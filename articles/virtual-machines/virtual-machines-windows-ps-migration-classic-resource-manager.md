<properties
	pageTitle="Migrar recursos de IaaS do Clássico para o Azure Resource Manager usando o Azure PowerShell | Microsoft Azure"
	description="Este artigo apresenta a migração de recursos com suporte da plataforma do clássico para o Azure Resource Manager usando os scripts do PowerShell"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="danlep"/>

# Migrar recursos de IaaS do Clássico para o Azure Resource Manager usando o Azure PowerShell

Estas etapas mostram como usar os comandos do Azure PowerShell para migrar os recursos de IaaS (infraestrutura como serviço) do modelo de implantação clássico para o Modelo de implantação do Azure Resource Manager. Estas etapas seguem uma abordagem de preencher lacunas para migrar seu ambiente personalizado. Assimile os comandos e substitua seus próprios valores pelas variáveis (as linhas que começam com "$").

## Etapa 1: preparar para a migração

Veja a seguir algumas das práticas que recomendamos durante a avaliação de migração dos recursos de IaaS do modelo clássico para o Gerenciador de Recursos:

- Leia a [lista de recursos ou de configurações sem suporte](virtual-machines-windows-migration-classic-resource-manager.md). Caso você tenha máquinas virtuais que usam recursos ou configurações sem suporte, recomendamos aguardar até que o suporte do recurso/configuração seja anunciado. Como alternativa, se isso atender às suas necessidades, remova esse recurso ou mude a configuração para habilitar a migração.
-	Se você tiver scripts automatizados que implantam sua infraestrutura e aplicativos atualmente, tente criar uma configuração de teste semelhante usando esses scripts para migração. Você também pode configurar ambientes de exemplo usando o portal do Azure.

## Etapa 2: instalar a versão mais recente do Azure PowerShell

Há duas opções principais para instalação, a [Galeria do PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) e o [WebPI (Web Platform Installer)](http://aka.ms/webpi-azps). WebPI recebe atualizações mensais. A Galeria do PowerShell receberá atualizações continuamente.

Para saber mais, consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md):

## Etapa 3: Definir sua assinatura e se inscrever para a migração

Primeiro, inicie um prompt do PowerShell. Para cenários de migração, é necessário instalar seu ambiente tanto para o modelo clássico quanto para o Gerenciador de Recursos.

Entre em sua conta para o modelo do Gerenciador de Recursos.

	Login-AzureRmAccount

Obtenha as assinaturas disponíveis usando o comando a seguir.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Defina sua assinatura do Azure para a sessão atual. Substitua tudo que estiver entre aspas, incluindo os caracteres < e >, pelo nome correto.

	Select-AzureRmSubscription –SubscriptionName "<subscription name>"

>[AZURE.NOTE] O registro é uma etapa única, mas é preciso executá-lo uma vez antes de tentar a migração. Sem o registro, você verá a seguinte mensagem de erro:

>	*BadRequest : Subscription is not registered for migration.* 

Registre-se no provedor de recursos de migração usando o comando a seguir.
	
	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Aguarde cinco minutos para concluir o registro. É possível verificar o status da aprovação usando o comando a seguir. Verifique se RegistrationState é `Registered` antes de continuar.

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Agora, entre em sua conta para o modelo clássico.

	Add-AzureAccount

Obtenha as assinaturas disponíveis usando o comando a seguir.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Defina sua assinatura do Azure para a sessão atual. Substitua tudo que estiver entre aspas, incluindo os caracteres < e >, pelo nome correto.

	Select-AzureSubscription –SubscriptionName "<subscription name>"

## Etapa 4: Verificar se você tem uma quantidade suficiente de núcleos de Máquina Virtual do Azure Resource Manager na região do Azure de sua implantação atual ou VNET

Você pode usar o seguinte comando do powershell para verificar a quantidade atual de núcleos no Azure Resource Manager. Para saber mais sobre cotas de núcleos, veja [Limites e o Azure Resource Manager](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
Get-AzureRmVMUsage -Location "<Your VNET or Deployment's Azure region"
```

## Etapa 5: Executar comandos para migrar os recursos de IaaS

>[AZURE.NOTE] Todas as operações descritas aqui são idempotentes. Caso você tenha algum problema que não seja um recurso sem suporte ou um erro de configuração, recomendamos que repita a operação de preparação, anulação ou confirmação. Em seguida, a plataforma tentará novamente a ação.

### Migrar máquinas virtuais em um serviço de nuvem (não em uma rede virtual)

Obtenha a lista de serviços de nuvem usando o comando a seguir e escolha o serviço de nuvem que deseja migrar. Se as VMs no serviço de nuvem estiverem em uma rede virtual, ou se tiverem funções Web/de trabalho, o comando retornará uma mensagem de erro.

	Get-AzureService | ft Servicename

Obtenha o nome da implantação do serviço de nuvem usando o comando a seguir.

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

Prepare as máquinas virtuais no serviço de nuvem para migração. Você tem duas opções entre as quais escolher.

1. Se você quiser migrar as VMs para uma rede virtual criada com uma plataforma

	Primeiro, valide se você pode migrar o serviço de nuvem usando o seguinte comando:

		$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
		$validate.ValidationMessages

	O comando anterior exibe avisos e erros que bloqueiam a migração. Se a validação for bem-sucedida, você poderá prosseguir com a seguinte etapa de preparação.

		Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

2. Se quiser migrar para uma rede virtual existente no modelo de implantação do Gerenciador de Recursos

		$existingVnetRGName = "<Existing VNET's Resource Group Name>"
		$vnetName = "<Virtual Network Name>"
		$subnetName = "<Subnet name>"

	Primeiro, valide se você pode migrar o serviço de nuvem usando o seguinte comando:

		$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
		$validate.ValidationMessages

	O comando anterior exibe avisos e erros que bloqueiam a migração. Se a validação for bem-sucedida, você poderá prosseguir com a seguinte etapa de preparação.

		Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName

Depois que a operação de preparação for bem-sucedida, você pode consultar o estado de migração das VMs e garantir que elas estejam no estado `Prepared`.

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

Verifique a configuração dos recursos preparados usando o PowerShell ou o Portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando a seguir.

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir.

	Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName

### Migrar máquinas virtuais em uma rede virtual

Selecione a rede virtual que você deseja migrar.

	$vnetName = "VNET-Name"

>[AZURE.NOTE] Se a rede virtual contiver funções Web/de trabalho ou VMs com configurações sem suporte, você receberá uma mensagem de erro de validação.

Primeiro, valide se você pode migrar a rede virtual usando o seguinte comando:

	Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName

O comando anterior exibe avisos e erros que bloqueiam a migração. Se a validação for bem-sucedida, você poderá prosseguir com a seguinte etapa de preparação.
	
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

Verifique a configuração para as máquinas virtuais preparadas usando o PowerShell ou o Portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando a seguir.

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir.

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

### Migrar uma conta de armazenamento

Depois de concluir a migração das máquinas virtuais, recomendamos a migração da conta de armazenamento.

Prepare a conta de armazenamento para migração usando o comando a seguir

	$storageAccountName = "storagename"
	Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName

Verifique a configuração da conta de armazenamento preparada usando o PowerShell ou o Portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando a seguir.

	Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir.

	Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName

## Próximas etapas

- [Migração de recursos de IaaS com suporte da plataforma do Clássico para o Gerenciador de Recursos](virtual-machines-windows-migration-classic-resource-manager.md)
- [Análise técnica aprofundada sobre a migração com suporte da plataforma do Clássico para o Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Clonar uma máquina virtual clássica para o Azure Resource Manager usando scripts da comunidade do PowerShell](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0914_2016-->