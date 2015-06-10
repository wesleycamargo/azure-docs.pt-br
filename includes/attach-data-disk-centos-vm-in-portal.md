1. No [Portal de Gerenciamento](http://manage.windowsazure.com) do Azure, clique em **Máquinas Virtuais** e selecione a máquina virtual que acabou de criar (**testlinuxvm**).

2. Na barra de comandos, clique em **Anexar** e, em seguida, clique em **Anexar Disco Vazio**.

	A caixa de diálogo **Anexar Disco Vazio** é exibida.


3. O **Nome da Máquina Virtual**, **Local de Armazenamento** e **Nome do Arquivo** já estão definidos para você. Tudo o que você precisa fazer é digitar o tamanho desejado para o disco. Digite **5** no campo **Tamanho**.

	![Anexar Disco Vazio][Image2]

	**Observação:** todos os discos são criados com base em um arquivo .vhd no armazenamento do Azure. Você pode fornecer um nome para o arquivo .vhd que é adicionado ao armazenamento, mas o Azure gera o nome do disco automaticamente.

4. Clique na marca de seleção para anexar o disco de dados à máquina virtual.

5. Clique no nome da máquina virtual para exibir o painel assim você pode verificar se o disco de dados foi anexado com êxito à máquina virtual. O disco que você anexou está listado na tabela **Discos**.

	Quando você anexa um disco de dados, ele não pode ser usado até que você faça logon para completar a instalação.

##Conectar-se à máquina virtual usando o SSH ou o PuTTY e concluir a instalação
Faça logon na máquina virtual para completar a instalação do disco e poder usá-lo para armazenar dados.

1. Depois que a máquina virtual estiver provisionada, conecte-se usando SSH ou PuTTY e faça logon como **newuser** (conforme descrito nas etapas acima).	


2. Na janela SSH ou PuTTY, digite o comando a seguir e, em seguida, digite a senha da conta:

	`$ sudo grep SCSI /var/log/messages`

	Você pode localizar o identificador do último disco de dados que foi adicionado nas mensagens que são exibidas (**sdc**, neste exemplo).

	![GREP][Image4]


3. Na janela SSH ou PuTTY, digite o comando a seguir para particionar o disco **/dev/sdc**:

	`$ sudo fdisk /dev/sdc`


4. Digite **n** para criar uma nova partição.

	![FDISK][Image5]


5. Digite **p** para definir a partição como primária, digite **1** para torná-la a primeira partição e clique enter para aceitar o valor padrão (1) para o cilindro.

	![FDISK][Image6]


6. Digite **p** para ver os detalhes sobre o disco que está sendo particionado.

	![FDISK][Image7]


7. Digite **w** para gravar as configurações do disco.

	![FDISK][Image8]


8. Formate o novo disco usando o comando **mkfs**:

	`$ sudo mkfs -t ext4 /dev/sdc1`

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

	Se as partições ou unidades de dados adicionais forem criadas será necessário inseri-las separadamente em/etc/fstab também.

	Agora você pode testar se o sistema de arquivo está montado corretamente ao simplesmente desmontar e montar novamente o sistema de arquivos, ou seja, usando o ponto de montagem de exemplo `/datadrive` criado nas etapas anteriores:

		`sudo umount /datadrive`
		`sudo mount /datadrive`

	Se o segundo comando produzir um erro, verifique o arquivo /etc/fstab para obter a sintaxe correta.


	>[AZURE.NOTE]Remover subsequentemente um disco de dados sem editar fstab pode fazer com que a VM falhe ao ser inicializada. Se essa for uma ocorrência comum, a maioria das distribuições fornecerá as opções fstab `nofail` e/ou `nobootwait`, que permitirão que o sistema seja inicializado mesmo que o disco não esteja presente. Consulte a documentação da distribuição para obter mais informações sobre esses parâmetros.


[Image2]: ./media/attach-data-disk-centos-vm-in-portal/AttachDataDiskLinuxVM2.png
[Image4]: ./media/attach-data-disk-centos-vm-in-portal/GrepScsiMessages.png
[Image5]: ./media/attach-data-disk-centos-vm-in-portal/fdisk1.png
[Image6]: ./media/attach-data-disk-centos-vm-in-portal/fdisk2.png
[Image7]: ./media/attach-data-disk-centos-vm-in-portal/fdisk3.png
[Image8]: ./media/attach-data-disk-centos-vm-in-portal/fdisk4.png
[Image9]: ./media/attach-data-disk-centos-vm-in-portal/mkfs.png

<!---HONumber=58-->