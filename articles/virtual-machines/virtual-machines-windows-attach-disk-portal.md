---
title: Anexar um disco de dados a uma VM do Windows | Microsoft Docs
description: "Como anexar um disco de dados novo ou existente a uma VM do Windows no portal do Azure usando o modelo de implantação do Gerenciador de Recursos."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3790fc59-7264-41df-b7a3-8d1226799885
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: b84e07b26506149cf9475491b32b9ff3ea9ae80d
ms.openlocfilehash: 40375aa411920f966aa6923f0899ca2f88a9ed39


---
# <a name="how-to-attach-a-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Como anexar um disco de dados a uma VM do Windows no portal do Azure
Este artigo mostra como anexar discos novos e existentes a uma máquina virtual Windows por meio do portal do Azure. Você também pode [anexar um disco de dados a uma VM Linux no portal do Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Antes de fazer isso, revise estas dicas:

* O tamanho da máquina virtual controla quantos discos de dados você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para usar o Armazenamento Premium, você precisará de uma máquina virtual da série DS ou GS. Você pode usar discos de contas de armazenamento Premium e Standard com essas máquinas virtuais. O armazenamento Premium está disponível em determinadas regiões. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para um novo disco, você não precisa criá-lo primeiro porque o Azure cria quando você anexa o mesmo.
* Para um disco existente, o arquivo. vhd deve estar disponível em uma conta de armazenamento do Azure. Você pode usar um .vhd que já existe, se ele não estiver anexado a outra máquina virtual, ou carregar seu próprio arquivo .vhd na conta de armazenamento.

Você também pode [anexar um disco de dados usando o Powershell](virtual-machines-windows-attach-disk-ps.md).


## <a name="find-the-virtual-machine"></a>Localizar a máquina virtual
1. Entre no [Portal do Azure](https://portal.azure.com/).
2. No menu Hub, clique em **Máquinas Virtuais**.
3. Selecione a máquina virtual na lista.
4. Na folha Máquinas Virtuais, em **Essentials**, clique em **Discos**.
   
    ![Abrir configurações de disco](./media/virtual-machines-windows-attach-disk-portal/find-disk-settings.png)

Continue seguindo as instruções para anexar um [novo disco](#option-1-attach-a-new-disk) ou um [disco existente](#option-2-attach-an-existing-disk).

## <a name="option-1-attach-and-initialize-a-new-disk"></a>Opção 1: Anexar e inicializar um novo disco
1. Na folha **Discos**, clique em **Anexar novo**.
2. Examine as configurações padrão, atualize conforme necessário e clique em **OK**.
   
   ![Analisar configurações de disco](./media/virtual-machines-windows-attach-disk-portal/attach-new.png)
3. Depois que o Azure cria o disco e o anexa à máquina virtual, o novo disco é listado nas configurações de disco da máquina virtual em **Discos de Dados**.

### <a name="initialize-a-new-data-disk"></a>Inicializar um novo disco de dados

1. Conectar-se à máquina virtual. Para obter instruções, consulte [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Depois de entrar na máquina virtual, abra o **Gerenciador de Servidores**. No painel esquerdo, selecione **Arquivos e serviços de armazenamento**.
   
    ![Abra o gerenciador de servidor.](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)
3. Expanda o menu e selecione **Discos**.
4. A seção **Discos** lista os discos. Na maioria dos casos, ele terá disco 0, disco 1 e disco 2. Disco 0 é o disco do sistema operacional, disco 1 é o disco temporário e disco 2 é o disco de dados que você acabou de anexar à VM. O novo disco de dados listará a Partição como **desconhecida**. Clique com o botão direito do mouse no disco e selecione **Inicializar**.
5. Você é notificado de que todos os dados serão apagados quando o disco for inicializado. Clique em **Sim** para confirmar o aviso e inicializar o disco. Depois de concluir, a partição será listada como **GPT**. Clique com o botão direito do mouse no disco novamente e selecione **Novo Volume**.
6. Conclua o assistente usando os valores padrão. Quando o assistente for concluído, a seção **Volumes** listará o novo volume. Agora, o disco está online e pronto para armazenar dados.

    ![Volume inicializado com êxito](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)


## <a name="option-2-attach-an-existing-disk"></a>Opção 2: Anexar um disco existente
1. Na folha **Discos**, clique em **Anexar existente**.
2. Em **Anexar disco existente**, clique em **Arquivo VHD**.
   
   ![Anexar disco existente](./media/virtual-machines-windows-attach-disk-portal/attach-existing.png)
3. Em **Contas de armazenamento**, selecione a conta e o contêiner que mantém o arquivo .vhd.
   
   ![Localização do VHD](./media/virtual-machines-windows-attach-disk-portal/find-storage-container.png)
4. Selecionar o arquivo .vhd
5. Em **Anexar disco existente**, o arquivo que você selecionou é listado em **Arquivo VHD**. Clique em **OK**.
6. Depois que o Azure anexa o disco à máquina virtual, ele é listado nas configurações de disco da máquina virtual em **Discos de Dados**.



## <a name="use-trim-with-standard-storage"></a>Usar TRIM com o armazenamento padrão

Se você usar o armazenamento padrão (HDD), será necessário habilitar o TRIM. O TRIM descarta os blocos não usados do disco para que você seja cobrado apenas pelo armazenamento que está efetivamente sendo usado. Isso poderá representar uma economia dos custos se você criar arquivos grandes e, em seguida, excluí-los. 

Você pode executar esse comando para verificar a configuração de TRIM. Abra um prompt de comando na sua VM do Windows e digite:

```
fsutil behavior query DisableDeleteNotify
```

Se o comando retornar 0, o TRIM estará habilitado corretamente. Se ele retornar 1, execute o seguinte comando para habilitar o TRIM:
```
fsutil behavior set DisableDeleteNotify 0
```

## <a name="next-steps"></a>Próximas etapas
Caso seu aplicativo precise usar a unidade D: para armazenar dados, é possível [alterar a letra da unidade do disco temporário do Windows](virtual-machines-windows-classic-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).




<!--HONumber=Feb17_HO2-->


