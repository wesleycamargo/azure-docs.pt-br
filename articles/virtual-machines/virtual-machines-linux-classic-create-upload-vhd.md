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
	ms.date="04/12/2016"
	ms.author="iainfou"/>

# Criando e carregando um disco rígido virtual que contém o sistema operacional Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.

Esse artigo mostra como carregar um disco rígido virtual (VHD) para que você o use como sua própria imagem para criar outras máquinas virtuais no Azure. Você aprenderá como preparar o sistema operacional para que você o use para criar outras máquinas virtuais com base nessa imagem.

**Importante**: o SLA da plataforma Azure aplica-se às máquinas virtuais que executam o sistema operacional Linux somente quando uma das distribuições endossadas é usada com os detalhes de configuração, como especificado nas “Versões com suporte” no [Linux em distribuições endossadas pelo Azure](virtual-machines-linux-endorsed-distros.md). Todas as distribuições do Linux fornecidas na galeria de imagens do Azure são distribuições endossadas com a configuração necessária.


## Pré-requisitos
Este artigo pressupõe que você tenha os seguintes itens:

- **Sistema operacional Linux instalado em um arquivo .vhd** — Você instalou um sistema operacional Linux com suporte em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd — por exemplo, você pode usar uma solução de virtualização como o Hyper-V para criar o arquivo .vhd e instalar o sistema operacional. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

	> [AZURE.NOTE] Não há suporte para o formato VHDX mais recente no Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd.

Para obter uma lista de distribuições endossadas, consulte [Linux no Azure-Distribuições endossadas](virtual-machines-linux-endorsed-distros.md). Para obter uma lista geral das distribuições do Linux, consulte [Informações para distribuições não endossadas](virtual-machines-linux-create-upload-generic.md).

- **Interface de Linha de Comando do Azure**: instale e use a [Interface de Linha de Comando do Azure](../virtual-machines-command-line-tools.md) para carregar o VHD.

> [AZURE.TIP] O Azure não dá suporte ao carregamento de VHDs dinâmicos, por isso você precisa converter esses discos para VHDs estáticos antes de carregar. Você pode usar ferramentas como [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) para converter discos dinâmicos.

<a id="prepimage"> </a>
## Etapa 1: preparar a imagem a ser carregada

O Azure dá suporte a várias distribuições do Linux (confira [Distribuições Endossadas](virtual-machines-linux-endorsed-distros.md)). Os artigos a seguir guiam você pela preparação das diversas distribuições do Linux que têm suporte no Azure:

- **[Distribuições com base em CentOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES e openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Outros — Distribuições não endossadas](virtual-machines-linux-create-upload-generic.md)**

Veja também **[Observações de instalação do Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** para obter mais dicas sobre como preparar imagens do Linux para o Azure.

Após as etapas descritas nos guias acima, você deve ter um arquivo VHD pronto para upload no Azure.

<a id="connect"> </a>
## Etapa 2: preparar a conexão com o Azure

Verifique se você está usando a CLI do Azure no modelo de implantação clássico (`azure config mode asm`) e faça logon na sua conta:

```
azure login
```


<a id="upload"> </a>
## Etapa 3: carregar a imagem no Azure

Você precisará de uma conta de armazenamento para carregar o arquivo do VHD. Você pode selecionar um já existente ou criar um novo. Para criar uma conta de armazenamento, consulte [Crie uma conta de armazenamento](../storage/storage-create-storage-account.md).

Quando carrega o arquivo .vhd, você pode colocá-lo em qualquer lugar no seu armazenamento de blob. Nos exemplos de comando a seguir, **BlobStorageURL** é a URL da conta de armazenamento que você pretende usar e **YourImagesFolder** é o contêiner no armazenamento de blobs no qual deseja armazenar as imagens. **VHDName** é o rótulo que aparece no [Portal do Azure](http://portal.azure.com) ou no [Portal Clássico do Azure](http://manage.windowsazure.com) para identificar o disco rígido virtual. **PathToVHDFile** é o caminho completo e o nome do arquivo .vhd em seu computador.

Use a CLI do Azure para carregar a imagem utilizando o seguinte comando:

		azure vm image create <ImageName> --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> --os Linux <PathToVHDFile>

Para obter mais informações, consulte [Referência da CLI do Azure para o Gerenciamento de Serviços do Azure](../virtual-machines-command-line-tools.md).

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=AcomDC_0518_2016-->