<properties 
	pageTitle="Anexar um disco a uma máquina virtual | Azure" 
	description="Saiba como anexar um disco de dados a uma máquina virtual Azure e inicializá-lo para que ele fique pronto para uso." 
	services="virtual-machines, storage" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2015" 
	ms.author="kathydav"/>

#Como anexar um disco de dados a uma máquina virtual Windows

Você pode anexar tanto discos vazios como discos que contenham dados. Em ambos os casos, os discos são arquivos .vhd que ficam em uma conta de armazenamento Azure. Em ambos os casos também, após anexar o disco, será necessário reiniciá-lo para usá-lo. 

> [AZURE.NOTE] É uma prática recomendada usar um ou mais discos separados para armazenar dados de uma máquina virtual. Quando você cria uma máquina virtual do Azure, ela tem um disco para o sistema operacional mapeado para a unidade C e um disco temporário mapeado para a unidade D. **Não utilize a unidade D para armazenar dados.** Como seu nome quer dizer, ele oferece armazenamento apenas temporariamente. Não oferece redundância nem backup porque eles não residem no armazenamento do Azure.

- [Como: Anexar um disco vazio](#attachempty)
- [Como: Anexar um disco existente](#attachexisting)
- [Como: Inicializar um novo disco de dados no Windows Server](#initializeinWS)


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

##<a id="initializeinWS"></a>Como: Inicializar um novo disco de dados no Windows Server

1. Conectar-se à máquina virtual. Para obter instruções, consulte [Como fazer logon uma máquina virtual que executa o Windows Server][logon].

2. Após fazer o logon, abra o **Gerenciador de servidor**, no painel esquerdo, expanda **Armazenamento** e, em seguida, clique em **Gerenciamento de disco**.



	![Open Server Manager](./media/storage-windows-attach-disk/ServerManager.png)



3. Clique com o botão direito do mouse em **Disco 2**, clique em **Inicializar Disco**, e depois em **OK**.



	![Initialize the disk](./media/storage-windows-attach-disk/InitializeDisk.png)


4. Com o botão direito do mouse na área de alocação de espaço para o Disco 2, clique em **Novo Volume Simples**e, em seguida, conclua o assistente com os valores padrão.
 

	![Initialize the volume](./media/storage-windows-attach-disk/InitializeDiskVolume.png)


[logon]: ../virtual-machines-log-on-windows-server/



	Agora, o disco está online e pronto para ser usado com uma nova letra da unidade.



	![Volume successfully initialized](./media/storage-windows-attach-disk/InitializeSuccess.png)

> [AZURE.NOTE] A quantidade máxima de discos que podem ser anexados a uma máquina virtual é variável com base no tamanho da máquina virtual. Por exemplo, você pode anexar somente 4 discos para o padrão A2, mas você pode anexar 32 discos o padrão D14 e 64 discos para o padrão G5. Os detalhes sobre quantos discos podem ser anexados ao tamanho da máquina virtual podem ser encontrados [aqui](https://msdn.microsoft.com/pt-br/library/azure/dn197896.aspx).


<!--HONumber=42-->
