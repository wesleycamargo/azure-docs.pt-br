<properties
	pageTitle="Anexar um disco a uma máquina virtual que executa Linux"
	description="Saiba como anexar um disco de dados a uma máquina virtual Azure e inicializá-lo para que ele fique pronto para uso."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/29/2015"
	ms.author="kathydav"/>

# Como anexar um disco de dados na máquina virtual Linux

Você pode anexar tanto discos vazios como discos que contenham dados. Em ambos os casos, os discos são arquivos .vhd que ficam em uma conta de armazenamento Azure. Em ambos os casos também, após anexar o disco, será necessário reiniciá-lo para usá-lo.

> [AZURE.NOTE]É uma prática recomendada usar um ou mais discos separados para armazenar dados de uma máquina virtual. Quando você cria uma máquina virtual Azure, há um disco do sistema operacional e um disco temporário. **Não use o disco temporário para armazenar dados.** Como seu nome quer dizer, ele oferece armazenamento apenas temporariamente. Não oferece redundância nem backup porque eles não residem no armazenamento do Azure. O disco temporário é normalmente gerenciado pelo agente Linux do Azure e montado automaticamente em **/mnt/resource** (ou **/mnt** nas imagens do Ubuntu). Já no Linux, o disco de dados pode ser nomeado pelo kernel como `/dev/sdc`. Quando for esse o caso, você precisará dividir, formatar e montar esse recurso. Consulte o [Guia do Usuário do Azure Linux Agent][Agent] para obter detalhes.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## Como: inicializar um novo disco de dados no Linux

1. Conectar-se à máquina virtual. Para obter instruções, consulte [Como fazer logon em uma máquina virtual que executa o Linux][Logon].



2. Na janela SSH, digite o seguinte comando e, em seguida, digite a senha para a conta criada para gerenciar a máquina virtual:

		# sudo grep SCSI /var/log/messages

	>[AZURE.NOTE]Para distribuições Ubuntu recentes, você talvez precise usar `sudo grep SCSI /var/log/syslog` pois o logon em `/var/log/messages` pode estar desabilitado por padrão.

	Você pode localizar o identificador do último disco de dados que foi adicionado nas mensagens que são exibidas.



	![Obter as mensagens do disco](./media/virtual-machines-linux-how-to-attach-disk/DiskMessages.png)



3. Na janela SSH, digite o seguinte comando para criar um novo dispositivo e, em seguida, digite a senha da conta:

		# sudo fdisk /dev/sdc

	>[AZURE.NOTE]Neste exemplo, você talvez precisará usar `sudo -i` em algumas distribuições, se /sbin ou /usr/sbin não estiverem em seu `$PATH`.


4. Digite **n** para criar uma nova partição.


	![Criar novo dispositivo](./media/virtual-machines-linux-how-to-attach-disk/DiskPartition.png)

5. Digite **p** para definir a partição como primária, digite **1** para torná-la a primeira partição e clique enter para aceitar o valor padrão para o cilindro.


	![Criar partição](./media/virtual-machines-linux-how-to-attach-disk/DiskCylinder.png)



6. Digite **p** para ver os detalhes sobre o disco que está sendo particionado.


	![Listar informações de disco](./media/virtual-machines-linux-how-to-attach-disk/DiskInfo.png)



7. Digite **w** para gravar as configurações do disco.


	![Gravar as alterações de disco](./media/virtual-machines-linux-how-to-attach-disk/DiskWrite.png)

8. Crie o sistema de arquivos na nova partição. Como exemplo, digite o seguinte comando e, em seguida, insira a senha da conta:

		# sudo mkfs -t ext4 /dev/sdc1

	![Criar sistema de arquivos](./media/virtual-machines-linux-how-to-attach-disk/DiskFileSystem.png)

	>[AZURE.NOTE]Observe que sistemas SUSE Linux Enterprise 11 dão suporte apenas a acesso somente leitura para sistemas de arquivos ext4. Para esses sistemas, é recomendável formatar o novo sistema de arquivos como ext3 em vez de ext4.


9. Crie um diretório para montar o novo sistema de arquivos. Como exemplo, digite o seguinte comando e, em seguida, digite a senha da conta:

		# sudo mkdir /datadrive


10. Digite o seguinte comando para montar a unidade:

		# sudo mount /dev/sdc1 /datadrive

	Agora o disco de dados está pronto para ser usado como **/datadrive**.


11. Adicione a nova unidade ao /etc/fstab:

	Para garantir que a unidade seja novamente montada automaticamente após uma reinicialização, ela deve ser adicionada ao arquivo /etc/fstab. Além disso, é altamente recomendável que o UUID (Identificador Universal Exclusivo) seja usado no /etc/fstab para referir-se à unidade em vez de apenas o nome do dispositivo (por exemplo, /dev/sdc1). Para localizar o UUID da nova unidade, você pode usar o utilitário **blkid**:

		# sudo -i blkid

	Uma saída será semelhante ao seguinte:

		/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
		/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
		/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"


	>[AZURE.NOTE]A edição inadequada do arquivo **/etc/fstab** pode resultar em um sistema não inicializável. Se não tiver certeza, consulte a documentação de distribuição para obter informações sobre como editá-lo corretamente. Também é recomendável que um backup do arquivo /etc/fstab seja criado antes da edição.

	Em seguida, abra o arquivo **/etc/fstab** em um editor de texto. Observe que /etc/fstab é um arquivo do sistema, então será necessário usar `sudo` para editá-lo. Por exemplo:

		# sudo vi /etc/fstab

	Neste exemplo, usaremos o valor UUID para o novo dispositivo **/dev/sdc1** criado nas etapas anteriores e no ponto de montagem de **/datadrive**. Adicione a seguinte linha no final do arquivo **/etc/fstab**:

		UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2

	Ou, em sistemas baseados em SUSE Linux, talvez você precise usar um formato ligeiramente diferente:

		/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2

	Agora você pode testar se o sistema de arquivo está montado corretamente ao simplesmente desmontar e montar novamente o sistema de arquivos, ou seja, usando o ponto de montagem de exemplo `/datadrive` criado nas etapas anteriores:

		# sudo umount /datadrive
		# sudo mount /datadrive

	Se o comando `mount` produzir um erro, verifique se o arquivo /etc/fstab tem a sintaxe correta. Se as partições ou unidades de dados adicionais forem criadas será necessário inseri-las separadamente em/etc/fstab também.


>[AZURE.NOTE]Remover subsequentemente um disco de dados sem editar fstab pode fazer com que a VM falhe ao ser inicializada. Se esta for uma ocorrência comum, a maioria das distribuições fornecerá as opções fstab `nofail` e/ou `nobootwait`, que permitirão que o sistema se inicialize mesmo se a montagem do disco falhar no momento da inicialização. Consulte a documentação da distribuição para obter mais informações sobre esses parâmetros.

## Recursos adicionais
[Como fazer logon em uma máquina virtual que executa o Linux][Logon]


<!--Link references-->
[Agent]: virtual-machines-linux-agent-user-guide.md
[Logon]: virtual-machines-linux-how-to-log-on.md
 

<!---HONumber=July15_HO1-->