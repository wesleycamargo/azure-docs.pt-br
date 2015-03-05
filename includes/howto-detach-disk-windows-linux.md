<properties writer="kathydav" editor="tysonn" manager="timlt" />



#Como desanexar um disco de dados de uma máquina virtual 

- [Etapa 1: Localizar o disco](#finddisks)
- [Etapa 2: Desanexar um disco de dados](#detachdisk)

Quando você não precisar mais um disco de dados que está conectado a uma máquina virtual, você pode desanexá-lo facilmente. Essa ação remove o disco da máquina virtual, mas não o remove do armazenamento. Se desejar usar os dados existentes no disco novamente, você pode reanexá-lo à mesma máquina virtual ou anexá-lo a uma outra máquina virtual.  

> [AZURE.NOTE] Uma máquina virtual no Azure usa diferentes tipos de discos: um disco de sistema operacional, um disco temporário local e discos de dados opcionais. Discos de dados são a forma recomendada de armazenar dados para uma máquina virtual. Para obter detalhes sobre discos, consulte [Sobre discos e imagens] []. Para obter instruções sobre como anexar novos discos, consulte [Como anexar um disco de dados a uma máquina Virtual] [attachdisk].

## <a id="finddisks"> </a>Etapa 1: Localizar o disco##


Se você não souber o nome do disco ou deseja verificá-lo antes de desanexá-lo, siga estas etapas. 

> [AZURE.NOTE] O Azure designa automaticamente um nome ao disco ao anexá-lo. O nome consiste no nome do serviço de nuvem, no nome da máquina virtual e no número.

1. Se você ainda não fez isso, entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com). 

2. Clique em **Máquinas virtuais**, clique no nome da máquina virtual e, em seguida, clique em **Painel**.

3. Em **Discos**, a tabela lista o nome e o tipo de todos os discos anexados. Por exemplo, esta tela mostra uma máquina virtual com um disco do sistema operacional (SO) e um disco de dados:
		
	![Find data disk](./media/howto-detach-disk-windows-linux/FindDataDisks.png)	


## <a id="detachdisk"> </a>Etapa 2: Desanexar o disco##

Depois de localizar o nome do disco, você estará pronto para desanexá-lo:

1. Clique em **Máquinas virtuais**, clique no nome da máquina virtual que tem o disco de dados que você deseja desanexar e clique **Painel**.
2. Na barra de comandos, clique em **Desanexar o disco**.

3. Selecione o disco de dados e, em seguida, clique na marca de seleção para desanexá-lo.


	![Detach disk details](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)

O disco permanece no armazenamento mas não esteja conectado a uma máquina virtual.



[attachdisk]:/pt-br/manage/windows/how-to-guides/attach-a-disk/

[Sobre discos e imagens]:http://go.microsoft.com/fwlink/p/?LinkId=263439

<!--HONumber=45--> 
