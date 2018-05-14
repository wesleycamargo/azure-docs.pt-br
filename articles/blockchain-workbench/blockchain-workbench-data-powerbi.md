---
title: Usar dados do Azure Blockchain Workbench no Microsoft Power BI
description: Saiba como carregar e exibir dados de Banco de Dados SQL do Azure Blockchain Workbench no Microsoft Power BI.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 2b909c0a8441010b87c913e5937d25c8127058f1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Usando dados do Azure Blockchain Workbench com o Microsoft Power BI

O Microsoft Power BI permite gerar facilmente relatórios sofisticados de bancos de dados SQL usando o Power BI Desktop e, em seguida, publicá-los no [https://www.powerbi.com](http://www.powerbi.com).

Este artigo contém uma explicação passo a passo de como se conectar ao Banco de Dados SQL do Azure Blockchain Workbench no Power BI Desktop, criar um relatório e implantar o relatório em powerbi.com.

## <a name="prerequisites"></a>pré-requisitos

* Baixe o [Power BI Desktop](https://aka.ms/pbidesktopstore).

## <a name="connecting-powerbi-to-data-in-azure-blockchain-workbench"></a>Conectando o Power BI aos dados no Azure Blockchain Workbench

1.  Abra o Power BI Desktop.
2.  Selecione **Obter Dados**.

    ![Obter dados](media/blockchain-workbench-data-powerbi/get-data.png)
3.  Selecione **SQL Server** na lista de tipos de fonte de dados.

4.  Forneça o nome do servidor e do banco de dados na caixa de diálogo. Especifique se você deseja importar os dados ou executar uma **DirectQuery**. Selecione **OK**.

    ![Selecionar SQL Server](media/blockchain-workbench-data-powerbi/select-sql.png)

5.  Forneça as credenciais de banco de dados para acessar o Azure Blockchain Workbench. Clique em **Banco de Dados** e insira suas credenciais.

    Se você estiver usando as credenciais criadas pelo processo de implantação do Azure Blockchain Workbench, o nome de usuário será **dbadmin**, e a senha será a fornecida durante a implantação.

    ![Configurações de Banco de Dados SQL](media/blockchain-workbench-data-powerbi/db-settings.png)

6.  Quando a conexão com o banco de dados estiver estabelecida, a caixa de diálogo **Navegador** mostrará as tabelas e as exibições disponíveis no banco de dados. As exibições são criadas para geração de relatórios, e todas elas recebem **vw** como prefixo.

    ![Navegador](media/blockchain-workbench-data-powerbi/navigator.png)

7.  Selecione as exibições que deseja incluir. Para fins de demonstração, incluímos **vwContractAction**, que fornece detalhes sobre todas as ações que foram executadas em um contrato.

    ![Selecionar exibições](media/blockchain-workbench-data-powerbi/select-views.png)

Agora, você pode criar e publicar relatórios, como faria normalmente com o Power BI.

## <a name="next-steps"></a>Próximas etapas

* [Exibições de banco de dados no Azure Blockchain Workbench](blockchain-workbench-database-views.md)