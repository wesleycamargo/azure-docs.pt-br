<properties urlDisplayName="Upload a VHD" pageTitle="Criar e carregar um VHD do Windows Server no Azure" metaKeywords ="VHD do Azure, carregando VHD" description="Aprenda como criar e carregar um VHD (disco rígido virtual) no Azure que tenha o sistema operacional do Windows Server." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create and upload a Windows Server VHD to Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/23/2014" ms.author="kathydav" />


#Criar e carregar um VHD do Windows Server no Azure

Esse artigo mostra como carregar um disco rígido virtual (VHD) com um sistema operacional para que você o use como modelo para criar outras máquinas virtuais com base nessa imagem. Para obter mais informações sobre discos e imagens no Microsoft Azure, consulte [Gerenciar discos e imagens no Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj672979.aspx).

**Observação**: Ao criar uma máquina virtual, você pode personalizar as configurações do sistema operacional para facilitar a execução de seu aplicativo. A configuração que você definir é armazenada em disco nessa máquina virtual. Para obter instruções, consulte [Como criar uma máquina virtual personalizada](http://www.windowsazure.com/pt-br/documentation/articles/virtual-machines-windows-tutorial/).

##Pré-requisitos##
Este artigo pressupõe que você tenha os seguintes itens:

**Uma assinatura do Azure** - Se você não tiver uma, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure](http://www.windowsazure.com/pt-br/develop/php/tutorials/create-a-windows-azure-account/).    

**PowerShell do Microsoft Azure** - Você tem o módulo do PowerShell do Microsoft Azure instalado. Para baixar o módulo, consulte [Downloads do Microsoft Azure](http://www.windowsazure.com/pt-br/downloads/). Você pode encontrar um tutorial [aqui](http://www.windowsazure.com/pt-br/documentation/articles/install-configure-powershell/) para instalar e configurar o PowerShell com sua assinatura do Azure.

- O cmdlet [Add-AzureVHD](http://msdn.microsoft.com/pt-br/library/windowsazure/dn205185.aspx) que faz parte do módulo do PowerShell do Microsoft Azure. Você irá usar esse cmdlet para carregar o VHD.

**Um sistema operacional Windows com suporte armazenado em um arquivo .vhd** - Você instalou um sistema operacional Windows Server com suporte em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd. Você pode usar uma solução de virtualização, como o Hyper-V, para criar o arquivo .vhd e instalar o sistema operacional. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](http://technet.microsoft.com/pt-br/library/hh846766.aspx).

**Importante**: Não há suporte para o formato VHDX no Microsoft Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o [cmdlet Convert-VHD](http://technet.microsoft.com/pt-br/library/hh848454.aspx). Você pode encontrar um tutorial sobre isso [aqui](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).
 
**Mídia do sistema operacional Windows Server.** Essa tarefa exige um arquivo .iso que contém o sistema operacional Windows Server. Há suporte para as seguintes versões do Windows Server:
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

- [Etapa 1: Preparar a imagem a ser carregada]  []
- [Etapa 2: Criar uma conta de armazenamento no Azure]  []
- [Etapa 3: Preparar a conexão com o Azure]  []
- [Etapa 4: Carregar o arquivo .vhd]  []

## <a id="prepimage"> </a>Etapa 1: Preparar a imagem a ser carregada ##


Para que a imagem possa ser carregada para o Azure, ela deve ser generalizada usando o comando Sysprep. Para obter mais informações sobre como usar o Sysprep, consulte [Como usar o Sysprep: Uma introdução](http://technet.microsoft.com/pt-br/library/bb457073.aspx).

Na máquina virtual que você acabou de criar, conclua o procedimento a seguir:

1. Faça logon no sistema operacional.

2. Abra uma janela de Prompt de comando como administrador. Altere o diretório para **%windir%\system32\sysprep** e, em seguida, execute "sysprep.exe".

	![Open Command Prompt window](./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png)

3.	A caixa de diálogo **Ferramenta de Preparação do Sistema** é aberta.

	![Start Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  Em **Ferramenta de Preparação do Sistema**, selecione **Entrar no Sistema OOBE (Configuração Inicial pelo Usuário)** e verifique se a opção Generalizar está marcada.

5.  Em **Opções de Desligamento**, selecione **Desligar**.

6.  Clique em **OK**.  




## <a id="createstorage"> </a>Etapa 2: Crie uma conta de armazenamento no Azure ##

A conta de armazenamento representa o mais alto nível do namespace para acessar os serviços de armazenamento e está associada à assinatura do Azure. Você precisa de uma conta de armazenamento no Azure para carregar um arquivo .vhd no Azure que pode ser usado para a criação de uma máquina virtual. Entre no Portal de Gerenciamento do Azure para criar uma conta de armazenamento.

1. Entre no Portal de Gerenciamento do Azure.

2. Na barra de comandos, clique em **Novo**.

3. Clique em **Conta de Armazenamento** e clique em **Criação Rápida**.

	![Quick create a storage account](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. Preencha os campos da seguinte maneira:

	
	
- Em **URL**, digite um nome de subdomínio para usar no URL para a conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas e números. Esse nome se torna o nome do host na URL que é usada para lidar com os recursos Blob, Fila ou Tabela da assinatura.
			
- Escolha o **local ou o grupo de afinidade** da conta de armazenamento. Especificando um grupo de afinidade, você pode localizar seus serviços de nuvem no mesmo data center com seu armazenamento.
		 
- Escolha entre usar a **replicação geográfica** ou não para a conta de armazenamento. A replicação geográfica é ativada por padrão. Essa opção replica os dados para um local secundário, sem nenhum custo para você, para que seja feito o failover para um local secundário em caso de uma falha grave que não possa ser tratada no local primário. O local secundário é atribuído automaticamente e não pode ser alterado. Se os requisitos legais ou a política organizacional exigir um controle mais restrito do local de seu armazenamento baseado em nuvem, você poderá desativar a replicação geográfica. No entanto, saiba que ao ativar a replicação geográfica posteriormente, você deverá pagar uma taxa de transferência de uma única vez para replicar seus dados existentes para o local secundário. Serviços de armazenamento sem replicação geográfica são oferecidos com desconto. Você pode encontrar mais detalhes sobre como gerenciar contas de armazenamento sem replicação geográfica aqui: [Criar, gerenciar ou excluir uma conta de armazenamento](../storage-create-storage-account/#replication-options).

	![Enter storage account details](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)

5. Clique em **Criar Conta de Armazenamento**.

	A conta aparece agora em **Contas de Armazenamento**.

	![Storage account successfully created](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. Em seguida, crie um contêiner para os seus VHDs carregados. Clique em **Nome da conta de armazenamento** e, em seguida, clique em **Contêineres**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. Clique em **Criar um contêiner**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. Digite um **Nome** para o seu contêiner e selecione a **Política de Acesso**.

	![Container name](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

	> [WACOM.NOTE] Por padrão, o contêiner é privado e pode ser acessado apenas pelo proprietário da conta. Para permitir acesso de leitura público dos blobs no contêiner, mas não das propriedades ou metadados do contêiner, use a opção "Public Blob". Para permitir o acesso de leitura público completo dos dados do contêiner e de blobs, use a opção "Public Container".

## <a id="PrepAzure"> </a>Etapa 3: Preparar a conexão com o Microsoft Azure ##

Para poder carregar um arquivo .vhd, você precisa estabelecer uma conexão segura entre seu computador e sua assinatura do Microsoft Azure. No entanto, você pode usar o método Active Directory do Microsoft Azure ou o método de certificado para realizar esse tipo de trabalho.

<h3>Use o método do AD do Microsoft Azure</h3>

1. Abra o console do PowerShell do Microsoft Azure, conforme instruído em [Como: Instalar o PowerShell do Microsoft Azure](#Install).

2. Digite o seguinte comando:    
	`Add-AzureAccount`
	
	Esse comando abre uma janela de entrada para que você possa conectar-se com sua conta do trabalho ou escola.

	![PowerShell Window](./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png)

3. O Microsoft Azure autentica e salva as informações de credenciais e, em seguida, fecha a janela.

<h3>Usar o método de certificado</h3> 

1. Abra uma janela do PowerShell do Microsoft Azure.  

2.	Tipo:  
	`Get-AzurePublishSettingsFile`.


3. Uma janela de navegador será aberta e baixará automaticamente um arquivo .publishsettings. Esse arquivo contém informações e um certificado de sua assinatura do Microsoft Azure.

	![Browser download page](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. Salve o arquivo .publishsettings.  

4. Tipo:  
	`Import-AzurePublishSettingsFile <PathToFile>`

	Onde "<PathToFile>" é o caminho completo para o arquivo .publishsettings.  


	Para obter mais informações, consulte [Introdução aos cmdlets do Microsoft Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj554332.aspx)  
	
	Para obter mais informações sobre instalação e configuração do PowerShell, consulte [Como instalar e configurar o PowerShell do Microsoft Azure](http://www.windowsazure.com/pt-br/documentation/articles/install-configure-powershell/)  


## <a id="upload"> </a>Etapa 4: Carregar o arquivo .vhd ##

Quando carrega o arquivo .vhd, você pode colocá-lo em qualquer lugar no seu armazenamento de blob. Nos exemplos de comando a seguir, **BlobStorageURL** é a URL da conta de armazenamento que você criou na Etapa 2, **YourImagesFolder** é o contêiner no armazenamento de blob onde você deseja armazenar as imagens. **VHDName** é o rótulo que aparece no Portal de Gerenciamento para identificar o disco rígido virtual. **PathToVHDFile** é o caminho completo e o nome do arquivo .vhd.  


1. Na janela PowerShell do Microsoft Azure que você usou na etapa anterior, digite:

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`
	
	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png)

	Para obter mais informações sobre os cmdlets do Add-AzureVhd, consulte [Add-AzureVhd](http://msdn.microsoft.com/pt-br/library/dn495173.aspx).

##Adicionar a imagem à sua lista de imagens personalizadas ##
Depois de carregar o .vhd, você o adiciona como uma imagem à lista de imagens personalizadas associadas à sua assinatura.

1. No Portal de Gerenciamento, em **Todos os Itens**, clique em **Máquinas Virtuais**.

2. Em Máquinas Virtuais, clique em **Imagens**.

3. E, em seguida, clique em **Criar uma imagem**.

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png)

4. Em **Criar uma imagem a partir de um VHD**, faça o seguinte:
 	
	- Especifique **nome**
	- Especifique **descrição**
	- Para especificar o **URL do seu VHD** clique no botão da pasta para instalar a caixa de diálogo abaixo
 
	![Select VHD](./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png)
	- Selecione a conta de armazenamento em que seu VHD está e clique em **Abrir**. Isso o levará de volta à janela**Criar uma imagem a partir de um VHD**.
	- Após retornar à janela **Criar uma imagem a partir de um VHD**, selecione a família do sistema operacional.
	- Marque **Executei o Sysprep na máquina virtual associada a este VHD** para confirmar que você generalizou o sistema operacional na Etapa 1 e, em seguida, clique em **OK**.  

	![Add Image](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png)

5. **OPCIONAL :** Você também pode usar o cmdlet Add-AzureVMImage do PowerShell para adicionar seu VHD como uma imagem.

	Na janela PowerShell do Microsoft Azure, digite:

	"Add-AzureVMImage -ImageName <Nome da sua imagem> Nome da sua imagem-MediaLocation <local do VHD> -OS <tipo de sistema operacional no VHD>"
	
	![PowerShell Add-AzureVMImage](./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png)

6. Após completar as etapas anteriores, a nova imagem será listada ao escolher a guia **Imagens**.  


	![custom image](./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png)

	Quando criar uma nova máquina virtual, você agora pode usar essa nova imagem. Escolha **Minhas Imagens** para mostrar a nova imagem. Para obter instruções, consulte [Criar uma máquina virtual que executa o Windows Server](http://www.windowsazure.com/pt-br/documentation/articles/virtual-machines-windows-tutorial/).

	![create VM from custom image](./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png)

## Próximas etapas ##
 

Após criar uma máquina virtual, tente criar uma máquina virtual do SQL Server. Para obter instruções, consulte [Provisionando uma máquina virtual do SQL Server no Microsoft Azure](http://www.windowsazure.com/pt-br/documentation/articles/virtual-machines-provision-sql-server/).  

[Etapa 1: Preparar a imagem a ser carregada]: #prepimage
[Etapa 2: Criar uma conta de armazenamento no Azure]: #createstorage
[Etapa 3: Preparar a conexão com o Azure]: #prepAzure
[Etapa 4: Carregar o arquivo .vhd]: #upload

<!--HONumber=35.2-->
