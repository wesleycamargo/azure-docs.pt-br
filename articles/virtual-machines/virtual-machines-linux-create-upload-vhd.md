<properties
	pageTitle="Criar e carregar um VHD do Linux | Microsoft Azure"
	description="Crie e carregue um VHD (disco rígido virtual) do Azure com o modelo de implantação clássico que contém o sistema operacional Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/04/2015"
	ms.author="dkshir"/>

# Criando e carregando um disco rígido virtual que contém o sistema operacional Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


Esse artigo mostra como carregar um disco rígido virtual (VHD) para que você o use como sua própria imagem para criar outras máquinas virtuais no Azure. Você aprenderá como preparar o sistema operacional para que você o use para criar outras máquinas virtuais com base nessa imagem.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Uma máquina virtual no Azure executa o sistema operacional com base na imagem que você escolheu ao criar a máquina virtual. Sua imagens estão guardadas em formato VHD, em arquivos .vhd em uma conta de armazenamento. Para obter detalhes, consulte [Discos no Azure](virtual-machines-disks-vhds.md) e [Imagens no Azure](virtual-machines-images.md).

Ao criar uma máquina virtual, você pode personalizar as configurações do sistema operacional para que sejam apropriadas ao aplicativo que você deseja executar. Para obter instruções, consulte [Como criar uma máquina virtual personalizada](virtual-machines-create-custom.md).

**Importante**: o SLA da plataforma Azure aplica-se às máquinas virtuais que executam o sistema operacional Linux somente quando uma das distribuições endossadas é usada com os detalhes de configuração, como especificado nas “Versões com suporte” no [Linux em distribuições endossadas pelo Azure](virtual-machines-../linux-endorsed-distributions.md). Todas as distribuições do Linux fornecidas na galeria de imagens do Azure são distribuições endossadas com a configuração necessária.


## Pré-requisitos
Este artigo pressupõe que você tenha os seguintes itens:

- **Um certificado de gerenciamento** - Você criou um certificado de gerenciamento para a assinatura para a qual você deseja carregar um VHD e exportou o certificado para um arquivo .cer. Para obter mais informações sobre criação de certificados, consulte [Visão geral dos certificados do Azure](../cloud-services/cloud-services-certs-create.md).

- **Sistema operacional Linux instalado em um arquivo .vhd** — Você instalou um sistema operacional Linux com suporte em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd — por exemplo, você pode usar uma solução de virtualização como o Hyper-V para criar o arquivo .vhd e instalar o sistema operacional. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

	**Importante**: não há suporte para o formato VHDX mais recente no Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd.

	Para obter uma lista de distribuições endossadas, consulte [Linux no Azure-Distribuições endossadas](../linux-endorsed-distributions.md). Para obter uma lista geral das distribuições do Linux, consulte [Informações para distribuições não endossadas](virtual-machines-linux-create-upload-vhd-generic.md).

- **Interface de Linha de Comando do Azure** – Se estiver usando um sistema operacional Linux para criar a imagem, use a [Interface de Linha de Comando do Azure](../virtual-machines-command-line-tools.md) para carregar o VHD.

- **Ferramentas do Azure PowerShell** – O cmdlet `Add-AzureVhd` também pode ser usado para carregar o VHD. Visite a página [Downloads do Azure](http://azure.microsoft.com/downloads/) para baixar os cmdlets do Azure PowerShell. Para obter informações de referência, consulte [Add-AzureVhd (a página pode estar em inglês)](https://msdn.microsoft.com/library/azure/dn495173.aspx).

<a id="prepimage"> </a>
## Etapa 1: preparar a imagem a ser carregada

O Azure dá suporte a várias distribuições do Linux (confira [Distribuições Endossadas](../linux-endorsed-distributions.md)). Os artigos a seguir guiam você pela preparação das diversas distribuições do Linux que têm suporte no Azure:

- **[Distribuições com base em CentOS](virtual-machines-linux-create-upload-vhd-centos.md)**
- **[Oracle Linux](virtual-machines-linux-create-upload-vhd-oracle.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-create-upload-vhd-redhat.md)**
- **[SLES e openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)**
- **[Outros — Distribuições não endossadas](virtual-machines-linux-create-upload-vhd-generic.md)**

Veja também **[Observações de instalação do Linux](virtual-machines-linux-create-upload-vhd-generic.md#linuxinstall)** para obter mais dicas sobre como preparar imagens do Linux para o Azure.

Após as etapas descritas nos guias acima, você deve ter um arquivo VHD pronto para upload no Azure.

<a id="connect"> </a>
## Etapa 2: preparar a conexão com o Azure

Para poder carregar um arquivo .vhd, você precisa estabelecer uma conexão segura entre seu computador e sua assinatura do Azure.


### Se estiver usando a CLI do Azure

A CLI do Azure mais recente utiliza por padrão o modelo de implantação do Gerenciador de recursos, então verifique se você está no modelo de implantação clássico usando este comando:

		azure change mode asm  

Em seguida, use qualquer um dos seguintes métodos de logon para conectar-se à sua assinatura do Azure.

Use o método do AD do Azure para fazer logon:

1. Abra uma janela da CLI do Azure

2. Digite:

	`azure login`

	Quando solicitado, digite seu nome de usuário e senha.

**OU** use um arquivo PublishSettings:

1. Abra uma janela da CLI do Azure

2. Digite:

	`azure account download`

	Esse comando abre uma janela de navegador e baixa automaticamente um arquivo .publishsettings que contém informações e um certificado de sua assinatura do Azure.

3. Salve o arquivo .publishsettings

4. Digite:

	`azure account import <PathToFile>`

	Em que `<PathToFile>` é o caminho completo para o arquivo .publishsettings.

	Para obter mais informações, leia [Conectar-se ao Azure a partir da CLI do Azure](../xplat-cli-connect.md).


### Se estiver usando o PowerShell do Azure

Use o método do AD do Azure para fazer logon:

1. Abra uma janela do PowerShell do Azure.

2. Digite:

	`Add-AzureAccount`

	Quando solicitado, insira seu id de usuário e senha organizacionais.

**OU** use os arquivos PublishSettings:

1. Abra uma janela do PowerShell do Azure.

2. Digite:

	`Get-AzurePublishSettingsFile`

	Esse comando abre uma janela de navegador e baixa automaticamente um arquivo .publishsettings que contém informações e um certificado de sua assinatura do Azure.

3. Salve o arquivo .publishsettings.

4. Digite:

	`Import-AzurePublishSettingsFile <PathToFile>`

	Em que `<PathToFile>` é o caminho completo para o arquivo .publishsettings.

	(Para obter mais informações, consulte [Como instalar e configurar o Azure PowerShell](powershell-install-configure.md)):

> [AZURE.NOTE]Recomendamos que você use o método mais recente do Active Directory do Azure para fazer logon na sua assinatura do Azure, CLI do Azure ou Azure PowerShell.

<a id="upload"> </a>
## Etapa 3: carregar a imagem no Azure

### Se estiver usando a CLI do Azure

Use a CLI do Azure para carregar a imagem. Você pode carregar uma imagem usando o seguinte comando:

		azure vm image create <image-name> --location <location-of-the-data-center> --os Linux <source-path-to the vhd>

### Se estiver usando o PowerShell

Você precisará de uma conta de armazenamento para carregar o arquivo do VHD. Você pode selecionar um já existente ou criar um novo. Para criar uma conta de armazenamento, veja [Criar uma conta de armazenamento](../storage-create-storage-account.md)

Quando carrega o arquivo .vhd, você pode colocá-lo em qualquer lugar no seu armazenamento de blob. Nos exemplos de comando a seguir, **BlobStorageURL** é a URL da conta de armazenamento que você pretende usar e **YourImagesFolder** é o contêiner no armazenamento de blobs no qual deseja armazenar as imagens. **VHDName** é o rótulo que aparece no [Portal de Gerenciamento](http://manage.windowsazure.com) para identificar o disco rígido virtual. **PathToVHDFile** é o caminho completo e o nome do arquivo .vhd.

Na janela PowerShell do Azure que você usou na etapa anterior, digite:

		Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>

Para obter mais informações, consulte [Add-AzureVhd (a página pode estar em inglês)](https://msdn.microsoft.com/library/azure/dn495173.aspx).

> [AZURE.NOTE]A [Versão de visualização do Azure Powershell 1.0](https://azure.microsoft.com/pt-BR/blog/azps-1-0-pre/) altera significativamente a maneira como ele manipula cmdlets para o modelo de implantação clássico e o modelo de implantação do Gerenciador de Recursos. Este artigo ainda não usa a versão de visualização.


[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=Nov15_HO4-->