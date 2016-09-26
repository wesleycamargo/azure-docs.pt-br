<properties
	pageTitle="Criar e carregar um VHD do Linux | Microsoft Azure"
	description="Crie e carregue um VHD (disco rígido virtual) do Azure com o modelo de implantação clássico que contém o sistema operacional Linux."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	ms.author="iainfou"/>

# Criando e carregando um disco rígido virtual que contém o sistema operacional Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Você também pode [carregar uma imagem de disco personalizada usando o Azure Resource Manager](virtual-machines-linux-upload-vhd.md).

Esse artigo mostra como carregar um disco rígido virtual (VHD) para que você o use como sua própria imagem para criar outras máquinas virtuais no Azure. Saiba como preparar o sistema operacional para que você o use para criar outras máquinas virtuais com base nessa imagem.

>  [AZURE.NOTE] Se tiver alguns minutos, ajude-nos a melhorar a documentação das VMs do Linux do Azure respondendo a essa [pesquisa rápida](https://aka.ms/linuxdocsurvey) sobre suas experiências. Cada resposta nos ajuda a realizar seu trabalho.

## Pré-requisitos
Este artigo pressupõe que você tenha os seguintes itens:

- **Sistema operacional Linux instalado em um arquivo .vhd**: você instalou uma [distribuição Linux endossada pelo Azure](virtual-machines-linux-endorsed-distros.md) (ou consulte as [informações para distribuições não endossadas](virtual-machines-linux-create-upload-generic.md)) em um disco virtual no formato VHD. Existem várias ferramentas para criar uma VM e o VHD:
	- Instalar e configurar o [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou o [KVM](http://www.linux-kvm.org/page/RunningKVM), tomando o cuidado de usar o VHD como seu formato de imagem. Se necessário, você pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando `qemu-img convert`.
	- Também pode usar o Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Não há suporte para o formato VHDX mais recente no Azure. Ao criar uma VM, especifique VHD como o formato. Se necessário, será possível converter discos VHDX para VHD usando o cmdlet [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) do PowerShell. Além disso, o Azure não dá suporte ao carregamento de VHDs dinâmicos, por isso você precisa converter esses discos para VHDs estáticos antes de carregar. Você pode usar ferramentas como o [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) para converter os discos dinâmicos durante o processo de carregamento no Azure.

- **Interface de Linha de Comando do Azure**: instale a [Interface de Linha de Comando do Azure](../virtual-machines-command-line-tools.md) mais recente para carregar o VHD.

<a id="prepimage"> </a>
## Etapa 1: preparar a imagem a ser carregada

O Azure dá suporte a várias distribuições do Linux (consulte [Distribuições endossadas](virtual-machines-linux-endorsed-distros.md)). Os seguintes artigos explicam como preparar as diversas distribuições Linux com suporte no Azure. Após concluir as etapas descritas nos guias abaixo, volte a este ponto quando tiver um arquivo VHD pronto para upload no Azure:

- **[Distribuições com base em CentOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES e openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Outros — Distribuições não endossadas](virtual-machines-linux-create-upload-generic.md)**

> [AZURE.NOTE] O SLA da plataforma Azure aplica-se às máquinas virtuais que executam o sistema operacional Linux somente quando uma das distribuições endossadas é usada com os detalhes de configuração, como especificado nas “Versões com suporte” no [Linux em distribuições endossadas pelo Azure](virtual-machines-linux-endorsed-distros.md). Todas as distribuições do Linux fornecidas na galeria de imagens do Azure são distribuições endossadas com a configuração necessária.

Veja também as **[Observações de instalação do Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** para obter mais dicas gerais sobre como preparar as imagens do Linux para o Azure.


<a id="connect"> </a>
## Etapa 2: preparar a conexão com o Azure

Verifique se você está usando a CLI do Azure no modelo de implantação clássico (`azure config mode asm`) e faça logon na sua conta:

```
azure login
```


<a id="upload"> </a>
## Etapa 3: carregar a imagem no Azure

Você precisa de uma conta de armazenamento para carregar o arquivo do VHD. Você pode escolher uma conta de armazenamento existente ou [criar uma nova](../storage/storage-create-storage-account.md).

Use a CLI do Azure para carregar a imagem utilizando o seguinte comando:

```bash
azure vm image create <ImageName> `
	--blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
	--os Linux <PathToVHDFile>
```

No exemplo anterior:

- **BlobStorageURL** é a URL para a conta de armazenamento que você planeja usar
- **YourImagesFolder** é o contêiner dentro do Armazenamento de Blobs em que você deseja armazenar as imagens
- **VHDName** é o rótulo que aparece no portal para identificar o disco rígido virtual.
- **PathToVHDFile** é o caminho completo e o nome do arquivo .vhd em seu computador.

Veja a seguir um exemplo completo:

```bash
azure vm image create UbuntuLTS `
	--blob-url https://teststorage.blob.core.windows.net/vhds/UbuntuLTS.vhd `
	--os Linux /home/ahmet/UbuntuLTS.vhd
```

## Etapa 4: criar uma VM da imagem
Crie uma VM usando o `azure vm create` da mesma forma que faria com uma VM comum. Especifique o nome que você deu à imagem na etapa anterior. No exemplo a seguir, usamos o nome de imagem **UbuntuLTS** fornecido na etapa anterior:

```bash
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
	--location "West US" "DeployedUbuntu" UbuntuLTS
```

Para criar suas próprias VMs, forneça seu próprio nome de usuário + senha, localização, nome DNS e nome da imagem.

## Próximas etapas

Para obter mais informações, consulte [Referência da CLI do Azure para o modelo de implantação clássico do Azure](../virtual-machines-command-line-tools.md).

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=AcomDC_0914_2016-->