1. No [Portal de Gerenciamento do Windows Azure (visualização)][AzurePreviewPortal], clique em **Máquinas Virtuais** e, em seguida, selecione a máquina virtual que você acabou de criar (**testlinuxvm**).

2. Na barra de comandos, clique em **Anexar** e, em seguida, clique em **Anexar Disco Vazio**.

	A caixa de diálogo **Anexar Disco Vazio** é exibida.


3. O **Nome da Máquina Virtual**, **Local de Armazenamento** e **Nome do Arquivo** já estão definidos para você. Tudo o que você precisa fazer é digitar o tamanho desejado para o disco. Digite **5** no campo **Tamanho**.

	![Anexar Disco Vazio][Image2]

	**Observação:** todos os discos são criados a partir de um arquivo VHD no armazenamento do Windows Azure. Você pode fornecer um nome para o arquivo VHD que é adicionado ao armazenamento, mas o Windows Azure gera o nome do disco automaticamente.

4. Clique na marca de seleção para anexar o disco de dados à máquina virtual.

5. Clique no nome da máquina virtual para exibir o painel. Isso permite que você verifique se o disco de dados foi anexado com êxito à máquina virtual.

	O número de discos agora é 2 para a máquina virtual. O disco que você anexou está listado na tabela **Discos**.

	![Anexar Disco Vazio][Image3]

	Depois de anexar os dados do disco à máquina virtual, o disco estará offline e não inicializado. Você precisa fazer logon na máquina virtual e inicializar o disco para poder usá-lo para armazenar dados.

## Conecte-se à Máquina Virtual usando SSH ou PuTTY e conclua a instalação.
O disco de dados que você acabou de anexar à máquina virtual está offline e não inicializado depois de adicionado. Você deve fazer logon no computador e inicializar o disco para usá-lo para armazenar dados.

1. Depois que a máquina virtual estiver provisionada, conecte-se usando SSH ou PuTTY e faça logon como **newuser** (conforme descrito nas etapas acima).	

2. Na janela SSH ou PuTTY digite o comando a seguir e, em seguida, digite a senha da conta:

	`$ sudo grep SCSI /var/log/messages`

	Você pode localizar o identificador do último disco de dados que foi adicionado nas mensagens que são exibidas (**sdc**, neste exemplo).

	![GREP][Image4]

3. Na janela SSH ou PuTTY, digite o comando a seguir para particionar o disco **/dev/sdc**:

	`$ sudo fdisk /dev/sdc`

4. Digite **n** para criar uma nova partição.

	![FDISK][Image5]

5. Digite **p** para tornar a partição primária, digite **1** para torná-la a primeira partição e clique enter para aceitar o valor padrão (1) para o cilindro.

	![FDISK][Image6]

6. Digite **p** para ver os detalhes do disco que está sendo particionado.

	![FDISK][Image7]

7. Digite **w** para gravar as configurações do disco.

	![FDISK][Image8]

8. Formate o novo disco usando o comando **mkfs.ext3**:

	`$ sudo mkfs.ext3 /dev/sdc1`

	![Format Disk][Image9]

9. Crie um diretório para fazer um ponto de montagem para a unidade:

	`$ sudo mkdir /mnt/datadrive`

10. Monte a unidade:

	`$ sudo mount /dev/sdc1 /mnt/datadrive`

11. Abra o arquivo **/etc/fstab** e acrescente a seguinte linha:

	`/dev/sdc1        /mnt/datadrive      ext3    defaults   1 2`

12. Salve e feche o arquivo **/etc/fstab**.

13. Rotule a partição usando e2label:

	`$ sudo e2label /dev/sdc1 /mnt/datadrive`




[Image2]: ./media/attach-data-disk-centos-vm-in-portal/AttachDataDiskLinuxVM2.png
[Image3]: ./media/attach-data-disk-centos-vm-in-portal/AttachDataDiskLinuxVM3.png
[Image4]: ./media/attach-data-disk-centos-vm-in-portal/GrepScsiMessages.png
[Image5]: ./media/attach-data-disk-centos-vm-in-portal/fdisk1.png
[Image6]: ./media/attach-data-disk-centos-vm-in-portal/fdisk2.png
[Image7]: ./media/attach-data-disk-centos-vm-in-portal/fdisk3.png
[Image8]: ./media/attach-data-disk-centos-vm-in-portal/fdisk4.png
[Image9]: ./media/attach-data-disk-centos-vm-in-portal/mkfs.png



