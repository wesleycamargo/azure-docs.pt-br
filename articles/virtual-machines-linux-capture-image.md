<properties linkid="manage-linux-howto-capture-an-image" urlDisplayName="Capturar uma imagem" pageTitle="Capturar uma imagem de uma máquina virtual que executa o Linux" metaKeywords="Azure Linux vm, Linux vm" description="Saiba como capturar uma imagem de uma VM (máquina virtual) do Azure que executa o Linux. " metaCanonical="" services="virtual-machines" documentationCenter="" title="Como capturar uma imagem de uma máquina virtual executando o Linux" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />





# Como capturar uma imagem de uma máquina virtual executando o Linux ##

Você pode usar imagens da Galeria de Imagens para criar máquinas virtuais facilmente ou capturar e usar suas próprias imagens para criar máquinas virtuais personalizadas. Uma imagem é um arquivo de disco rígido virtual (.vhd) que é usado como modelo para criar uma máquina virtual. Uma imagem é um modelo porque não tem configurações específicas como uma máquina virtual configurada, como o nome do host e as configurações da conta do usuário. Se você quiser criar várias máquinas virtuais configuradas do mesmo modo, poderá capturar uma imagem de uma máquina virtual configurada e usar essa imagem como um modelo.

1. Conectar-se à máquina virtual usando as etapas listadas em [Como fazer logon em uma máquina virtual que executa o Linux][].

2. Na janela SSH, digite o comando a seguir e, em seguida, digite a senha da conta criada na máquina virtual.  Observe que a saída de `waagent` podem variar um pouco dependendo da versão do utilitário:

	`sudo waagent -deprovision`

	![Cancelamento do provisionamento da máquina virtual](./media/virtual-machines-linux-capture-image/LinuxDeprovision.png)


3. Digite **y** para continuar.

	![Cancelamento da provisão da máquina virtual com êxito](./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png)

4. Digite **Exit** para fechar o cliente SSH.

5. No [Portal de Gerenciamento](http://manage.windowsazure.com), selecione a máquina virtual e, em seguida, clique em **Desligar**.

	![Desligar a máquina virtual](./media/virtual-machines-linux-capture-image/ShutdownVM.png)

6. Clique em **Sim** para confirmar que você continuará a ser cobrado pela máquina virtual quando ela não estiver sendo executada.

7. Quando a máquina virtual for interrompida, clique em **Capturar** na barra de comando.

	![Capturar uma imagem da máquina virtual](./media/virtual-machines-linux-capture-image/CaptureVM.png)

	A caixa de diálogo **Capturar máquina virtual** é exibida.
	
	![Insira os detalhes da captura](./media/virtual-machines-linux-capture-image/CaptureLinux.png)

8.	Em **nome de imagem**, digite o nome da nova imagem.

9.	Todas as imagens do Linux devem ser *desprovisionadas* executando o comando `waagent` na opção `-deprovision`. Clique em **Executei o comando de cancelamento de provisionamento na máquina virtual** para indicar que o sistema operacional está preparado para ser uma imagem.

10.	Clique na marca de verificação para capturar a imagem.

	A nova imagem agora está disponível em **Imagens**. A máquina virtual é excluída depois que a imagem for capturada.

	![Captura de imagem bem-sucedida](./media/virtual-machines-linux-capture-image/CaptureSuccess.png)

	Quando você cria uma máquina virtual usando o método **Da galeria**, você pode usar a imagem capturada clicando em **Minhas imagens** na página **Seleção do sistema operacional da máquina virtual**.
	
[Como fazer logon em uma máquina virtual que executa o Linux]: ../virtual-machines-linux-how-to-log-on


