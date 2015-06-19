

## <a id="logon"> </a>Como fazer o logon na máquina virtual após criá-la ##

Para gerenciar as configurações da máquina virtual e os aplicativos executados na máquina, você pode usar um cliente SSH. Para fazer isso, você deve instalar um cliente SSH no computador que você deseja usar para acessar a máquina virtual. Há muitos programas de cliente SSH que você pode escolher. Estas são algumas opções:

- Se estiver usando um computador que executa um sistema operacional Windows, poderá usar um cliente SSH como PuTTY. Para obter mais informações, consulte [Download do PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
- Se estiver usando um computador que executa um sistema operacional Linux, poderá usar um cliente SSH como OpenSSH. Para obter mais informações, consulte [OpenSSH](http://www.openssh.org/).

Este tutorial mostra como usar o programa PuTTY para acessar a máquina virtual.

1. Encontre o **Nome do Host** e as **Informações da porta** no Portal de gerenciamento. Você pode encontrar as informações que você precisa no painel da máquina virtual. Clique no nome da máquina virtual e procure os **Detalhes de SSH** na seção **Visão Rápida** do painel de controle.

	![Obtenha os detalhes de SSH](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/SSHdetails.png)

2. Abra o programa PuTTY.

3. Digite o **Nome do Host** e as **Informações da porta** coletados do painel de controle e, em seguida, clique em **abrir**.

	![Digite o nome do host e as informações da porta](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/putty.png)

4. Faça logon na máquina virtual usando a conta de NewUser1 adicionada quando você criou a máquina virtual.

	![Faça logon em uma nova máquina virtual](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/sshlogin.png)

	Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.


## <a id="attachdisk"> </a>Como anexar um disco de dados à nova máquina virtual ##

Seu aplicativo pode precisar armazenar dados. Para configurar isso, você pode anexar um disco de dados à máquina virtual criada anteriormente. A maneira mais fácil de fazer isso é anexar um disco de dados vazio à máquina.

No Linux, o Disco de Recurso é normalmente gerenciado pelo agente do Linux do Azure e montado automaticamente em **/mnt/resource** (ou **/mnt** nas imagens do Ubuntu). Já no Linux, o disco de dados pode ser nomeado pelo kernel como `/dev/sdc` e os usuários precisarão particionar, formatar e montar esse recurso. Consulte o [Guia de usuário agente do Linux do Azure](../articles/virtual-machines/virtual-machines-linux-agent-user-guide.md) para obter mais informações.

>[AZURE.NOTE]Não armazene dados no disco de recursos. Este disco oferece armazenamento temporário para aplicativos e processos e é usado para armazenar dados que não são mais necessários, como arquivos de permuta. Os discos de dados ficam localizados no Armazenamento do Azure, como arquivos .vhd em blobs de páginas, e oferecem redundância de armazenamento para proteger seus dados. Para obter detalhes, consulte [Sobre discos e imagens no Azure](http://msdn.microsoft.com/library/jj672979.aspx).

1. Se você ainda não fez isso, entre no Portal de Gerenciamento do Azure.

2. Clique em **Máquinas Virtuais**e, em seguida, selecione a máquina virtual **MyTestVM1** criada anteriormente.

3. Na barra de comandos, clique em **Anexar** e, em seguida, clique em **Anexar Disco Vazio**.
	
	A caixa de diálogo **Anexar Disco Vazio** é exibida.

	![Definir detalhes do disco](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/attachnewdisklinux.png)

4. O **Nome da Máquina Virtual**, **Local de Armazenamento** e **Nome do Arquivo** já estão definidos para você. Tudo o que você precisa fazer é digitar o tamanho desejado para o disco. Digite **5** no campo **Tamanho**.

	**Observação:** todos os discos são criados de um arquivo VHD no armazenamento do Azure. Você pode fornecer um nome para o arquivo VHD que é adicionado ao armazenamento, mas o nome do disco é gerado automaticamente.

5. Clique na marca de seleção para anexar o disco de dados à máquina virtual.

6. Você pode verificar se o disco de dados foi anexado com êxito à máquina virtual, observando o painel. Clique no nome da máquina virtual para exibir o painel.

	O número de discos agora é 2 para a máquina virtual e o disco que você anexou é listado na tabela **Discos**.

	![Anexar disco com êxito](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/attachemptysuccess.png)


O disco de dados que acabou de anexar à máquina virtual estará offline e não será inicializado depois de anexá-lo. Você deve fazer logon no computador e inicializar o disco para usá-lo para armazenar dados.

1. Conectar-se à máquina virtual usando as etapas listadas acima em **Como fazer logon na máquina virtual depois de criá-la**.


2. Na janela SSH, digite o comando a seguir e insira a senha da conta:

	`sudo grep SCSI /var/log/messages`

	Você pode localizar o identificador do último disco de dados que foi adicionado nas mensagens que são exibidas.

	![Identificar o disco](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskmessages.png)


3. Na janela SSH, digite o seguinte comando para criar um novo dispositivo e, em seguida, digite a senha da conta:

	`sudo fdisk /dev/sdc`

	>[AZURE.NOTE]Neste exemplo, você talvez precisará usar `sudo -i` em algumas distribuições, se /sbin ou /usr/sbin não estiverem em seu `$PATH`.


4. Digite **n** para criar uma nova partição.

	![Criar novo dispositivo](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskpartition.png)


5. Digite **p** para definir a partição como primária, digite **1** para torná-la a primeira partição e clique enter para aceitar o valor padrão para o cilindro.

	![Criar partição](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskcylinder.png)


6. Digite **p** para ver os detalhes sobre o disco que está sendo particionado.

	![Listar informações de disco](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskinfo.png)


7. Digite **w** para gravar as configurações do disco.

	![Gravar as alterações de disco](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskwrite.png)


8. Você deve criar o sistema de arquivos na nova partição. Como exemplo, digite o seguinte comando para criar o sistema de arquivo e, em seguida, digite a senha da conta:

	`sudo mkfs -t ext4 /dev/sdc1`

	![Criar sistema de arquivos](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskfilesystem.png)

	>[AZURE.NOTE]Observe que nos sistemas SUSE Linux Enterprise 11 só há acesso somente leitura para sistemas de arquivos ext4. Para esses sistemas, é recomendável formatar o novo sistema de arquivos como ext3 em vez de ext4.


9. Crie um diretório para montar o novo sistema de arquivos. Como exemplo, digite o seguinte comando e, em seguida, digite a senha da conta:

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


	>[AZURE.NOTE]Remover subsequentemente um disco de dados sem editar fstab pode fazer com que a VM falhe ao ser inicializada. Se esta é uma ocorrência comum, então a maioria das distribuições fornecem tanto as opções `nofail` e/ou `nobootwait`, que permitirão que o sistema inicialize mesmo se o disco não estiver presente. Consulte a documentação da distribuição para obter mais informações sobre esses parâmetros.

<!---HONumber=58-->