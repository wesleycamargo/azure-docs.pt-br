<properties urlDisplayName="Capture an image" pageTitle="Capturar imagens de m&aacute;quinas virtuais que executam Linux" metaKeywords="Azure Linux vm, Linux vm" description="Saiba como capturar imagens de m&aacute;quinas virtuais do Azure que executam Linux. " metaCanonical="" services="virtual-machines" documentationCenter="" title="Como capturar uma imagem de uma m&aacute;quina virtual executando o Linux" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Como capturar uma máquina virtual Linux para ser usada como um modelo

Esse artigo mostra como capturar a máquina virtual do Linux para que você a use como modelo para criar outras máquinas virtuais. O modelo de máquina virtual inclui o disco do SO e qualquer disco de dados anexados à máquina virtual. Isso não inclui a configuração da rede, então você precisará configurá-la quando você criar as outras máquinas virtuais que usam o modelo.

Após capturar a máquina virtual, ela ficará disponível sob **Minhas Imagens** quando você cria uma máquina virtual. O arquivo de imagem fica guardado como VHD na sua conta de armazenamento. Para obter mais informações sobre imagens, consulte [Gerenciar discos e imagens][Gerenciar discos e imagens].

## Antes de começar

Essas etapas assumem que você já criou uma máquina virtual do Azure e já configurou o sistema operacional, incluindo os anexos de discos de dados. Se você ainda não fez isso, consulte essas instruções:

-   [Como criar uma máquina virtual personalizada][Como criar uma máquina virtual personalizada]
-   [Como anexar um disco de dados à máquina virtual][Como anexar um disco de dados à máquina virtual]

## Capturar a máquina virtual

1.  Conectar-se à máquina virtual clicando em **Conecte-se** na barra de comando. Para mais detalhes, consulte [Como fazer logon em uma máquina virtual executando o Linux][Como fazer logon em uma máquina virtual executando o Linux].

2.  Na janela SSH, digite o comando a seguir e, em seguida, digite a senha da conta criada na máquina virtual. Observe que a saída de `waagent`pode variar um pouco dependendo da versão do utilitário:

    `sudo waagent -deprovision`

    ![Cancelamento do provisionamento da máquina virtual][Cancelamento do provisionamento da máquina virtual]

3.  Digite **y** para continuar.

    ![Cancelamento da provisão da máquina virtual com êxito][Cancelamento da provisão da máquina virtual com êxito]

4.  Digite **Exit** para fechar o cliente SSH.

5.  No [Portal de Gerenciamento][Portal de Gerenciamento], selecione a máquina virtual e, em seguida, clique em **Desligar**.

6.  Quando a máquina virtual for interrompida, clique em **Capturar** para abrir a caixa de diálogo **Capturar a máquina virtual**.

7.  Em **Nome da Imagem**, digite o nome da nova imagem.

8.  Todas as imagens do Linux devem ter o *provisionamento cancelado* executando o `waagent` com a opção de `-deprovision`. Clique em **Executei o comando de cancelamento de provisionamento waagent na máquina virtual** para indicar que o sistema operacional está preparado para ser uma imagem.

9.  Clique na marca de verificação para capturar a imagem.

    A nova imagem agora está disponível em **Imagens**. A máquina virtual é excluída depois que a imagem for capturada.

    ![Captura de imagem bem-sucedida][Captura de imagem bem-sucedida]

## Próximas etapas

A imagem está pronta para ser usada como modelo para criar uma nova máquina virtual. Para fazer isto, crie uma máquina virtual personalizada usando o método **de Galeria** e selecione a imagem que você acabou de criar. Para obter instruções, consulte [Como criar uma máquina virtual personalizada][Como criar uma máquina virtual personalizada].

  [Gerenciar discos e imagens]: http://go.microsoft.com/fwlink/p/?LinkId=397536
  [Como criar uma máquina virtual personalizada]: ../virtual-machines-create-custom/
  [Como anexar um disco de dados à máquina virtual]: ../storage-windows-attach-disk/
  [Como fazer logon em uma máquina virtual executando o Linux]: ../virtual-machines-linux-how-to-log-on
  [Cancelamento do provisionamento da máquina virtual]: ./media/virtual-machines-linux-capture-image/LinuxDeprovision.png
  [Cancelamento da provisão da máquina virtual com êxito]: ./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png
  [Portal de Gerenciamento]: http://manage.windowsazure.com
  [Captura de imagem bem-sucedida]: ./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png
