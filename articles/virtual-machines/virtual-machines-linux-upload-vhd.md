<properties
	pageTitle="Criar e carregar um VHD do Linux | Microsoft Azure"
	description="Crie e carregue no Azure um disco rígido virtual (VHD) usando o modelo de implantação do gerenciador de recursos."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2016"
	ms.author="iainfou"/>

# Carregando um disco rígido virtual

Este artigo mostra como carregar um disco rígido virtual (VHD) usando o modelo de implantação do Gerenciador de Recursos. Essa funcionalidade permite que você instale e configure uma distribuição do Linux segundo suas necessidades e use esse VHD para criar rapidamente máquinas virtuais (VMs) do Azure.

## Comandos rápidos
Verifique se você tem [a CLI do Azure](../xplat-cli-install.md) conectada e está usando o modo do gerenciador de recursos (`azure config mode arm`).

Em primeiro lugar, crie um grupo de recursos:

```bash
azure group create TestRG --location "WestUS"
```

Crie uma conta de armazenamento para manter seus discos virtuais:

```bash
azure storage account create testuploadedstorage --resource-group TestRG \
	--location "WestUS" --kind Storage --sku-name LRS
```

Liste as chaves de armazenamento da conta de armazenamento que você acabou de criar e anote `key1`:

```bash
azure storage account keys list testuploadedstorage --resource-group TestRG
```

A saída será semelhante a:

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```

Crie um contêiner dentro de sua conta de armazenamento usando a chave de armazenamento que você acabou de obter:

```bash
azure storage container create --account-name testuploadedstorage \
	--account-key <key1> --container vm-images
```

Por fim, carregue o VHD no contêiner que você acabou de criar:

```bash
azure storage blob upload --blobtype page --account-name testuploadedstorage \
	--account-key <key1> --container vm-images /path/to/disk/yourdisk.vhd
```

Agora, você pode criar uma VM a partir do disco virtual carregado [usando um modelo do gerenciador de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) ou por meio da CLI especificando o URI para o disco, como a seguir:

```bash
azure vm create TestVM -l "WestUS" --resource-group TestRG \
	-Q https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd
```

Observe que você ainda precisará de todos os parâmetros adicionais necessários pelo comando `azure vm create`, como a rede virtual, endereço IP público, nome de usuário, chaves SSH, etc. Você pode ler mais sobre os [parâmetros do gerenciador de recursos da CLI disponíveis](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## Requisitos
Para concluir as etapas acima, você precisará de:

- **Sistema operacional Linux instalado em um arquivo .vhd**: instale uma [distribuição Linux endossada pelo Azure](virtual-machines-linux-endorsed-distros.md) (ou consulte as [informações para as distribuições não endossadas](virtual-machines-linux-create-upload-generic.md)) em um disco virtual no formato VHD. Existem várias ferramentas para criar uma VM e o VHD:
	- Instalar e configurar o [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou o [KVM](http://www.linux-kvm.org/page/RunningKVM), tomando o cuidado de usar o VHD como seu formato de imagem. Você pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando `qemu-img convert` se necessário.
	- Também pode usar o Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Não há suporte para o formato VHDX mais recente no Azure. Quando você criar uma VM, especifique o formato VHD original. Se necessário, você pode converter um VHDX no formato VHD usando [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou o[`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) cmdlet do PowerShell. Além disso, o Azure não dá suporte ao carregamento de VHDs dinâmicos, por isso você precisa converter esses discos para VHDs estáticos antes de carregar. Você pode usar ferramentas como o [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) para converter os discos dinâmicos durante o processo de carregamento no Azure.


<a id="prepimage"> </a>
## Preparar a imagem a ser carregada

O Azure dá suporte a várias distribuições do Linux (confira [Distribuições Endossadas](virtual-machines-linux-endorsed-distros.md)). Os artigos a seguir guiam você pela preparação das diversas distribuições do Linux com suporte no Azure. Verifique se você preparou corretamente seu disco virtual antes de carregar:

- **[Distribuições com base em CentOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES e openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Outros — Distribuições não endossadas](virtual-machines-linux-create-upload-generic.md)**

Veja também **[Observações de Instalação do Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** para obter mais dicas gerais sobre como preparar as imagens do Linux para o Azure.

> [AZURE.NOTE] O [SLA da plataforma do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) se aplica às VMs que executam o Linux somente quando uma das distribuições endossadas é usada com os detalhes da configuração, conforme especificado em 'Versões com Suporte' em [Linux nas Distribuições Endossadas](virtual-machines-linux-endorsed-distros.md).

## Próximas etapas
Após ter preparado e carregado o disco virtual personalizado, você pode ler mais sobre [usando o gerenciador de recursos e modelos](../resource-group-overview.md). É recomendável [adicionar um disco de dados](virtual-machines-linux-add-disk.md) às suas novas VMs. Se você tiver aplicativos em execução em suas VMs que precisa acessar, não esqueça de [abrir as portas e os pontos de extremidade](virtual-machines-linux-nsg-quickstart.md).

<!---HONumber=AcomDC_0713_2016-->