#Como anexar um disco de dados à máquina virtual




- [Conceitos](#concepts)
- [Como: anexar um disco existente](#attachexisting)
- [Como: anexar um disco vazio](#attachempty)
- [Como: inicializar um novo disco de dados no Windows Server 2008 R2](#initializeinWS)
- [Como: inicializar um novo disco de dados no Linux](#initializeinlinux)



##<a id="concepts"></a>Conceitos



Você também pode anexar um disco de dados a uma máquina virtual para armazenar dados de aplicativo. Um disco de dados é um disco rígido virtual (VHD) que pode ser criado localmente com seu computador ou na nuvem com o Azure. Você gerenciar discos de dados na máquina virtual da mesma maneira que você faz em um servidor no seu escritório.

Você pode usar o Portal de gerenciamento para fazer upload e anexar um disco de dados que contenha os dados para a máquina virtual, bem como adicionar um disco vazio da mesma conta de armazenamento usada pela máquina virtual. Este artigo descreve esses processos. Para anexar um disco vazio, localizado em uma conta de armazenamento diferente, use o [Add-AzureDataDisk](http://go.microsoft.com/fwlink/p/?LinkId=391661) cmdlet, disponível no módulo Azure PowerShell. Para baixar o módulo, consulte a página [Downloads](http://www.windowsazure.com/pt-br/downloads/).

A máquina virtual não é interrompida para adicionar o disco. O número de discos que você pode anexar a uma máquina virtual é baseado no tamanho da máquina virtual. Para obter mais informações sobre o uso de máquinas virtuais e tamanho de discos, consulte [Tamanhos de máquina virtual para o Azure](http://go.microsoft.com/FWLink/p/?LinkID=294683).

> [WACOM.NOTE] 
> O armazenamento do Azure suporta blobs de até 1 TB em tamanho, o que acomoda um VHD com um tamanho virtual máximo de 999 GB. No entanto, se você usar o Hyper-V para criar um novo VHD, o tamanho especificado representa o tamanho virtual. Para usar o VHD no Azure, especifique um tamanho maior do que 999 GB.

**Disco de Dados versus Disco de Recurso**  
Os discos de dados residem no armazenamento do Azure e podem ser usados para o armazenamento persistente de arquivos e dados de aplicativo.

Cada máquina virtual também tem um *Disco de Recurso* local temporário anexado. Devido aos dados em um disco de recurso poderem não ser duráveis entre as reinicializações, geralmente ele é usado por aplicativos e processos em execução na máquina virtual para o armazenamento de dados transitório e temporário. Ele também é usado para armazenar páginas ou trocar de arquivos do sistema operacional.

No Windows, o disco de recurso é rotulado como a unidade **D:**.  No Linux, o disco de recurso é normalmente gerenciado pelo agente do Linux do Azure e montado automaticamente em **/mnt/resource** (ou **/mnt** nas imagens do Ubuntu). Consulte o [Guia de usuário agente do Linux do Azure](http://www.windowsazure.com/pt-br/manage/linux/how-to-guides/linux-agent-guide/) para obter mais informações.

Para obter mais informações sobre o uso de discos de dados, consulte [Gerenciar discos e imagens](http://msdn.microsoft.com/pt-br/library/windowsazure/jj672979.aspx)

##<a id="attachexisting"></a>Como: anexar um disco existente


1. Se você ainda não tiver feito isso, entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).

2. Clique em **Máquinas Virtuais** e, em seguida, selecione em qual máquina virtual deseja anexar o disco.

3. Na barra de comandos, clique em **Anexar** e, em seguida, selecione **Anexar Disco**.


	![Anexar disco de dados](./media/howto-attach-disk-window-linux/AttachExistingDiskWindows.png)

	A caixa de diálogo **Anexar Disco** é exibida.



	![Insira os detalhes do disco de dados](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)

3. Selecione o disco de dados que deseja anexar à máquina virtual.
4. Clique na marca de seleção para anexar o disco de dados à máquina virtual.
  
 
	Agora você verá o disco de dados listado no painel da máquina virtual.


	![Disco de dados anexado com êxito](./media/howto-attach-disk-window-linux/AttachSuccess.png)

##<a id="attachempty"></a>Como: anexar um disco vazio

Depois de você criar e fazer o upload de um arquivo .vhd para usar como um disco vazio, você poderá associá-lo a uma máquina virtual. Use o [Add-AzureVhd](http://go.microsoft.com/FWLink/p/?LinkID=391684) cmdlet para fazer o upload do arquivo .vhd para a conta de armazenamento.  

1. Clique em **Máquinas Virtuais** e, em seguida, selecione em qual máquina virtual deseja anexar o disco.



2. Na barra de comandos, clique em **Anexar** e, em seguida, selecione **Anexar Disco Vazio**.


	![Anexar um disco vazio](./media/howto-attach-disk-window-linux/AttachDiskWindows.png)



	A caixa de diálogo **Anexar Disco Vazio** é exibida.



	![Anexar um novo disco vazio](./media/howto-attach-disk-window-linux/AttachNewDiskWindows.png)

 
3. Em **Nome do arquivo**, aceite o nome gerado automaticamente ou digite um nome que você deseja usar para o arquivo VHD armazenado. O disco de dados que é criado do arquivo VHD sempre usará o nome gerado automaticamente.



4. Em **Tamanho**, digite o tamanho do disco de dados. 



5. Clique na marca de seleção para anexar o disco de dados vazio.

	Agora você verá o disco de dados listado no painel da máquina virtual.


	![Disco de dados vazio anexado com êxito](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)


> [WACOM.NOTE] 
> Depois de adicionar um disco de dados, você precisará fazer logon na máquina virtual e inicializar o disco para que a máquina virtual possa usar o disco para armazenamento.



##<a id="initializeinWS"></a>Como: inicializar um novo disco de dados no Windows Server

1. Conecte-se à máquina virtual executando as etapas listadas em [Como fazer logon em uma máquina virtual que executa o Windows Server][logon].



2. Após fazer o logon, abra o **Gerenciador de servidor**, no painel esquerdo, expanda **Armazenamento** e, em seguida, clique em **Gerenciamento de disco**.



	![Abra o gerenciador de servidor.](./media/howto-attach-disk-window-linux/ServerManager.png)



3. Clique com o botão direito em **Disco 2** e, em seguida, clique em **Inicializar disco**.



	![Inicializar o disco](./media/howto-attach-disk-window-linux/InitializeDisk.png)

4. Clique em **OK** para iniciar o processo de inicialização.



5. Com o botão direito do mouse na área de alocação de espaço para o Disco 2, clique em **Novo Volume Simples** e, em seguida, conclua o assistente com os valores padrão.
 

	![Inicializar o volume](./media/howto-attach-disk-window-linux/InitializeDiskVolume.png)



	O disco está online e pronto para ser usado com uma nova letra de unidade.



	![Volume inicializado com êxito](./media/howto-attach-disk-window-linux/InitializeSuccess.png)




##<a id="initializeinlinux"></a>Como: inicializar um novo disco de dados no Linux



1. Conectar-se à máquina virtual usando as etapas listadas em [Como fazer logon na máquina virtual executando Linux][logonlinux].



2. Na janela SSH, digite o seguinte comando e, em seguida, digite a senha da conta criada para gerenciar a máquina virtual:

	`sudo grep SCSI /var/log/messages`

	Você pode localizar o identificador do último disco de dados que foi adicionado nas mensagens que são exibidas.



	![Obter as mensagens do disco](./media/howto-attach-disk-window-linux/DiskMessages.png)



3. Na janela SSH, digite o seguinte comando para criar um novo dispositivo e, em seguida, digite a senha da conta:

	`sudo fdisk /dev/sdc`

	>[WACOM.NOTE] Neste exemplo, você talvez precise usar `sudo -i` em algumas distribuições se /sbin ou /usr/sbin não estiverem em seu `$PATH`.


4. Digite **n** para criar uma nova partição.


	![Criar novo dispositivo](./media/howto-attach-disk-window-linux/DiskPartition.png)

5. Digite **p** para definir a partição como primária, digite **1** para torná-la a primeira partição e clique enter para aceitar o valor padrão para o cilindro.


	![Criar partição](./media/howto-attach-disk-window-linux/DiskCylinder.png)



6. Digite **p** para ver os detalhes sobre o disco que está sendo particionado.


	![Listar informações de disco](./media/howto-attach-disk-window-linux/DiskInfo.png)



7. Digite **w** para gravar as configurações do disco.


	![Gravar as alterações de disco](./media/howto-attach-disk-window-linux/DiskWrite.png)

8. Você deve criar o sistema de arquivos na nova partição. Como exemplo, digite o seguinte comando para criar o sistema de arquivo e, em seguida, digite a senha da conta:

	`sudo mkfs -t ext4 /dev/sdc1`

	![Criar sistema de arquivos](./media/howto-attach-disk-window-linux/DiskFileSystem.png)

	>[WACOM.NOTE] Observe que, nos sistemas SUSE Linux Enterprise 11 só há acesso somente leitura para sistemas de arquivos ext4.  Para esses sistemas é recomendável formatar o novo sistema de arquivos como ext3 em vez de ext4.


9. Em seguida, você deve ter um diretório disponível para montar o novo sistema de arquivos. Como exemplo, digite o seguinte comando para criar um novo diretório para montar a unidade e, em seguida, digite a senha da conta:

	`sudo mkdir /datadrive`


10. Digite o seguinte comando para montar a unidade:

	`sudo mount /dev/sdc1 /datadrive`

	Agora o disco de dados está pronto para ser usado como **/datadrive**.


11. Adicione a nova unidade ao /etc/fstab:

	Para garantir que a unidade seja novamente montada automaticamente após uma reinicialização, ela deve ser adicionada ao arquivo /etc/fstab. Além disso, é altamente recomendável que o UUID (Identificador Universal Exclusivo) seja usado no /etc/fstab para referir-se à unidade em vez de apenas o nome do dispositivo (por exemplo, /dev/sdc1). Para localizar o UUID da nova unidade, você pode usar o utilitário **blkid**:
	
		`sudo -i blkid`

	Uma saída será semelhante ao seguinte:

		`/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
		`/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
		`/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`

	>[WACOM.NOTE] o blkid pode não exigir acesso sudo em todos os casos, no entanto, pode ser mais fácil executar com `sudo -i` em algumas distribuições se /sbin ou/usr/sbin não é `$PATH`.

	**Cuidado:** Edição inadequada do arquivo /etc/fstab pode resultar em um sistema não inicializável. Se não tiver certeza, consulte a documentação de distribuição para obter informações sobre como editá-lo corretamente. Também é recomendável que um backup do arquivo /etc/fstab seja criado antes da edição.

	Usando um editor de texto, insira as informações sobre o novo sistema de arquivo no final do arquivo /etc/fstab.  Neste exemplo, usaremos o valor UUID para o novo dispositivo **/dev/sdc1** criado nas etapas anteriores e no ponto de montagem de **/datadrive**:

		`UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`

	Ou, em sistemas baseados em SUSE Linux, talvez você precise usar um formato ligeiramente diferente:

		`/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`

	Se as partições ou unidades de dados adicionais forem criadas será necessário inseri-las separadamente em/etc/fstab também.

	Agora você pode testar se o sistema de arquivo é montado corretamente ao simplesmente desmontar e, em seguida, montar novamente o sistema de arquivo, ou seja, usando o ponto de montagem de exemplo `/datadrive` criado nas etapas anteriores: 

		`sudo umount /datadrive`
		`sudo mount /datadrive`

	Se o segundo comando produzir um erro, verifique o arquivo /etc/fstab para obter a sintaxe correta.


	>[WACOM.NOTE] Remover subsequentemente um disco de dados sem editar fstab pode fazer com que a VM falhe ao ser inicializada. Se esta é uma ocorrência comum, então a maioria das distribuições fornecem tanto as opções `nofail` e/ou `nobootwait` fstab que permitirá que o sistema inicialize o mesmo se o disco não estiver presente. Consulte a documentação da distribuição para obter mais informações sobre esses parâmetros.



[logon]: ../virtual-machines-log-on-windows-server/
[logonlinux]: ../virtual-machines-linux-how-to-log-on/



