<!---
Don't use this file. It's deprecated and will be removed. Instead use, virtual-machines-Linux-tutorial-log-on-attach-disk.md
-->

## <a id="logon"> </a>Como fazer o logon na máquina virtual após criá-la ##

Para gerenciar as configurações da máquina virtual e os aplicativos executados na máquina, você pode usar um cliente SSH. Para fazer isso, você deve instalar um cliente SSH no computador que você deseja usar para acessar a máquina virtual. Há muitos programas de cliente SSH que você pode escolher. Estas são algumas opções:

- Se estiver usando um computador que executa um sistema operacional Windows, poderá usar um cliente SSH como PuTTY. Para obter mais informações, consulte [Download do PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
- Se estiver usando um computador que executa um sistema operacional Linux, poderá usar um cliente SSH como OpenSSH. Para obter mais informações, consulte [OpenSSH](http://www.openssh.org/).

Este tutorial mostra como usar o programa PuTTY para acessar a máquina virtual.

1. Encontre o **Nome do Host** e as **Informações da porta** no Portal de gerenciamento. Você pode encontrar as informações que você precisa no painel da máquina virtual. Clique no nome da máquina virtual e procure os **Detalhes de SSH** na seção **Visão Rápida** do painel de controle.

	![Obtenha os detalhes de SSH](./media/CreateVirtualMachineLinuxTutorial/SSHdetails.png)

2. Abra o programa PuTTY.

3. Digite o **Nome do Host** e as **Informações da porta** coletados do painel de controle e, em seguida, clique em **abrir**.

	![Digite o nome do host e as informações da porta](./media/CreateVirtualMachineLinuxTutorial/putty.png)

4. Faça logon na máquina virtual usando a conta de NewUser1 especificada quando a máquina foi criada.

	![Faça logon em uma nova máquina virtual](./media/CreateVirtualMachineLinuxTutorial/sshlogin.png)

	Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.


## <a id="attachdisk"> </a>Como anexar um disco de dados à nova máquina virtual ##

Seu aplicativo pode precisar armazenar dados. Para configurar isso, você pode anexar um disco de dados à máquina virtual criada anteriormente. A maneira mais fácil de fazer isso é anexar um disco de dados vazio à máquina.

**Observação: disco de dados vs. discos de recurso** Os discos de dados do residem no armazenamento do Azure e podem ser usados para o armazenamento persistente de arquivos e dados de aplicativo.

Cada máquina virtual criada também tem um *Disco de Recurso* local temporário anexado. Devido aos dados em um disco de recurso poderem não ser duráveis entre as reinicializações, geralmente ele é usado por aplicativos e processos em execução na máquina virtual para o armazenamento de dados transitório e temporário. Ele também é usado para armazenar páginas ou trocar de arquivos do sistema operacional.

No Linux, o Disco de Recurso é normalmente gerenciado pelo agente do Linux do Azure e montado automaticamente em **/mnt/resource** (ou **/mnt** nas imagens do Ubuntu). Observe que o disco de recurso é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Já no Linux, o disco de dados pode ser nomeado pelo kernel como `/dev/sdc` e os usuários precisarão particionar, formatar e montar esse recurso. Consulte o [Guia de usuário agente do Linux do Azure](http://www.windowsazure.com/manage/linux/how-to-guides/linux-agent-guide/) para obter mais informações.



1. Se você ainda não fez isso, entre no Portal de Gerenciamento do Azure.

2. Clique em **Máquinas Virtuais**e, em seguida, selecione a máquina virtual **MyTestVM1** criada anteriormente.

3. Na barra de comandos, clique em **Anexar** e, em seguida, clique em **Anexar Disco Vazio**.
	
	A caixa de diálogo **Anexar Disco Vazio** é exibida.

	![Definir detalhes do disco](./media/CreateVirtualMachineLinuxTutorial/attachnewdisklinux.png)

4. O **Nome da Máquina Virtual**, **Local de Armazenamento** e **Nome do Arquivo** já estão definidos para você. Tudo o que você precisa fazer é digitar o tamanho desejado para o disco. Digite **5** no campo **Tamanho**.

	**Observação:** todos os discos são criados de um arquivo VHD no armazenamento do Azure. Você pode fornecer um nome para o arquivo VHD que é adicionado ao armazenamento, mas o nome do disco é gerado automaticamente.

5. Clique na marca de seleção para anexar o disco de dados à máquina virtual.

6. Você pode verificar se o disco de dados foi anexado com êxito à máquina virtual, observando o painel. Clique no nome da máquina virtual para exibir o painel.

	O número de discos agora é 2 para a máquina virtual e o disco que você anexou é listado na tabela **Discos**.

	![Anexar disco com êxito](./media/CreateVirtualMachineLinuxTutorial/attachemptysuccess.png)


O disco de dados que acabou de anexar à máquina virtual estará offline e não será inicializado depois de anexá-lo. Você deve fazer logon no computador e inicializar o disco para usá-lo para armazenar dados.

1. Conectar-se à máquina virtual usando as etapas listadas acima em **Como fazer logon na máquina virtual depois de criá-la**.


2. Na janela SSH, digite o comando a seguir e insira a senha da conta:

	`sudo grep SCSI /var/log/messages`

	Você pode localizar o identificador do último disco de dados que foi adicionado nas mensagens que são exibidas.

	![Identificar o disco](./media/CreateVirtualMachineLinuxTutorial/diskmessages.png)


3. Na janela SSH, digite o seguinte comando para criar um novo dispositivo e, em seguida, digite a senha da conta:

	`sudo fdisk /dev/sdc`

	>[AZURE.NOTE]Neste exemplo, você talvez precisará usar `sudo -i` em algumas distribuições, se /sbin ou /usr/sbin não estiverem em seu `$PATH`.


4. Digite **n** para criar uma nova partição.

	![Criar novo dispositivo](./media/CreateVirtualMachineLinuxTutorial/diskpartition.png)


5. Digite **p** para definir a partição como primária, digite **1** para torná-la a primeira partição e clique enter para aceitar o valor padrão para o cilindro.

	![Criar partição](./media/CreateVirtualMachineLinuxTutorial/diskcylinder.png)


6. Digite **p** para ver os detalhes sobre o disco que está sendo particionado.

	![Listar informações de disco](./media/CreateVirtualMachineLinuxTutorial/diskinfo.png)


7. Digite **w** para gravar as configurações do disco.

	![Gravar as alterações de disco](./media/CreateVirtualMachineLinuxTutorial/diskwrite.png)


8. Você deve criar o sistema de arquivos na nova partição. Como exemplo, digite o seguinte comando para criar o sistema de arquivo e, em seguida, digite a senha da conta:

	`sudo mkfs -t ext4 /dev/sdc1`

	![Criar sistema de arquivos](./media/CreateVirtualMachineLinuxTutorial/diskfilesystem.png)

	>[AZURE.NOTE]Observe que, nos sistemas SUSE Linux Enterprise 11 só há acesso somente leitura para sistemas de arquivos ext4. Para esses sistemas é recomendável formatar o novo sistema de arquivos como ext3 em vez de ext4.


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

	>[AZURE.NOTE]O blkid pode não exigir acesso sudo em todos os casos, no entanto, pode ser mais fácil executar com `sudo -i` em algumas distribuições, se /sbin ou /usr/sbin não estiverem em seu `$PATH`.

	**Cuidado:** a edição inadequada do arquivo /etc/fstab pode resultar em um sistema não inicializável. Se não tiver certeza, consulte a documentação de distribuição para obter informações sobre como editá-lo corretamente. Também é recomendável que um backup do arquivo /etc/fstab seja criado antes da edição.

	Usando um editor de texto, insira as informações sobre o novo sistema de arquivo no final do arquivo /etc/fstab. Neste exemplo, usaremos o valor UUID para o novo dispositivo **/dev/sdc1** criado nas etapas anteriores e no ponto de montagem de **/datadrive**:

		`UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`

	Ou, em sistemas baseados em SUSE Linux, talvez você precise usar um formato ligeiramente diferente:

		`/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`

	Se as partições ou unidades de dados adicionais forem criadas será necessário inseri-las separadamente em/etc/fstab também.

	Agora você pode testar se o sistema de arquivo está montado corretamente ao simplesmente desmontar e montar novamente o sistema de arquivos, ou seja, usando o ponto de montagem de exemplo `/datadrive` criado nas etapas anteriores:

		`sudo umount /datadrive`
		`sudo mount /datadrive`

	Se o segundo comando produzir um erro, verifique o arquivo /etc/fstab para obter a sintaxe correta.


	>[AZURE.NOTE] Subsequently removing a data disk without editing fstab could cause the VM to fail to boot. If this is a common occurrence, then most distributions provide either the `nofail` and/or `nobootwait` fstab options that will allow a system to boot even if the disk is not present. Please consult your distribution's documentation for more information on these parameters.



<!--HONumber=52-->
