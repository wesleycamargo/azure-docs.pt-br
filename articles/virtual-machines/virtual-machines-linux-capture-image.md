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
	ms.date="06/11/2015" 
	ms.author="kathydav"/>


# Como capturar uma máquina virtual Linux para ser usada como um modelo##

Esse artigo mostra como capturar a máquina virtual do Azure executando Linux para que você a use como modelo para criar outras máquinas virtuais. O modelo inclui o disco do SO e qualquer disco de dados anexados à máquina virtual. Isso não inclui a configuração da rede, então você precisará configurá-la quando você criar as outras máquinas virtuais que usam o modelo.

O Azure trata esse modelo como uma imagem e o armazena em **Minhas Imagens**. Esse também é o local em que quaisquer imagens carregadas são armazenadas. Para saber mais sobre imagens, confira [Sobre imagens da máquina virtual no Azure][].

##Antes de começar##

Essas etapas assumem que você já criou uma máquina virtual do Azure e já configurou o sistema operacional, incluindo os anexos de discos de dados. Se você ainda não fez isso, consulte essas instruções:

- [Como criar uma máquina virtual personalizada][]
- [Como anexar um disco de dados à máquina virtual][]

##Capturar a máquina virtual##

1. Conectar-se à máquina virtual clicando em **Conecte-se** na barra de comando. Para mais detalhes, consulte [ Como fazer logon em uma máquina virtual executando o Linux][].

2. Na janela SSH, digite o comando a seguir. Observe que a saída de `waagent` pode variar um pouco dependendo da versão do utilitário:

	`$ sudo waagent -deprovision+user`

	Esse comando tentará limpar o sistema e torná-lo adequado para reprovisionamento. Essa operação realiza as seguintes tarefas:

	- Remove as chaves de host SSH (se Provisioning.RegenerateSshHostKeyPair for 'y' no arquivo de configuração)
	- Limpa a configuração de servidor de nomes em /etc/resolv.conf
	- Remove a senha do usuário `root` de /etc/shadow (se Provisioning.DeleteRootPassword for 'y' no arquivo de configuração)
	- Remove concessões de cliente DHCP em cache
	- Reinicia o nome de host para localdomain.localdomain
	- Exclui a última conta de usuário provisionado (obtida em /var/lib/waagent) **e dados associados**.

	![Cancelamento do provisionamento da máquina virtual](./media/virtual-machines-linux-capture-image/LinuxDeprovision.png)

	>[AZURE.NOTE]O desprovisionamento exclui arquivos e dados em um esforço para "generalizar" a imagem. Execute esse comando apenas em máquinas virtuais que você deseje capturar como um novo modelo de imagem.


3. Digite **y** para continuar.

	![Cancelamento da provisão da máquina virtual com êxito](./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png)

	**Observe** que você pode adicionar o parâmetro `-force` para evitar essa etapa de confirmação.

	>[AZURE.NOTE]O desprovisionamento somente generaliza a imagem do Linux, mas não garante que a imagem fique sem qualquer informação confidencial e que seja adequada para a redistribuição a terceiros.


4. Digite **Exit** para fechar o cliente SSH.

5. No [Portal de Gerenciamento](http://manage.windowsazure.com), selecione a máquina virtual e, em seguida, clique em **Desligar**.

6. Quando a máquina virtual for interrompida, clique em **Capturar** para abrir a caixa de diálogo **Capturar a máquina virtual**.

7.	Em **Nome da Imagem**, digite o nome da nova imagem.

8.	Todas as imagens do Linux devem ter o *provisionamento* cancelado executando o comando `waagent` com a opção `-deprovision`. Clique em **Executei o comando de cancelamento de provisionamento waagent na máquina virtual** para indicar que o sistema operacional está preparado para ser uma imagem.

9.	Clique na marca de verificação para capturar a imagem.

	A nova imagem agora está disponível em **Imagens**. A máquina virtual é excluída depois que a imagem for capturada.

	![Captura de imagem bem-sucedida](./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png)


##Próximas etapas##
A imagem está pronta para ser usada como modelo para criar uma nova máquina virtual. Para fazer isto, crie uma máquina virtual personalizada usando o método **de Galeria** e selecione a imagem que você acabou de criar. Para obter instruções, consulte [Como criar uma máquina virtual personalizada][].

**Consulte também:** [Guia do usuário do agente Linux para o Azure](virtual-machines-linux-agent-user-guide.md)

[ Como fazer logon em uma máquina virtual executando o Linux]: virtual-machines-linux-how-to-log-on.md
[Sobre imagens da máquina virtual no Azure]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[Como criar uma máquina virtual personalizada]: virtual-machines-create-custom.md
[Como anexar um disco de dados à máquina virtual]: storage-windows-attach-disk.md

 

<!---HONumber=58_postMigration-->