<properties writer="kathydav" editor="tysonn" manager="timlt" />



# Como desanexar um disco de dados de uma máquina virtual 

Quando não precisar mais de um disco de dados conectado a uma máquina virtual, você poderá desanexá-lo facilmente. Essa ação remove o disco da máquina virtual, mas não o remove do armazenamento. Se desejar usar os dados existentes no disco novamente, você pode reanexá-lo à mesma máquina virtual ou anexá-lo a uma outra máquina virtual.

> [AZURE.NOTE]Uma máquina virtual no Azure usa diferentes tipos de discos -- um disco de sistema operacional, um disco temporário local e discos de dados opcionais. Discos de dados são a forma recomendada de armazenar dados para uma máquina virtual. Para obter detalhes sobre discos, consulte [Sobre discos e imagens](http://go.microsoft.com/fwlink/p/?LinkId=263439). Não é possível desanexar um disco do sistema operacional, a menos que você também exclua a máquina virtual.

## Localize o disco##

Se você não souber o nome do disco ou deseja verificá-lo antes de desanexá-lo, siga estas etapas.

> [AZURE.NOTE]O Azure designa automaticamente um nome ao disco ao anexá-lo. O nome consiste no nome do serviço de nuvem, no nome da máquina virtual e no número.

1. Se você ainda não fez isso, entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com). 

2. Clique em **Máquinas virtuais**, clique no nome da máquina virtual e, em seguida, clique em **Painel**.

3. Em **Discos**, a tabela lista o nome e o tipo de todos os discos anexados. Por exemplo, esta tela mostra uma máquina virtual com um disco do sistema operacional (SO) e um disco de dados:
		
	![Encontrar disco de dados](./media/howto-detach-disk-windows-linux/FindDataDisks.png)


## Desanexar o disco##

Depois de localizar o nome do disco, você estará pronto para desanexá-lo:

1. Clique em **Máquinas virtuais**, clique no nome da máquina virtual que tem o disco de dados que você deseja desanexar e clique **Painel**.
2. Na barra de comandos, clique em **Desanexar o disco**.

3. Selecione o disco de dados e, em seguida, clique na marca de seleção para desanexá-lo.

	![Desanexar detalhes do disco](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)

O disco permanece no armazenamento mas não esteja conectado a uma máquina virtual.

 

<!---HONumber=58_postMigration-->