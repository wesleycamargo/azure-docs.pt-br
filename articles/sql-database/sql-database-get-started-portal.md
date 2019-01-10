---
title: Criar um Banco de Dados SQL do Azure usando o portal | Microsoft Docs
description: Crie um servidor lógico do Banco de Dados SQL do Azure e um banco de dados no portal do Azure e consulte-o.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 12/21/2018
ms.openlocfilehash: b8ff482f2aec406ef4c1c545db7844a861317518
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994412"
---
# <a name="quickstart-create-an-azure-sql-database-in-the-azure-portal"></a>Início Rápido: Criar um Banco de Dados SQL do Azure no portal do Azure

O Banco de Dados SQL do Azure é um *Banco de Dados como Serviço* que permite executar e dimensionar bancos de dados do SQL Server altamente disponíveis na nuvem. Este início rápido mostra como começar criando e, em seguida, consultando um Banco de Dados SQL do Azure usando o portal do Azure. 

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Para todas as etapas deste início rápido, entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Criar um banco de dados SQL

Um Banco de Dados SQL do Azure tem um conjunto definido de [recursos de computação e armazenamento](sql-database-service-tiers-dtu.md). Crie o banco de dados em um [servidor lógico do Banco de Dados SQL do Azure](sql-database-features.md) em um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

Para criar um Banco de Dados SQL que contenha os dados de exemplo AdventureWorksLT:

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
   
1. Selecione **Bancos de Dados** e, em seguida, selecione **Banco de Dados SQL**.
   
1. No formulário **Banco de Dados SQL**, digite ou selecione os seguintes valores: 
   
   - **Nome do banco de dados**: Digite *mySampleDatabase*.
   - **Assinatura**: Selecione a lista suspensa e escolha a assinatura correta, caso ela não seja exibida.  
   - **Grupo de recursos**: Selecione **Criar**, digite *myResourceGroup* e selecione **OK**. 
   - **Selecionar fonte**: Selecione a lista suspensa e escolha **Amostra (AdventureWorksLT)**. 
   
   >[!IMPORTANT]
   >Selecione os dados da **Amostra (AdventureWorksLT)** para acompanhar este e outros inícios rápidos do Banco de Dados SQL do Azure que usam esses dados. 
   
   ![Criar um Banco de Dados SQL do Azure](./media/sql-database-get-started-portal/create-database-1.png)
   
1. Selecione **Servidor** e, em seguida, **Criar um servidor**. 
   
1. No formulário **Novo servidor**, digite ou selecione os seguintes valores: 
   
   - **Nome do servidor**: Digite *mysqlserver*.
   - **Logon de administrador do servidor**: digite *azureuser*. 
   - **Senha**: digite *Azure1234567*. 
   - **Confirmar Senha**: Digite a senha novamente.
   - **Localização**: Selecione a lista suspensa e escolha qualquer localização válida.  
   
   >[!IMPORTANT]
   >Memorize ou registre o logon de administrador do servidor e a senha para fazer logon no servidor e nos bancos de dados deste e de outros inícios rápidos. Caso esqueça seu logon ou a senha, obtenha o nome de logon ou redefina a senha na página **SQL Server**. Para abrir a página **SQL Server**, selecione o nome do servidor na página **Visão Geral** do banco de dados após a criação do banco de dados.
   
1. Selecione **Selecionar**.
   
   ![Criar servidor](./media/sql-database-get-started-portal/create-database-server.png)
   
1. No formulário **Banco de Dados SQL**, selecione **Tipo de preço**. Explore a quantidade de DTUs e de armazenamento disponível para cada camada de serviço.
   
   >[!NOTE]
   >Este início rápido usa o [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md), mas o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) também está disponível.
   
   >[!NOTE]
   >Mais de 1 TB de armazenamento na camada Premium está atualmente disponível em todas as regiões, exceto: Norte do Reino Unido, Centro-oeste dos EUA, Sul do Reino Unido 2, Leste da China, US DoD Central, Alemanha Central, US DoD Leste, US Gov Sudoeste, US Gov Centro-Sul, Nordeste da Alemanha, Norte da China e US Gov Leste. Nessas regiões, o armazenamento máximo na camada Premium é limitado a 1 TB. Para obter mais informações, confira [Limitações atuais de P11-P15](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   
1. Para este início rápido, selecione a camada de serviço **Standard** e, em seguida, use o controle deslizante para selecionar **10 DTUs (S0)** e **1** GB de armazenamento.
   
1. Escolha **Aplicar**.  
   
   ![Selecionar o preço](./media/sql-database-get-started-portal/create-database-s1.png)
   
1. No formulário **Banco de Dados SQL**, selecione **Criar** para implantar e provisionar o grupo de recursos, o servidor e o banco de dados. 
   
   A implantação leva alguns minutos. Você pode selecionar **Notificações** na barra de ferramentas para monitorar o progresso da implantação.

   ![Notificação](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-sql-database"></a>Consultar o Banco de Dados SQL

Agora que você criou um Banco de Dados SQL do Azure, use a ferramenta de consulta interna no portal do Azure para se conectar ao banco de dados e consultar os dados.

1. Na página **Banco de Dados SQL** do banco de dados, selecione **Editor de consulta (versão prévia)** no menu à esquerda. 
   
   ![Fazer logon no Editor de consultas](./media/sql-database-get-started-portal/query-editor-login.png)
   
1. Digite suas informações de logon e selecione **OK**.
   
1. Insira a consulta a seguir no painel **Editor de consultas**.
   
   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```
   
1. Selecione **Executar** e, em seguida, examine os resultados da consulta no painel **Resultados**.

   ![Resultados do Editor de consultas](./media/sql-database-get-started-portal/query-editor-results.png)
   
1. Feche a página **Editor de consultas** e selecione **OK** quando solicitado para descartar as edições não salvas.

## <a name="clean-up-resources"></a>Limpar recursos

Mantenha esse grupo de recursos, SQL Server e Banco de Dados SQL se desejar ir para as [Próximas etapas](#next-steps) e saber como se conectar e consultar o banco de dados usando vários métodos diferentes. 

Quando terminar de usar esses recursos, você poderá excluí-los da seguinte maneira:

1. No menu à esquerda no portal do Azure, selecione **Grupos de recursos** e, em seguida, **myResourceGroup**.
1. Na página do grupo de recursos, selecione **Excluir grupo de recursos**. 
1. Digite *myResourceGroup* no campo e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- É necessário criar uma regra de firewall no nível do servidor para se conectar ao Banco de Dados SQL do Azure por meio de ferramentas locais ou remotas. Para obter mais informações, consulte [Criar uma regra de firewall no nível do servidor](sql-database-get-started-portal-firewall.md).
- Depois de criar uma regra de firewall no nível do servidor, [conecte-se e consulte](sql-database-connect-query.md) seu banco de dados usando várias ferramentas e linguagens diferentes. 
  - [Conectar e consultar usando o SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Conectar e consultar usando o Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Para criar bancos de dados SQL do Azure usando a CLI do Azure, confira [Amostras da CLI do Azure](sql-database-cli-samples.md).
- Para criar bancos de dados SQL do Azure usando o Azure PowerShell, confira [Amostras do Azure PowerShell](sql-database-powershell-samples.md).
