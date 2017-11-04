---
title: Conecte-se o Gerenciador de armazenamento para uma assinatura de pilha do Azure
description: Saiba como conectar o armazenamento Exporer a uma assinatura de pilha do Azure
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: c7e6d70148d39fd74f6409a0a239833f8e9f7614
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription"></a>Conecte-se o Gerenciador de armazenamento para uma assinatura de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Gerenciador de armazenamento do Azure (visualização) é um aplicativo autônomo que permite que você trabalhe facilmente com dados de armazenamento do Azure pilha no Linux, Windows e macOS. Há vários disponíveis ferramentas para mover dados para e do armazenamento de pilha do Azure. Para obter mais informações, consulte [transferência de dados de ferramentas para o armazenamento do Azure pilha](azure-stack-storage-transfer.md).

Neste artigo, você aprenderá como conectar-se às suas contas de armazenamento do Azure pilha usando o Gerenciador de armazenamento. 

Se você ainda não instalou o Gerenciador de armazenamento ainda, [baixar](http://www.storageexplorer.com/) e e instalá-lo.

Depois de se conectar à sua assinatura de pilha do Azure, você pode usar o [artigos Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) para trabalhar com os dados da pilha do Azure. 

## <a name="prepare-an-azure-stack-subscription"></a>Preparar uma assinatura de pilha do Azure

Você precisa ter acesso à área de trabalho da máquina de host de pilha do Azure ou uma conexão VPN para o Gerenciador de armazenamento acessar a assinatura de pilha do Azure. Para saber como configurar uma conexão VPN ao Azure Stack, confira [Como conectar-se com o Azure Stack com VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

Para o Kit de desenvolvimento de pilha do Azure, você precisa exportar o certificado de raiz da autoridade de pilha do Azure.

### <a name="to-export-and-then-import-the-azure-stack-certificate"></a>Para exportar e importar o certificado de pilha do Azure

1. Abra `mmc.exe` em uma máquina de host de pilha do Azure, ou um computador local com uma conexão VPN com a pilha do Azure. 

2. Em **arquivo**, selecione **Adicionar/Remover Snap-in**e, em seguida, adicione **certificados** para gerenciar **minha conta de usuário**.



3. Em **Root\Certificated Console (computador Local) \Trusted Root Certification Authorities\Certificates** localizar **AzureStackSelfSignedRootCert**.

    ![Como carregar o certificado raiz do Azure Stack por meio do mmc.exe][25]

4. Clique com botão direito no certificado, selecione **todas as tarefas** > **exportar**e, em seguida, siga as instruções para exportar o certificado com **x. 509 codificado na Base 64 (. CER)**.  

    O certificado exportado será usado na próxima etapa.
5. Iniciar o Gerenciador de armazenamento (visualização), e se você vir o **conectar ao armazenamento do Azure** caixa de diálogo caixa, cancelá-la.

6. Sobre o **editar** , aponte para **certificados SSL**e, em seguida, clique em **importar certificados**. Use a caixa de diálogo do seletor de arquivos para localizar e abrir o certificado que você exportou na etapa anterior.

    Após a importação, você será solicitado para reiniciar o Gerenciador de armazenamento.

    ![Importe o certificado no Gerenciador de armazenamento (visualização)][27]

Agora você está pronto para conectar-se o Gerenciador de armazenamento para uma assinatura de pilha do Azure.

### <a name="to-connect-an-azure-stack-subscription"></a>Para se conectar a uma assinatura de pilha do Azure


1. Após reiniciar o Gerenciador de armazenamento (visualização), selecione o menu **Editar** e verifique se o **Azure Stack de destino** está selecionado. Se não estiver selecionado, selecione-o e, em seguida, reinicie o Gerenciador de armazenamento para que a alteração tenha efeito. Essa configuração é necessária para que haja compatibilidade com seu ambiente do Azure Stack.

    ![Confira se o Destino Azure Stack está selecionado][28]

7. No painel esquerdo, selecione **Gerenciar Contas**.  
    Todas as contas da Microsoft as quais você está conectado são exibidas.

8. Para conectar-se com a conta do Azure Stack, selecione **Adicionar uma conta**.

    ![Adicionar uma conta do Azure Stack][29]

9. No **conectar ao armazenamento do Azure** caixa de diálogo **ambiente Azure**, selecione **ambiente de pilha do uso do Azure**e, em seguida, clique em **próximo**.

10. Para entrar com a conta de pilha do Azure que está associado a pelo menos uma assinatura ativa do Azure pilha, preencha o **entrar no ambiente do Azure pilha** caixa de diálogo.  

    Veja em seguida os detalhes para cada campo:

    * **Nome do ambiente**: o campo pode ser personalizado pelo usuário.
    * **O ponto de extremidade dos recursos do ARM**: as amostras dos pontos de extremidade do recurso do Azure Resource Manager:

        * Operador de nuvem:<br> https://adminmanagement.local.azurestack.external   
        * Para locatário:<br> https://Management.local.azurestack.external
 
    * **Id do locatário**: opcional. O valor é fornecido apenas quando o diretório deve ser especificado.

12. Depois de entrar com êxito usando uma conta do Azure Stack, o painel esquerdo é preenchido com as assinaturas do Azure Stack associadas à conta. Selecione as assinaturas do Azure Stack que você deseja trabalhar e, então, selecione **Aplicar**. (Marcar ou desmarcar a caixa de seleção **Todas as assinaturas** seleciona todas ou nenhuma das assinaturas do Azure Stack listadas.)

    ![Selecione as assinaturas do Azure Stack depois de preencher a caixa de diálogo do ambiente de nuvem personalizado][30]  
    O painel esquerdo exibe as contas de armazenamento associadas às assinaturas do Azure Stack selecionadas.

    ![Lista de contas de armazenamento, incluindo contas de assinatura do Azure Stack][31]

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Gerenciador de armazenamento (visualização)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Armazenamento do Azure de pilha: as diferenças e considerações](azure-stack-acs-differences.md)


* Para saber mais sobre o armazenamento do Azure, consulte [Introdução ao armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)

[25]: ./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png
[26]: ./media/azure-stack-storage-connect-se/export-root-cert-azure-stack.png
[27]: ./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png
[28]: ./media/azure-stack-storage-connect-se/select-target-azure-stack.png
[29]: ./media/azure-stack-storage-connect-se/add-azure-stack-account.png
[30]: ./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png
[31]: ./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png
