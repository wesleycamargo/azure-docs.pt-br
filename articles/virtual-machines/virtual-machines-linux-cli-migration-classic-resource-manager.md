<properties
	pageTitle="Migrar recursos de IaaS do modelo clássico para o Azure Resource Manager usando a CLI do Azure | Microsoft Azure"
	description="Este artigo apresenta a migração de recursos com suporte da plataforma do modelo clássico para o Azure Resource Manager usando a CLI do Azure"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/19/2016"
	ms.author="cynthn"/>

# Migrar recursos de IaaS do modelo clássico para o Azure Resource Manager usando a CLI do Azure

Estas etapas mostram como usar a CLI (interface de linha de comando) do Azure para migrar recursos de IaaS (infraestrutura como serviço) do modelo de implantação clássico para o modelo de implantação do Azure Resource Manager. O artigo exige a [CLI do Azure](../xplat-cli-install.md).

>[AZURE.NOTE] Todas as operações descritas aqui são idempotentes. Caso você tenha algum problema que não seja um recurso sem suporte ou um erro de configuração, recomendamos que repita a operação de preparação, anulação ou confirmação. Em seguida, a plataforma repetirá a ação.

## Etapa 1: preparar para a migração

Veja a seguir algumas das práticas que recomendamos durante a avaliação de migração dos recursos de IaaS do modelo clássico para o Gerenciador de Recursos:

- Leia a [lista de recursos ou de configurações sem suporte](virtual-machines-windows-migration-classic-resource-manager.md). Caso você tenha máquinas virtuais que usam recursos ou configurações sem suporte, recomendamos que aguarde até que o suporte para o recurso/configuração seja anunciado. Como alternativa, é possível remover esse recurso ou mudar a configuração para habilitar a migração, caso ela atenda às suas necessidades.
-	Se você tiver scripts automatizados que implantam sua infraestrutura e aplicativos atualmente, tente criar uma configuração de teste semelhante usando esses scripts para migração. Você também pode configurar ambientes de exemplo usando o portal do Azure.

## Etapa 2: Definir sua assinatura e registrar o provedor

Para cenários de migração, é necessário instalar seu ambiente tanto para o modelo clássico quanto para o Gerenciador de Recursos. [Instale a CLI do Azure](../xplat-cli-install.md) e [selecione sua assinatura](../xplat-cli-connect.md).

Entre em sua conta.
	
	azure login

Selecione a assinatura do Azure usando o seguinte comando.

	azure account set "<azure-subscription-name>"

>[AZURE.NOTE] O registro é uma etapa única, mas é preciso executá-lo uma vez antes de tentar a migração. Sem o registro, você verá a seguinte mensagem de erro

>	*BadRequest : Subscription is not registered for migration.* 

Registre-se no provedor de recursos de migração usando o comando a seguir. Observe que, em alguns casos, esse comando atinge o tempo limite. No entanto, o registro será bem-sucedido.

	azure provider register Microsoft.ClassicInfrastructureMigrate

Aguarde cinco minutos para concluir o registro. É possível verificar o status da aprovação usando o comando a seguir. Verifique se RegistrationState é `Registered` antes de continuar.

	azure provider show Microsoft.ClassicInfrastructureMigrate

Agora mude a CLI para o modo `asm`.

	azure config mode asm


## Etapa 3: Opção 1 – Migrar máquinas virtuais em um serviço de nuvem 

Obtenha a lista de serviços de nuvem usando o comando a seguir e escolha o serviço de nuvem que deseja migrar. Observe que, se as VMs no serviço de nuvem estiverem em uma rede virtual ou se tiverem funções web/de trabalho, você receberá uma mensagem de erro.

	azure service list

Execute a comando a seguir para obter o nome da implantação do serviço de nuvem por meio da saída detalhada. Na maioria dos casos, o nome da implantação é igual ao nome do serviço de nuvem.

	azure service show <serviceName> -vv

Prepare as máquinas virtuais no serviço de nuvem para migração. Você tem duas opções entre as quais escolher.

Se quiser migrar as máquinas virtuais em uma rede virtual criada por plataforma, use o comando a seguir.

	azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Se quiser migrar para uma rede virtual existente no modelo de implantação do Gerenciador de Recursos, use o comando a seguir.

	azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> subnetName <vnetName>

Após uma operação de preparação bem-sucedida, você poderá examinar a saída detalhada para obter o estado de migração das VMs e assegurar que as elas estejam no estado `Prepared`.

	azure vm show <vmName> -vv

Verifique a configuração dos recursos preparados usando a CLI ou o portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando a seguir.

	azure service deployment abort-migration <serviceName> <deploymentName>

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir.

	azure service deployment commit-migration <serviceName> <deploymentName>


	
## Etapa 3: Opção 2 – Migrar máquinas virtuais em uma rede virtual

Selecione a rede virtual que você deseja migrar. Observe que, se a rede virtual contiver funções web/de trabalho ou VMs com configurações sem suporte, você receberá uma mensagem de erro de validação.

Obtenha todas as redes virtuais na assinatura usando o comando a seguir.

	azure network vnet list
	
A saída será parecida com esta:

![Captura de tela da linha de comando com o nome inteiro da rede virtual realçado.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

No exemplo acima, **virtualNetworkName** é o nome inteiro **“Grupo classicubuntu16 classicubuntu16”**.

Prepare a rede virtual de sua preferência para migração usando o comando a seguir.

	azure network vnet prepare-migration <virtualNetworkName>

Verifique a configuração para as máquinas virtuais preparadas usando a CLI ou o portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando a seguir.

	azure network vnet abort-migration <virtualNetworkName>

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir.

	azure network vnet commit-migration <virtualNetworkName>

## Etapa 4: Migrar uma conta de armazenamento

Depois de concluir a migração das máquinas virtuais, recomendamos a migração da conta de armazenamento.

Prepare a conta de armazenamento para migração usando o comando a seguir

	azure storage account prepare-migration <storageAccountName>

Verifique a configuração da conta de armazenamento preparada usando a CLI ou o Portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando a seguir.

	azure storage account abort-migration <storageAccountName>

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir.

	azure storage account commit-migration <storageAccountName>

## Próximas etapas

- [Migração de recursos de IaaS com suporte da plataforma do Clássico para o Gerenciador de Recursos](virtual-machines-windows-migration-classic-resource-manager.md)
- [Análise técnica aprofundada sobre a migração com suporte da plataforma do Clássico para o Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)

<!---HONumber=AcomDC_0720_2016-->