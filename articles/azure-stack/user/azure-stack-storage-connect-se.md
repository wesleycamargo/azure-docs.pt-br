---
title: Conecte-se o Gerenciador de armazenamento para uma assinatura do Azure pilha ou uma conta de armazenamento | Microsoft Docs
description: Saiba como conectar-se o Gerenciador de armazenamento para uma assinatura de pilha do Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/20/2018
ms.author: mattbriggs
ms.reviewer: xiaofmao
ms.openlocfilehash: 8b670ec7040aab7eca26d411c9e31a934052be19
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Conecte-se o Gerenciador de armazenamento para uma assinatura do Azure pilha ou uma conta de armazenamento

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Azure Storage Explorer é um aplicativo autônomo que permite que você trabalhe facilmente com dados de armazenamento do Azure pilha no Linux, Windows e macOS. Há várias ferramentas disponíveis para mover dados para e do armazenamento de pilha do Azure. Para obter mais informações, consulte [transferência de dados de ferramentas para o armazenamento do Azure pilha](azure-stack-storage-transfer.md).

Neste artigo, você aprenderá como conectar-se às contas de armazenamento usando o Gerenciador de armazenamento e assinaturas de pilha do Azure. 

Se você ainda não instalou o Gerenciador de armazenamento ainda, [baixar](http://www.storageexplorer.com/) e instalá-lo.

Depois que você se conectar a uma assinatura do Azure pilha ou uma conta de armazenamento, você pode usar o [artigos Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) para trabalhar com os dados da pilha do Azure. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Preparar para conectar-se a pilha do Azure

Você precisa ter acesso direto para a pilha do Azure ou uma conexão VPN para o Gerenciador de armazenamento para acessar a assinatura de pilha do Azure. Para saber como configurar uma conexão VPN ao Azure Stack, confira [Como conectar-se com o Azure Stack com VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

Para o Kit de desenvolvimento de pilha do Azure, você precisa exportar o certificado de raiz da autoridade de pilha do Azure.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Exportar e importar o certificado de pilha do Azure

1. Abra `mmc.exe` em uma máquina de host de pilha do Azure, ou um computador local com uma conexão VPN com a pilha do Azure. 

2. Em **arquivo**, selecione **Adicionar/Remover Snap-in**e, em seguida, adicione **certificados** para gerenciar **minha conta de usuário**.

3. Em **Root\Certificated Console (computador Local) \Trusted Root Certification Authorities\Certificates** localizar **AzureStackSelfSignedRootCert**.

    ![Como carregar o certificado raiz do Azure Stack por meio do mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

4. Clique com botão direito no certificado, selecione **todas as tarefas** > **exportar**e, em seguida, siga as instruções para exportar o certificado com **x. 509 codificado na Base 64 (. CER)**.  

    O certificado exportado será usado na próxima etapa.

5. Iniciar o Gerenciador de armazenamento, e se você vir o **conectar ao armazenamento do Azure** caixa de diálogo caixa, cancelá-la.

6. Sobre o **editar** , aponte para **certificados SSL**e, em seguida, selecione **importar certificados**. Use a caixa de diálogo do seletor de arquivos para localizar e abrir o certificado que você exportou na etapa anterior.

    Após a importação, você será solicitado para reiniciar o Gerenciador de armazenamento.

    ![Importe o certificado no Gerenciador de armazenamento](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

7. Após a reinicialização do Gerenciador de armazenamento, selecione o **editar** menu e, em seguida, verifique se **pilha do Azure de destino** está selecionado. Se não estiver selecionado, selecione-o e, em seguida, reinicie o Gerenciador de armazenamento para que a alteração tenha efeito. Essa configuração é necessária para que haja compatibilidade com seu ambiente do Azure Stack.

    ![Confira se o Destino Azure Stack está selecionado](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription"></a>Conexão com uma assinatura do Azure Stack

Agora você está pronto para conectar-se o Gerenciador de armazenamento para uma assinatura de pilha do Azure.

1. No painel esquerdo do Gerenciador de armazenamento, selecione **gerenciar contas**.  
    Todos os a assinatura da Microsoft que você entrou são exibidos.

2. Para conectar-se para a assinatura de pilha do Azure, selecione **adicionar uma conta**.

    ![Adicionar uma conta do Azure Stack](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. Na caixa de diálogo do armazenamento do Azure, conectar em **ambiente Azure**, selecione **Azure** ou **do Azure na China**, que depende da conta de pilha do Azure que está sendo usada, e em seguida, selecione **entrar**. Para entrar com a conta de pilha do Azure que está associado a pelo menos uma assinatura ativa do Azure pilha.

    ![Conectar ao armazenamento do Azure](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Depois de entrar com êxito usando uma conta do Azure Stack, o painel esquerdo é preenchido com as assinaturas do Azure Stack associadas à conta. Selecione as assinaturas de pilha do Azure que você deseja trabalhar e, em seguida, selecione Aplicar. (Selecionar ou desmarcar todas as alternâncias de caixa de seleção de assinaturas selecionando todas ou nenhuma das assinaturas do Azure pilha listadas).

    ![Selecione as assinaturas do Azure Stack depois de preencher a caixa de diálogo do ambiente de nuvem personalizado](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)  

    O painel esquerdo exibe as contas de armazenamento associadas às assinaturas do Azure Stack selecionadas.

    ![Lista de contas de armazenamento, incluindo contas de assinatura do Azure Stack](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Conecte-se a uma conta de armazenamento do Azure pilha

Você também pode se conectar a uma conta de armazenamento do Azure pilha usando o nome da conta de armazenamento e o par de chaves.

1.  No painel esquerdo do Gerenciador de armazenamento, selecione Gerenciar contas. Todas as contas da Microsoft que você entrou são exibidas.

    ![Adicionar uma conta](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2.  Para conectar-se para a assinatura de pilha do Azure, selecione **adicionar uma conta**.
 
    ![Adicionar uma conta](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3.  Em conectar-se a caixa de diálogo do armazenamento do Azure, selecione **usar um nome de conta de armazenamento e chave**.

4. Insira seu nome de conta no **nome da conta**, cole a chave de conta no **chave da conta** caixa de texto, selecione **outros (Insira abaixo)** em **pontos de extremidade de armazenamento domínio** e o ponto de extremidade do Azure pilha de entrada.  

    Um ponto de extremidade de pilha do Azure inclui duas partes: o nome de uma região e o domínio de pilha do Azure. O Kit de desenvolvimento de pilha do Azure, o ponto de extremidade padrão é **local.azurestack.external**. Se você não tiver certeza sobre o ponto de extremidade, entre em contato com seu administrador de nuvem.

    ![Anexar o nome e a chave](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5.  Selecione **Conectar**.
6.  Depois que a conta de armazenamento tenha sido anexada com êxito, a conta de armazenamento é exibida com (**externo,**) anexados a seu nome.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Gerenciador de armazenamento](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Armazenamento do Azure de pilha: as diferenças e considerações](azure-stack-acs-differences.md)
* Para saber mais sobre o armazenamento do Azure, consulte [Introdução ao armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)
