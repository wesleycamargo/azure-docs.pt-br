<properties
	pageTitle="Comandos equivalentes do Azure CLI para tarefas de VM | Microsoft Azure"
	description="Comandos equivalentes do CLI do Azure para criar e gerenciar VMs do Azure nos modos de Gerenciador de Recursos do Azure e Gerenciamento de Serviços do Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="command-line-interface"
	ms.workload="infrastructure-services"
	ms.date="08/28/2015"
	ms.author="danlep"/>


# Comandos equivalentes do Gerenciador de Recursos e de Gerenciamento de Serviços para tarefas de VM com a CLI do Azure para Mac, Linux e Windows
Este artigo mostra os comandos equivalentes do Azure CLI (interface de linha de comando do Microsoft Azure) para criar e gerenciar VMs do Azure no Gerenciamento de Serviços do Azure e Gerenciador de Recursos do Azure. Use-o como um guia útil para migrar scripts de um modo de comando para outro.

* Se você ainda não tiver instalado a CLI do Azure e se conectado à sua assinatura, consulte [Instalar a CLI do Azure](../xplat-cli-install.md) e [Conectar-se a uma assinatura do Azure a partir da CLI do Azure](../xplat-cli-connect.md). Quando você quiser usar os comandos do modo do Gerenciador de Recursos, conecte-se com o método de logon.

* Para se familiarizar com o modo do Gerenciador de Recursos no CLI do Azure e com os modos de comando de opção, consulte [Usando a Interface de linha de comando do Azure com o Gerenciador de Recursos](xplat-cli-azure-resource-manager.md). Por padrão, a CLI do Azure é iniciada no modo de Gerenciamento de Serviços. Para alterar para o modo do Gerenciador de Recursos, execute `azure config mode arm`. Para retornar ao modo de Gerenciamento de Serviços, execute `azure config mode asm`.

* Para obter ajuda e opções de comandos online, digite `azure <command> <subcommand> --help` ou `azure help <command> <subcommand>`.

## Tarefas da VM
A tabela a seguir compara as tarefas comuns de VM, que você pode executar com comandos do CLI do Azure no Gerenciador de Recursos e no Gerenciamento de Serviços. Com muitos comandos do Gerenciador de Recursos, você precisa passar o nome de um grupo de recursos existente.

> [AZURE.NOTE]Esses exemplos não incluem operações baseadas em modelo no Gerenciador de recursos. Para obter informações, consulte [Usando a Interface de linha de comando do Azure com o Gerenciador de Recursos](xplat-cli-azure-resource-manager.md).

Tarefa | Gerenciamento de serviços | Gerenciador de Recursos
-------------- | ----------- | -------------------------
Criar a VM mais básica | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Obtenha o `image-urn` com o comando `azure vm image list`.)
Criar uma VM do Linux | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Criar uma VM do Windows | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
Listar VMs | `azure  vm list [options]` | `azure  vm list [options] <resource_group>`
Obter informações sobre uma VM | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
Iniciar uma VM | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
Parar uma VM | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
Desalocar uma VM | Não disponível | `azure vm deallocate [options] <resource-group> <name>`
Reiniciar uma VM | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
Excluir uma VM | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
Capturar uma VM | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
Criar uma VM por meio de uma imagem do usuário | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
Criar uma VM por meio de um disco especializado | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
Adicionar um disco de dados a uma VM | `azure  vm disk attach [options] <vm-name> <disk-image-name>` -OU- <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
Remover um disco de dados de uma VM | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
Adicionar uma extensão genérica a uma VM | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
Adicionar a extensão de acesso de VM a uma VM | Não disponível | `azure vm reset-access [options] <resource-group> <name>`
Adicionar uma extensão Docker a uma VM | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
Adicionar uma extensão Chef a uma VM | `azure  vm extension get-chef [options] <vm-name>` | Não disponível
Desabilitar uma extensão de VM | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | Não disponível
Remover uma extensão de VM | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
Listar extensões de VM | `azure vm extension list [options]` | `azure  vm extension get [options] <resource-group> <vm-name>`
Listar imagens de VM | `azure vm image list [options]` | `azure vm image list [options] <location> <publisher> [offer] [sku]` -OU- <br/> `azure vm image list-publishers [options] <location>` -OU- <br/> `azure vm image list-offers [options] <location>` -OU- <br/> `azure vm image list-skus [options] <location>`
Mostrar uma imagem de VM | `azure vm image show [options]` | Não disponível
Conferir o uso dos recursos de VM | Não disponível | `azure vm list-usage [options] <location>`
Conferir todos os tamanhos de VM disponíveis | Não disponível | `azure vm sizes [options]`


## Próximas etapas

* Para saber mais sobre como usar a CLI do Azure para trabalhar com os recursos do Gerenciador de Recursos, veja [Usando a Interface de Linha de Comando do Azure com o Gerenciador de Recursos](xplat-cli-azure-resource-manager.md) e [Gerenciando o Controle de Acesso Baseado em Função com a Interface de Linha de Comando do Azure](../role-based-access-control-xplat-cli-install.md).
* Para obter exemplos adicionais dos comandos de CLI, consulte [Usando a interface de linha de comando do Azure com o Gerenciamento de serviços do Azure](../virtual-machines-command-line-tools.md) e [Usando a CLI do Azure com o Gerenciador de Recursos do Azure](azure-cli-arm-commands.md).

<!---HONumber=Oct15_HO1-->