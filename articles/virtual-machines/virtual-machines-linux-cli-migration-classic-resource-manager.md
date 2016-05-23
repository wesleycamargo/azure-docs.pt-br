<properties
	pageTitle="Migrar recursos de IaaS do Clássico para o Azure Resource Manager usando a CLI do Azure"
	description="Este artigo apresenta as funcionalidades de serviço de migração com suporte da plataforma, do Gerenciamento de Serviços, para o Azure Resource Manager usando a CLI do Azure"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Migrar recursos de IaaS do Clássico para o Azure Resource Manager usando a CLI do Azure

Estas etapas mostram como usar os comandos da CLI do Azure para migrar recursos de IaaS do Clássico para o Gerenciador de Recursos. O artigo requer [a CLI do Azure](../xplat-cli-install.md) conectada (`azure login`).

## Etapa 1: Preparando para a migração

Veja a seguir algumas das práticas recomendadas durante a avaliação de migração dos recursos de IaaS do Clássico para o Resource Manager

- Leia a lista de recursos ou de configurações sem suporte [aqui](virtual-machines-windows-migration-classic-resource-manager.md). Se você tiver Máquinas Virtuais que usam recursos ou configurações sem suporte, será recomendável aguardar até que o suporte do recurso ou da configuração seja anunciado. Como alternativa, é possível remover esse recurso ou mudar a configuração para habilitar a migração, caso ela atenda às suas necessidades.
-	Se você tiver scripts automatizados, isso implantará sua infraestrutura e seus aplicativos hoje. Tente criar uma configuração de teste semelhante usando esses scripts para migração. Como alternativa, é possível configurar ambientes de exemplo usando o Portal do Azure também.
- Como o serviço está em Preview Pública, certifique-se de que o ambiente de teste para migração esteja isolado do ambiente de produção. Não combine contas de armazenamento, VNETs ou outros recursos entre os ambientes de teste e de produção.

## Etapa 2: Definir sua assinatura e inscrever-se na Preview Pública da Migração

Para cenários de migração, é necessário definir seu ambiente tanto para o Clássico quanto para o Resource Manager. [Instale a CLI do Azure](../xplat-cli-install.md) e [selecione sua assinatura](../xplat-cli-connect.md).

Selecione a assinatura do Azure usando o seguinte comando

	azure account set "azure-subscription-name"

Inscreva-se na Preview Pública com o comando a seguir. Observe que, em alguns casos, esse comando atinge o tempo limite; no entanto, o registro será bem-sucedido. Siga a próxima etapa para verificar o status do registro.

	azure provider register Microsoft.ClassicInfrastructureMigrate

Aguarde cinco minutos até que o registro seja concluído. É possível verificar o status da aprovação usando o comando a seguir. É necessário certificar-se de que o RegistrationState é “Registrado” antes de continuar.

	azure provider show Microsoft.ClassicInfrastructureMigrate

Agora alterne a CLI para o modo ASM

	azure config mode asm

## Etapa 3: Comandos para migrar os recursos de IaaS

>[AZURE.NOTE] Observe que todas as operações descritas abaixo são idempotentes. Caso encontre algo que não seja um recurso sem suporte ou um erro de configuração, é recomendável tentar novamente a operação de preparação, anulação ou confirmação e a plataforma tentará executar a ação novamente.

### Migrando máquinas virtuais em um serviço de nuvem (não em uma rede virtual)

Obtenha a lista de Serviços de Nuvem usando o comando a seguir e escolha o serviço de nuvem que deseja migrar. Observe que, se as VMs no Serviço de Nuvem estiverem em uma VNET ou se tiverem funções web/de trabalho, você receberá uma mensagem de erro.

	azure service list

Execute a comando a seguir para obter o nome da implantação do Serviço de Nuvem por meio da saída detalhada. Na maioria dos casos, o nome da implantação é igual ao nome do serviço de nuvem.

	azure service show servicename -vv

Prepare as máquinas virtuais no serviço de nuvem para a migração. Você tem duas opções para escolher.

Se você deseja migrar as VMs para uma Rede Virtual criada com a plataforma, use o comando abaixo.

	azure service deployment prepare-migration servicename deploymentname new "" "" ""

Se você deseja migrar para uma Rede Virtual existente no modelo de implantação do Resource Manager, use o comando abaixo.

	azure service deployment prepare-migration serviceName deploymentName existing destinationVNETResourceGroupName subnetName vnetName

Quando a operação de preparação for bem-sucedida, você poderá examinar a saída detalhada para obter o estado de migração da VM e assegurar que as VMs estão no estado “Preparado”.

	azure vm show vmName -vv

Verifique a configuração dos recursos preparados usando a CLI ou o Portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando abaixo.

	azure service deployment abort-migration serviceName deploymentName

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando abaixo.

	azure service deployment commit-migration serviceName deploymentName

### Migrando máquinas virtuais em uma rede virtual

Selecione a Rede Virtual que você deseja migrar. Observe que, se a Rede Virtual contiver funções web/de trabalho ou VMs com configurações sem suporte, você receberá uma mensagem de erro de validação.

Obtenha todas as Redes Virtuais na assinatura usando o comando a seguir.

	azure network vnet list

Prepare a Rede Virtual de sua preferência para a Migração usando o comando abaixo.

	azure network vnet prepare-migration virtualnetworkname

Verifique a configuração das Máquinas Virtuais preparadas usando a CLI ou o Portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando abaixo.

	azure network vnet abort-migration virtualnetworkname

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando abaixo.

	azure network vnet commit-migration virtualnetworkname

## Referências

- [Platform supported migration of IaaS resources from Classic to Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md) (Migração de recursos de IaaS com suporte da plataforma do Clássico para o Resource Manager)
- [Technical Deep Dive on Platform supported migration from Classic to Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md) (Análise técnica aprofundada sobre a migração com suporte da plataforma do Clássico para o Resource Manager)

<!---HONumber=AcomDC_0511_2016-->