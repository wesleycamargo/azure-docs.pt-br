<properties
	pageTitle="Comandos equivalentes do Gerenciador de Recursos e de Gerenciamento de Serviços para operações de VM com a CLI do Azure para Mac, Linux e Windows"
	description="Mostra os comandos equivalentes da CLI do Azure para criar e gerenciar VMs do Azure no Gerenciador de Recursos e os modos de Gerenciamento de Serviços"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="command-line-interface"
	ms.workload="infrastructure-services"
	ms.date="04/28/2015"
	ms.author="danlep"/>


# Comandos equivalentes do Gerenciador de Recursos e de Gerenciamento de Serviços para operações de VM com a CLI do Azure para Mac, Linux e Windows
Este artigo mostra os comandos equivalentes da CLI do Azure (interface de linha de comando do Azure) para criar e gerenciar VMs do Azure nos modos de Gerenciamento de Serviços (asm) e Gerenciador de Recursos (arm). Use-o como um guia útil para migrar scripts de um modo de comando para outro.

* Se você ainda não tiver instalado a CLI do Azure e se conectado à sua assinatura, consulte [Instalar a CLI do Azure para Mac, Linux e Windows](xplat-cli-install.md) e [Conectar-se a uma assinatura da CLI do Azure do Azure para Mac, Linux e Windows](xplat-cli-connect.md). Conecte-se com o método de logon ao usar os comandos do modo arm.

* Para se familiarizar com o modo arm nos modos da CLI do Azure e de comando de opção, consulte [Usando a Interface de linha de comando do Azure com o Gerenciador de Recursos](xplat-cli-azure-resource-manager.md).

* Para obter ajuda e opções de comandos online, digite `azure <command> <subcommand> --help` ou `azure help <command> <subcommand>`.

## Cenários
A seguir estão operações comuns de VM que você pode executar com comandos da CLI do Azure nos modos asm e arm. Com muitos comandos do modo arm, você precisa passar o nome de um grupo de recursos existente.

> [AZURE.NOTE]Esses exemplos não incluem operações baseadas em modelo no modo arm. Para obter informações, consulte [Usando a interface de linha de comando do Azure com o Gerenciador de Recursos](xplat-cli-azure-resource-manager.md).

Cenário | modo asm | modo arm
-------------- | ----------- | -------------------------
Criar a VM mais básica | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`
Criar uma VM do Linux | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Criar uma VM do Windows | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
Listar VMs | `azure  vm list [options]` | `azure  vm list [options] <resource_group>`
Obter informações sobre uma VM | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
Iniciar uma VM | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
Parar uma VM | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
Reiniciar uma VM | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
Excluir uma VM | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
Capturar uma VM | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
Criar uma VM por meio de uma imagem do usuário | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
Criar uma VM por meio de um disco especializado | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
Adicionar um disco de dados a uma VM | `azure  vm disk attach [options] <vm-name> <disk-image-name>` -OU- <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
Remover um disco de dados de uma VM | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
Adicionar uma extensão genérica a uma VM | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
Adicionar a extensão de acesso de VM a uma VM | não disponível | `azure vm reset-access [options] <resource-group> <name>`
Adicionar uma extensão Docker a uma VM | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
Adicionar uma extensão Chef a uma VM | `azure  vm extension get-chef [options] <vm-name>` | não disponível
Desabilitar uma extensão de VM | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | não disponível
Remover uma extensão de VM | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
Listar extensões de VM | `azure vm extension list [options]` | `azure  vm extension get [options] <resource-group> <vm-name>`
Listar imagens de VM | `azure vm image list [options]` | `azure vm image list [options] <location> <publisher> [offer] [sku]` -OU- <br/> `azure vm image list-publishers [options] <location>` -OU- <br/> `azure vm image list-offers [options] <location>` -OU- <br/> `azure vm image list-skus [options] <location>`
Mostrar uma imagem de VM | `azure vm image show [options]` | não disponível


## Próximas etapas

* Para obter mais informações sobre como usar a CLI do Azure para trabalhar com recursos no modo arm, consulte [Usando a interface de linha de comando do Azure com o Gerenciador de Recursos](xplat-cli-azure-resource-manager.md) e [Gerenciando o controle de acesso baseado em função com a interface de linha de comando do Azure](role-based-access-control-xplat-cli.md).
* Para obter exemplos adicionais dos comandos da CLI, consulte [Usando a interface de linha de comando do Azure para Mac, Linux e Windows](virtual-machines-command-line-tools.md) e [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciador de Recursos do Azure](azure-cli-arm-commands.md).

<!--HONumber=52-->
