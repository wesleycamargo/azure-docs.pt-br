* Se você ainda não tiver instalado a CLI do Azure e se conectado à sua assinatura, consulte [Instalar a CLI do Azure](../articles/xplat-cli-install.md) e [Conectar-se a uma assinatura do Azure a partir da CLI do Azure](../articles/xplat-cli-connect.md). Quando você quiser usar os comandos do modo do Resource Manager, conecte-se com o comando `azure login`.

* Para começar a usar o modo do Gerenciador de Recursos na CLI do Azure, talvez seja necessário alternar os modos de comando. Por padrão, a CLI do Azure é iniciada no modo de Gerenciamento de Serviços. Para mudar para o modo do Gerenciador de Recursos, execute `azure config mode arm`. Para retornar ao modo de Gerenciamento de Serviços, execute `azure config mode asm`.

* Para obter ajuda e opções de comandos online, digite `azure <command> <subcommand> --help` ou `azure help <command> <subcommand>`.

## Tarefas da VM
A tabela a seguir compara as tarefas comuns de VM, que você pode executar com comandos da CLI do Azure no modo do Resource Manager e no modo do Service Management. Com muitos comandos do Gerenciador de Recursos, você precisa passar o nome de um grupo de recursos existente.

> [AZURE.NOTE] Esses exemplos não incluem operações baseadas em modelo que geralmente são recomendadas para implantações de VM no Gerenciador de Recursos. Para saber mais, confira [Usar a CLI do Azure com o Gerenciador de Recursos do Azure](../articles/xplat-cli-azure-resource-manager.md) [Implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de Recursos do Azure e a CLI do Azure](../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md).

Tarefa | Modo do Service Management | modelo do Gerenciador de Recursos
-------------- | ----------- | -------------------------
Criar a VM mais básica | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Obtenha o `image-urn` para imagens do Marketplace populares executando `azure help vm quick-create`. Para URNs no formulário *publisherName:offer:skus:version*, consulte [este artigo](../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md) para obter exemplos.)
Criar uma VM do Linux | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Criar uma VM do Windows | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
Listar VMs | `azure  vm list [options]` | `azure  vm list [options]`
Obter informações sobre uma VM | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
Iniciar uma VM | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
Parar uma VM | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
Desalocar uma VM | Não disponível | `azure vm deallocate [options] <resource-group> <name>`
Reiniciar uma VM | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
Excluir uma VM | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
Capturar uma VM | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
Criar uma VM por meio de uma imagem do usuário | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
Criar uma VM por meio de um disco especializado | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azure  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
Adicionar um disco de dados a uma VM | `azure  vm disk attach [options] <vm-name> <disk-image-name>` -OU- <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
Remover um disco de dados de uma VM | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
Adicionar uma extensão genérica a uma VM | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
Adicionar a extensão de acesso de VM a uma VM | Não disponível | `azure vm reset-access [options] <resource-group> <name>`
Adicionar uma extensão Docker a uma VM | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
Adicionar uma extensão Chef a uma VM | `azure  vm extension get-chef [options] <vm-name>` | Não disponível
Desabilitar uma extensão de VM | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | Não disponível
Remover uma extensão de VM | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
Listar extensões de VM | `azure vm extension list [options]` | Não disponível
Mostrar uma imagem de VM | `azure vm image show [options]` | Não disponível
Conferir o uso dos recursos de VM | Não disponível | `azure vm list-usage [options] <location>`
Conferir todos os tamanhos de VM disponíveis | Não disponível | `azure vm sizes [options]`


## Próximas etapas

* Para ver exemplos adicionais de comandos da CLI, consulte [Comandos da CLI do Azure no modo do Gerenciamento de Serviços do Azure](../articles/virtual-machines-command-line-tools.md) e [Comandos da CLI do Azure no modo do Azure Resource Manager](../articles/virtual-machines/azure-cli-arm-commands.md).

