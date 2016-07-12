<properties
	pageTitle="Diferentes maneiras de criar uma VM do Linux | Microsoft Azure"
	description="Lista as diferentes maneiras de criar uma máquina virtual do Linux no Azure e fornece links para ferramentas e tutoriais para cada método."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="07/06/2016"
	ms.author="iainfou"/>

# Diferentes maneiras de criar uma máquina virtual do Linux com o Gerenciador de Recursos

O Azure oferece maneiras diferentes de criar uma VM usando o modelo de implantação do Resource Manager para se adaptar a diferentes usuários e finalidades. Este artigo resume essas diferenças e as opções que você pode fazer para criar máquinas virtuais (VMs) do Linux.

## CLI do Azure 

A CLI do Azure está disponível entre plataformas por meio de pacote npm, pacotes de distribuição fornecida ou contêineres de Docker. Você pode ler mais sobre [como instalar e configurar a CLI do Azure](../xplat-cli-install.md). Os tutoriais a seguir fornecem exemplos sobre como usar a CLI do Azure. Leia cada artigo para obter mais detalhes sobre os comandos de início rápido da CLI mostrados:

* [Criar uma VM do Linux na CLI do Azure para desenvolvimento e teste](virtual-machines-linux-quick-create-cli.md)

	```bash
	azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
	```

* [Criar uma VM do Linux protegida usando um modelo do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

	```bash
	azure group create --name TestRG --location WestUS 
		--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	```

* [Criar uma VM Linux a partir do zero usando a CLI do Azure](virtual-machines-linux-create-cli-complete.md)

* [Adicionar um disco a uma VM do Linux](virtual-machines-linux-add-disk.md)

	```bash
	azure vm disk attach-new --resource-group TestRG --vm-name TestVM <size-in-GB>
	```

## Portal do Azure

A interface gráfica do usuário do [portal do Azure](https://portal.azure.com) é uma maneira fácil de experimentar uma VM, especialmente se você estiver começando a usar o Azure, uma vez que não há nada para instalar em seu sistema. Use o portal do Azure para criar a VM:

* [Criar uma VM do Linux usando o portal do Azure](virtual-machines-linux-quick-create-portal.md)
* [Anexar um disco usando o portal do Azure](virtual-machines-linux-attach-disk-portal.md)

## Sistema operacional e opções de imagem
Ao criar uma VM, você escolherá uma imagem com base no sistema operacional que deseja executar. A Azure e seus parceiros oferecem muitas imagens, algumas delas incluem aplicativos e ferramentas pré-instalados. Ou você pode carregar uma de suas próprias imagens (confira abaixo).

### Imagens do Azure
Você pode usar os comandos CLI `azure vm image` para ver o que está disponível pelo editor, a versão de distribuição e as compilações.

Liste os editores disponíveis:

```bash
azure vm image list-publishers --location WestUS
```

Listar produtos (ofertas) disponíveis para determinado editor:

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Listar SKUs disponíveis (versões de distribuição) de determinada oferta:

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Liste todas as imagens disponíveis para determinada versão:

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Confira [Navegar e selecionar imagens da máquina virtual do Azure com a CLI do Azure](virtual-machines-linux-cli-ps-findimage.md) para obter mais exemplos sobre como navegar e usar as imagens disponíveis.

Os comandos `azure vm quick-create` e `azure vm create` também têm alguns aliases que você pode usar para acessar rapidamente as distribuições mais comuns e suas versões mais recentes. Isso é mais fácil do que precisar especificar o editor, a oferta, o SKU e a versão cada vez que você cria uma máquina virtual:

| Alias | Editor | Oferta | SKU | Versão |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | Centos | 7,2 | mais recente |
| CoreOS | CoreOS | CoreOS | Estável | mais recente |
| Debian | credativ | Debian | 8 | mais recente |
| openSUSE | SUSE | openSUSE | 13\.2 | mais recente |
| RHEL | Redhat | RHEL | 7,2 | mais recente |
| SLES | SLES | SLES | 12-SP1 | mais recente |
| UbuntuLTS | Canônico | UbuntuServer | 14\.04.4-LTS | mais recente |

### Usar sua própria imagem

Se você precisar de personalizações específicas, poderá usar uma imagem com base em uma VM do Azure existente *capturando* essa VM ou carregar uma imagem própria, armazenada em um disco rígido virtual (VHD). Para obter mais informações sobre as distribuições com suporte e como usar suas próprias imagens, confira os artigos a seguir:

* [Distribuições endossadas pelo Azure](virtual-machines-linux-endorsed-distros.md)

* [Informações para distribuições não endossadas](virtual-machines-linux-create-upload-generic.md)

* [Como capturar uma máquina virtual do Linux como um modelo do Resource Manager](virtual-machines-linux-capture-image.md). Comandos de início rápido:

	```bash
	azure vm deallocate --resource-group TestRG --vm-name TestVM
	azure vm generalize --resource-group TestRG --vm-name TestVM
	azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
	```

## Próximas etapas

* Experimente um dos tutoriais para criar uma VM do Linux a partir do [portal](virtual-machines-linux-quick-create-portal.md) com a [CLI](virtual-machines-linux-quick-create-cli.md) ou usando um modelo do [Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md).

* Após criar uma VM do Linux, você poderá [adicionar um disco de dados](virtual-machines-linux-add-disk.md) facilmente.

* Etapas rápidas para [redefinir uma senha ou chaves SSH e gerenciar os usuários](virtual-machines-linux-using-vmaccess-extension.md)

<!---HONumber=AcomDC_0706_2016-->