---
title: Conectar-se o Gerenciador de armazenamento para uma assinatura do Azure Stack ou uma conta de armazenamento | Microsoft Docs
description: Saiba como conectar o Gerenciador de armazenamento para uma assinatura do Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/24/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: d1bddc8331fc1a9ded37949a8065636947074852
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246722"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Conectar-se o Gerenciador de armazenamento para uma assinatura do Azure Stack ou uma conta de armazenamento

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Neste artigo, você aprenderá como se conectar a suas assinaturas do Azure Stack e contas de armazenamento usando o Gerenciador de armazenamento. O Gerenciador de armazenamento do Azure é um aplicativo autônomo que permite que você trabalhe facilmente com dados do armazenamento do Azure Stack no Windows, macOS e Linux.

> [!NOTE]  
> Há várias ferramentas disponíveis para mover dados bidirecionalmente no armazenamento do Azure Stack. Para obter mais informações, consulte [transferência de dados de ferramentas para o armazenamento do Azure Stack](azure-stack-storage-transfer.md).

Se você ainda não instalou ainda, o Gerenciador de armazenamento [baixar o Gerenciador de armazenamento](http://www.storageexplorer.com/) e instalá-lo.

Depois de se conectar a uma assinatura do Azure Stack ou uma conta de armazenamento, você pode usar o [artigos do Gerenciador de armazenamento do Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) para trabalhar com seus dados do Azure Stack. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Preparar para conectar-se ao Azure Stack

Você precisa ter acesso direto para o Azure Stack ou uma conexão VPN para o Gerenciador de armazenamento acessar a assinatura do Azure Stack. Para saber como configurar uma conexão VPN ao Azure Stack, confira [Como conectar-se com o Azure Stack com VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

Para o Azure Stack desenvolvimento ASDK (Kit), você precisa exportar o certificado raiz da autoridade do Azure Stack.

> [!Note]  
> Para o ASDK, se você estiver se conectando ao seu ASDK por meio de VPN, não use o certificado raiz (CA.cer) que foi criado durante o processo de configuração VPN.  Isso é um certificado codificado em DER e não permitirá que o Gerenciador de armazenamento para recuperar as assinaturas do Azure Stack. Siga as etapas a seguir para exportar um certificado codificado em Base 64 para usar com o Gerenciador de armazenamento.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Exportar e importar o certificado do Azure Stack

1. Abra `mmc.exe` em uma máquina de host do Azure Stack ou em um computador local com uma conexão VPN para o Azure Stack. 

2. Na **arquivo**, selecione **Adicionar/Remover Snap-in**e, em seguida, adicione **certificados** gerenciar **minha conta de usuário**.

3.  Sob **Console raiz do console\certificado (computador Local) \Trusted Root Certification Authorities\Certificates**.

    - Para o ASDK, encontre **AzureStackSelfSignedRootCert**.

        ![Como carregar o certificado raiz do Azure Stack por meio do mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

    - Para um sistema integrado, localize o certificado de raiz de seus certificados externos. 
    
        ![Como carregar o certificado raiz do Azure Stack por meio do mmc.exe](./media/azure-stack-storage-connect-se/azure-stack-storage-cert-location-is.png)
        

4. Clique com botão direito no certificado, selecione **todas as tarefas** > **exportar**e, em seguida, siga as instruções para exportar o certificado com **x. 509 codificado em Base-64 (. CER)**.

    O certificado exportado será usado na próxima etapa.

5. Iniciar o Gerenciador de armazenamento, e se você vir as **conectar-se ao armazenamento do Azure** caixa de diálogo caixa, cancelá-la.

6. Sobre o **edite** , aponte para **certificados SSL**e, em seguida, selecione **importar certificados**. Use a caixa de diálogo do seletor de arquivos para localizar e abrir o certificado que você exportou na etapa anterior.

    Depois de importar o certificado, você for solicitado a reiniciar o Gerenciador de armazenamento.

    ![Importe o certificado no Gerenciador de armazenamento](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

7. Depois de reinicia o Gerenciador de armazenamento, selecione o **editar** menu e verifique se **destino Azure Stack** está selecionado. Se não estiver, selecione **destino Azure Stack**e, em seguida, reinicie o Gerenciador de armazenamento para que a alteração tenha efeito. Essa configuração é necessária para que haja compatibilidade com seu ambiente do Azure Stack.

    ![Confira se o Destino Azure Stack está selecionado](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription-with-azure-ad"></a>Conectar-se a uma assinatura do Azure Stack com o Azure AD

Use as etapas a seguir para conectar o Gerenciador de armazenamento para uma assinatura do Azure Stack, que pertence a uma conta do Azure Active Directory (Azure AD).

1. No painel esquerdo do Gerenciador de armazenamento, selecione **gerenciar contas**. 
    Todos os a assinatura da Microsoft que você conectado são exibidos.

2. Para se conectar à assinatura do Azure Stack, selecione **adicionar uma conta**.

    ![Adicionar uma conta do Azure Stack](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. Na caixa de diálogo de armazenamento do Azure, conectar sob **ambiente do Azure**, selecione **Azure** ou **Azure China**, que depende da conta do Azure Stack que está sendo usada, selecione **Entrar** para entrar com a conta do Azure Stack associada com pelo menos uma assinatura ativa do Azure Stack.

    ![Conectar-se ao armazenamento do Azure](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Depois de entrar com êxito usando uma conta do Azure Stack, o painel esquerdo é preenchido com as assinaturas do Azure Stack associadas à conta. Selecione as assinaturas do Azure Stack que você deseja trabalhar e, então, selecione **Aplicar**. (Marcar ou desmarcar a caixa de seleção **Todas as assinaturas** seleciona todas ou nenhuma das assinaturas do Azure Stack listadas.)

    ![Selecione as assinaturas do Azure Stack depois de preencher a caixa de diálogo do ambiente de nuvem personalizado](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    O painel esquerdo exibe as contas de armazenamento associadas às assinaturas do Azure Stack selecionadas.

    ![Lista de contas de armazenamento, incluindo contas de assinatura do Azure Stack](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-subscription-with-ad-fs-account"></a>Conectar-se a uma assinatura do Azure Stack com a conta do AD FS

> [!Note]  
> A experiência de logon de serviço federado do Azure (AD FS) dá suporte ao Gerenciador de armazenamento 1.2.0 ou versões mais recentes com o Azure Stack 1804 ou atualização mais recente.
Use as etapas a seguir para conectar o Gerenciador de armazenamento para uma assinatura do Azure Stack que pertence a uma conta do AD FS.

1. Selecione **gerenciar contas**. O explorer lista as assinaturas do Microsoft que você entrou.
2. Selecione **adicionar uma conta** para se conectar à assinatura do Azure Stack.

    ![Adicionar uma conta](media/azure-stack-storage-connect-se/add-an-account.png)

3. Selecione **Avançar**. Na caixa de diálogo de armazenamento do Azure, conectar sob **ambiente do Azure**, selecione **ambiente personalizado de uso**, em seguida, clique em **próxima**.

    ![Conectar-se ao armazenamento do Azure](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. Insira as informações necessárias do ambiente personalizado do Azure Stack. 

    | Campo | Observações |
    | ---   | ---   |
    | Nome do ambiente | O campo pode ser personalizado pelo usuário. |
    | Ponto de extremidade do Azure Resource Manager | Os exemplos de pontos de extremidade de recursos do Azure Resource Manager do Kit de desenvolvimento do Azure Stack.<br>Para operadores: https://adminmanagement.local.azurestack.external <br> Para usuários: https://management.local.azurestack.external |

    Se você estiver trabalhando no Azure Stack sistema integrado e não souber seu ponto de extremidade de gerenciamento, entre em contato com seu operador.

    ![Adicionar uma conta](./media/azure-stack-storage-connect-se/custom-environments.png)

5. Selecione **entrar**, para conectar à conta do Azure Stack associada com pelo menos uma assinatura ativa do Azure Stack.



6. Selecione as assinaturas do Azure Stack que você deseja trabalhar. Escolha **Aplicar**.

    ![Gerenciamento de contas](./media/azure-stack-storage-connect-se/account-management.png)

    O painel esquerdo exibe as contas de armazenamento associadas às assinaturas do Azure Stack selecionadas.

    ![Lista de assinaturas associadas](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Conectar a uma conta de armazenamento do Azure Stack

Você também pode se conectar a uma conta de armazenamento do Azure Stack usando o nome da conta de armazenamento e o par de chaves.

1. No painel esquerdo do Gerenciador de armazenamento, selecione Gerenciar contas. Todas as contas da Microsoft que você conectado são exibidas.

    ![Adicionar uma conta](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Para se conectar à assinatura do Azure Stack, selecione **adicionar uma conta**.

    ![Adicionar uma conta](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. Em conectar-se a caixa de diálogo de armazenamento do Azure, selecione **usar um nome de conta de armazenamento e a chave**.

4. Insira o nome de conta no **nome da conta**, cole a chave de conta no **chave de conta** caixa de texto, selecione **outros (digite abaixo)** no **pontos de extremidade de armazenamento domínio** e o ponto de extremidade do Azure Stack de entrada.

    Um ponto de extremidade do Azure Stack inclui duas partes: o nome de uma região e domínio do Azure Stack. O Kit de desenvolvimento do Azure Stack, o ponto de extremidade padrão é **local.azurestack.external**. Se você não tiver certeza sobre o ponto de extremidade, entre em contato com seu administrador de nuvem.

    ![Anexar o nome e a chave](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Selecione **Conectar**.
6. Depois que a conta de armazenamento é anexada com êxito, a conta de armazenamento é exibida com (**externa,**) acrescentado ao nome.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Gerenciador de armazenamento](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [O armazenamento do Azure Stack: diferenças e considerações](azure-stack-acs-differences.md)
* Para saber mais sobre o armazenamento do Azure, consulte [Introdução ao armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)
