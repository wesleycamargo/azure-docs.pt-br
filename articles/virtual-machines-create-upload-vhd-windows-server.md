<properties urlDisplayName="Upload a VHD" pageTitle="Crie e carregue um VHD do Windows Server no Azure" metaKeywords="Azure VHD, uploading VHD" description="Saiba como criar e carregar um VHD (disco r&iacute;gido virtual) no Azure que tenha o Windows Server como sistema operacional." metaCanonical="" services="virtual-machines" documentationCenter="" title="Crie e carregue um VHD do Windows Server no Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/23/2014" ms.author="kathydav" />

# Crie e carregue um VHD do Windows Server no Azure

Esse artigo mostra como carregar um disco rígido virtual (VHD) com um sistema operacional para que você o use como modelo para criar outras máquinas virtuais com base nessa imagem. Para obter mais informações sobre discos e imagens no Microsoft Azure, consulte [Gerenciar discos e imagens no Azure][Gerenciar discos e imagens no Azure].

**Observação**: Ao criar uma máquina virtual, você pode personalizar as configurações do sistema operacional para facilitar a execução de seu aplicativo. A configuração que você definir é armazenada em disco nessa máquina virtual. Para obter instruções, consulte [Como criar uma máquina virtual personalizada][Como criar uma máquina virtual personalizada].

## Pré-requisitos

Este artigo pressupõe que você tenha os seguintes itens:

**Uma assinatura do Azure** - Se você não tiver uma, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure][Criar uma conta do Azure].

**PowerShell do Microsoft Azure** - Você tem o módulo do PowerShell do Microsoft Azure instalado. Para baixar o módulo, consulte [Downloads do Microsoft Azure][Downloads do Microsoft Azure]. Você pode encontrar um tutorial [aqui][aqui] para instalar e configurar o PowerShell com sua assinatura do Azure.

-   O cmdlet [Add-AzureVHD][Add-AzureVHD] que faz parte do módulo do PowerShell do Microsoft Azure. Yocê irá usar esse cmdlet para carregar o VHD.

**Um sistema operacional Windows com suporte armazenado em um arquivo .vhd** - Você instalou um sistema operacional Windows Server com suporte em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd. Você pode usar uma solução de virtualização, como o Hyper-V, para criar o arquivo .vhd e instalar o sistema operacional. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual][Instalar a função Hyper-V e configurar uma máquina Virtual].

**Importante**: Não há suporte para o formato VHDX no Microsoft Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o [cmdlet convert-vhd][cmdlet convert-vhd]. Você pode encontrar um tutorial sobre isso [aqui][1].

**Mídia do sistema operacional Windows Server.** Esta tarefa exige um arquivo .iso que contenha o sistema operacional Windows Server. Há suporte para as seguintes versões do Windows Server:

<table border="1" width="600">

<tr bgcolor="#E9E7E7">

<th>
SO

</th>

<th>
SKU

</th>

<th>
Service Pack

</th>

<th>
Arquitetura

</th>

</tr>

<tr>

<td>
Windows Server 2012 R2

</td>

<td>
Todas as edições

</td>

<td>
N/D

</td>

<td>
x64

</td>

</tr>

<tr>

<td>
Windows Server 2012

</td>

<td>
Todas as edições

</td>

<td>
N/D

</td>

<td>
x64

</td>

</tr>

<tr>

<td>
Windows Server 2008 R2

</td>

<td>
Todas as edições

</td>

<td>
SP1

</td>

<td>
x64

</td>

</tr>

</table>

</p>
</p>
Esta tarefa inclui as seguintes etapas:

-   [Etapa 1: Preparar a imagem a ser carregada][Etapa 1: Preparar a imagem a ser carregada]
-   [Etapa 2: Criar uma conta de armazenamento no Azure][Etapa 2: Criar uma conta de armazenamento no Azure]
-   [Etapa 3: Preparar a conexão com o Azure][Etapa 3: Preparar a conexão com o Azure]
-   [Etapa 4: Carregar o arquivo .vhd][Etapa 4: Carregar o arquivo .vhd]

## <span id="prepimage"></span> </a>Etapa 1: Preparar a imagem a ser carregada

Para que a imagem possa ser carregada para o Azure, ela deve ser generalizada usando o comando Sysprep. Para obter mais informações sobre como usar o Sysprep, consulte [Como usar o Sysprep: Uma introdução][Como usar o Sysprep: Uma introdução].

Na máquina virtual que você acabou de criar, conclua o procedimento a seguir:

1.  Faça logon no sistema operacional.

2.  Abra uma janela de Prompt de comando como administrador. Altere o diretório para **%windir%\\system32\\sysprep** e, a seguir, execute `sysprep.exe`.

    ![Abrir janela de Prompt de comando][Abrir janela de Prompt de comando]

3.  A caixa de diálogo **Ferramenta de Preparação do Sistema** é aberta.

    ![Inicie o Sysprep][Inicie o Sysprep]

4.  Em **Ferramenta de Preparação do Sistema**, selecione **Entrar no Sistema OOBE** e verifique se a opção Generalizar está marcada.

5.  Em **Opções de Desligamento**, selecione **Desligar**.

6.  Clique em **OK**.

## <span id="createstorage"></span> </a>Etapa 2: Criar uma conta de armazenamento no Azure

A conta de armazenamento representa o mais alto nível do namespace para acessar os serviços de armazenamento e está associada à assinatura do Azure. Você precisa de uma conta de armazenamento no Azure para carregar um arquivo .vhd no Azure que pode ser usado para a criação de uma máquina virtual. Entre no Portal de Gerenciamento do Azure para criar uma conta de armazenamento.

1.  Entre no Portal de Gerenciamento do Azure.

2.  Na barra de comandos, clique em **Nova**.

3.  Clique em **Conta de Armazenamento** e clique em **Criação Rápida**.

    ![Criação rápida de uma conta de armazenamento][Criação rápida de uma conta de armazenamento]

4.  Preencha os campos da seguinte maneira:

-   Em **URL**, digite um nome de subdomínio para usar no URL para a conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas e números. Esse nome se torna o nome do host na URL que é usada para lidar com os recursos Blob, Fila ou Tabela da assinatura.

-   Escolha o **local ou o grupo de afinidade** da conta de armazenamento. Especificando um grupo de afinidade, você pode localizar seus serviços de nuvem no mesmo data center com seu armazenamento.

-   Escolha entre usar a **replicação geográfica** ou não para a conta de armazenamento. A replicação geográfica é ativada por padrão. Essa opção replica os dados para um local secundário, sem nenhum custo para você, para que seja feito o failover para um local secundário em caso de uma falha grave que não possa ser tratada no local primário. O local secundário é atribuído automaticamente e não pode ser alterado. Se os requisitos legais ou a política organizacional exigir um controle mais restrito do local de seu armazenamento baseado em nuvem, você poderá desativar a replicação geográfica. No entanto, saiba que ao ativar a replicação geográfica posteriormente, você deverá pagar uma taxa de transferência de uma única vez para replicar seus dados existentes para o local secundário. Serviços de armazenamento sem replicação geográfica são oferecidos com desconto. Você pode encontrar mais detalhes sobre como gerenciar contas de armazenamento sem replicação geográfica aqui: [Como gerenciar contas de armazenamento][Como gerenciar contas de armazenamento].

    ![Insira os detalhes da conta de armazenamento][Insira os detalhes da conta de armazenamento]

1.  Clique em **Criar Conta de Armazenamento**.

    A conta aparece agora em **Contas de Armazenamento**.

    ![Conta de armazenamento criada com êxito][Conta de armazenamento criada com êxito]

2.  Em seguida, crie um contêiner para os seus VHDs carregados. Clique em **Nome da conta de armazenamento** e, em seguida, clique em **Contêineres**.

    ![Detalhes da conta de armazenamento][Detalhes da conta de armazenamento]

3.  Clique em**Criar um contêiner**.

    ![Detalhes da conta de armazenamento][2]

4.  Digite um **Nome** para o seu contêiner e selecione a **Política de Acesso**.

    ![Nome do contêiner][Nome do contêiner]

    > [WACOM.NOTE] Por padrão, o contêiner é privado e pode ser acessado apenas pelo proprietário da conta. Para permitir acesso de leitura público dos blobs no contêiner, mas não das propriedades ou metadados do contêiner, use a opção "Public Blob". Para permitir o acesso de leitura público completo dos dados do contêiner e de blobs, use a opção "Public Container".

## <span id="PrepAzure"></span> </a>Etapa 3: Prepare a conexão com o Microsoft Azure

Para poder carregar um arquivo .vhd, você precisa estabelecer uma conexão segura entre seu computador e sua assinatura do Microsoft Azure. No entanto, você pode usar o método Active Directory do Microsoft Azure ou o método de certificado para realizar esse tipo de trabalho.

### Use o método do AD do Microsoft Azure

1.  Abra o console do PowerShell do Microsoft Azure, conforme instruído em [Como: Instalar o PowerShell do Microsoft Azure][Como: Instalar o PowerShell do Microsoft Azure].

2.  Digite o seguinte comando:
    `Add-AzureAccount`

    Esse comando abre uma janela de entrada para que você possa conectar-se com sua conta do trabalho ou escola.

    ![Janela do PowerShell][Janela do PowerShell]

3.  O Microsoft Azure autentica e salva as informações de credenciais e, em seguida, fecha a janela.

### Usar o método de certificado

1.  Abra uma janela do PowerShell do Microsoft Azure.

2.  Tipo:
    `Get-AzurePublishSettingsFile`.

3.  Uma janela de navegador será aberta e baixará automaticamente um arquivo .publishsettings. Esse arquivo contém informações e um certificado de sua assinatura do Microsoft Azure.

    ![Procurar página de download][Procurar página de download]

4.  Salve o arquivo .publishsettings.

5.  Tipo:
    `Import-AzurePublishSettingsFile <PathToFile>`

    Onde `<PathToFile>` é o caminho completo para o arquivo .publishsettings.

    Para obter mais informações, consulte [Introdução aos cmdlets do Microsoft Azure][Introdução aos cmdlets do Microsoft Azure]

    Para obter mais informações sobre instalação e configuração do PowerShell, consulte [Como instalar e configurar o PowerShell do Microsoft Azure][aqui].

## <span id="upload"></span> </a>Etapa 4: Carregar o arquivo .vhd

Quando carrega o arquivo .vhd, você pode colocá-lo em qualquer lugar no seu armazenamento de blob. Nos exemplos de comando a seguir, **BlobStorageURL** é a URL da conta de armazenamento que você criou na Etapa 2, **YourImagesFolder** é o contêiner no armazenamento de blob onde você deseja armazenar as imagens. **VHDName** é o rótulo que aparece no Portal de Gerenciamento para identificar o disco rígido virtual. **PathToVHDFile** é o caminho completo e o nome do arquivo .vhd.

1.  Na janela PowerShell do Microsoft Azure que você usou na etapa anterior, digite:

    `Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`

    ![PowerShell Add-AzureVHD][PowerShell Add-AzureVHD]

    Para obter mais informações sobre os cmdlets do Add-AzureVhd, consulte [Add-AzureVhd][Add-AzureVhd].

## Adicionar a imagem à sua lista de imagens personalizadas

Depois de carregar o .vhd, você o adiciona como uma imagem à lista de imagens personalizadas associadas à sua assinatura.

1.  No Portal de Gerenciamento, em **Todos os Itens**, clique em **Máquinas Virtuais**.

2.  Em Máquinas Virtuais, clique em **Imagens**.

3.  E, em seguida, clique em **Criar uma imagem**.

    ![PowerShell Add-AzureVHD][3]

4.  Em **Criar uma imagem a partir de um VHD**, faça o seguinte:

    -   Especifique **nome**
    -   Especifique **descrição**
    -   Para especificar a **URL do seu VHD** clique no botão da pasta para instalar a caixa de diálogo abaixo

    ![Selecione VHD][Selecione VHD]

    -   Selecione a conta de armazenamento em que seu VHD está e clique em **Abrir**. Isso o levará de volta à janela**Criar uma imagem a partir de um VHD**.
    -   Após retornar à janela **Criar uma imagem a partir de um VHD**, selecione a família do sistema operacional.
    -   Marque **Executei o Sysprep na máquina virtual associada a este VHD** para confirmar que você generalizou o sistema operacional na Etapa 1 e, em seguida, clique em **OK**.

    ![Adicionar uma imagem][Adicionar uma imagem]

5.  **OPCIONAL:** Você também pode usar o cmdlet Add-AzureVMImage do PowerShell para adicionar seu VHD como imagem.

    Na janela PowerShell do Microsoft Azure, digite:

    `Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>`

    ![Add-AzureVMImage do PowerShell][Add-AzureVMImage do PowerShell]

6.  Após completar as etapas anteriores, a nome imagem será listada ao escolher a guia **Imagens**.

    ![imagem personalizada][imagem personalizada]

    Quando criar uma nova máquina virtual, você agora pode usar essa nova imagem. Escolha **Minhas Imagens** para mostrar a nova imagem. Para obter instruções, consulte [Criar uma máquina virtual que executa o Windows Server][Como criar uma máquina virtual personalizada].

    ![crie uma VM da imagem personalizada][crie uma VM da imagem personalizada]

## Próximas etapas

Após criar uma máquina virtual, tente criar uma máquina virtual do SQL Server. Para instruções, consulte [Provisionando uma máquina virtual do SQL Server no Microsoft Azure][Provisionando uma máquina virtual do SQL Server no Microsoft Azure].

  [Gerenciar discos e imagens no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj672979.aspx
  [Como criar uma máquina virtual personalizada]: http://www.windowsazure.com/pt-br/documentation/articles/virtual-machines-windows-tutorial/
  [Criar uma conta do Azure]: http://www.windowsazure.com/pt-br/develop/php/tutorials/create-a-windows-azure-account/
  [Downloads do Microsoft Azure]: http://www.windowsazure.com/pt-br/downloads/
  [aqui]: http://www.windowsazure.com/pt-br/documentation/articles/install-configure-powershell/
  [Add-AzureVHD]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn205185.aspx
  [Instalar a função Hyper-V e configurar uma máquina Virtual]: http://technet.microsoft.com/pt-br/library/hh846766.aspx
  [cmdlet convert-vhd]: http://technet.microsoft.com/pt-br/library/hh848454.aspx
  [1]: http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx
  [Etapa 1: Preparar a imagem a ser carregada]: #prepimage
  [Etapa 2: Criar uma conta de armazenamento no Azure]: #createstorage
  [Etapa 3: Preparar a conexão com o Azure]: #prepAzure
  [Etapa 4: Carregar o arquivo .vhd]: #upload
  [Como usar o Sysprep: Uma introdução]: http://technet.microsoft.com/pt-br/library/bb457073.aspx
  [Abrir janela de Prompt de comando]: ./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png
  [Inicie o Sysprep]: ./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png
  [Criação rápida de uma conta de armazenamento]: ./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png
  [Como gerenciar contas de armazenamento]: http://www.windowsazure.com/pt-br/documentation/articles/storage-manage-storage-account/
  [Insira os detalhes da conta de armazenamento]: ./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png
  [Conta de armazenamento criada com êxito]: ./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png
  [Detalhes da conta de armazenamento]: ./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png
  [2]: ./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png
  [Nome do contêiner]: ./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png
  [Como: Instalar o PowerShell do Microsoft Azure]: #Install
  [Janela do PowerShell]: ./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png
  [Procurar página de download]: ./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png
  [Introdução aos cmdlets do Microsoft Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554332.aspx
  [PowerShell Add-AzureVHD]: ./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png
  [Add-AzureVhd]: http://msdn.microsoft.com/pt-br/library/dn495173.aspx
  [3]: ./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png
  [Selecione VHD]: ./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png
  [Adicionar uma imagem]: ./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png
  [Add-AzureVMImage do PowerShell]: ./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png
  [imagem personalizada]: ./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png
  [crie uma VM da imagem personalizada]: ./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png
  [Provisionando uma máquina virtual do SQL Server no Microsoft Azure]: http://www.windowsazure.com/pt-br/documentation/articles/virtual-machines-provision-sql-server/
