---
title: "Generalizar uma máquina virtual do Windows no Azure | Microsoft Docs"
description: "Saiba como usar o Sysprep para generalizar uma VM do Windows para usar com o modelo de implantação do Gerenciador de Recursos."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a745d400-c8be-48b4-a891-4a18495ef3fd
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: a0fca1e2a1d354be9ad34fb3a36714d0bdf393d2
ms.lasthandoff: 03/06/2017


---
# <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalizar uma máquina virtual do Windows usando o Sysprep
Esta seção mostra como generalizar a máquina virtual do Windows para usar como uma imagem. O Sysprep remove todas as informações pessoais da conta, entre outros itens, e prepara o computador para ser utilizado como uma imagem. Para obter detalhes sobre o Sysprep, consulte [Como usar o Sysprep: uma introdução](http://technet.microsoft.com/library/bb457073.aspx).

Verifique se as funções de servidor em execução no computador são suportadas pelo Sysprep. Para obter mais informações, consulte [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se você estiver executando o Sysprep antes de carregar o VHD para o Azure pela primeira vez, verifique se você [preparou sua VM](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de executar o Sysprep. 
> 
> 

1. Entre na máquina virtual Windows.
2. Abra uma janela de prompt de comando como administrador. Altere o diretório para **%windir%\system32\sysprep** e, a seguir, execute`sysprep.exe`.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Configuração Inicial pelo Usuário do Sistema (OOBE)** e verifique se a caixa de seleção **Generalizar** está marcada.
4. Em **Opções de Desligamento**, selecione **Desligar**.
5. Clique em **OK**.
   
    ![Inicie o Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)
6. Quando o Sysprep for concluído, desligará a máquina virtual. 

> [!IMPORTANT]
> Só reinicie a VM quando concluir o carregamento do VHD no Azure ou a criação de uma imagem da VM. Se a VM for reiniciada acidentalmente, execute o Sysprep para generalizá-la novamente.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* Se a VM for local, você pode agora [carregar o VHD no Azure](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Se a VM já está no Azure, você pode agora [criar uma imagem da VM generalizada](virtual-machines-windows-capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


