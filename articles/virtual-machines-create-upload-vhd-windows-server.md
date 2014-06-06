<properties linkid="manage-windows-common-task-upload-vhd" urlDisplayName="Carregar um VHD" pageTitle="Criar e carregar um VHD do Windows Server para o Azure" metaKeywords="VHD do Azure, carregando VHD" description="Saiba como criar e carregar um VHD (disco rígido virtual) no Azure com o sistema operacional Windows Server." metaCanonical="" services="virtual-machines" documentationCenter="" title="Criando e carregando um disco rígido virtual que contém o sistema operacional Windows Server" authors=""  solutions="" writer="kathydav" manager="jeffreyg" editor="tysonn"  />





#Criando e carregando um Disco Rígido Virtual que contém o sistema operacional Windows Server #

Uma máquina virtual no Azure executa o sistema operacional que você escolhe ao criar a máquina virtual. o Azure armazena o sistema operacional de uma máquina virtual em um disco rígido virtual no formato VHD (um arquivo .vhd). Um VHD de um sistema operacional que foi preparado para duplicação é chamado de uma imagem. Este artigo mostra como criar sua própria imagem, carregando um arquivo .vhd com um sistema operacional que você instalou e generalizou. Para obter mais informações sobre discos e imagens no Azure, consulte [Gerenciar discos e imagens (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/jj672979.aspx)

**Observação**: ao criar uma máquina virtual, você pode personalizar as configurações do sistema operacional para facilitar a execução de seu aplicativo. A configuração que você definir é armazenada em disco nessa máquina virtual. Para obter instruções, consulte [Como criar uma máquina virtual personalizada](/pt-br/manage/windows/how-to-guides/custom-create-a-vm/).

##Pré-requisitos##
Este artigo pressupõe que você tenha os seguintes itens:

**Um certificado de gerenciamento** - Você criou um certificado de gerenciamento para a assinatura para a qual você deseja carregar um VHD e exportou o certificado para um arquivo .cer. Para obter mais informações sobre a criação de certificados, consulte [Criar um certificado de gerenciamento para o Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/gg551722.aspx) 

**Um sistema operacional Windows com suporte armazenado em um arquivo .vhd** - Você instalou um sistema operacional Windows Server com suporte em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd. Você pode usar uma solução de virtualização, como o Hyper-V, para criar o arquivo .vhd e instalar o sistema operacional. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](http://technet.microsoft.com/pt-br/library/hh846766.aspx)

**Importante**: não há suporte para o formato mais recente do VHDX no Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd.     
 
- **Mídia do sistema operacional Windows Server.** Esta tarefa exige um arquivo .iso que contém o sistema operacional Windows Server. Há suporte para as seguintes versões do Windows Server:
<P>
  <TABLE BORDER="1" WIDTH="600">
  <TR BGCOLOR="#E9E7E7">
    <TH>SO</TH>
    <TH>SKU</TH>
    <TH>Service Pack</TH>
    <TH>Arquitetura</TH>
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

- O cmdlet [Add-AzureVHD](http://msdn.microsoft.com/pt-br/library/windowsazure/dn205185.aspx) que faz parte do módulo do PowerShell do Azure. Para baixar o módulo, consulte [Downloads do Azure (a página pode estar em inglês)](/pt-br/develop/downloads/).


Esta tarefa inclui as seguintes etapas:

- [Etapa 1: preparar a imagem a ser carregada] []
- [Etapa 2: criar uma conta de armazenamento no Azure] []
- [Etapa 3: preparar a conexão com o Azure] []
- [Etapa 4: carregar o arquivo .vhd] []

## <a id="prepimage"> </a>Etapa 1: preparar a imagem a ser carregada ##

Para que a imagem possa ser carregada para o Azure, ela deve ser generalizada usando o comando Sysprep. Para obter mais informações sobre como usar o Sysprep, consulte [Como usar Sysprep: Uma introdução (a página pode estar em inglês)](http://technet.microsoft.com/pt-br/library/bb457073.aspx).

Na máquina virtual que você acabou de criar, conclua o procedimento a seguir:

1. Faça logon no sistema operacional.

2. Abra uma janela de Prompt de comando como administrador. Altere o diretório para **%windir%\system32\sysprep** e execute `sysprep.exe`.

	![Abrir janela de Prompt de comando](./media/virtual-machines-create-upload-vhd-windows-server/sysprepcommand.png)

3.	A caixa de diálogo **Ferramenta de Preparação do Sistema** é aberta.

	![Inicie o Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4. Em **Ação de Limpeza do Sistema**, selecione **Entrar no Sistema OOBE** e verifique se a opção **Generalizar** está marcada.

5. Em **Opções de Desligamento**, selecione **Desligar**.

6. Clique em **OK**.


## <a id="createstorage"> </a>Etapa 2: criar uma conta de armazenamento no Azure ##

A conta de armazenamento representa o mais alto nível do namespace para acessar os serviços de armazenamento e está associada à assinatura do Azure. Você precisa de uma conta de armazenamento no Azure para carregar um arquivo .vhd no Azure que pode ser usada para a criação de uma máquina virtual. Entre no Portal de Gerenciamento do Azure para criar uma conta de armazenamento.

1. Entre no Portal de Gerenciamento do Azure.

2. Na barra de comandos, clique em **Nova**.

3. Clique em **Conta de Armazenamento** e clique em **Criação Rápida**.

	![Criação rápida de uma conta de armazenamento](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. Preencha os campos da seguinte maneira:

	![Insira os detalhes da conta de armazenamento](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)

- Em **URL**, digite um nome de subdomínio a ser usado na URL da conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas e números. Esse nome se torna o nome do host na URL que é usada para lidar com os recursos Blob, Fila ou Tabela da assinatura.
	
- Escolha o local ou o grupo de afinidade da conta de armazenamento. Especificando um grupo de afinidade, você pode colocalizar seus serviços de nuvem no mesmo datacenter com seu armazenamento.
 
- Decida se deseja usar a replicação geográfica da conta de armazenamento. A replicação geográfica é ativada por padrão. Essa opção replica os dados para um local secundário, sem nenhum custo para você, para que seja feito o failover para um local secundário em caso de uma falha grave que não possa ser tratada no local primário. O local secundário é atribuído automaticamente e não pode ser alterado. Se os requisitos legais ou a política organizacional exigir um controle mais restrito do local de seu armazenamento baseado em nuvem, você poderá desativar a replicação geográfica. No entanto, saiba que ao ativar a replicação geográfica posteriormente, você deverá pagar uma taxa de transferência de uma única vez para replicar seus dados existentes para o local secundário. Serviços de armazenamento sem replicação geográfica são oferecidos com desconto.

5. Clique em **Criar Conta de Armazenamento**.

	A conta aparece agora em **Contas de Armazenamento**.

	![Conta de armazenamento criada com êxito](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)


## <a id="PrepAzure"> </a>Etapa 3: preparar a conexão com o Azure ##

Para poder carregar um arquivo .vhd, você precisa estabelecer uma conexão segura entre seu computador e sua assinatura no Azure. 

1. Abra uma janela PowerShell do Azure.

2. Tipo: 

	`Get-AzurePublishSettingsFile`

	Esse comando abre uma janela de navegador e baixa automaticamente um arquivo .publishsettings que contém informações e um certificado de sua assinatura do Azure. 

3. Salve o arquivo .publishsettings. 

4. Tipo:

	`Import-AzurePublishSettingsFile <PathToFile>`

	Em que `<PathToFile>` é o caminho completo para o arquivo .publishsettings. 

	Para obter mais informações, consulte [Introdução aos cmdlets do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj554332.aspx). 


## <a id="upload"> </a>Etapa 4: carregar o arquivo .vhd ##

Quando carrega o arquivo .vhd, você pode colocá-lo em qualquer lugar no seu armazenamento de blob. Nos exemplos de comando a seguir, **BlobStorageURL** é a URL da conta de armazenamento que você criou na Etapa 2, **YourImagesFolder** é o contêiner no armazenamento de blob onde você deseja armazenar as imagens. **VHDName** é o rótulo que aparece no Portal de Gerenciamento para identificar o disco rígido virtual. **PathToVHDFile** é o caminho completo e o nome do arquivo .vhd. 

1. Na janela PowerShell do Azure usada na etapa anterior, digite:

	`Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

	Para obter mais informações, consulte [Add-AzureVhd (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/dn205185.aspx).

##Adicionar a imagem à sua lista de imagens personalizadas ##
Depois de carregar o .vhd, você o adiciona como uma imagem à lista de imagens personalizadas associadas à sua assinatura.

1. No Portal de Gerenciamento, em **Todos os Itens**, clique em **Máquinas Virtuais**.

2. Em Máquinas Virtuais, clique em **Imagens** e, em seguida, em **Criar**.

3. Em **Criar uma imagem a partir de um VHD**, especifique um nome e a URL do .vhd que você carregou.

4. Marque **Executei o Sysprep na máquina virtual associada a este VHD** para confirmar que você generalizou o sistema operacional na Etapa 2 e, em seguida, clique em **OK**. 


## Próximas etapas ##
Depois que a imagem estiver disponível em sua lista, você poderá usá-la para criar máquinas virtuais. Para obter instruções, consulte [Criar uma máquina virtual que executa o Windows Server](../virtual-machines-windows-tutorial/).

[Etapa 1: preparar a imagem a ser carregada]: #prepimage
[Etapa 2: criar uma conta de armazenamento no Azure]: #createstorage
[Etapa 3: preparar a conexão com o Azure]: #prepAzure
[Etapa 4: carregar o arquivo .vhd]: #upload





