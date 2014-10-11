<properties linkid="manage-linux-howto-attach-a-disk" urlDisplayName="Attach a disk" pageTitle="Attach a disk to a virtual machine | Azure" metaKeywords="Azure Windows virtual machine, Azure attach disk, Azure initialize disk" description="Learn how to attach a data disk to an Azure virtual machine and initialize it so it's ready for use." metaCanonical="" services="virtual-machines,storage" documentationCenter="" title="How to Attach a Data Disk to a Virtual Machine" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Como anexar um disco de dados a uma máquina virtual do Windows

Você pode anexar discos vazios e discos que contêm dados. Em ambos os casos, os discos são, na verdade, arquivos .vhd que residem em uma conta de armazenamento do Azure. Também em ambos os casos, depois de anexar o disco, você precisará inicializá-lo, para que ele esteja pronto para uso.

> [WACOM.NOTE] É uma prática recomendada usar um ou mais discos separados para armazenar dados de uma máquina virtual. Quando você cria uma máquina virtual do Azure, ela tem um disco para o sistema operacional mapeado para a unidade C e um disco temporário mapeado para a unidade D. **Não utilize a unidade D para armazenar dados.** Como o nome sugere, ela fornece apenas armazenamento temporário. Não oferece redundância nem backup porque eles não residem no armazenamento do Azure.

-   [Como: Anexar um disco vazio][]
-   [Como: Anexar um disco existente][]
-   [Como: Inicializar um novo disco de dados no Windows Server][]

[WACOM.INCLUDE [howto-attach-disk-windows-linux][]]

## <span id="initializeinWS"></span></a>Como: Inicializar um novo disco de dados no Windows Server

1.  Conectar-se à máquina virtual usando as etapas listadas em [Como fazer logon na máquina virtual com o Windows Server em execução][].

2.  Após fazer o logon, abra o **Gerenciador de servidor**, no painel esquerdo, expanda **Armazenamento** e, em seguida, clique em **Gerenciamento de disco**.

    ![Abra o gerenciador de servidor.][]

3.  Clique com o botão direito do mouse em **Disco 2**, clique em **Inicializar Disco**, e depois em **OK**.

    ![Inicializar o disco][]

4.  Com o botão direito do mouse na área de alocação de espaço para o Disco 2, clique em **Novo Volume Simples**e, em seguida, conclua o assistente com os valores padrão.

    ![Inicializar o volume][]

   The disk is now online and ready to use with a new drive letter.

    ![Volume successfully initialized](./media/storage-windows-attach-disk/InitializeSuccess.png)

  [Como: Anexar um disco vazio]: #attachempty
  [Como: Anexar um disco existente]: #attachexisting
  [Como: Inicializar um novo disco de dados no Windows Server]: #initializeinWS
  [howto-attach-disk-windows-linux]: ../includes/howto-attach-disk-windows-linux.md
  [Como fazer logon na máquina virtual com o Windows Server em execução]: ../virtual-machines-log-on-windows-server/
  [Abra o gerenciador de servidor.]: ./media/storage-windows-attach-disk/ServerManager.png
  [Inicializar o disco]: ./media/storage-windows-attach-disk/InitializeDisk.png
  [Inicializar o volume]: ./media/storage-windows-attach-disk/InitializeDiskVolume.png
