---
title: Usar Dados do Azure Blockchain Workbench com o SQL Server Management Studio
description: Saiba como se conectar ao Banco de Dados SQL do Azure Blockchain Workbench pelo SQL Server Management Studio.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: b640aec322d6dc184a428e67d9576cf162c8f394
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075248"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Usando os dados do Azure Blockchain Workbench com o SQL Server Management Studio

Microsoft SQL Server Management Studio permite escrever e testar as consultas rapidamente no Banco de Dados SQL do Azure Blockhain Workbench. Esta seção contém instruções passo a passo de como se conectar ao Banco de Dados SQL do Azure Blockchain Workbench pelo SQL Server Management Studio.

## <a name="prerequisites"></a>pré-requisitos

* Baixe o [SQL Server Management Studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>Conectando o SQL Server Management Studio aos dados no Azure Blockchain Workbench

1. Abra o SQL Server Management Studio e selecione **Conectar**.
2. Selecione **Mecanismo de Banco de Dados**.

    ![Mecanismo de banco de dados](media/blockchain-workbench-data-sql-management-studio/database-engine.png)

3. Na caixa de diálogo **Conectar-se ao Servidor**, insira o nome do servidor e suas credenciais de banco de dados.

    Se você estiver usando as credenciais criadas pelo processo de implantação do Azure Blockchain Workbench, o nome de usuário será **dbadmin**, e a senha será a fornecida durante a implantação.

    ![Inserir credenciais de SQL](media/blockchain-workbench-data-sql-management-studio/sql-creds.png)

 4. O SQL Server Management Studio exibe a lista de bancos de dados, exibições de bancos de dados e procedimentos armazenados no banco de dados do Azure Blockchain Workbench.

    ![Lista de banco de dados](media/blockchain-workbench-data-sql-management-studio/db-list.png)

5. Para exibir os dados associados a qualquer uma das exibições de banco de dados, você poderá gerar automaticamente uma instrução select usando as etapas a seguir.
6. Clique com botão direito do mouse em qualquer uma das exibições de banco de dados no Pesquisador de Objetos.
7. Selecione **Exibição de Script como**.
8. Escolha **SELECIONAR para**.
9. Selecione **Janela do Editor de Nova Consulta**.
10. Uma nova consulta pode ser criada selecionando **Nova Consulta**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exibições de banco de dados no Azure Blockchain Workbench](blockchain-workbench-database-views.md)