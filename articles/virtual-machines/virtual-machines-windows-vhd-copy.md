---
title: "Criar uma cópia de uma VM especializada no Azure | Microsoft Docs"
description: "Saiba como criar uma cópia de uma VM especializada do Windows em execução no Azure, no modelo de implantação do Gerenciador de Recursos."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ce7e6cd3-6a4a-4fab-bf66-52f699b1398a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: e5bacb7ce32845b97e06cb0d35cc62935a3c86f6
ms.openlocfilehash: 79b597fb1d76ec0dd7bdfca701a16f885d245b94


---
# <a name="create-a-copy-of-a-specialized-windows-vm-running-in-azure"></a>Criar uma cópia de uma VM especializada do Windows em execução no Azure
Este artigo mostra como usar a ferramenta AZCopy para criar uma cópia do VHD de uma VM especializada do Windows que está em execução no Azure. Você pode usar a cópia do VHD para criar uma nova VM. 

* Se desejar copiar uma VM generalizada, confira [How to create a VM image from an existing generalized Azure VM](virtual-machines-windows-capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Como criar uma imagem de VM de uma VM do Azure generalizada existente).
* Se você quiser carregar um VHD de uma VM local, como uma criada usando o Hyper-V, confira [Upload a Windows VHD from an on-premises VM to Azure](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Carregar um VHD do Windows de uma VM local para o Azure).

## <a name="before-you-begin"></a>Antes de começar
Lembre-se de:

* Ter as informações sobre as **contas de armazenamento de origem e destino**. Para a VM de origem, você precisa dos nomes da conta de armazenamento e do contêiner. Normalmente, o nome do contêiner será **vhds**. Você também precisa ter uma conta de armazenamento de destino. Se não tiver uma, você poderá criá-la usando o portal (**Mais serviços** > Contas de armazenamento > Adicionar) ou usando o cmdlet [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx). 
* Instalar o [Azure PowerShell 1.0](/powershell/azureps-cmdlets-docs) (ou posterior).
* Baixar e instalar a [ferramenta AzCopy](../storage/storage-use-azcopy.md). 

## <a name="deallocate-the-vm"></a>Desalocar a VM
Desaloque a VM para liberar o VHD a ser copiado. 

* **Portal**: clique em **Máquinas virtuais** > **myVM** > Parar
* **PowerShell**: `Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM` desaloca a VM chamada **myVM** no grupo de recursos **myResourceGroup**.

O **Status** da VM no Portal do Azure muda de **Parado** para **Parado (desalocado)**.

## <a name="get-the-storage-account-urls"></a>Obter as URLs da conta de armazenamento
Você precisará das URLs das contas de armazenamento de origem e de destino. As URLs serão semelhantes a: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Se você já souber os nomes da conta de armazenamento e do contêiner, basta substituir as informações entre colchetes para criar sua URL. 

Você pode usar o Portal do Azure ou o Azure PowerShell para obter a URL:

* **Portal**: clique em **Mais serviços** > **Contas de armazenamento** > <storage account> **Blobs** e seu arquivo VHD de origem provavelmente estará no contêiner **vhds**. Clique em **Propriedades** do contêiner e copie o texto rotulado como **URL**. Você precisará das URLs dos contêineres de origem e de destino. 
* **PowerShell**: `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` obtém as informações da VM chamada **myVM** no grupo de recursos **myResourceGroup**. Nos resultados, examine a seção **Perfil de armazenamento** para o **URI do VHD**. A primeira parte do URI é a URL do contêiner e a última parte é o nome do VHD do sistema operacional da VM.

## <a name="get-the-storage-access-keys"></a>Obter as chaves de acesso de armazenamento
Localize as chaves de acesso para as contas de armazenamento de origem e destino. Para obter mais informações sobre as chaves de acesso, consulte [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md).

* **Portal**: clique em **Mais serviços** > **Contas de armazenamento** > <storage account> **Todas as configurações** > **Chaves de acesso**. Copie a chave rotulada como **key1**.
* **PowerShell**: `Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup` obtém a chave de armazenamento para a conta de armazenamento **mystorageaccount** no grupo de recursos **myResourceGroup**. Copie a chave rotulada como **key1**.

## <a name="copy-the-vhd"></a>Copie o VHD
Você pode copiar arquivos entre as contas de armazenamento usando o AzCopy. Para o contêiner de destino, se o contêiner especificado não existir, ele será criado para você. 

Para usar o AzCopy, abra um prompt de comando no computador local e navegue até a pasta na qual o AzCopy está instalado. Ela será semelhante a *C:\Arquivos de Programas (x86)\Microsoft SDKs\Azure\AzCopy*. 

Para copiar todos os arquivos dentro de um contêiner, você deve usar a opção **/S**. Ele pode ser usado para copiar o VHD do OS e todos os discos de dados, se eles estiverem no mesmo contêiner. Este exemplo mostra como copiar todos os arquivos no contêiner **mysourcecontainer** na conta de armazenamento **mysourcestorageaccount** para o contêiner **mydestinationcontainer** na conta de armazenamento **mydestinationstorageaccount**. Substitua os nomes das contas de armazenamento e dos contêineres por seus próprios. Substitua `<sourceStorageAccountKey1>` e `<destinationStorageAccountKey1>` pelas suas próprias chaves.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Se você quiser copiar apenas um VHD específico em um contêiner com vários arquivos, você também poderá especificar o nome do arquivo usando a opção /Pattern. Neste exemplo, somente o arquivo chamado **myFileName.vhd** será copiado.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Quando ele for concluído, você receberá uma mensagem como a seguinte:

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

## <a name="troubleshooting"></a>Solucionar problemas
* Ao usar o AZCopy, se ver o erro Falha do servidor na autenticação da solicitação, assegure-se de que o valor do cabeçalho Autorização esteja formado corretamente, incluindo a assinatura, e de que esteja usando a Chave 2 ou a chave de armazenamento secundária, tente usar a primeira chave de armazenamento ou a chave primária.

## <a name="next-steps"></a>Próximas etapas
* Você pode criar uma nova VM ao [anexar a cópia do VHD a uma VM como um disco do SO](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Jan17_HO4-->


