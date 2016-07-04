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
	ms.date="06/14/2016"
	ms.author="iainfou"/>

# Criando e carregando um disco rígido virtual que contém o sistema operacional Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.

Esse artigo mostra como carregar um disco rígido virtual (VHD) para que você o use como sua própria imagem para criar outras máquinas virtuais no Azure. Você aprenderá como preparar o sistema operacional para que você o use para criar outras máquinas virtuais com base nessa imagem.

**Importante**: o SLA da plataforma Azure aplica-se às máquinas virtuais que executam o sistema operacional Linux somente quando uma das distribuições endossadas é usada com os detalhes de configuração, como especificado nas “Versões com suporte” no [Linux em distribuições endossadas pelo Azure](virtual-machines-linux-endorsed-distros.md). Todas as distribuições do Linux fornecidas na galeria de imagens do Azure são distribuições endossadas com a configuração necessária.


## Pré-requisitos
Este artigo pressupõe que você tenha os seguintes itens:

- **Sistema operacional Linux instalado em um arquivo .vhd**: você instalou uma [distribuição Linux endossada pelo Azure](virtual-machines-linux-endorsed-distros.md) (ou consulte as [informações para distribuições não endossadas](virtual-machines-linux-create-upload-generic.md)) em um disco virtual no formato VHD. Existem várias ferramentas para criar arquivos .vhd. Por exemplo, você pode usar uma solução de virtualização como o Hyper-V para criar o arquivo .vhd e instalar o sistema operacional. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

	> [AZURE.NOTE] Não há suporte para o formato VHDX mais recente no Azure. É possível converter um VHDX para o formato VHD usando o Gerenciador do Hyper-V ou o cmdlet `Convert-VHD`. Além disso, o Azure não dá suporte ao carregamento de VHDs dinâmicos, por isso você precisa converter esses discos para VHDs estáticos antes de carregar. Você pode usar ferramentas como o [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) para converter discos dinâmicos.

- **Interface de Linha de Comando do Azure**: instale a [Interface de Linha de Comando do Azure](../virtual-machines-command-line-tools.md) mais recente para carregar o VHD.

<a id="prepimage"> </a>
## Etapa 1: preparar a imagem a ser carregada

O Azure dá suporte a várias distribuições do Linux (confira [Distribuições Endossadas](virtual-machines-linux-endorsed-distros.md)). Os artigos a seguir guiam você pela preparação das diversas distribuições do Linux com suporte no Azure. Após seguir as etapas descritas nos guias abaixo, volte a este ponto e você deverá ter um arquivo VHD pronto para upload no Azure:

- **[Distribuições com base em CentOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES e openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Outros — Distribuições não endossadas](virtual-machines-linux-create-upload-generic.md)**

Veja também **[Observações de instalação do Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** para obter mais dicas gerais sobre como preparar imagens do Linux para o Azure.


<a id="connect"> </a>
## Etapa 2: preparar a conexão com o Azure

Verifique se você está usando a CLI do Azure no modelo de implantação clássico (`azure config mode asm`) e faça logon na sua conta:

```
azure login
```


<a id="upload"> </a>
## Etapa 3: carregar a imagem no Azure

Você precisará de uma conta de armazenamento para carregar o arquivo do VHD. Você pode selecionar uma já existente ou [criar uma nova](../storage/storage-create-storage-account.md).

Use a CLI do Azure para carregar a imagem utilizando o seguinte comando:

		azure vm image create <ImageName> --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> --os Linux <PathToVHDFile>

No exemplo anterior:

- **BlobStorageURL** é a URL para a conta de armazenamento que você planeja usar
- **YourImagesFolder** é o contêiner dentro do armazenamento de blobs em que você deseja armazenar as imagens
- **VHDName** é o rótulo que aparece no portal para identificar o disco rígido virtual.
- **PathToVHDFile** é o caminho completo e o nome do arquivo .vhd em seu computador.

Para obter mais informações, consulte [Referência da CLI do Azure para o Gerenciamento de Serviços do Azure](../virtual-machines-command-line-tools.md).

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=AcomDC_0622_2016-->