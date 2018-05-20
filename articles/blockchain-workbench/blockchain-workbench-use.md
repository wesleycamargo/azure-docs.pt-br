---
title: Usando aplicativos no Azure Blockchain Workbench
description: Como usar contratos de aplicativo no Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/26/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 35a45947c2766ccc5e64f4c3523ce163a9680680
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
---
# <a name="using-applications-in-azure-blockchain-workbench"></a>Usando aplicativos no Azure Blockchain Workbench

Você pode usar o Blockchain Workbench para criar e executar ações em contratos. Você também pode exibir detalhes do contrato como o histórico de transações e o status.

## <a name="prerequisites"></a>pré-requisitos

* Uma implantação do Blockchain Workbench. Para obter mais informações, consulte [implantação do Azure Blockchain Workbench](blockchain-workbench-deploy.md) para obter detalhes sobre a implantação
* Um aplicativo blockchain implantado no Blockchain Workbench. Consulte [Criar um aplicativo blockchain implantado no Azure Blockchain Workbench]()

[Abra o Blockchain Workbench](blockchain-workbench-deploy.md#blockchain-workbench-web-url) em seu navegador.

![Blockchain Workbench](media/blockchain-workbench-use/workbench.png)

Você precisa entrar como um membro do Blockchain Workbench. Se não houver nenhum aplicativo listado, você é um membro do Blockchain Workbench, mas não é um membro de todos os aplicativos. O administrador Blockchain Workbench pode atribuir membros a aplicativos.

## <a name="create-new-contract"></a>Criar novo contrato 

Para criar um novo contrato, você precisa ser um membro da função **AllowedInstanceRoles**. 

1. Na seção de aplicativo Blockchain Workbench, selecione o bloco de aplicativo que contém o contrato que você deseja criar. Uma lista de contratos ativos é exibida.

2. Para criar um novo contrato, selecione **Novo contrato**.

    ![Novo botão de contrato](media/blockchain-workbench-use/contract-list.png)

3. O painel **Novo contrato** é exibido. Especifique os valores de parâmetros iniciais. Clique em **Criar**.

    ![Painel novo contrato](media/blockchain-workbench-use/new-contract.png)

    O contrato recém-criado é exibido na lista com os outros contratos ativos.

    ![Lista de contratos ativos](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>Executar ação no contrato

1. Na seção de aplicativo Blockchain Workbench, selecione o bloco de aplicativo que contém o contrato para executar a ação.

    ![Lista de aplicativos](media/blockchain-workbench-use/apps-list.png)

2. Selecione o contrato na lista.

    ![Lista de contratos](media/blockchain-workbench-use/select-contract.png)

    Os detalhes do contrato são exibidos em seções diferentes. 

    ![Detalhes do contrato](media/blockchain-workbench-use/contract-details.png)

    | Seção  | DESCRIÇÃO  |
    |---------|---------|
    | Status | Lista o progresso atual em estágios de contrato |
    | Detalhes | Os valores atuais do contrato |
    | Ação | Detalhes sobre a última ação |
    | Atividade | Histórico de transações do contrato |
    
3. Na seção **Ação**, selecione **Executar ação**.

4. Os detalhes sobre o estado atual do contrato são exibidos em um painel. Escolha a ação que deseja colocar na lista suspensa. 

    ![Executar ação](media/blockchain-workbench-use/take-action.png)

5. Selecione **Executar** para executar a ação.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como solucionar problemas do Azure Blockchain Workbench](blockchain-workbench-troubleshooting.md)