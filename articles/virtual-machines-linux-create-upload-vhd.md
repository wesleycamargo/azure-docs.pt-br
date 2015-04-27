<properties 
	pageTitle="Criar e carregar um VHD Linux no Azure" 
	description="Saiba como criar e carregar um VHD (disco rígido virtual) do Azure que contenha o sistema operacional Linux." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="kathydav, szarkos"/>

# Criando e carregando um disco rígido virtual que contém o sistema operacional Linux

Esse artigo mostra como carregar um disco rígido virtual (VHD) para que você o use como sua própria imagem para criar outras máquinas virtuais no Azure. Você aprenderá como preparar o sistema operacional para que você o use para criar outras máquinas virtuais com base nessa imagem.  

> [AZURE.NOTE] Não é necessária nenhuma experiência com VMs do Azure para concluir estas etapas nesse artigo. Porém, você precisa de uma conta do Azure. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/). 

Uma máquina virtual no Azure executa o sistema operacional com base na imagem que você escolheu ao criar a máquina virtual. Sua imagens estão guardadas em formato VHD, em arquivos .vhd em uma conta de armazenamento. Para obter mais informações sobre discos e imagens no Azure, consulte [Gerenciar discos e imagens (a página pode estar em inglês)](http://msdn.microsoft.com/library/windowsazure/jj672979.aspx).

Ao criar uma máquina virtual, você pode personalizar as configurações do sistema operacional para que sejam apropriadas ao aplicativo que você deseja executar. Para obter instruções, consulte [Como criar uma máquina virtual personalizada](/pt-br/manage/windows/how-to-guides/custom-create-a-vm/).

**Importante**: O SLA da plataforma do Azure se aplica a máquinas virtuais com o sistema operacional Linux somente quando uma das distribuições endossadas é usada com os detalhes da configuração especificados [neste artigo](http://support.microsoft.com/kb/2805216). Todas as distribuições do Linux fornecidas na galeria de imagens do Azure são distribuições endossadas com a configuração necessária.


##Pré-requisitos##
Este artigo pressupõe que você tenha os seguintes itens:

- **Um certificado de gerenciamento** - Você criou um certificado de gerenciamento para a assinatura para a qual você deseja carregar um VHD e exportou o certificado para um arquivo .cer. Para obter mais informações sobre como criar certificados, consulte [Criar um certificado de gerenciamento para o Azure (a página pode estar em inglês)](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx). 

- **Sistema operacional Linux instalado em um arquivo .vhd**  - Você instalou um sistema operacional Linux com suporte em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd - por exemplo, você pode usar uma solução de virtualização como o Hyper-V para criar o arquivo .vhd e instalar o sistema operacional. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina virtual](http://technet.microsoft.com/library/hh846766.aspx). 

	**Importante**: Não há suporte para o formato VHDX mais recente no Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd.

	Para obter uma lista de distribuições endossadas, consulte [Linux no Azure-Distribuições endossadas](linux-endorsed-distributions.md). Como alternativa, consulte a seção no final deste artigo para obter [informações para distribuições não endossadas](virtual-machines-linux-create-upload-vhd-generic.md).

- **Ferramenta de linha de comando do Linux Azure** - Se você estiver usando um sistema operacional Linux para criar sua imagem, use as [ferramentas de linha de comando do Azure para Linux e Mac](http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409) para carregar o VHD.

- **Ferramentas do PowerShell do Azure** - o cmdlet `Add-AzureVhd` também pode ser usado para carregar o VHD. Visite a página de [downloads do Azure](http://azure.microsoft.com/downloads/) para baixar os cmdlets do PowerShell do Azure. Para obter informações de referência, consulte [Add-AzureVhd (a página pode estar em inglês)](http://msdn.microsoft.com/library/windowsazure/dn495173.aspx).


Esta tarefa inclui as seguintes etapas:

- [Etapa 1: Preparar a imagem a ser carregada] []
- [Etapa 2: Criar uma conta de armazenamento no Azure] []
- [Etapa 3: Preparar a conexão com o Azure] []
- [Etapa 4: Carregar a imagem no Azure] []

## <a id="prepimage"> </a>Etapa 1: Preparar a imagem a ser carregada ##

O Microsoft Azure dá suporte a diversas distribuições Linux (confira [Distribuições endossadas](linux-endorsed-distributions.md)). Os artigos a seguir guiam você pela preparação das diversas distribuições do Linux que têm suporte no Azure:

- **[Distribuições com base em CentOS](virtual-machines-linux-create-upload-vhd-centos.md)**
- **[Oracle Linux](virtual-machines-linux-create-upload-vhd-oracle.md)**
- **[SLES e openSUSE](virtual-machines-linux-create-upload-vhd-suse.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)**
- **[Outros - Distribuições não endossadas](virtual-machines-linux-create-upload-vhd-generic.md)**

Consulte também as **[observações de instalação Linux](../virtual-machines-linux-create-upload-vhd-generic/#linuxinstall)** para obter mais dicas sobre como preparar imagens do Linux do Azure.

Após as etapas descritas nos guias acima, você deve ter um arquivo VHD pronto para carregamento no Azure.


## <a id="createstorage"> </a>Etapa 2: Criar uma conta de armazenamento no Azure ##

A conta de armazenamento representa o mais alto nível do namespace para acessar os serviços de armazenamento e está associada à assinatura do Azure. Você precisa de uma conta de armazenamento no Azure para carregar um arquivo .vhd no Azure que pode ser usado para a criação de uma máquina virtual. Você pode criar uma conta de armazenamento usando o Portal de Gerenciamento do Azure.

1. Entre no Portal de Gerenciamento do Azure.

2. Na barra de comandos, clique em **Novo**.

	![Create storage account](./media/virtual-machines-linux-create-upload-vhd/create.png)

3. Clique em **Conta de Armazenamento** e clique em **Criação Rápida**.

	![Quick create a storage account](./media/virtual-machines-linux-create-upload-vhd/storage-quick-create.png)

4. Preencha os campos da seguinte maneira:

	![Enter storage account details](./media/virtual-machines-linux-create-upload-vhd/storage-create-account.png)

- Em **URL**, digite um nome de subdomínio para usar no URL para a conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas e números. Esse nome se torna o nome do host na URL que é usada para lidar com os recursos Blob, Fila ou Tabela da assinatura.
	
- Escolha o local ou o grupo de afinidade da conta de armazenamento. Especificando um grupo de afinidade, você pode localizar seus serviços de nuvem no mesmo data center com seu armazenamento.
 
- Escolha entre usar a replicação geográfica ou não para a conta de armazenamento. A replicação geográfica é ativada por padrão. Essa opção replica os dados para um local secundário, sem nenhum custo para você, para que seja feito o failover para um local secundário em caso de uma falha grave que não possa ser tratada no local primário. O local secundário é atribuído automaticamente e não pode ser alterado. Se os requisitos legais ou a política organizacional exigir um controle mais restrito do local de seu armazenamento baseado em nuvem, você poderá desligar a replicação geográfica. No entanto, saiba que ao ativar a replicação geográfica posteriormente, você deverá pagar uma taxa de transferência de dados uma única vez para replicar seus dados existentes para o local secundário. Serviços de armazenamento sem replicação geográfica são oferecidos com desconto.

5. Clique em **Criar Conta de Armazenamento**.

	A conta é listada em **Contas de Armazenamento**.

	![Storage account successfully created](./media/virtual-machines-linux-create-upload-vhd/Storagenewaccount.png)


## <a id="connect"> </a>Etapa 3: Preparar a conexão com o Azure ##

Para poder carregar um arquivo .vhd, você precisa estabelecer uma conexão segura entre seu computador e sua assinatura do Azure. 

1. Abra uma janela do PowerShell do Azure.

2. Digite: 

	`Get-AzurePublishSettingsFile`

	Esse comando abre uma janela de navegador e baixa automaticamente um arquivo .publishsettings que contém informações e um certificado de sua assinatura do Azure. 

3. Salve o arquivo .publishsettings. 

4. Digite:

	`Import-AzurePublishSettingsFile <PathToFile>`

	Em que `<CaminhoParaOArquivo>` é o caminho completo para o arquivo .publishsettings. 

	Para obter mais informações, consulte [Introdução aos cmdlets do Azure](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx) 


## <a id="upload"> </a>Etapa 4: Carregar a imagem no Azure ##

Quando carrega o arquivo .vhd, você pode colocá-lo em qualquer lugar no seu armazenamento de blob. Nos exemplos de comando a seguir, **BlobStorageURL** é a URL da conta de armazenamento que você criou na Etapa 2, **YourImagesFolder** é o contêiner no armazenamento de blob onde você deseja armazenar as imagens. **VHDName** é o rótulo que aparece no Portal de Gerenciamento para identificar o disco rígido virtual. **PathToVHDFile** é o caminho completo e o nome do arquivo .vhd. 

Faça uma das opções a seguir:

- Na janela PowerShell do Azure que você usou na etapa anterior, digite:

		`Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

	Para obter mais informações, consulte [Add-AzureVhd (a página pode estar em inglês)](http://msdn.microsoft.com/library/windowsazure/dn205185.aspx).

- Use a ferramenta de linha de comando do Linux para carregar a imagem. Você pode carregar uma imagem usando o seguinte comando:

		# azure vm image create <image-name> --location <location-of-the-data-center> --os Linux <source-path-to the vhd>



[Etapa 1: Preparar a imagem a ser carregada]: #prepimage
[Etapa 2: Criar uma conta de armazenamento no Azure]: #createstorage
[Etapa 3: Preparar a conexão com o Azure]: #connect
[Etapa 4: Carregar a imagem no Azure]: #upload


<!--HONumber=45--> 
