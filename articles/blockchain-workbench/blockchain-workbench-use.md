---
title: Usando aplicativos no Azure Blockchain Workbench
description: Como usar contratos de aplicativo no Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: b15d4980875b805b49f7dc1cdb941e607232f3cb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258032"
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

Para criar um novo contrato, você precisa ser um membro especificado como um iniciador de **contrato**. Para obter informações sobre como definir funções de aplicativo e os iniciadores para o contrato, consulte [fluxos de trabalho na visão geral de configuração](blockchain-workbench-configuration-overview.md#workflows). Para obter informações sobre como atribuir membros a funções de aplicativo, consulte [adicionar um membro a aplicativo](blockchain-workbench-manage-users.md#add-member-to-application).

1. Na seção de aplicativo Blockchain Workbench, selecione o bloco de aplicativo que contém o contrato que você deseja criar. Uma lista de contratos ativos é exibida.

2. Para criar um novo contrato, selecione **Novo contrato**.

    ![Novo botão de contrato](media/blockchain-workbench-use/contract-list.png)

3. O painel **Novo contrato** é exibido. Especifique os valores de parâmetros iniciais. Clique em **Criar**.

    ![Painel novo contrato](media/blockchain-workbench-use/new-contract.png)

    O contrato recém-criado é exibido na lista com os outros contratos ativos.

    ![Lista de contratos ativos](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>Executar ação no contrato

Dependendo do estado do contrato que está, os membros podem executar ações para fazer a transição para o próximo estado do contrato. As ações são definidas como [transições](blockchain-workbench-configuration-overview.md#transitions) dentro de um [estado](blockchain-workbench-configuration-overview.md#states). Membros que pertencem a um aplicativo ou função de instância para a transição podem executar a ação. 

1. Na seção de aplicativo Blockchain Workbench, selecione o bloco de aplicativo que contém o contrato para executar a ação.
2. Selecione o contrato na lista. Os detalhes do contrato são exibidos em seções diferentes. 

    ![Detalhes do contrato](media/blockchain-workbench-use/contract-details.png)

    | Seção  | DESCRIÇÃO  |
    |---------|---------|
    | Status | Lista o progresso atual em estágios de contrato |
    | Detalhes | Os valores atuais do contrato |
    | Ação | Detalhes sobre a última ação |
    | Atividade | Histórico de transações do contrato |
    
3. Na seção **Ação**, selecione **Executar ação**.

4. Os detalhes sobre o estado atual do contrato são exibidos em um painel. Escolha a ação que deseja colocar na lista suspensa. 

    ![Escolher ação](media/blockchain-workbench-use/choose-action.png)

5. Na seção **Executar Ação**, selecione a ação.
6. Se os parâmetros são necessários para a ação, especifique os valores para a ação.

    ![Executar ação](media/blockchain-workbench-use/take-action.png)

7. Selecione **Executar ação** para executar a ação.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como solucionar problemas do Azure Blockchain Workbench](blockchain-workbench-troubleshooting.md)