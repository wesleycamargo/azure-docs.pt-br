---
title: "Solucione erros ao excluir contas de Armazenamento do Azure, contêineres ou VHDs | Microsoft Docs"
description: "Solucione erros ao excluir contas de Armazenamento do Azure, contêineres ou VHDs"
services: storage
documentationcenter: 
author: genlin
manager: cshepard
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
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 11944dd38b1cc30106c0b76a108480c018ca39d4
ms.contentlocale: pt-br
ms.lasthandoff: 08/21/2017

---
# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds"></a>Solucione erros ao excluir contas de Armazenamento do Azure, contêineres ou VHDs

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

Para tal, use um dos seguintes métodos:

### <a name="method-1---use-azure-storage-explorer"></a>Método 1 – Usar o Gerenciador de Armazenamento do Azure

### <a name="step-1-identify-the-vhd-that-prevent-deletion-of-the-storage-account"></a>Etapa 1 Identificar o VHD que impede a exclusão da conta de armazenamento

1. Quando você excluir a conta de armazenamento, será exibida uma caixa de diálogo de mensagem como a seguinte: 

    ![mensagem ao excluir a conta de armazenamento](././media/storage-resource-manager-cannot-delete-storage-account-container-vhd/delete-storage-error.png) 

2. Verifique a **URL do Disco** para identificar a conta de armazenamento e o VHD que impede a exclusão da conta de armazenamento. No exemplo a seguir, a cadeia de caracteres antes de “.blob.core.windows.net “ é o nome da conta de armazenamento e “SCCM2012-2015-08-28.vhd” é o nome do VHD.  

        https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd

### <a name="step-2-delete-the-vhd-by-using-azure-storage-explorer"></a>Etapa 2 Excluir o VHD usando o Gerenciador de Armazenamento do Azure

1. Baixe e instale a versão mais recente do [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/). Essa ferramenta é um aplicativo independente da Microsoft que permite trabalhar com os dados do Armazenamento do Azure no Windows, macOS e Linux.
2. Abra o Gerenciador de Armazenamento do Azure e selecione ![o ícone de conta](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/account.png) na barra de ferramentas à esquerda, selecione seu ambiente do Azure e entre na conta.

3. Selecione todas as assinaturas ou a assinatura que contém a conta de armazenamento que você deseja excluir.

    ![adicionar assinatura](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/addsub.png)

4. Acesse a conta de armazenamento obtida da URL do disco anteriormente, selecione os **Contêineres de Blob** > **vhds** e pesquise o VHD que impede a exclusão da conta de armazenamento.
5. Se o VHD for encontrado, verifique a coluna **Nome da VM** para encontrar a máquina virtual que está usando esse VHD.

    ![Verifique a VM](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/check-vm.png)

6. Remova a concessão do VHD usando o Portal do Azure. Para obter mais informações, consulte [Remover a concessão do VHD](#remove-the-lease-from-the-vhd). 

7. Acesse o Gerenciador de Armazenamento do Azure, clique com o botão direito no VHD e selecione Excluir.

8. Excluir a conta de armazenamento.

### <a name="method-2---use-azure-portal"></a>Método 2 – Usar o Portal do Azure 

#### <a name="step-1-identify-the-vhd-that-prevent-deletion-of-the-storage-account"></a>Etapa 1 Identificar o VHD que impede a exclusão da conta de armazenamento

1. Quando você excluir a conta de armazenamento, será exibida uma caixa de diálogo de mensagem como a seguinte: 

    ![mensagem ao excluir a conta de armazenamento](././media/storage-resource-manager-cannot-delete-storage-account-container-vhd/delete-storage-error.png) 

2. Verifique a **URL do Disco** para identificar a conta de armazenamento e o VHD que impede a exclusão da conta de armazenamento. No exemplo a seguir, a cadeia de caracteres antes de “.blob.core.windows.net “ é o nome da conta de armazenamento e “SCCM2012-2015-08-28.vhd” é o nome do VHD.  

        https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd

2. Entre no [Portal do Azure](https://portal.azure.com).
3. No menu Hub, selecione **Todos os recursos**. Vá para a conta de armazenamento e selecione **Blobs** > **vhds**.

    ![Captura de tela do portal, com a conta de armazenamento e o contêiner "vhds" realçados](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)

4. Localize o VHD obtido da URL de disco anteriormente. Em seguida, determine qual VM está usando o VHD. Normalmente, você pode determinar qual VM armazena o VHD verificando o nome do VHD:

VM no modelo de desenvolvimento do Resource Manager

   * Discos de sistema operacional geralmente seguem esta convenção de nomenclatura: NomeDaVM-AAAA-MM-DD-HHMMSS.vhd
   * Discos de dados geralmente seguem esta convenção de nomenclatura: NomeDaVM-AAAA-MM-DD-HHMMSS.vhd

VM no modelo de desenvolvimento clássico

   * Discos de sistema operacional geralmente seguem esta convenção de nomenclatura: CloudServiceName-VMName-YYYY-MM-DD-HHMMSS.vhd
   * Discos de dados geralmente seguem esta convenção de nomenclatura: CloudServiceName-VMName-YYYY-MM-DD-HHMMSS.vhd

#### <a name="step-2-remove-the-lease-from-the-vhd"></a>Etapa 2: Remover a concessão do VHD

[Remova a concessão do VHD](#remove-the-lease-from-the-vhd) e exclua a conta de armazenamento.

## <a name="what-is-a-lease"></a>O que é uma concessão?
Uma concessão é um bloqueio que pode ser usado para controlar o acesso a um blob (por exemplo, um VHD). Quando um blob é concedido, somente os proprietários da concessão podem acessar o blob. Uma concessão é importante pelos seguintes motivos:

* Ele impede a corrupção de dados se vários proprietários tentarem gravar na mesma parte do blob ao mesmo tempo.
* Ela impede a exclusão do blob se algo o estiver usando ativamente (por exemplo, uma VM).
* Ela impede a exclusão da conta de armazenamento se algo a estiver usando ativamente (por exemplo, uma VM).

### <a name="remove-the-lease-from-the-vhd"></a>Remover a concessão do VHD
Se o VHD for um disco do sistema operacional, você deverá excluir a VM para remover a concessão:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu do **Hub**, selecione **Máquinas Virtuais**.
3. Selecione a VM que contém uma concessão no VHD.
4. Certifique-se de que nada esteja usando ativamente a máquina virtual, e que você não precise mais da máquina virtual.
5. Na parte superior da folha **Detalhes da VM**, selecione **Excluir** e clique em **Sim** para confirmar.
6. A VM deve ser excluída, mas o VHD pode ser mantido. No entanto, o VHD não deve ter mais uma concessão. Talvez demore alguns minutos para o serviço ser liberado. Para verificar se a concessão foi liberada, acesse **Todos os recursos** > **Nome da Conta de Armazenamento** > **Blobs** > **vhds**. No painel **Propriedades de blob**, o valor do **Status de Concessão** deve ser **Desbloqueado**.

Se o VHD for um disco de dados, desanexe o VHD da VM para remover a concessão:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu do **Hub**, selecione **Máquinas Virtuais**.
3. Selecione a VM que contém uma concessão no VHD.
4. Selecione **Discos** na folha **Detalhes da VM**.
5. Selecione o disco de dados que contém uma concessão no VHD. Você pode determinar qual VHD está anexado ao disco verificando a URL do VHD.
6. Determine com certeza que nada está usando ativamente o disco de dados.
7. Clique em **Desanexar** na folha **Detalhes do disco**.
8. Agora, o disco deve ser desanexado da VM, e o VHD não deve ter mais uma concessão. Talvez demore alguns minutos para o serviço ser liberado. Para verificar se a concessão foi liberada, acesse **Todos os recursos** > **Nome da Conta de Armazenamento** > **Blobs** > **vhds**. No painel **Propriedades de blob**, o valor do **Status de Concessão** deve ser **Desbloqueado**.

## <a name="next-steps"></a>Próximas etapas
* [Excluir uma conta de armazenamento](storage-create-storage-account.md#delete-a-storage-account)
* [How to break the locked lease of blob storage in Microsoft Azure (PowerShell) (Como interromper a liberação de bloqueio do armazenamento de blobs no Microsoft Azure (PowerShell))](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

