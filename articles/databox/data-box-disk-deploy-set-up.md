---
title: Configurar o Disco do Microsoft Azure Data Box | Microsoft Docs
description: Use este tutorial para aprender a configurar o Disco do Azure Data Box
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 7f382e3b6e70aadf8c6a090a3d5c049f6b5c0337
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010354"
---
# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Tutorial: Desempacotar, conectar e desbloquear o Disco do Azure Data Box

Este tutorial descreve como desempacotar, conectar e desbloquear o Disco do Azure Data Box.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Desempacotar o Disco do Data Box
> * Conecte e desbloqueie o Disco do Data Box.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você concluiu o [Tutorial: Ordenar o Disco do Azure Data Box](data-box-disk-deploy-ordered.md).
2. Você recebeu os discos e o status do trabalho o portal foi atualizado para **Entregue**.
3. Você possui um computador host no qual você pode instalar a ferramenta de desbloqueio do Disco do Data Box. O computador host deve
    - Executar um [Sistema operacional com suporte](data-box-disk-system-requirements.md).
    - Ter o [Windows PowerShell 4 instalado](https://www.microsoft.com/download/details.aspx?id=40855).
    - Ter o [.NET Framework 4.5 instalado](https://www.microsoft.com/download/details.aspx?id=30653).

## <a name="unpack-your-disks"></a>Desempacotar os discos

 Conclua as etapas a seguir para desempacotar os discos.

1. Os discos do Data Box Disks são enviados pelo correio em uma caixa pequena. Abra a caixa e remova o seu conteúdo. Verifique se a caixa possui de 1 a 5 discos de estado sólido (SSDs) e um cabo de conexão USB por disco. Inspecione a caixa e verifique se há algum sinal de adulteração ou qualquer outro dano visível. 

    ![Pacote de envio do Disco do Data Box](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Se a caixa estiver adulterada ou extremamente danificada, não abra-a. Entre em contato com o Suporte da Microsoft para obter ajuda e avaliar se os discos estão em boas condições de funcionamento e se é necessário enviar novos discos.
3. Verifique se a caixa possui um compartimento transparente com uma etiqueta de remessa (abaixo da etiqueta atual) para devolução. Se essa etiqueta for perdida ou danificada, você pode baixar e imprimir uma nova no portal do Azure. 

    ![Etiqueta de remessa do Disco do Data Box](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Guarde a caixa e a espuma da embalagem para devolver os discos.

## <a name="connect-and-unlock-your-disks"></a>Conectar e desbloquear os discos

Execute as seguintes etapas para conectar e desbloquear os discos.

1. Use o cabo incluído para conectar o disco em um computador Windows executando um SO com suporte conforme especificado nos pré-requisitos. 

    ![Conectar o Disco do Data Box](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. No portal do Azure, vá até **Geral > Detalhes do Dispositivo**. 
3. Clique em **Ferramenta de desbloqueio do Disco do Data Box**. 

    ![Download da ferramenta de desbloqueio do Disco do Data Box](media/data-box-disk-deploy-set-up/data-box-disk-unload1.png)     

4. Extraia a ferramenta no mesmo computador que você usará para copiar os dados.
5. Abra uma janela do prompt de comando ou execute o Windows PowerShell como administrador no mesmo computador.
6. (Opcional) Para verificar se o computador que você está usando para desbloquear o disco atende aos requisitos de sistema operacional, execute o comando de verificação do sistema. Um exemplo de saída é mostrado abaixo. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

7. No portal do Azure, vá até **Geral > Detalhes do Dispositivo**. Use o ícone de cópia para copiar a chave de acesso.
8. Execute `DataBoxDiskUnlock.exe` e forneça a chave de acesso. A letra da unidade atribuída ao disco é exibida. Um exemplo de saída é mostrado abaixo.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkeys (format: passkey1;passkey2;passkey3):
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

9. Repita as etapas 6 a 8 para quaisquer futuras reinserções de disco. Use o comando de ajuda se você precisar de ajuda com a ferramenta de desbloqueio do Disco do Data Box.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKeys:<passkey_list_separated_by_semicolon>
    
    Example: DataBoxUnlock /PassKeys:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKeys:       Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
10. Depois que o disco estiver desbloqueado, você pode exibir o conteúdo do disco.    

    ![Conteúdo do Disco do Data Box](media/data-box-disk-deploy-set-up/data-box-disk-content.png) 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box Disk como:

> [!div class="checklist"]
> * Desempacotar o Disco do Data Box
> * Conectar e desbloquear os Discos do Data Box


Avance para o próximo tutorial para saber como copiar dados para o Disco do Data Box.

> [!div class="nextstepaction"]
> [Copiar dados para o seu Disco do Data Box](./data-box-disk-deploy-copy-data.md)

