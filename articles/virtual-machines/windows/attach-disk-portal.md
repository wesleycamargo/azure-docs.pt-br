---
title: "Anexar um disco de dados não gerenciado a uma VM Windows – Azure | Microsoft Docs"
description: "Como anexar um disco de dados não gerenciado novo ou existente a uma VM Windows no portal do Azure usando o modelo de implantação do Resource Manager."
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
ms.date: 05/09/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: d02f92a8809efd6f58312af8cb40739299ea28f6
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-attach-an-unmanaged-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Como anexar um disco de dados não gerenciado a uma VM Windows no portal do Azure

Este artigo mostra como anexar discos não gerenciados novos e existentes a máquinas virtuais Windows por meio do portal do Azure. Você também pode [anexar um disco de dados usando o PowerShell](./attach-disk-ps.md). Antes de fazer isso, revise estas dicas:

* O tamanho da máquina virtual controla quantos discos de dados você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](sizes.md).
* Para usar o Armazenamento Premium, você precisa de uma máquina virtual da série DS ou GS. Você pode usar discos de contas de armazenamento Premium e Standard com essas máquinas virtuais. O armazenamento Premium está disponível em determinadas regiões. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](../../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para um novo disco, você não precisa criá-lo primeiro porque o Azure cria quando você anexa o mesmo.


Você também pode [anexar um disco de dados usando o Powershell](attach-disk-ps.md).


## <a name="find-the-virtual-machine"></a>Localizar a máquina virtual
1. Entre no [Portal do Azure](https://portal.azure.com/).
2. No menu à esquerda, clique em **Máquinas Virtuais**.
3. Selecione a máquina virtual na lista.
4. Na folha Máquinas virtuais, clique em **Discos**.
   
Continue seguindo as instruções para anexar um [novo disco](#option-1-attach-a-new-disk) ou um [disco existente](#option-2-attach-an-existing-disk).

## <a name="option-1-attach-and-initialize-a-new-disk"></a>Opção 1: Anexar e inicializar um novo disco
1. Na folha **Discos**, clique em **+ Adicionar disco de dados**.
2. Na folha **Anexar disco gerenciado**, digite um nome para o disco em **Nome** e, em seguida, selecione **Novo (disco vazio)** em **Tipo de fonte**.
3. Em **Contêiner de armazenamento**, clique no botão **Procurar** e acesse a conta de armazenamento e o contêiner nos quais você deseja que o novo VHD seja armazenado e, em seguida, clique em **Selecionar**. 
  
   ![Analisar configurações de disco](./media/attach-disk-portal/attach-empty-unmanaged.png)
   
3. Quando concluir as configurações do disco de dados, clique em **OK**.
4. Volte à folha **Discos** e clique em **Salvar** para adicionar o disco à configuração da VM.


### <a name="initialize-a-new-data-disk"></a>Inicializar um novo disco de dados

1. Conectar-se à máquina virtual. Para obter instruções, consulte [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
1. Clique no menu **Iniciar** dentro da VM, digite **diskmgmt.msc** e clique em **Enter**. Isso inicia o snap-in Gerenciamento de Disco.
2. O Gerenciamento de Disco reconhece que você tem um disco novo não inicializado e a janela Inicializar Disco será exibida.
3. Verifique se o novo disco está selecionado e clique em **OK** para inicializá-lo.
4. Agora, o novo disco é exibido como **não alocado**. Clique com o botão direito do mouse em qualquer lugar do disco e selecione **Novo volume simples**. O assistente de **Novo Volume Simples** é iniciado.
5. Percorra as etapas do assistente mantendo todos os padrões. Quando terminar, selecione **Concluir**.
6. Feche Gerenciamento de Disco.
7. Você recebe um pop-up que é necessário para formatar o novo disco antes que você possa usá-lo. Clique em **Formatar disco**.
8. Na caixa de diálogo **Formatar novo disco**, verifique as configurações e, em seguida, clique em **Iniciar**.
9. Você recebe um aviso informando que a formatação dos discos apaga todos os dados; clique em **OK**.
10. Quando a formatação for concluída, clique em **OK**.


## <a name="option-2-attach-an-existing-disk"></a>Opção 2: Anexar um disco existente
1. Na folha **Discos**, clique em **+ Adicionar disco de dados**.
2. Na folha **Anexar disco não gerenciado**, em **Tipo de fonte**, selecione **Blob existente**.

    ![Analisar configurações de disco](./media/attach-disk-portal/attach-existing-unmanaged.png)

    3. Clique em **Procurar** para navegar para a conta de armazenamento e o contêiner em que o VHD existente está localizado. Clique no VHD e, depois, em **Selecionar**.
4. Clique em **OK** na folha **Anexar disco não gerenciado**.
5. Na folha **Discos**, clique em **Salvar** para adicionar o disco à configuração da VM.
   


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
                
Após a exclusão de dados do disco, você pode garantir a liberação de operações TRIM corretamente executando a desfragmentação com TRIM:

```
defrag.exe <volume:> -l
```

## <a name="next-steps"></a>Próximas etapas
Caso seu aplicativo precise usar a unidade D: para armazenar dados, é possível [alterar a letra da unidade do disco temporário do Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


