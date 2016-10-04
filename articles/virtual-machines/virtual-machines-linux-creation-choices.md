<properties
	pageTitle="Diferentes maneiras de criar uma VM do Linux | Microsoft Azure"
	description="Aprenda as diferentes maneiras de criar uma máquina virtual do Linux no Azure, incluindo links para ferramentas e tutoriais para cada método."
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
	ms.date="09/27/2016"
	ms.author="iainfou"/>

# Diferentes maneiras de criar uma máquina virtual do Linux no Azure

Você tem a flexibilidade no Azure de criar uma máquina virtual Linux (VM) usando ferramentas e fluxos de trabalho confortáveis para você. Este artigo resume essas diferenças e exemplos para criar suas VMs do Linux.


## CLI do Azure 

A CLI do Azure está disponível entre plataformas por meio de pacote npm, pacotes de distribuição fornecida ou contêineres de Docker. Você pode ler mais sobre [como instalar e configurar a CLI do Azure](../xplat-cli-install.md). Os tutoriais a seguir fornecem exemplos sobre como usar a CLI do Azure. Leia cada artigo para obter mais detalhes sobre os comandos de início rápido da CLI mostrados:

- [Criar uma VM do Linux na CLI do Azure para desenvolvimento e teste](virtual-machines-linux-quick-create-cli.md)
	- O exemplo a seguir cria uma VM CoreOS usando uma chave pública denominada `azure_id_rsa.pub`:

	```bash
	azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
		--image-urn CoreOS
	```

- [Criar uma VM do Linux protegida usando um modelo do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
	- O exemplo a seguir cria uma VM usando o modelo armazenado no GitHub:

	```bash
	azure group create --name TestRG --location WestUS 
		--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	```

- [Criar um ambiente Linux completo com a CLI do Azure](virtual-machines-linux-create-cli-complete.md)
	- Inclui a criação de um balanceador de carga e várias VMs em um conjunto de disponibilidade.

- [Adicionar um disco a uma VM do Linux](virtual-machines-linux-add-disk.md)
	- O exemplo a seguir adiciona um disco de 5Gb a uma VM existente denominada `TestVM`:

	```bash
	azure vm disk attach-new --resource-group TestRG --vm-name TestVM \
		--size-in-GB 5
	```

## Portal do Azure

O [portal do Azure](https://portal.azure.com) permite que você crie rapidamente uma VM porque não há nada para instalar em seu sistema. Use o portal do Azure para criar a VM:

- [Criar uma VM do Linux usando o portal do Azure](virtual-machines-linux-quick-create-portal.md)
- [Anexar um disco usando o portal do Azure](virtual-machines-linux-attach-disk-portal.md)


## Sistema operacional e opções de imagem
Ao criar uma VM, você escolherá uma imagem com base no sistema operacional que deseja executar. A Azure e seus parceiros oferecem muitas imagens, algumas delas incluem aplicativos e ferramentas pré-instalados. Ou carregue uma de suas próprias imagens (consulte [a seção a seguir](#use-your-own-image)).

### Imagens do Azure
Use os `azure vm image` comandos da CLI para ver o que está disponível no editor, versão de distribuição e compilações.

Liste os editores disponíveis como a seguir:

```bash
azure vm image list-publishers --location WestUS
```

Liste os produtos (ofertas) disponíveis para um determinado editor como a seguir:

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Liste os SKUs disponíveis (versões de distribuição) de uma determinada oferta como a seguir:

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Liste todas as imagens disponíveis para uma determinada versão como a seguir:

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Para obter mais exemplos de navegação e uso das imagens disponíveis, consulte [Navegar e selecionar imagens da máquina virtual do Azure com a CLI do Azure ](virtual-machines-linux-cli-ps-findimage.md).

Os comandos `azure vm quick-create` e `azure vm create` têm aliases que você pode usar para acessar rapidamente as distribuições mais comuns e suas versões mais recentes. Usar aliases geralmente é mais rápido do que especificar o editor, oferta, SKU e versão sempre que você cria uma VM:

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

Se você precisar de personalizações específicas, poderá usar uma imagem com base em uma VM existente do Azure *capturando* essa VM. Você também pode carregar uma imagem criada no local. Para obter mais informações sobre as distribuições com suporte e como usar suas próprias imagens, confira os artigos a seguir:

- [Distribuições endossadas pelo Azure](virtual-machines-linux-endorsed-distros.md)

- [Informações para distribuições não endossadas](virtual-machines-linux-create-upload-generic.md)

- [Como capturar uma máquina virtual do Linux como um modelo do Resource Manager](virtual-machines-linux-capture-image.md).
	- Comandos de exemplo de início rápido para capturar uma VM existente:

	```bash
	azure vm deallocate --resource-group TestRG --vm-name TestVM
	azure vm generalize --resource-group TestRG --vm-name TestVM
	azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
	```

## Próximas etapas

- Crie uma VM do Linux o [portal](virtual-machines-linux-quick-create-portal.md), com a [CLI](virtual-machines-linux-quick-create-cli.md) ou usando um [modelo do Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md).

- Depois de criar uma VM do Linux, [adicione um disco de dados](virtual-machines-linux-add-disk.md).

- Etapas rápidas para [redefinir uma senha ou chaves SSH e gerenciar os usuários](virtual-machines-linux-using-vmaccess-extension.md)

<!---HONumber=AcomDC_0928_2016-->