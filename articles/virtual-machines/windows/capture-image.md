---
title: Capturar uma imagem de VM de uma VM do Azure generalizada | Microsoft Docs
description: "Saiba como capturar uma imagem de VM de uma VM do Azure generalizada criada no modelo de implantação do Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: afdae4a1-6dfb-47b4-902a-f327f9bfe5b4
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 8f1d488fd8f71bf90c8bf7b7c1544445ffbd7686
ms.contentlocale: pt-br
ms.lasthandoff: 05/26/2017


---
# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>Como capturar uma imagem de VM de uma VM do Azure generalizada | Microsoft Azure
Este artigo mostra como usar o Azure PowerShell para criar uma imagem de uma VM do Azure generalizada. Depois você pode usar a imagem para criar outra VM. Esta imagem inclui o disco do SO e os discos de dados anexados à máquina virtual. A imagem não inclui os recursos de rede virtual, de modo que você precisará configurar esses recursos quando criar a nova VM. 

## <a name="prerequisites"></a>Pré-requisitos
Você precisa ter a versão 1.0.x ou mais recente do Azure PowerShell instalada. Se você ainda não tiver instalado o PowerShell, leia [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para ver as etapas de instalação.

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizar a VM Windows usando Sysprep

O Sysprep remove todas as informações pessoais da conta, entre outros itens, e prepara o computador para ser utilizado como uma imagem. Para obter detalhes sobre o Sysprep, consulte [Como usar o Sysprep: uma introdução](http://technet.microsoft.com/library/bb457073.aspx).

Verifique se as funções de servidor em execução no computador são suportadas pelo Sysprep. Para obter mais informações, consulte [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se você estiver executando o Sysprep antes de carregar o VHD para o Azure pela primeira vez, verifique se você [preparou sua VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de executar o Sysprep. 
> 
> 

1. Entre na máquina virtual Windows.
2. Abra uma janela de prompt de comando como administrador. Altere o diretório para **%windir%\system32\sysprep** e, a seguir, execute`sysprep.exe`.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Configuração Inicial pelo Usuário do Sistema (OOBE)** e verifique se a caixa de seleção **Generalizar** está marcada.
4. Em **Opções de Desligamento**, selecione **Desligar**.
5. Clique em **OK**.
   
    ![Inicie o Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Quando o Sysprep for concluído, desligará a máquina virtual. Não reinicie a VM.


Você também pode generalizar uma VM Linux usando `sudo waagent -deprovision+user` e depois o PowerShell para capturar a VM. Para saber mais sobre como usar a CLI para capturar uma VM, veja [Como generalizar e capturar uma máquina virtual do Linux usando a CLI do Azure](../linux/capture-image.md)

## <a name="log-in-to-azure-powershell"></a>Fazer logon no Azure PowerShell
1. Abra o Azure PowerShell e conecte-se à sua conta do Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
   
    Uma janela pop-up é aberta para inserir as credenciais da conta do Azure.
2. Obtenha as IDs de assinatura das assinaturas disponíveis.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Defina a assinatura correta usando a ID da assinatura.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Desalocar a VM e definir o estado como generalizada
1. Desaloque os recursos da VM.
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    O *Status* da VM no Portal do Azure muda de **Parado** para **Parado (desalocado)**.
2. Defina o status da máquina virtual como **Generalizado**. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Verifique o status da VM. A seção **OSState/generalizado** da VM deve ter **DisplayStatus** definido como **VM generalizada**.  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Criar a imagem
1. Copie a imagem da máquina virtual para o contêiner de armazenamento de destino usando este comando. A imagem é criada na mesma conta de armazenamento que a máquina virtual original. O parâmetro `-Path` salva uma cópia do modelo JSON localmente. O parâmetro `-DestinationContainerName` é o nome do contêiner em que você quer manter as imagens. Se o contêiner não existir, ele será criado para você.
   
    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```
   
    Você pode obter a URL da imagem no modelo do arquivo JSON. Vá para a seção **recursos** > **storageProfile** > **osDisk** > **image** > **uri** para obter o caminho completo da imagem. A URL da imagem parece com esta: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
    Você também pode verificar o URI no portal. A imagem é copiada em um contêiner denominado **sistema** em sua conta de armazenamento. 

## <a name="next-steps"></a>Próximas etapas
* Agora você pode [criar uma VM da imagem](create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


