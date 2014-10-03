<properties linkid="manage-windows-tutorial-virtual-machine-gallery" urlDisplayName="Create a virtual machine" pageTitle="Create a virtual machine running Windows Server in Azure" metaKeywords="Azure capture image vm, capturing vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Windows Server. " metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Criar uma máquina virtual executando o Windows Server

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/virtual-machines-windows-tutorial/" title="Portal do Azure" class="current">Portal do Azure</a><a href="/en-us/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Portal de visualiza&ccedil;&atilde;o do Azure">Portal de visualiza&ccedil;&atilde;o do Azure</a></div>

Este tutorial mostra como é fácil criar uma máquina virtual (VM) no Azure executando o Windows Server, usando a Galeria de imagem no Portal de Gerenciamento do Azure. A Galeria de imagem oferece uma variedade de imagens, inclusive os sistema operacionais Windows, os sistemas operacionais baseados em Linux e as imagens do aplicativo.

> [WACOM.NOTE] Não é necessária nenhuma experiência com VMs do Azure para concluir este tutorial. Porém, você precisa de uma conta do Azure. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure][].

Este tutorial mostra:

-   [Como criar a máquina virtual][]
-   [Como fazer o logon na máquina virtual após você criá-la][]
-   [Como anexar um disco de dados à nova máquina virtual][]

Para saber mais, consulte [Máquinas Virtuais][].

## <span id="createvirtualmachine"></span> </a>Como criar a máquina virtual

Esta seção mostra como usar a opção **Da galeria** no Portal de Gerenciamento para criar a máquina virtual. Esta opção fornece mais escolhas de configuração que a opção**Criação rápida**. Por exemplo, se desejar entrar em uma máquina virtual em uma rede virtual, será necessário usar a opção **Da galeria**.

> [WACOM.NOTE] Também é possível tentar um [Portal de visualização do Azure][1] melhor e customizável, automatizar a implantação de modelos de aplicativo de vários computadores, usar os recursos melhorados de monitoramento e diagnóstico de VM e mais. Os opções de configuração da VM disponível nos dois Portais sobrepõem-se bastante, mas não são idênticos.

[WACOM.INCLUDE [virtual-machines-create-WindowsVM][]]

## <span id="logon"></span> </a>Como fazer o logon na máquina virtual após você criá-la

É possível fazer logon na máquina virtual que você criou para gerenciar as configurações e os aplicativos que estão em execução.

[WACOM.INCLUDE [virtual-machines-log-on-win-server][]]

## <span id="attachdisk"></span> </a>Como anexar um disco de dados à nova máquina virtual

Esta seção mostra como anexar um disco de dados vazio à máquina virtual. Consulte o [Tutorial Anexar um disco de dados][] para obter mais informações de anexagem de discos vazios, assim como em como anexar os discos existentes.

1.  Entre no [Portal de Gerenciamento][] do Azure.

2.  Clique em **Máquinas Virtuais** e, em seguida, selecione a máquina virtual **MyTestVM**.

    ![Selecione MyTestVM][]

3.  Você pode ser levado primeiro à página Início Rápido. Se isso ocorrer, selecione **Painel** na parte superior.

    ![Selecione Painel][]

4.  Na barra de comandos, clique em **Anexar** e, em seguida, clique em **Anexar disco vazio** quando ele aparecer.

    ![Selecione Anexar da barra de comandos][]

5.  O **Nome da Máquina Virtuale**, **Local de Armazenamento**, **Nome do Arquivo**, e **Preferência de cachê do Host** já estão definidos para você. Tudo o que você precisa fazer é digitar o tamanho desejado para o disco. Digite **5** no campo **Tamanho**. Em seguida, clique na marca de seleção para anexar o disco vazio à máquina virtual.

    ![Especifique o tamanho do disco vazio][]

    > [WACOM.NOTE] Todos os discos criados a partir de um arquivo VHD no armazenameto do Microsoft Azure. Em **Nome do arquivo**, é possível fornecer o nome do arquivo VHD adicionado ao armazenamento, mas o Azure gera o nome do disco automaticamente.

6.  Volte para o painel para verificar se o disco vazio foi anexado com sucesso à máquina virtual. Estará listado como um segundo disco na lista **Discos** junto com o OS Disk.

    ![Anexar disco vazio][]

    Depois de anexar os dados do disco à máquina virtual, o disco estará offline e não inicializado. Antes de você poder usar os dados armazenados, você precisa fazer logon na máquina virtual e inicializar o disco.

7.  Conecte-se à máquina virtual usando as etapas na seção anterior, [Como fazer logon na máquina virtual após criá-la][Como fazer o logon na máquina virtual após você criá-la] (\#logon).

8.  Depois de fazer logon na máquina virtual, abra o **Gerenciador de Servidores**. No painel esquerdo, selecione **Arquivos e serviços de armazenamento**.

    ![Expandir Arquivo e Serviços de Armazenamento no Gerenciador do Servidor][]

9.  Selecione **Discos** a partir do menu expandido.

    ![Expandir Arquivo e Serviços de Armazenamento no Gerenciador do Servidor][2]

10. Na seção **Discos**, há três discos na lista: disco 0, disco 1 e disco 2. O disco 0 é o disco do OS, o disco 1 é um disco de recurso temporário (que não deve ser usado para o armazenamento de dados) e o disco 2 é o disco de dados anexado à máquina virtual. Observe que o disco de dados tem uma capacidade de 5 GB, como especificado anteriormente. Clique com o botão direito do mouse no disco 2 e selecione **Inicializar**.

    ![Começar inicialização][]

11. Clique em **Sim** para iniciar o processo de inicialização.

    ![Continuar a inicialização][]

12. Clique com o botão direito do mouse no disco 2 e selecione **Novo volume**.

    ![Criar o volume][]

13. Conclua o assistente usando os valores padrão fornecidos. Quando o assistente for concluído, um novo volume será listado na seção **Volumes**.

    ![Criar o volume][3]

    O disco está online e pronto para ser usado com uma nova letra de unidade.

## Próximas etapas

Para saber mais sobre a configuração de máquinas virtuais do Windows no Azure, consulte os artigos a seguir:

[Como conectar máquinas virtuais em um serviço de nuvem][]

[Como criar e carregar um Disco Rígido Virtual que contém o sistema operacional Windows Server][]

[Anexar um disco de dados à máquina virtual][Tutorial Anexar um disco de dados]

[Gerenciar a disponibilidade de máquinas virtuais][]

  [Portal do Azure]: /en-us/documentation/articles/virtual-machines-windows-tutorial/ "Portal do Azure"
  [Portal de visualização do Azure]: /en-us/documentation/articles/virtual-machines-windows-tutorial-azure-preview/ "Portal de visualização do Azure"
  [Criar uma conta do Azure]: http://www.windowsazure.com/en-us/develop/php/tutorials/create-a-windows-azure-account/
  [Como criar a máquina virtual]: #createvirtualmachine
  [Como fazer o logon na máquina virtual após você criá-la]: #logon
  [Como anexar um disco de dados à nova máquina virtual]: #attachdisk
  [Máquinas Virtuais]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [1]: https://portal.azure.com
  [virtual-machines-create-WindowsVM]: ../includes/virtual-machines-create-WindowsVM.md
  [virtual-machines-log-on-win-server]: ../includes/virtual-machines-log-on-win-server.md
  [Tutorial Anexar um disco de dados]: http://www.windowsazure.com/en-us/documentation/articles/storage-windows-attach-disk/
  [Portal de Gerenciamento]: http://manage.windowsazure.com
  [Selecione MyTestVM]: ./media/virtual-machines-windows-tutorial/selectvm.png
  [Selecione Painel]: ./media/virtual-machines-windows-tutorial/dashboard.png
  [Selecione Anexar da barra de comandos]: ./media/virtual-machines-windows-tutorial/commandbarattach.png
  [Especifique o tamanho do disco vazio]: ./media/virtual-machines-windows-tutorial/emptydisksize.png
  [Anexar disco vazio]: ./media/virtual-machines-windows-tutorial/disklistwithdatadisk.png
  [Expandir Arquivo e Serviços de Armazenamento no Gerenciador do Servidor]: ./media/virtual-machines-windows-tutorial/fileandstorageservices.png
  [2]: ./media/virtual-machines-windows-tutorial/selectdisks.png
  [Começar inicialização]: ./media/virtual-machines-windows-tutorial/initializedisk.png
  [Continuar a inicialização]: ./media/virtual-machines-windows-tutorial/yesinitialize.png
  [Criar o volume]: ./media/virtual-machines-windows-tutorial/initializediskvolume.png
  [3]: ./media/virtual-machines-windows-tutorial/newvolumecreated.png
  [Como conectar máquinas virtuais em um serviço de nuvem]: http://www.windowsazure.com/en-us/documentation/articles/cloud-services-connect-virtual-machine/
  [Como criar e carregar um Disco Rígido Virtual que contém o sistema operacional Windows Server]: http://www.windowsazure.com/en-us/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [Gerenciar a disponibilidade de máquinas virtuais]: http://www.windowsazure.com/en-us/documentation/articles/manage-availability-virtual-machines/
