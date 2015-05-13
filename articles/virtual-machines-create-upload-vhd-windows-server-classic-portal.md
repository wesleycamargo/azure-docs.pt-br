\<properties pageTitle="Criar e carregar um VHD do Windows Server no Azure" description="Saiba como criar e carregar um VHD \(disco rígido virtual\) no Azure com o sistema operacional Windows Server." services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor="tysonn"/ tags="AZURE CLASSIC PORTAL"/\>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="kathydav"/>


#Criar e carregar um VHD do Windows Server no Portal Clássico do Azure#

Esse artigo mostra como carregar um disco rígido virtual \(VHD\) com um sistema operacional para que você o use como modelo para criar outras máquinas virtuais com base nessa imagem. Para obter mais informações sobre discos e imagens no Microsoft Azure, consulte [Gerenciar discos e imagens no Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

> [AZURE.NOTE]Quando você cria uma máquina virtual baseada em uma imagem, você pode personalizar as configurações do sistema operacional conforme apropriado para os aplicativos que você planeja executar na máquina virtual. Essa configuração é salva para a máquina virtual e não afeta a imagem. Para obter instruções, consulte [virtual-machines-windows-create-custom.md](virtual-machines-windows-create-custom.md).

##Pré-requisitos##
Este artigo pressupõe que você tenha o seguinte:

1. **Uma assinatura do Azure** – se não tiver uma, você poderá [abrir uma conta do Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F): obtenha créditos que você pode usar para experimentar os serviços pagos do Azure e, mesmo depois que eles forem usados, você poderá manter a conta e usar serviços gratuitos do Azure, como sites. Seu cartão de crédito nunca será cobrado, a menos que você altere explicitamente suas configurações, solicitando esse tipo de cobrança. Você também pode [ativar benefícios para assinantes do MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): todos os meses, sua assinatura do MSDN oferece créditos que podem ser usados para serviços pagos do Azure.   

2. **PowerShell do Microsoft Azure ** – você tem o módulo do PowerShell do Microsoft Azure instalado e configurado para usar sua assinatura. Para baixar o módulo, consulte [Downloads do Microsoft Azure](http://azure.microsoft.com/downloads/). Um tutorial para instalar e configurar o módulo está disponível [aqui](install-configure-powershell.md). Você usará o cmdlet [Add-AzureVHD](http://msdn.microsoft.com/library/azure/dn495173.aspx) para carregar o VHD.

3. **Um sistema operacional Windows com suporte armazenado em um arquivo .vhd** - Você instalou um sistema operacional Windows Server com suporte em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd. Você pode usar uma solução de virtualização como o Hyper-V para criar uma máquina virtual e instalar o sistema operacional. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE]Não há suporte para o formato VHDX no Microsoft Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o [cmdlet convert-vhd](http://technet.microsoft.com/library/hh848454.aspx). Você pode encontrar um tutorial sobre isso [aqui](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).
 
 Há suporte para as seguintes versões do Windows Server: <P> <TABLE BORDER="1" WIDTH="600"> <TR BGCOLOR="#E9E7E7"> <TH>Sistema operacional</TH> <TH>SKU</TH> <TH>Service Pack</TH> <TH>Arquitetura</TH> </TR> <TR> <TD>Windows Server 2012 R2</TD> <TD>Todas as edições</TD> <TD>N/D</TD> <TD>x64</TD> </TR> <TR> <TD>Windows Server 2012</TD> <TD>Todas as edições</TD> <TD>N/D</TD> <TD>x64</TD> </TR> <TR> <TD>Windows Server 2008 R2</TD> <TD>Todas as edições</TD> <TD>SP1</TD> <TD>x64</TD> </TR> </TABLE> </P>


Esta tarefa inclui as seguintes etapas:

- [Etapa 1: preparar a imagem a ser carregada][]
- [Etapa 2: criar uma conta de armazenamento no Azure][]
- [Etapa 3: preparar a conexão com o Azure][]
- [Etapa 4: carregar o arquivo .vhd][]

## Etapa 1: preparar a imagem a ser carregada ##

Para que a imagem possa ser carregada para o Azure, ela deve ser generalizada usando o comando Sysprep. Para obter mais informações sobre como usar o Sysprep, consulte [Como usar Sysprep: uma introdução](http://technet.microsoft.com/library/bb457073.aspx).

Na máquina virtual na qual o sistema operacional foi instalado, conclua o procedimento a seguir:

1. Faça logon no sistema operacional.

2. Abra uma janela de Prompt de comando como administrador. Altere o diretório para **%windir%\\system32\\sysprep** e, a seguir, execute `sysprep.exe`.

	![Abrir janela de Prompt de comando](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/sysprep_commandprompt.png)

3.	A caixa de diálogo **Ferramenta de Preparação do Sistema** é aberta.

	![Inicie o Sysprep](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/sysprepgeneral.png)

4.  Em **Ferramenta de Preparação do Sistema**, selecione **Entrar no Sistema OOBE** e verifique se a opção Generalizar está marcada.

5.  Em **Opções de Desligamento**, selecione **Desligar**.

6.  Clique em **OK**.


## Etapa 2: Criar uma conta de armazenamento no Azure ##

Você precisa de uma conta de armazenamento no Azure para carregar um arquivo .vhd para que ele possa ser usado no Azure para criar uma máquina virtual. Você pode usar o portal clássico do Azure para criar uma conta de armazenamento.

1. Entre no portal clássico do Azure.

2. Na barra de comandos, clique em **Nova**.

3. Clique em **Serviços de Dados** \> **Armazenamento** \> **Criação Rápida**.

	![Criação rápida de uma conta de armazenamento](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Storage-quick-create.png)

4. Preencha os campos da seguinte maneira:
	
	- Em **URL**, digite um nome de subdomínio para usar no URL para a conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas e números. Esse nome se torna o nome do host na URL que é usada para lidar com os recursos Blob, Fila ou Tabela da assinatura.
			
	- Escolha o **local ou o grupo de afinidade** da conta de armazenamento. Um grupo de afinidade permite colocar seus serviços de nuvem e armazenamento no mesmo data center.
		 
	- Escolha entre usar a **replicação geográfica** ou não para a conta de armazenamento. A replicação geográfica é ativada por padrão. Essa opção replica os dados para um local secundário, sem nenhum custo para você, para que o armazenamento efetue o failover para o local se ocorrer uma falha grave no local principal. O local secundário é atribuído automaticamente e não pode ser alterado. Se você precisar de mais controle sobre o local do armazenamento baseado em nuvem devido a requisitos legais ou política organizacional, você pode desligar a replicação geográfica. No entanto, saiba que ao ativar a replicação geográfica posteriormente, você deverá pagar uma taxa de transferência de uma única vez para replicar seus dados existentes para o local secundário. Serviços de armazenamento sem replicação geográfica são oferecidos com desconto. Mais detalhes sobre como gerenciar a replicação geográfica de contas de armazenamento podem ser encontrados aqui: [Criar, gerenciar ou excluir uma conta de armazenamento](../storage-create-storage-account/#replication-options).

	![Insira os detalhes da conta de armazenamento](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Storage-create-account.png)


5. Clique em **Criar Conta de Armazenamento**. A conta aparece agora em **Armazenamento**.

	![Conta de armazenamento criada com êxito](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Storagenewaccount.png)

6. Em seguida, crie um contêiner para os seus VHDs carregados. Clique no nome da conta de armazenamento e, em seguida, clique em **Contêineres**.

	![Detalhes da conta de armazenamento](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/storageaccount_detail.png)

7. Clique em**Criar um contêiner**.

	![Detalhes da conta de armazenamento](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/storageaccount_container.png)

8. Digite um **Nome** para o contêiner e selecione a política de **Acesso**.

	![Nome do contêiner](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/storageaccount_containervalues.png)

	> [AZURE.NOTE]Por padrão, o contêiner é privado e pode ser acessado apenas pelo proprietário da conta. Para permitir acesso de leitura público dos blobs no contêiner, mas não das propriedades ou metadados do contêiner, use a opção "Public Blob". Para permitir o acesso de leitura público completo dos dados do contêiner e de blobs, use a opção "Public Container".

## Etapa 3: preparar a conexão com o Microsoft Azure ##

Para poder carregar um arquivo .vhd, você precisa estabelecer uma conexão segura entre seu computador e sua assinatura do Azure. No entanto, você pode usar o método Active Directory do Microsoft Azure ou o método de certificado para realizar esse tipo de trabalho.

### Use o método do AD do Microsoft Azure

1. Abra o console do PowerShell do Azure, conforme instruído em [Como instalar o PowerShell do Microsoft Azure](#Install).

2. Digite o seguinte comando: `Add-AzureAccount`
	
	Esse comando abre uma janela de entrada para que você possa conectar-se com sua conta do trabalho ou escola.

	![Janela do PowerShell](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/add_azureaccount.png)

3. O Azure autentica e salva as informações de credenciais e, em seguida, fecha a janela.

### Usar o método de certificado 

1. Abra o console do PowerShell do Azure. 

2.	Digite: `Get-AzurePublishSettingsFile`.


3. Uma janela de navegador será aberta e baixará automaticamente um arquivo .publishsettings. Esse arquivo contém informações e um certificado de sua assinatura do Microsoft Azure.

	![Procurar página de download](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Browser_download_GetPublishSettingsFile.png)

3. Salve o arquivo .publishsettings.

4. Digite: `Import-AzurePublishSettingsFile <PathToFile>`

	Em que `<PathToFile>` é o caminho completo para o arquivo .publishsettings.


	For more information, see [Get Started with Microsoft Azure Cmdlets](https://msdn.microsoft.com/library/azure/jj554332.aspx). 
	
	For more information on installing and configuring PowerShell, see [How to install and configure Microsoft Azure PowerShell](install-configure-powershell.md). 


## Etapa 4: carregar o arquivo .vhd ##

Quando carrega o arquivo .vhd, você pode colocá-lo em qualquer lugar no seu armazenamento de blob. Nos exemplos de comando a seguir, **BlobStorageURL** é a URL da conta de armazenamento que você criou na Etapa 2, **YourImagesFolder** é o contêiner no armazenamento de blob onde você deseja armazenar as imagens. **VHDName** é o rótulo que aparece no portal clássico para identificar o disco rígido virtual. **PathToVHDFile** é o caminho completo e o nome do arquivo .vhd.


1. Na janela PowerShell do Azure que você usou na etapa anterior, digite:

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`
	
	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/powershell_upload_vhd.png)

	Para obter mais informações sobre esse cmdlet, consulte [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx).

## Etapa 5: adicionar a imagem à sua lista de imagens personalizadas ##
Depois de carregar o .vhd, você o adiciona como uma imagem à lista de imagens personalizadas associadas à sua assinatura.

1. No portal clássico, em **Todos os Itens**, clique em **Máquinas Virtuais**.

2. Em Máquinas Virtuais, clique em **Imagens**.

3. E, em seguida, clique em **Criar uma imagem**.

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Create_Image.png)

4. Em **Criar uma imagem a partir de um VHD**, faça o seguinte:
 	

	- Specify **name**
	- Specify **description**
	- To specify the **URL of your VHD**, click the folder button to open the following window:
 
	![Select VHD](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Select_VHD.png)

	- Select the storage account your VHD is in and click **Open**. This returns you to the **Create an image from a VHD** window.
	- After you return to the **Create an image from a VHD** window, select the Operating System Family.
	- Check **I have run Sysprep on the virtual machine associated with this VHD** to acknowledge that you generalized the operating system in Step 1, and then click **OK**. 

	![Add Image](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Create_Image_From_VHD.png)

5. **Opcional:** você pode usar o cmdlet Add-AzureVMImage em vez do portal clássico para adicionar o VHD como uma imagem. No console do PowerShell do Azure, digite:

	`Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>`
	
	![Add-AzureVMImage do PowerShell](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/add_azureimage_powershell.png)

6. A nova imagem agora é listada quando você escolhe a guia **Imagens**.


	![custom image](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/vm_custom_image.png)

	This new image is now available under **My Images** when you create a virtual machine. For instructions, see [Create a Virtual Machine Running Windows Server](virtual-machines-windows-tutorial.md).

	![create VM from custom image](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/create_vm_custom_image.png)

	> [AZURE.TIP] If you get an error when you try to create a VM, with this error message, "The VHD https://XXXXX... has an unsupported virtual size of YYYY bytes. The size must be a whole number (in MBs)," it means your VHD is not a whole number of MBs and needs to be a fixed size VHD. Try using the Add-AzureVMImage PowerShell cmdlet instead of the classic portal to add the image (see step 5, above). The Azure cmdlets ensure that the VHD meets the Azure requirements.
	
## Próximas etapas ##

Após criar uma máquina virtual, tente criar uma máquina virtual do SQL Server. Para instruções, consulte [Provisionando uma máquina virtual do SQL Server no Microsoft Azure](virtual-machines-provision-sql-server.md).

[Etapa 1: preparar a imagem a ser carregada]: #prepimage
[Etapa 2: criar uma conta de armazenamento no Azure]: #createstorage
[Etapa 3: preparar a conexão com o Azure]: #prepAzure
[Etapa 4: carregar o arquivo .vhd]: #upload

<!--HONumber=52-->
