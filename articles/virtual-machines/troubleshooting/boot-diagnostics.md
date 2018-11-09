---
title: Diagnósticos de inicialização para VMs no Azure | Microsoft Docs
description: Visão geral dos dois recursos de depuração para máquinas virtuais no Azure
services: virtual-machines
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: 9341458336e4c95b84590eadbc86073e7dbf09a0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419547"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Como usar o diagnóstico de inicialização para solucionar problemas das máquinas virtuais no Azure

O suporte a dois recursos de depuração agora está disponível no Azure: Saída do Console e a Captura de Tela para o modelo de implantação do Resource Manager de máquinas virtuais do azure. 

Ao colocar sua própria imagem no Azure ou iniciar uma das imagens da plataforma, muitas razões podem contribuir para que uma máquina virtual fique em um estado não inicializável. Esses recursos permitem que você diagnostique e recupere as máquinas virtuais de falhas de inicialização facilmente.

Para máquinas virtuais do Linux, é possível exibir a saída do log de console com facilidade no Portal. Para máquinas virtuais do Windows e do Linux, o Azure também permite que você veja uma captura de tela da VM no hipervisor. Esses dois recursos têm suporte em máquinas virtuais do Azure em todas as regiões. Anotações, capturas de tela e saídas podem levar até 10 minutos para aparecer na sua conta de armazenamento.

Você pode selecionar a opção **Diagnósticos de inicialização** para exibir o log e a captura de tela.

![Gerenciador de Recursos](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)

## <a name="common-boot-errors"></a>Erros comuns de inicialização

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Não foi possível encontrar um sistema operacional](https://support.microsoft.com/help/4010142)
- [Falha de inicialização ou INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Habilitar o diagnóstico em uma nova máquina virtual
1. Ao criar uma nova máquina virtual no Portal do Azure, selecione **Azure Resource Manager** no menu suspenso do modelo de implantação:
 
    ![Gerenciador de Recursos](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. Em **Configurações**, habilite os **Diagnósticos de inicialização**e, em seguida, selecione uma conta de armazenamento que deseja colocar esses arquivos de diagnósticos.
 
    ![Criar VM](./media/virtual-machines-common-boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > O recurso de diagnóstico de inicialização não oferece suporte à conta de armazenamento premium. Se você usar a conta de armazenamento premium para o diagnóstico de inicialização, você poderá receber o erro StorageAccountTypeNotSupported quando você iniciar a VM.
    >
    > 

3. Se você estiver implantando um modelo do Azure Resource Manager, navegue até seu recurso de máquina virtual e acrescente a seção de diagnóstico do perfil. Lembre-se de usar o cabeçalho de versão de API "2015-06-15".

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. O perfil de diagnóstico permite que você selecione a conta de armazenamento em que deseja colocar esses logs.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('https://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

Para implantar um exemplo de máquina virtual com o diagnóstico de inicialização habilitado, confira nosso repositório aqui.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Habilitar o diagnóstico de inicialização na máquina virtual existente 

Para habilitar o diagnóstico de inicialização em uma máquina virtual existente, siga estas etapas:

1. Faça logon no [Portal do Azure](https://portal.azure.com) e selecione a máquina virtual.
2. Em **Suporte + solução de problemas**, selecione **Diagnósticos de inicialização** > **Configurações**, altere o status para **Ligado**e, em seguida, selecione uma conta de armazenamento. 
4. Verifique se a opção diagnóstico de inicialização está selecionada e, em seguida, salve as alterações.

    ![Atualizar VM existente](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

3. Reinicie a VM para que entre em vigor.


