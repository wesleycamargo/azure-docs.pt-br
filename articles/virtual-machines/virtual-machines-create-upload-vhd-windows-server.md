<properties
	pageTitle="Criar e carregar um VHD do Windows Server usando o PowerShell | Microsoft Azure"
	description="Saiba como criar e carregar um disco rígido virtual (VHD) baseado no Windows Server usando o modelo de implantação clássico e o Azure PowerShell."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/10/2015"
	ms.author="cynthn"/>

# Criar e carregar um VHD do Windows Server no Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


Esse artigo mostra como carregar um disco rígido virtual (VHD) com um sistema operacional para que você o use como modelo para criar outras máquinas virtuais com base nessa imagem. Para mais detalhes sobre discos e os VHDs no Microsoft Azure, confira a seção [Sobre discos e VHDs para Máquinas Virtuais](virtual-machines-disks-vhds.md).



## Pré-requisitos

Este artigo supõe que você tem:

1. **Uma assinatura do Azure** – se não tiver uma, você poderá [abrir uma conta do Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F): obtenha créditos que você pode usar para experimentar os serviços pagos do Azure e, mesmo depois que eles forem usados, você poderá manter a conta e usar serviços gratuitos do Azure, como sites. Seu cartão de crédito não será cobrado, a menos que você altere explicitamente suas configurações, solicitando esse tipo de cobrança. Você também pode [ativar benefícios para assinantes do MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): todos os meses, sua assinatura do MSDN oferece créditos que podem ser usados para serviços pagos do Azure.

2. **PowerShell do Microsoft Azure ** – você tem o módulo do PowerShell do Microsoft Azure instalado e configurado para usar sua assinatura. Para baixar o módulo, consulte [Downloads do Microsoft Azure](http://azure.microsoft.com/downloads/). Um tutorial para instalar e configurar o módulo está disponível [aqui](../powershell-install-configure.md). Você usará o cmdlet [Add-AzureVHD](http://msdn.microsoft.com/library/azure/dn495173.aspx) para carregar o VHD.

3. **Um sistema operacional Windows para o qual há suporte, armazenado em um arquivo .vhd e anexado a uma máquina virtual** - existem várias ferramentas para criar arquivos .vhd. Por exemplo, você pode usar o Hyper-V para criar uma máquina virtual e instalar o sistema operacional. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina virtual](http://technet.microsoft.com/library/hh846766.aspx). Para obter detalhes sobre os sistemas operacionais, consulte [Suporte do software de servidor Microsoft para máquinas virtuais do Microsoft Azure](http://go.microsoft.com/fwlink/p/?LinkId=393550).

> [AZURE.IMPORTANT]Não há suporte para o formato VHDX no Microsoft Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o [cmdlet convert-vhd](http://technet.microsoft.com/library/hh848454.aspx). Consulte esta [publicação de blog](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx) para obter detalhes.

## Etapa 1: preparar o VHD 

Antes de carregar o VHD no Azure, ele precisa ser generalizado usando a ferramenta Sysprep. Ela prepara o VHD a ser usado como uma imagem. Para obter detalhes sobre o Sysprep, consulte [Como usar o Sysprep: uma introdução](http://technet.microsoft.com/library/bb457073.aspx).

Na máquina virtual na qual o sistema operacional foi instalado, conclua o procedimento a seguir:

1. Entre no sistema operacional.

2. Abra uma janela de prompt de comando como administrador. Altere o diretório para **%windir%\\system32\\sysprep** e, a seguir, execute `sysprep.exe`.

	![Abrir una janela de Prompt de comando](./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png)

3.	A caixa de diálogo **Ferramenta de Preparação do Sistema** é aberta.

	![Inicie o Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  Em **Ferramenta de Preparação do Sistema**, selecione **Entrar no Sistema OOBE** e verifique se a opção **Generalizar** está marcada.

5.  Em **Opções de Desligamento**, selecione **Desligar**.

6.  Clique em **OK**.

## Etapa 2: criar ou obter informações sobre a sua conta de armazenamento do Azure

Você precisa de uma conta de armazenamento no Azure para que você tenha um local para carregar o arquivo .vhd. Esta etapa mostra como criar uma conta, ou obter as informações de que você precisa de uma conta existente.

### Opção 1: criar uma conta de armazenamento

1. Entre no Portal do Azure.

2. Na barra de comandos, clique em **Nova**.

3. Clique em **Serviços de Dados** > **Armazenamento** > **Criação Rápida**.

	![Criação rápida de uma conta de armazenamento](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. Preencha os campos da seguinte maneira:

 - Em **URL**, digite um nome de subdomínio para usar no URL para a conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas e números. Esse nome se torna o nome do host na URL que é usada para acessar os recursos de blob, fila ou tabela para a assinatura.
 - Escolha o **local ou o grupo de afinidade** da conta de armazenamento. Um grupo de afinidades permite colocar seus serviços de nuvem e armazenamento no mesmo data center.
 - Escolha entre usar a **replicação geográfica** ou não para a conta de armazenamento. A replicação geográfica é ativada por padrão. Essa opção replica os dados para um local secundário, sem nenhum custo para você, para que o armazenamento efetue o failover para o local se ocorrer uma falha grave no local principal. O local secundário é atribuído automaticamente e não pode ser alterado. Se você precisar de mais controle sobre o local do armazenamento baseado em nuvem devido a requisitos legais ou política organizacional, você pode desligar a replicação geográfica. No entanto, se você ativar a replicação geográfica posteriormente, você deverá pagar uma taxa de transferência de dados de uma única vez para replicar seus dados existentes para o local secundário. Serviços de armazenamento sem replicação geográfica são oferecidos com desconto. Para obter mais detalhes, confira [Criar, gerenciar ou excluir uma conta de armazenamento](../storage-create-storage-account/#replication-options).

      ![Insira os detalhes da conta de armazenamento](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)

5. Clique em **Criar Conta de Armazenamento**. A conta aparece agora em **Armazenamento**.

	![Conta de armazenamento criada com êxito](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. Em seguida, crie um contêiner para os seus VHDs carregados. Clique no nome da conta de armazenamento e, em seguida, clique em **Contêineres**.

	![Detalhes da conta de armazenamento](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. Clique em**Criar um contêiner**.

	![Detalhes da conta de armazenamento](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. Digite um **Nome** para o contêiner e selecione a política de **Acesso**.

	![Nome do contêiner](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

	> [AZURE.NOTE]Por padrão, o contêiner é privado e pode ser acessado apenas pelo proprietário da conta. Para permitir acesso de leitura público dos blobs no contêiner, mas não das propriedades ou metadados do contêiner, use a opção **Blob Público**. Para permitir o acesso de leitura público completo do contêiner e de blobs, use a opção **Contêiner Público**.

### Opção 2: obter as informações de conta de armazenamento

1.	Entre no Portal do Azure.

2.	No painel de navegação, clique em **Armazenamento**.

3.	Clique no nome da conta de armazenamento e, em seguida, clique em **Painel**.

4.	No painel, em **Serviços**, passe o mouse sobre a URL de Blobs, clique no ícone da área de transferência para copiar a URL e, em seguida, cole-a e salve-a. Você usará essa URL ao criar o comando para carregar o VHD.

## Etapa 3: conectar-se à sua assinatura do Azure PowerShell

Para poder carregar um arquivo .vhd, você precisa estabelecer uma conexão segura entre seu computador e sua assinatura do Azure. No entanto, você pode usar o método Active Directory do Microsoft Azure ou o método de certificado para realizar esse tipo de trabalho.

> [AZURE.TIP]Para começar com o PowerShell do Azure, confira [Como instalar e configurar o Microsoft Azure PowerShell](../install-configure-powershell.md). Para saber mais, confira [Introdução aos Cmdlets do Microsoft Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

### Opção 1: usar o AD do Microsoft Azure

1. Abra o console do PowerShell do Azure.

2. Digite: `Add-AzureAccount`

3.	Nas janelas de entrada, digite o nome de usuário e a senha da sua conta corporativa ou de estudante.

4. O Azure autentica e salva as informações de credenciais e, em seguida, fecha a janela.

### Opção 2: usar um certificado

1. Abra o console do PowerShell do Azure.

2.	Digite: `Get-AzurePublishSettingsFile`.

3. Uma janela de navegador será aberta e baixará automaticamente um arquivo .publishsettings. Esse arquivo contém informações e um certificado de sua assinatura do Microsoft Azure.

	![Procurar página de download](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. Salve o arquivo .publishsettings.

4. Digite: `Import-AzurePublishSettingsFile <PathToFile>`

	Em que `<PathToFile>` é o caminho completo para o arquivo .publishsettings.

## Etapa 4: carregar o arquivo .vhd

Quando carrega o arquivo .vhd, você pode colocá-lo em qualquer lugar no seu armazenamento de blob.

1. Na janela do Azure PowerShell que você usou na etapa anterior, digite um comando parecido com esse:

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`

	Em que: - **BlobStorageURL** é a URL da conta de armazenamento - **YourImagesFolder** é o contêiner dentro do armazenamento de blobs no qual você deseja armazenar as imagens - **VHDName** é o nome que você deseja que o portal do Azure exiba para identificar o disco rígido virtual - **PathToVHDFile** é o nome e caminho completo do arquivo .vhd

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png)

Para obter mais informações sobre os cmdlets do Add-AzureVhd, consulte [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx).

## Etapa 5: adicionar a imagem à sua lista de imagens personalizadas

> [AZURE.TIP]Para usar o Azure PowerShell em vez do portal do Azure para adicionar a imagem, use o cmdlet **Add-AzureVMImage**. Por exemplo:

>	`Add-AzureVMImage -ImageName <ImageName> -MediaLocation <VHDLocation> -OS <OSType>`

1. No portal do Azure, sob a opção **Todos os Itens**, clique em **Máquinas Virtuais**.

2. Em Máquinas Virtuais, clique em **Imagens**.

3. Clique em **Criar uma imagem**.

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png)

4. Na janela **Criar uma imagem a partir de um VHD**:

	- Especifique o **nome**.

	- Especifique a **descrição**.

	- Em **URL do VHD**, clique no ícone de pasta para abrir a janela **Procurar Armazenamento em Nuvem**. Localize o arquivo .vhd e, em seguida, clique em **Abrir**.

    ![Selecione VHD](./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png)

5.	Na janela **Criar uma imagem a partir de um VHD**, em **Família do Sistema Operacional**, selecione seu sistema operacional. Marque **Executei o Sysprep na máquina virtual associada a este VHD** para confirmar que você generalizou o sistema operacional, então clique em **OK**.

    ![Adicionar uma imagem](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png)

6. Após completar as etapas anteriores, a nome imagem será listada ao escolher a guia **Imagens**.

	![imagem personalizada](./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png)

	Essa nova imagem agora está disponível em **Minhas Imagens** quando você cria uma máquina virtual. Para obter instruções, confira [Como criar uma máquina virtual personalizada executando o Windows](virtual-machines-windows-create-custom.md).

	![crie uma VM da imagem personalizada](./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png)

	> [AZURE.TIP]Se você receber um erro ao tentar criar uma VM, com essa mensagem de erro "O VHD https://XXXXX...tem um tamanho virtual sem suporte de bytes aaaa. O tamanho deve ser um número inteiro (em MB),” significa que o VHD não tem um número inteiro de MB e precisa ser um VHD de tamanho fixo. Tente usar o cmdlet **Add-AzureVMImage** do PowerShell em vez do Portal do Azure para adicionar a imagem (consulte a etapa 5, acima). Os cmdlets do Azure asseguram que o VHD atende aos requisitos do Azure.

## Próximas etapas ##

Após criar uma máquina virtual, tente criar uma máquina virtual do SQL Server. Para instruções, consulte [Provisionando uma máquina virtual do SQL Server no Microsoft Azure](virtual-machines-provision-sql-server.md).

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Create a storage account in Azure]: #createstorage
[Step 3: Prepare the connection to Azure]: #prepAzure
[Step 4: Upload the .vhd file]: #upload

<!---HONumber=Nov15_HO3-->