<properties 
	pageTitle="Crie e carregue um VHD do Windows Server no Azure" 
	description="Saiba como criar e carregar um VHD (disco rígido virtual) no Azure que tenha o Windows Server como sistema operacional." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/19/2015" 
	ms.author="kathydav"/>


#Crie e carregue um VHD do Windows Server no Azure#

Esse artigo mostra como carregar um disco rígido virtual (VHD) com um sistema operacional para que você o use como modelo para criar outras máquinas virtuais com base nessa imagem. Para obter mais informações sobre discos e imagens no Microsoft Azure, consulte [Gerenciar discos e imagens no Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj672979.aspx).

> [AZURE.NOTE] Quando você cria uma máquina virtual baseada em uma imagem, você pode personalizar as configurações do sistema operacional conforme apropriado para os aplicativos que você planeja executar na máquina virtual. Essa configuração é salva para a máquina virtual e não afeta a imagem. Para obter instruções, consulte [Como criar uma máquina virtual personalizada](http://www.windowsazure.com/pt-br/documentation/articles/virtual-machines-windows-tutorial/).

##Pré-requisitos##
Este artigo pressupõe que você tenha o seguinte:

1. **Uma assinatura do Azure** - Se você não tiver uma, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure](http://www.windowsazure.com/pt-br/develop/php/tutorials/create-a-windows-azure-account/).  

2. **Microsoft Azure PowerShell** - você possui o módulo do Microsoft Azure PowerShell instalado e configurado para usar sua assinatura. Para baixar o módulo, consulte [Downloads do Microsoft Azure](http://www.windowsazure.com/pt-br/downloads/). Um tutorial para instalar e configurar o módulo está disponível [aqui](http://www.windowsazure.com/pt-br/documentation/articles/install-configure-powershell/).Você usará o cmdlet [Add-AzureVHD](http://msdn.microsoft.com/pt-br/library/azure/dn495173.aspx) para carregar o VHD.

3. **Um sistema operacional Windows com suporte armazenado em um arquivo .vhd** - Você instalou um sistema operacional Windows Server com suporte em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd. Você pode usar uma solução de virtualização como o Hyper-V para criar uma máquina virtual e instalar o sistema operacional. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](http://technet.microsoft.com/pt-br/library/hh846766.aspx).

> [AZURE.NOTE] Não há suporte para o formato VHDX no Microsoft Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o [cmdlet Convert-VHD](http://technet.microsoft.com/pt-br/library/hh848454.aspx). Você pode encontrar um tutorial sobre isso [aqui](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).
 
 Há suporte para as seguintes versões do Windows Server:
<P>
  <TABLE BORDER="1" WIDTH="600">
  <TR BGCOLOR="#E9E7E7">
    <TH>SO</TH>
    <TH>SKU</TH>
    <TH>Service Pack</TH>
    <TH>Arquitetura</TH>
  </TR>
  <TR>
    <TD>Windows Server 2012 R2</TD>
    <TD>Todas as edições</TD>
    <TD>N/D</TD>
    <TD>x64</TD>
  </TR>
  <TR>
    <TD>Windows Server 2012</TD>
    <TD>Todas as edições</TD>
    <TD>N/D</TD>
    <TD>x64</TD>
  </TR>
  <TR>
    <TD>Windows Server 2008 R2</TD>
    <TD>Todas as edições</TD>
    <TD>SP1</TD>
    <TD>x64</TD>
  </TR>
  </TABLE>
</P>


Esta tarefa inclui as seguintes etapas:

- [Etapa 1: Preparar a imagem a ser carregada] []
- [Etapa 2: Criar uma conta de armazenamento no Azure] []
- [Etapa 3: Preparar a conexão com o Azure] []
- [Etapa 4: Carregar o arquivo .vhd] []

## <a id="prepimage"></a>Etapa 1: Preparar a imagem a ser carregada ##

Para que a imagem possa ser carregada para o Azure, ela deve ser generalizada usando o comando Sysprep. Para obter mais informações sobre como usar o Sysprep, consulte [Como usar o Sysprep: Uma introdução](http://technet.microsoft.com/pt-br/library/bb457073.aspx).

Na máquina virtual na qual o sistema operacional foi instalado, conclua o procedimento a seguir:

1. Faça logon no sistema operacional.

2. Abra uma janela de Prompt de comando como administrador. Altere o diretório para **%windir%\system32\sysprep** e, a seguir, execute `sysprep.exe`.

	![Open Command Prompt window](./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png)

3.	A caixa de diálogo **Ferramenta de Preparação do Sistema** é aberta.

	![Start Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  Em **Ferramenta de Preparação do Sistema**, selecione **Entrar no Sistema OOBE (Configuração Inicial pelo Usuário)** e verifique se a opção Generalizar está marcada.

5.  Em **Opções de Desligamento**, selecione **Desligar**.

6.  Clique em **OK**. 


## <a id="createstorage"></a>Etapa 2: Criar uma conta de armazenamento no Azure ##

Você precisa de uma conta de armazenamento no Azure para carregar um arquivo .vhd para que ele possa ser usado no Azure para criar uma máquina virtual. Entre no Portal de Gerenciamento do Azure para criar uma conta de armazenamento.

1. Entre no Portal de Gerenciamento do Azure.

2. Na barra de comandos, clique em **Novo**.

3. Clique em **Serviços de Dados** > **Armazenamento** > **Criação Rápida**.

	![Quick create a storage account](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. Preencha os campos da seguinte maneira:
	
	- Em **URL**, digite um nome de subdomínio para usar no URL para a conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas e números. Esse nome se torna o nome do host na URL que é usada para lidar com os recursos Blob, Fila ou Tabela da assinatura.
			
	- Escolha o **local ou o grupo de afinidade** da conta de armazenamento. Um grupo de afinidade permite colocar seus serviços na nuvem e armazenamento no mesmo data center.
		 
	- Escolha entre usar a **replicação geográfica** ou não para a conta de armazenamento. A replicação geográfica é ativada por padrão. Essa opção replica os dados para um local secundário, sem nenhum custo para você, para que o armazenamento efetue o failover para o local se ocorrer uma falha grave no local principal. O local secundário é atribuído automaticamente e não pode ser alterado. Se você precisar de mais controle sobre o local do armazenamento baseado na nuvem devido a requisitos legais ou política organizacional, você pode desativar a replicação geográfica. No entanto, saiba que ao ativar a replicação geográfica posteriormente, você deverá pagar uma taxa de transferência de uma única vez para replicar seus dados existentes para o local secundário. Serviços de armazenamento sem replicação geográfica são oferecidos com desconto. Você pode encontrar mais detalhes sobre como gerenciar contas de armazenamento sem replicação geográfica aqui: [Criar, gerenciar ou excluir uma conta de armazenamento](../storage-create-storage-account/#replication-options).

	![Enter storage account details](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)


5. Clique em **Criar Conta de Armazenamento**. A conta aparece agora em **armazenamento**.

	![Storage account successfully created](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. Em seguida, crie um contêiner para os seus VHDs carregados. Clique no nome da conta de armazenamento e, em seguida, clique em **Contêineres**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. Clique em **Criar um contêiner**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. Digite um **Nome** para o seu contêiner e selecione a Política de **Acesso**.

	![Container name](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

	> [AZURE.NOTE] Por padrão, o contêiner é privado e pode ser acessado apenas pelo proprietário da conta. Para permitir acesso de leitura público dos blobs no contêiner, mas não das propriedades ou metadados do contêiner, use a opção "Public Blob". Para permitir o acesso de leitura público completo dos dados do contêiner e de blobs, use a opção "Public Container".

## <a id="PrepAzure"> </a>Etapa 3: Prepare a conexão com o Microsoft Azure ##

Para poder carregar um arquivo .vhd, você precisa estabelecer uma conexão segura entre seu computador e sua assinatura do Azure. No entanto, você pode usar o método Active Directory do Microsoft Azure ou o método de certificado para realizar esse tipo de trabalho.

<h3>Use o método do AD do Microsoft Azure</h3>

1. Abra o console do PowerShell do Azure, conforme instruído em [Como: Instalar o PowerShell do Microsoft Azure](#Install).

2. Digite o seguinte comando:  
	`Add-AzureAccount`
	
	Esse comando abre uma janela de entrada para que você possa conectar-se com sua conta do trabalho ou escola.

	![PowerShell Window](./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png)

3. O Azure autentica e salva as informações de credenciais e, em seguida, fecha a janela.

<h3>Usar o método de certificado</h3> 

1. Abra o console do PowerShell do Azure. 

2.	Tipo: 
	`Get-AzurePublishSettingsFile`.


3. Uma janela de navegador será aberta e baixará automaticamente um arquivo .publishsettings. Esse arquivo contém informações e um certificado de sua assinatura do Microsoft Azure.

	![Browser download page](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. Salve o arquivo .publishsettings. 

4. Tipo: 
	`Import-AzurePublishSettingsFile <PathToFile>`

	Onde `<PathToFile>` é o caminho completo para o arquivo .publishsettings. 


	Para obter mais informações, consulte [Introdução aos cmdlets do Microsoft Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj554332.aspx) 
	
	Para obter mais informações sobre instalação e configuração do PowerShell, consulte [Como instalar e configurar o PowerShell do Microsoft Azure](http://www.windowsazure.com/pt-br/documentation/articles/install-configure-powershell/) 


## <a id="upload"> </a>Etapa 4: Carregar o arquivo .vhd ##

Quando carrega o arquivo .vhd, você pode colocá-lo em qualquer lugar no seu armazenamento de blob. Nos exemplos de comando a seguir, **BlobStorageURL** é a URL da conta de armazenamento que você criou na Etapa 2, **YourImagesFolder** é o contêiner no armazenamento de blob onde você deseja armazenar as imagens. **VHDName** é o rótulo que aparece no Portal de Gerenciamento para identificar o disco rígido virtual. **PathToVHDFile** é o caminho completo e o nome do arquivo .vhd. 


1. Na janela PowerShell do Azure que você usou na etapa anterior, digite:

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`
	
	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png)

	Para obter mais informações sobre os cmdlets do Add-AzureVhd, consulte [Add-AzureVhd](http://msdn.microsoft.com/pt-br/library/dn495173.aspx).

##Etapa 5: Adicionar a imagem à sua lista de imagens personalizadas ##
Depois de carregar o .vhd, você o adiciona como uma imagem à lista de imagens personalizadas associadas à sua assinatura.

1. No Portal de Gerenciamento, em **Todos os Itens**, clique em **Máquinas Virtuais**.

2. Em Máquinas Virtuais, clique em **Imagens**.

3. E, em seguida, clique em **Criar uma imagem**.

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png)

4. Em **Criar uma imagem a partir de um VHD**, faça o seguinte:
 	

	- Especifique **nome**
	- Especifique **descrição**
	- Para especificar o **URL do seu VHD**, clique no botão de pasta para abrir a seguinte janela:
 
	![Select VHD](./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png)

	- Selecione a conta de armazenamento na qual seu VHD está e clique em **Abrir**. Isso o levará de volta à janela**Criar uma imagem a partir de um VHD**.
	- Após retornar à janela **Criar uma imagem a partir de um VHD**, selecione a família do sistema operacional.
	- Marque **Executei o Sysprep na máquina virtual associada a este VHD** para confirmar que você generalizou o sistema operacional na Etapa 1 e, em seguida, clique em **OK**. 

	![Add Image](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png)

5. **OPCIONAL:** é possível usar o cmdlet Add-AzureVMImage em vez do Portal de gerenciamento para adicionar seu VHD como uma imagem. 	No console do PowerShell do Azure, digite:

	`Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>`
	
	![PowerShell Add-AzureVMImage](./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png)

6. Após completar as etapas anteriores, a nova imagem será listada ao escolher a guia **Imagens**. 


	![custom image](./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png)

	Essa nova imagem agora está disponível em **Minhas imagens** quando você cria uma máquina virtual. Para obter instruções, consulte [Criar uma máquina virtual que executa o Windows Server](http://www.windowsazure.com/pt-br/documentation/articles/virtual-machines-windows-tutorial/).

	![create VM from custom image](./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png)

	> [AZURE.TIP] Se você receber um erro ao tentar criar uma VM, com essa mensagem de erro "O https://XXXXX VHD tem um tamanho virtual sem suporte de bytes aaaa. O tamanho deve ser um número inteiro (em MB)," significa que o VHD não tem um número inteiro de MB e precisa ser um VHD de tamanho fixo. Tente usar o cmdlet Add-AzureVMImage PowerShell em vez do Portal de gerenciamento para adicionar a imagem (consulte a etapa 5, acima). Os cmdlets do Azure asseguram que o VHD atende aos requisitos do Azure.
	
## Próximas etapas ##
 

Após criar uma máquina virtual, tente criar uma máquina virtual do SQL Server. Para obter instruções, consulte [Provisionando uma máquina virtual do SQL Server no Microsoft Azure](http://www.windowsazure.com/pt-br/documentation/articles/virtual-machines-provision-sql-server/). 

[Etapa 1: Preparar a imagem a ser carregada]: #prepimage
[Etapa 2: Criar uma conta de armazenamento no Azure]: #createstorage
[Etapa 3: Preparar a conexão com o Azure]: #prepAzure
[Etapa 4: Carregar o arquivo .vhd]: #upload

<!--HONumber=42-->
