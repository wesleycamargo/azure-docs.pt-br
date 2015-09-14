<properties
	pageTitle="Anexar um disco a uma máquina virtual | Microsoft Azure"
	description="Saiba como anexar um disco de dados a uma máquina virtual Azure e inicializá-lo para que ele fique pronto para uso."
	services="virtual-machines, storage"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="kathydav"/>

# Como anexar um disco de dados a uma máquina virtual Windows

É possível anexar discos vazios e discos com dados. Em ambos os casos, os discos são arquivos .vhd que ficam em uma conta de armazenamento Azure. Em ambos os casos também, após anexar o disco, será necessário reiniciá-lo para usá-lo.

> [AZURE.NOTE]É uma prática recomendada usar um ou mais discos separados para armazenar dados de uma máquina virtual. Quando você cria uma máquina virtual do Azure, ela tem um disco para o sistema operacional mapeado para a unidade C e um disco temporário mapeado para a unidade D. **Não use a unidade D para armazenar dados.** Como seu nome quer dizer, a unidade D oferece armazenamento apenas temporariamente. Não oferece redundância nem backup porque não reside no armazenamento do Azure.

## Passo a passo em vídeo

Veja um passo a passo deste tutorial.

[AZURE.VIDEO attaching-a-data-disk-to-a-windows-vm]

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a id="initializeinWS"></a>Como inicializar um novo disco de dados no Windows Server

1. Conectar-se à máquina virtual. Para obter instruções, consulte [Como fazer logon em uma máquina virtual que esteja executando o Windows Server][logon].

2. Depois de entrar na máquina virtual, abra o **Gerenciador de Servidores**. No painel esquerdo, selecione **Arquivos e serviços de armazenamento**.

	![Abra o gerenciador de servidor.](./media/storage-windows-attach-disk/fileandstorageservices.png)

3. Expanda o menu e selecione **Discos**.

4. A seção **Discos** lista disco 0, disco 1 e disco 2. Disco 0 é o disco do sistema operacional, disco 1 é o disco temporário (que não deve ser usado para armazenamento de dados) e o disco 2 é o disco de dados que você anexou à máquina virtual. O disco de dados tem uma capacidade de 5 GB, com base no que você especificou quando anexou o disco. Clique com o botão direito do mouse em disco 2 e selecione **Inicializar**.

5.	Você é notificado de que todos os dados serão apagados quando o disco for inicializado. Clique em **Sim** para confirmar o aviso e inicializar o disco. Em seguida, clique novamente em disco 2 e selecione **Novo Volume**.

6.	Conclua o assistente usando os valores padrão. Quando o assistente for concluído, a seção **Volumes** listará o novo volume. Agora, o disco está online e pronto para armazenar dados.

	![Volume inicializado com êxito](./media/storage-windows-attach-disk/newvolumecreated.png)

> [AZURE.NOTE]O tamanho da máquina virtual determina quantos discos você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](virtual-machines-size-specs.md).

## Recursos adicionais

[Como desanexar um disco de uma máquina virtual Windows](storage-windows-detach-disk.md)

[Sobre discos e VHDs para máquinas virtuais](virtual-machines-disks-vhds.md)

[logon]: virtual-machines-log-on-windows-server.md

<!---HONumber=September15_HO1-->