<properties urlDisplayName="Create a virtual machine" pageTitle="Criar uma m&aacute;quina virtual que executa o Windows no Azure" metaKeywords="Azure capture image vm, capturing vm" description="Saiba como criar uma VM (m&aacute;quina virtual) do Windows no Azure, fazer logon e anexar um disco de dados" metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="kathydav, rasquill" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/12/2014" ms.author="kathydav" />

# Criar uma máquina virtual que executa o Windows

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/virtual-machines-windows-tutorial/" title="Portal do Azure" class="current">Portal do Azure</a><a href="/pt-br/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Portal de visualiza&ccedil;&atilde;o do Azure">Portal de visualiza&ccedil;&atilde;o do Azure</a></div>

Este tutorial mostra como é fácil criar uma máquina virtual (VM) no Azure executando o Windows, usando como exemplo uma imagem do Windows Server da Galeria de Imagens no Portal de Gerenciamento do Azure. A Galeria de imagem oferece uma variedade de imagens, inclusive os sistema operacionais Windows, os sistemas operacionais baseados em Linux e as imagens do aplicativo.

> [WACOM.NOTE] Não é necessária nenhuma experiência com VMs do Azure para concluir este tutorial. Porém, você precisa de uma conta do Azure. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure][Criar uma conta do Azure].

Este tutorial mostra:

-   [Como criar a máquina virtual][Como criar a máquina virtual]
-   [Como fazer o logon na máquina virtual após você criá-la][Como fazer o logon na máquina virtual após você criá-la]
-   [Como anexar um disco de dados à nova máquina virtual][Como anexar um disco de dados à nova máquina virtual]

Para saber mais, consulte [Máquinas Virtuais][Máquinas Virtuais].

## <span id="createvirtualmachine"></span> </a>Como criar a máquina virtual

Esta seção mostra como usar a opção **Da galeria** no Portal de Gerenciamento para criar a máquina virtual. Esta opção fornece mais escolhas de configuração que a opção **Criação rápida**. Por exemplo, se desejar entrar em uma máquina virtual em uma rede virtual, será necessário usar a opção **Da galeria**.

> [WACOM.NOTE] O número e o tipo de imagens que são disponibilizadas na galeria dependem do tipo de assinatura que você tem. Este tutorial usa uma imagem do Windows Server, mas uma assinatura do MSDN pode ter imagens adicionais disponíveis, incluindo imagens da área de trabalho.

> Também é possível tentar um [Portal de Visualização do Azure][Portal de Visualização do Azure] melhor e customizável, para criar uma máquina virtual, automatizar a implantação de modelos de aplicativo de vários computadores, usar os recursos melhorados de monitoramento e diagnóstico de VM e mais. Os opções de configuração da VM disponível nos dois Portais sobrepõem-se bastante, mas não são idênticos.

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <span id="logon"></span> </a>Como fazer o logon na máquina virtual após você criá-la

É possível fazer logon na máquina virtual que você criou para gerenciar as configurações e os aplicativos que estão em execução.

[WACOM.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

## <span id="attachdisk"></span> </a>Como anexar um disco de dados à nova máquina virtual

Esta seção mostra como anexar um disco de dados vazio à máquina virtual. Consulte o [Tutorial Anexar um disco de dados][Tutorial Anexar um disco de dados] para obter mais informações sobre anexação de discos vazios, assim como em como anexar os discos existentes.

1.  Entre no [Portal de Gerenciamento][Portal de Gerenciamento] do Azure.

2.  Clique em **Máquinas Virtuais** e, em seguida, selecione a máquina virtual **MyTestVM**.

    ![Selecione MyTestVM][Selecione MyTestVM]

3.  Você pode ser levado primeiro à página Início Rápido. Se isso ocorrer, selecione **Painel** na parte superior.

    ![Selecione Painel][Selecione Painel]

4.  Na barra de comandos, clique em **Anexar** e, em seguida, clique em **Anexar disco vazio** quando ele aparecer.

    ![Selecione Anexar da barra de comandos][Selecione Anexar da barra de comandos]

5.  O **Nome da Máquina Virtuale**, **Local de Armazenamento**, **Nome do Arquivo**, e **Preferência de cachê do Host** já estão definidos para você. Tudo o que você precisa fazer é digitar o tamanho desejado para o disco. Digite **5** no campo **Tamanho**. Em seguida, clique na marca de seleção para anexar o disco vazio à máquina virtual.

    > [WACOM.NOTE] Vale a pena lembrar que as imagens de disco no Azure são armazenadas como blobs de página no armazenamento do Azure. Fora do Azure, os discos rígidos virtuais podem usar um formato VHD ou VHDX. Eles também podem ser fixos, ter expansão dinâmica ou diferenciação. O Azure é compatível com discos fixos de formato VHD. O formato fixo dispõe o disco lógico linearmente dentro do arquivo, de forma que o deslocamento X do disco é armazenado no deslocamento X do blob. Um pequeno rodapé ao final do blob descreve as propriedades do VHD. Frequentemente, o formato fixo desperdiça espaço, porque muitos discos têm em si grandes intervalos não usados. No entanto, o Azure armazena arquivos .vhd em um formato livre, assim você obtém os benefícios de discos fixos e dinâmicos ao mesmo tempo. Leia mais sobre isso em [Sobre VHDs no Azure][Sobre VHDs no Azure]

    ![Especifique o tamanho do disco vazio][Especifique o tamanho do disco vazio]

    > [WACOM.NOTE] Todos os discos criados a partir de um arquivo VHD no armazenamento do Microsoft Azure. Em **Nome do arquivo**, é possível fornecer o nome do arquivo VHD adicionado ao armazenamento, mas o Azure gera o nome do disco automaticamente.

6.  Volte para o painel para verificar se o disco vazio foi anexado com sucesso à máquina virtual. Estará listado como um segundo disco na lista **Discos** junto com o OS Disk.

    ![Anexar disco vazio][Anexar disco vazio]

    Depois de anexar os dados do disco à máquina virtual, o disco estará offline e não inicializado. Antes de você poder usar os dados armazenados, você precisa fazer logon na máquina virtual e inicializar o disco.

7.  Conecte-se à máquina virtual usando as etapas na seção anterior, [Como fazer logon na máquina virtual após criá-la][Como fazer o logon na máquina virtual após você criá-la] (\#logon).

8.  Depois de entrar na máquina virtual, abra o **Gerenciador de Servidores**. No painel esquerdo, selecione **Arquivos e serviços de armazenamento**.

    ![Expandir Arquivo e Serviços de Armazenamento no Gerenciador do Servidor][Expandir Arquivo e Serviços de Armazenamento no Gerenciador do Servidor]

9.  Selecione **Discos** a partir do menu expandido.

    ![Expandir Arquivo e Serviços de Armazenamento no Gerenciador do Servidor][1]

10. Na seção **Discos**, há três discos na lista: disco 0, disco 1 e disco 2. O disco 0 é o disco do OS, o disco 1 é um disco de recurso temporário (que não deve ser usado para o armazenamento de dados) e o disco 2 é o disco de dados anexado à máquina virtual. Observe que o disco de dados tem uma capacidade de 5 GB, como especificado anteriormente. Clique com o botão direito do mouse no disco 2 e selecione **Inicializar**.

    ![Começar inicialização][Começar inicialização]

11. Clique em **Sim** para iniciar o processo de inicialização.

    ![Continuar a inicialização][Continuar a inicialização]

12. Clique com o botão direito do mouse no disco 2 e selecione **Novo volume**.

    ![Criar o volume][Criar o volume]

13. Conclua o assistente usando os valores padrão fornecidos. Quando o assistente for concluído, um novo volume será listado na seção **Volumes**.

    ![Criar o volume][2]

    O disco está online e pronto para ser usado com uma nova letra de unidade.

## Próximas etapas

Para saber mais sobre a configuração de máquinas virtuais do Windows no Azure, consulte os artigos a seguir:

[Como conectar máquinas virtuais em um serviço de nuvem][Como conectar máquinas virtuais em um serviço de nuvem]

[Como criar e carregar um Disco Rígido Virtual que contém o sistema operacional Windows Server][Como criar e carregar um Disco Rígido Virtual que contém o sistema operacional Windows Server]

[Gerenciar a disponibilidade de máquinas virtuais][Gerenciar a disponibilidade de máquinas virtuais]

[Sobre as definições de configuração da VM do Azure][Sobre as definições de configuração da VM do Azure]

[VÍDEO: Introdução aos VHDs - O que realmente está acontecendo][VÍDEO: Introdução aos VHDs - O que realmente está acontecendo]

[VÍDEO: Perguntas frequentes com Mark Russinovich - O Microsoft Azure executa o Windows?][VÍDEO: Perguntas frequentes com Mark Russinovich - O Microsoft Azure executa o Windows?]

[VÍDEO: Adicionando uma nova máquina virtual a um Web Farm criando imagens reutilizáveis][VÍDEO: Adicionando uma nova máquina virtual a um Web Farm criando imagens reutilizáveis]

[VÍDEO: Adicionando discos rígidos virtuais, contas de armazenamento e dimensionando máquinas virtuais][VÍDEO: Adicionando discos rígidos virtuais, contas de armazenamento e dimensionando máquinas virtuais]

[VÍDEO: Scott Guthrie começa com as máquinas virtuais][VÍDEO: Scott Guthrie começa com as máquinas virtuais]

[VÍDEO: Princípios fundamentais de armazenamento e disco para máquinas virtuais do Azure][VÍDEO: Princípios fundamentais de armazenamento e disco para máquinas virtuais do Azure]

  [Criar uma conta do Azure]: http://www.windowsazure.com/pt-br/develop/php/tutorials/create-a-windows-azure-account/
  [Como criar a máquina virtual]: #createvirtualmachine
  [Como fazer o logon na máquina virtual após você criá-la]: #logon
  [Como anexar um disco de dados à nova máquina virtual]: #attachdisk
  [Máquinas Virtuais]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [Portal de Visualização do Azure]: https://portal.azure.com
  [Tutorial Anexar um disco de dados]: http://www.windowsazure.com/pt-br/documentation/articles/storage-windows-attach-disk/
  [Portal de Gerenciamento]: http://manage.windowsazure.com
  [Selecione MyTestVM]: ./media/virtual-machines-windows-tutorial/selectvm.png
  [Selecione Painel]: ./media/virtual-machines-windows-tutorial/dashboard.png
  [Selecione Anexar da barra de comandos]: ./media/virtual-machines-windows-tutorial/commandbarattach.png
  [Sobre VHDs no Azure]: http://msdn.microsoft.com/pt-br/library/azure/dn790344.aspx
  [Especifique o tamanho do disco vazio]: ./media/virtual-machines-windows-tutorial/emptydisksize.png
  [Anexar disco vazio]: ./media/virtual-machines-windows-tutorial/disklistwithdatadisk.png
  [Expandir Arquivo e Serviços de Armazenamento no Gerenciador do Servidor]: ./media/virtual-machines-windows-tutorial/fileandstorageservices.png
  [1]: ./media/virtual-machines-windows-tutorial/selectdisks.png
  [Começar inicialização]: ./media/virtual-machines-windows-tutorial/initializedisk.png
  [Continuar a inicialização]: ./media/virtual-machines-windows-tutorial/yesinitialize.png
  [Criar o volume]: ./media/virtual-machines-windows-tutorial/initializediskvolume.png
  [2]: ./media/virtual-machines-windows-tutorial/newvolumecreated.png
  [Como conectar máquinas virtuais em um serviço de nuvem]: http://www.windowsazure.com/pt-br/documentation/articles/cloud-services-connect-virtual-machine/
  [Como criar e carregar um Disco Rígido Virtual que contém o sistema operacional Windows Server]: http://www.windowsazure.com/pt-br/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [Gerenciar a disponibilidade de máquinas virtuais]: http://www.windowsazure.com/pt-br/documentation/articles/manage-availability-virtual-machines/
  [Sobre as definições de configuração da VM do Azure]: http://msdn.microsoft.com/library/azure/dn763935.aspx
  [VÍDEO: Introdução aos VHDs - O que realmente está acontecendo]: http://azure.microsoft.com/pt-br/documentation/videos/getting-started-with-azure-virtual-machines
  [VÍDEO: Perguntas frequentes com Mark Russinovich - O Microsoft Azure executa o Windows?]: http://azure.microsoft.com/pt-br/documentation/videos/mark-russinovich-windows-on-azure
  [VÍDEO: Adicionando uma nova máquina virtual a um Web Farm criando imagens reutilizáveis]: http://azure.microsoft.com/pt-br/documentation/videos/adding-virtual-machines-web-farm
  [VÍDEO: Adicionando discos rígidos virtuais, contas de armazenamento e dimensionando máquinas virtuais]: http://azure.microsoft.com/pt-br/documentation/videos/adding-drives-scaling-virtual-machines
  [VÍDEO: Scott Guthrie começa com as máquinas virtuais]: http://azure.microsoft.com/pt-br/documentation/videos/virtual-machines-scottgu
  [VÍDEO: Princípios fundamentais de armazenamento e disco para máquinas virtuais do Azure]: http://azure.microsoft.com/pt-br/documentation/videos/storage-and-disks-virtual-machines
