<properties
	pageTitle="Anexar um disco de dados | Microsoft Azure"
	description="Como anexar um disco de dados novo ou existente a uma VM no portal do Azure usando o modelo de implantação do Gerenciador de Recursos."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="cynthn"/>

# Como anexar um disco de dados no portal do Azure

[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a id="initializeinWS"></a>Como: inicializar um novo disco de dados no Windows Server

1. Conectar-se à máquina virtual. Para obter instruções, consulte [Como fazer logon em uma máquina virtual que executa o Windows Server](virtual-machines-windows-log-on.md).

2. Depois de entrar na máquina virtual, abra o **Gerenciador de Servidores**. No painel esquerdo, selecione **Arquivos e serviços de armazenamento**.

	![Abra o gerenciador de servidor.](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. Expanda o menu e selecione **Discos**.

4. A seção **Discos** lista os discos. Na maioria dos casos, ele terá disco 0, disco 1 e disco 2. Disco 0 é o disco do sistema operacional, disco 1 é o disco temporário e disco 2 é o disco de dados que você acabou de anexar à VM. O novo disco de dados listará a Partição como **desconhecida**. Clique com o botão direito do mouse no disco e selecione **Inicializar**.

5.	Você é notificado de que todos os dados serão apagados quando o disco for inicializado. Clique em **Sim** para confirmar o aviso e inicializar o disco. Depois de concluir, a Partição será listada como **GPT**. Clique com o botão direito do mouse no disco novamente e selecione **Novo Volume**.

6.	Conclua o assistente usando os valores padrão. Quando o assistente for concluído, a seção **Volumes** listará o novo volume. Agora, o disco está online e pronto para armazenar dados.


	![Volume inicializado com êxito](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] O tamanho da VM determina quantos discos você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](virtual-machines-linux-sizes.md).


## Próximas etapas

Caso seu aplicativo precise usar a unidade D: para armazenar dados, é possível [alterar a letra da unidade do disco temporário do Windows].

<!---HONumber=AcomDC_0323_2016-->