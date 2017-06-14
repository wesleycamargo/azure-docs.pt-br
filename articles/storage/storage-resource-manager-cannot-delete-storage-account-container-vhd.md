---
title: "Solucionar erros ao excluir contas de armazenamento do Azure, contêineres ou VHDs em uma implantação do Resource Manager | Microsoft Docs"
description: "Solucionar erros ao excluir contas de armazenamento do Azure, contêineres ou VHDs em uma implantação do Resource Manager"
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 75d5e82e2f4e747747f24376239e23f6512f916a
ms.contentlocale: pt-br
ms.lasthandoff: 11/17/2016


---
# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds-in-a-resource-manager-deployment"></a>Solucionar erros ao excluir contas de armazenamento do Azure, contêineres ou VHDs em uma implantação do Resource Manager
[!INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

Você pode receber erros ao tentar excluir a conta de armazenamento do Azure, o contêiner ou o VHD (disco rígido virtual) no [Portal do Azure](https://portal.azure.com). Este artigo fornece orientação a fim de ajudar a resolver o problema em uma implantação do Azure Resource Manager.

Se este artigo não solucionar o problema do Azure, visite os fóruns do Azure na [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou usando @AzureSupport no Twitter. Além disso, você pode registrar uma solicitação de suporte do Azure selecionando **Obter suporte** no site de [suporte do Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Sintomas
### <a name="scenario-1"></a>Cenário 1
Quando você tenta excluir um VHD em uma conta de armazenamento de uma implantação do Resource Manager, a seguinte mensagem de erro é exibida:

**Falha ao excluir o blob 'vhds/BlobName.vhd'. Erro: Atualmente, há uma concessão no blob e nenhuma ID de concessão foi especificada na solicitação**

Esse problema pode ocorrer porque uma VM (máquina virtual) tem uma concessão no VHD que você está tentando excluir.

### <a name="scenario-2"></a>Cenário 2
Quando você tenta excluir um contêiner em uma conta de armazenamento de uma implantação do Resource Manager, a seguinte mensagem de erro é exibida:

**Falha ao excluir o contêiner de armazenamento 'vhds'. Erro: Atualmente, há uma concessão no contêiner e nenhuma ID de concessão foi especificada na solicitação.**

Esse problema pode ocorrer porque o contêiner tem um VHD que está bloqueado no estado de concessão.

### <a name="scenario-3"></a>Cenário 3
Quando você tenta excluir uma conta de armazenamento em uma conta de armazenamento em uma implantação do Resource Manager, a seguinte mensagem de erro é exibida:

**Falha ao excluir a conta de armazenamento 'StorageAccountName'. Erro: A conta de armazenamento não pode ser excluída, pois seus artefatos estão em uso.**

Esse problema pode ocorrer porque a conta de armazenamento tem um VHD que está em estado de concessão.

## <a name="solution"></a>Solução
Para resolver esses problemas, você deve identificar o VHD que está causando o erro e a VM associada. Em seguida, desanexe o VHD da VM (para discos de dados) ou exclua a VM que está usando o VHD (para discos do sistema operacional). Isso remove a concessão do VHD e permite que ele seja excluído.

### <a name="step-1-identify-the-problem-vhd-and-the-associated-vm"></a>Etapa 1: Identificar o VHD com problema e a VM associada
1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu **Hub**, selecione **Todos os recursos**. Vá para a conta de armazenamento que você deseja excluir e selecione **Blobs** > **vhds**.

    ![Captura de tela do portal, com a conta de armazenamento e o contêiner "vhds" realçados](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)
3. Verifique as propriedades de cada VHD no contêiner. Localize o VHD que está no estado **Concedido**. Em seguida, determine qual VM está usando o VHD. Normalmente, você pode determinar qual VM armazena o VHD verificando o nome do VHD:

   * Os discos do sistema operacional geralmente seguem esta convenção de nomenclatura: NomeDaVMAAAAMMDDHHMMSS.vhd
   * Os discos de dados geralmente seguem esta convenção de nomenclatura: NomeDaVM-AAAAMMDD-HHMMSS.vhd

     ![Captura de tela das informações de contêiner no portal, com o nome da VM, o status de concessão de "Bloqueado", e o estado de concessão de "Concedido" realçado](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/locatevm.png)

### <a name="step-2-remove-the-lease-from-the-vhd"></a>Etapa 2: Remover a concessão do VHD
Para excluir a VM que está usando o VHD (para discos do sistema operacional):

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu do **Hub**, selecione **Máquinas Virtuais**.
3. Selecione a VM que contém uma concessão no VHD.
4. Certifique-se de que nada esteja usando ativamente a máquina virtual, e que você não precise mais da máquina virtual.
5. Na parte superior da folha **Detalhes da VM**, selecione **Excluir** e clique em **Sim** para confirmar.
6. A VM deve ser excluída, mas o VHD deve ser mantido. No entanto, o VHD não deve ter mais uma concessão. Talvez demore alguns minutos para o serviço ser liberado. Para verificar se a concessão foi liberada, acesse **Todos os recursos** > **Nome da Conta de Armazenamento** > **Blobs** > **vhds**. No painel **Propriedades de blob**, o valor do **Status de Concessão** deve ser **Desbloqueado**.

Para desanexar o VHD da VM que o está usando (para discos de dados):

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu do **Hub**, selecione **Máquinas Virtuais**.
3. Selecione a VM que contém uma concessão no VHD.
4. Selecione **Discos** na folha **Detalhes da VM**.
5. Selecione o disco de dados que contém uma concessão no VHD. Você pode determinar qual VHD está anexado ao disco verificando a URL do VHD.
6. Determine com certeza que nada está usando ativamente o disco de dados.
7. Clique em **Desanexar** na folha **Detalhes do disco**.
8. Agora, o disco deve ser desanexado da VM, e o VHD não deve ter mais uma concessão. Talvez demore alguns minutos para o serviço ser liberado. Para verificar se a concessão foi liberada, acesse **Todos os recursos** > **Nome da Conta de Armazenamento** > **Blobs** > **vhds**. No painel **Propriedades de blob**, o valor do **Status de Concessão** deve ser **Desbloqueado**.

## <a name="what-is-a-lease"></a>O que é uma concessão?
Uma concessão é um bloqueio que pode ser usado para controlar o acesso a um blob (por exemplo, um VHD). Quando um blob é concedido, somente os proprietários da concessão podem acessar o blob. Uma concessão é importante pelos seguintes motivos:

* Ele impede a corrupção de dados se vários proprietários tentarem gravar na mesma parte do blob ao mesmo tempo.
* Ela impede a exclusão do blob se algo o estiver usando ativamente (por exemplo, uma VM).
* Ela impede a exclusão da conta de armazenamento se algo a estiver usando ativamente (por exemplo, uma VM).

## <a name="next-steps"></a>Próximas etapas
* [Excluir uma conta de armazenamento](storage-create-storage-account.md#delete-a-storage-account)
* [How to break the locked lease of blob storage in Microsoft Azure (PowerShell) (Como interromper a liberação de bloqueio do armazenamento de blobs no Microsoft Azure (PowerShell))](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

