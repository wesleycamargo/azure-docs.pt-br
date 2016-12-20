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
ms.date: 11/02/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: e7ec8715087abaf5c96a6327d4bcde0ad3fd547a


---
# <a name="how-to-attach-a-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Como anexar um disco de dados a uma VM do Windows no portal do Azure
Este artigo mostra como anexar discos novos e existentes a uma máquina virtual Windows por meio do portal do Azure. Você também pode [anexar um disco de dados a uma VM Linux no portal do Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Antes de fazer isso, revise estas dicas:

* O tamanho da máquina virtual controla quantos discos de dados você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para um novo disco, você não precisa criá-lo primeiro porque o Azure cria quando você anexa o mesmo.
* Para um disco existente, o arquivo. vhd deve estar disponível em uma conta de armazenamento do Azure. Você pode usar um .vhd que já existe, se ele não estiver anexado a outra máquina virtual, ou carregar seu próprio arquivo .vhd na conta de armazenamento.

Você também pode [anexar um disco de dados usando o Powershell](virtual-machines-windows-ps-manage.md#add-a-data-disk-to-a-virtual-machine).

[!INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="a-idinitializeinwsahow-to-initialize-a-new-data-disk-in-windows-server"></a><a id="initializeinWS"></a>Como: inicializar um novo disco de dados no Windows Server
1. Conectar-se à máquina virtual. Para obter instruções, consulte [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Depois de entrar na máquina virtual, abra o **Gerenciador de Servidores**. No painel esquerdo, selecione **Arquivos e serviços de armazenamento**.
   
    ![Abra o gerenciador de servidor.](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)
3. Expanda o menu e selecione **Discos**.
4. A seção **Discos** lista os discos. Na maioria dos casos, ele terá disco 0, disco 1 e disco 2. Disco 0 é o disco do sistema operacional, disco 1 é o disco temporário e disco 2 é o disco de dados que você acabou de anexar à VM. O novo disco de dados listará a Partição como **desconhecida**. Clique com o botão direito do mouse no disco e selecione **Inicializar**.
5. Você é notificado de que todos os dados serão apagados quando o disco for inicializado. Clique em **Sim** para confirmar o aviso e inicializar o disco. Depois de concluir, a partição será listada como **GPT**. Clique com o botão direito do mouse no disco novamente e selecione **Novo Volume**.
6. Conclua o assistente usando os valores padrão. Quando o assistente for concluído, a seção **Volumes** listará o novo volume. Agora, o disco está online e pronto para armazenar dados.

    ![Volume inicializado com êxito](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [!NOTE]
> O tamanho da VM determina quantos discos você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="next-steps"></a>Próximas etapas
Caso seu aplicativo precise usar a unidade D: para armazenar dados, é possível [alterar a letra da unidade do disco temporário do Windows](virtual-machines-windows-classic-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).




<!--HONumber=Nov16_HO3-->


