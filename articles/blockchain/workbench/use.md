---
title: Usando aplicativos no Azure Blockchain Workbench
description: Tutorial sobre como usar contratos de aplicativo no Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/08/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 55207e1eb8fe1b88df7f9bfb84a1da505d0e0252
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332210"
---
# <a name="tutorial-using-applications-in-azure-blockchain-workbench"></a>Tutorial: Usando aplicativos no Azure Blockchain Workbench

Você pode usar o Blockchain Workbench para criar e executar ações em contratos. Você também pode exibir detalhes do contrato como o histórico de transações e o status.

Você aprenderá a:

> [!div class="checklist"]
> * Criar um contrato
> * Executar uma ação em um contrato

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma implantação do Blockchain Workbench. Para obter mais informações, consulte [implantação do Azure Blockchain Workbench](deploy.md) para obter detalhes sobre a implantação
* Um aplicativo blockchain implantado no Blockchain Workbench. Consulte [Criar um aplicativo blockchain implantado no Azure Blockchain Workbench](create-app.md)

[Abra o Blockchain Workbench](deploy.md#blockchain-workbench-web-url) em seu navegador.

![Blockchain Workbench](./media/use/workbench.png)

Você precisa entrar como um membro do Blockchain Workbench. Se não houver nenhum aplicativo listado, você é um membro do Blockchain Workbench, mas não é um membro de todos os aplicativos. O administrador Blockchain Workbench pode atribuir membros a aplicativos.

## <a name="create-new-contract"></a>Criar novo contrato 

Para criar um novo contrato, você precisa ser um membro especificado como um iniciador de **contrato**. Para obter informações sobre como definir funções de aplicativo e os iniciadores para o contrato, consulte [fluxos de trabalho na visão geral de configuração](configuration.md#workflows). Para obter informações sobre como atribuir membros a funções de aplicativo, consulte [adicionar um membro a aplicativo](manage-users.md#add-member-to-application).

1. Na seção de aplicativo Blockchain Workbench, selecione o bloco de aplicativo que contém o contrato que você deseja criar. Uma lista de contratos ativos é exibida.

2. Para criar um novo contrato, selecione **Novo contrato**.

    ![Novo botão de contrato](./media/use/contract-list.png)

3. O painel **Novo contrato** é exibido. Especifique os valores de parâmetros iniciais. Selecione **Criar**.

    ![Painel novo contrato](./media/use/new-contract.png)

    O contrato recém-criado é exibido na lista com os outros contratos ativos.

    ![Lista de contratos ativos](./media/use/active-contracts.png)

## <a name="take-action-on-contract"></a>Executar ação no contrato

Dependendo do estado do contrato que está, os membros podem executar ações para fazer a transição para o próximo estado do contrato. As ações são definidas como [transições](configuration.md#transitions) dentro de um [estado](configuration.md#states). Membros que pertencem a um aplicativo ou função de instância para a transição podem executar a ação. 

1. Na seção de aplicativo Blockchain Workbench, selecione o bloco de aplicativo que contém o contrato para executar a ação.
2. Selecione o contrato na lista. Os detalhes do contrato são exibidos em seções diferentes. 

    ![Detalhes do contrato](./media/use/contract-details.png)

    | Seção  | DESCRIÇÃO  |
    |---------|---------|
    | Status | Lista o progresso atual em estágios de contrato |
    | Detalhes | Os valores atuais do contrato |
    | Ação | Detalhes sobre a última ação |
    | Atividade | Histórico de transações do contrato |
    
3. Na seção **Ação**, selecione **Executar ação**.

4. Os detalhes sobre o estado atual do contrato são exibidos em um painel. Escolha a ação que deseja colocar na lista suspensa. 

    ![Escolher ação](./media/use/choose-action.png)

5. Na seção **Executar Ação**, selecione a ação.
6. Se os parâmetros são necessários para a ação, especifique os valores para a ação.

    ![Executar ação](./media/use/take-action.png)

7. Selecione **Executar ação** para executar a ação.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Controle de versão de aplicativos do Azure Blockchain Workbench](version-app.md)
