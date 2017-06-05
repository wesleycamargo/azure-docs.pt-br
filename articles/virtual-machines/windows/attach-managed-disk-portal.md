---
title: "Anexar um disco de dados gerenciado a uma VM Windows – Azure | Microsoft Docs"
description: "Como anexar um novo disco de dados gerenciado a uma VM Windows no portal do Azure usando o modelo de implantação do Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 9c29390756ac2cd925ed7d2989393e63ed0a1239
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-attach-a-managed-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Como anexar um disco de dados gerenciado a uma VM Windows no portal do Azure

Este artigo mostra como anexar um novo disco de dados gerenciado a máquinas virtuais Windows por meio do portal do Azure. Antes de fazer isso, revise estas dicas:

* O tamanho da máquina virtual controla quantos discos de dados você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](sizes.md).
* Para um novo disco, você não precisa criá-lo primeiro porque o Azure cria quando você anexa o mesmo.

Você também pode [anexar um disco de dados usando o Powershell](attach-disk-ps.md).



## <a name="add-a-data-disk"></a>Adicionar um disco de dados
1. No menu à esquerda, clique em **Máquinas Virtuais**.
2. Selecione a máquina virtual na lista.
3. Na folha da máquina virtual, clique em **Discos**.
   4. Na folha **Discos**, clique em **+ Adicionar disco de dados**.
5. No menu suspenso do novo disco, selecione **Criar vazio**.
6. Na folha **Criar disco gerenciado**, digite um nome para o disco e ajuste as outras configurações, conforme necessário. Quando terminar, clique em **Criar**.
7. Na folha **Discos**, clique em Salvar para salvar a nova configuração de disco da VM.
6. Depois que o Azure cria o disco e o anexa à máquina virtual, o novo disco é listado nas configurações de disco da máquina virtual em **Discos de Dados**.


## <a name="initialize-a-new-data-disk"></a>Inicializar um novo disco de dados

1. Conecte-se à VM.
1. Clique no menu Iniciar dentro da VM, digite **diskmgmt.msc** e clique em **Enter**. Isso iniciará o snap-in Gerenciamento de Disco.
2. O Gerenciamento de Disco reconhecerá que você tem um disco novo não inicializado e a janela Inicializar Disco será exibida.
3. Verifique se o novo disco está selecionado e clique em **OK** para inicializá-lo.
4. O novo disco agora será exibido como **não alocado**. Clique com o botão direito do mouse em qualquer lugar do disco e selecione **Novo volume simples**. O **Assistente para Novo Volume Simples** será iniciado.
5. Percorra as etapas do assistente mantendo todos os padrões. Quando terminar, selecione **Concluir**.
6. Feche Gerenciamento de Disco.
7. Você receberá um pop-up que é necessário para formatar o novo disco antes que você possa usá-lo. Clique em **Formatar disco**.
8. Na caixa de diálogo **Formatar novo disco**, verifique as configurações e, em seguida, clique em **Iniciar**.
9. Você receberá um aviso informando que a formatação dos discos apagará todos os dados; clique em **OK**.
10. Quando a formatação for concluída, clique em **OK**.

## <a name="next-steps"></a>Próximas etapas
Caso seu aplicativo precise usar a unidade D: para armazenar dados, é possível [alterar a letra da unidade do disco temporário do Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

