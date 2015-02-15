<properties 
	pageTitle="Capturar imagens de máquinas virtuais que executam Linux" 
	description="Saiba como capturar imagens de máquina virtual do Azure que executam Linux." 
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
	ms.date="11/25/2014" 
	ms.author="kathydav"/>


# Como capturar uma máquina virtual Linux para ser usada como um modelo##

Esse artigo mostra como capturar a máquina virtual do Azure executando Linux para que você a use como modelo para criar outras máquinas virtuais. O modelo inclui o disco do SO e qualquer disco de dados anexados à máquina virtual. Isso não inclui a configuração da rede, então você precisará configurá-la quando você criar as outras máquinas virtuais que usam o modelo.

O Azure trata esse modelo como uma imagem e o armazena em **Minhas Imagens**. Esse também é o local em que quaisquer imagens carregadas são armazenadas. Para mais informações sobre imagens, consulte [Sobre imagens da máquina virtual no Azure] [].

##Antes de começar##

Essas etapas assumem que você já criou uma máquina virtual do Azure e já configurou o sistema operacional, incluindo os anexos de discos de dados. Se você ainda não fez isso, consulte essas instruções:

- [Como criar uma máquina virtual personalizada][]
- [Como anexar um disco de dados à máquina virtual][]

##Capturar a máquina virtual##

1. Conectar-se à máquina virtual clicando em **Conecte-se** na barra de comando. Para mais detalhes, consulte [Como fazer logon em uma máquina virtual executando o Linux][].

2. Na janela SSH, digite o comando a seguir e, em seguida, digite a senha da conta criada na máquina virtual.  Observe que a saída de  `waagent`pode variar um pouco dependendo da versão do utilitário:

	`sudo waagent -deprovision`

	![Deprovision the virtual machine](./media/virtual-machines-linux-capture-image/LinuxDeprovision.png)


3. Digite **y** para continuar.

	![Deprovision of virtual machine successful](./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png)

4. Digite **Exit** para fechar o cliente SSH.

5. No [Portal de Gerenciamento](http://manage.windowsazure.com), selecione a máquina virtual e, em seguida, clique em **Desligar**.

6. Quando a máquina virtual for interrompida, clique em **Capturar** para abrir a caixa de diálogo **Capturar a máquina virtual**.

7.	Em **Nome da Imagem**, digite o nome da nova imagem.

8.	Todas as imagens do Linux devem ser *deprovisioned* ao executar o comando `waagent` com a opção `-deprovision`. Clique em **Executei o comando de cancelamento de provisionamento waagent na máquina virtual** para indicar que o sistema operacional está preparado para ser uma imagem.

9.	Clique na marca de verificação para capturar a imagem.

	A nova imagem agora está disponível em **Imagens**. A máquina virtual é excluída depois que a imagem for capturada.

	![Image capture successful](./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png)

##Próximas etapas##
A imagem está pronta para ser usada como modelo para criar uma nova máquina virtual. Para fazer isto, crie uma máquina virtual personalizada usando o método **de Galeria** e selecione a imagem que você acabou de criar. Para obter instruções, consulte [Como criar uma máquina virtual personalizada][].
	
[Como fazer logon em uma máquina virtual executando o Linux]: ../virtual-machines-linux-how-to-log-on
[Sobre imagens da máquina virtual no Azure]: http://msdn.microsoft.com/pt-br/library/azure/dn790290.aspx
[Como criar uma máquina virtual personalizada]: ../virtual-machines-create-custom/
[Como anexar um disco de dados à máquina virtual]: ../storage-windows-attach-disk/


<!--HONumber=42-->
