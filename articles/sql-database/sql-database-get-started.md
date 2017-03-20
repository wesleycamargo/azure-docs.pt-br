---
title: "Portal do Azure: Criar e consultar um único banco de dados SQL | Microsoft Docs"
description: "Aprenda a criar um servidor lógico do Banco de Dados SQL, uma regra de firewall no nível de servidor e bancos de dados com o portal do Azure. Saiba também como consultar um Banco de Dados SQL do Azure usando o Portal do Azure."
keywords: tutorial do banco de dados SQL, criar um banco de dados SQL
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 248885d041378301c5202263d24fdc9609e293d6
ms.lasthandoff: 03/15/2017


---
# <a name="create-and-query-a-single-azure-sql-database-in-the-azure-portal"></a>Criar um único Banco de Dados SQL do Azure no Portal do Azure

Bancos de dados SQL do Azure podem ser criados por meio do Portal do Azure. Esse método fornece uma interface de usuário baseada em navegador para a criação e configuração de Bancos de Dados SQL do Azure e todos os recursos relacionados do Azure.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Criar um banco de dados SQL

Um Banco de Dados SQL do Azure é criado dentro de um grupo de recursos do Azure, em um servidor lógico e com um conjunto definido de recursos de computação e armazenamento. Execute estas etapas para criar um Banco de Dados SQL que contém os dados de exemplo do Adventure Works LT. 

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

2. Selecione **Bancos de Dados** na folha **Novo** e selecione **Banco de Dados SQL** na folha **Bancos de Dados**.

3. Preencha o formulário do Banco de Dados SQL com as informações necessárias: 
   - Nome do Banco de Dados: fornece um nome de banco de dados
   - Assinatura: selecione sua assinatura
   - Grupo de recursos: selecione novo ou existente
   - Fonte: selecionar **Exemplo (AdventureWorksLT)**
   - Servidor: crie um novo servidor (o nome do **Servidor** deve ser globalmente exclusivo)
   - Pool elástico: selecione **Agora não** para esse início rápido
   - Tipo de preço: selecione **20 DTUs** e **250** GB de armazenamento
   - Agrupamento: não é possível alterar esse valor ao importar o banco de dados de exemplo 
   - Fixar no painel: marque esta caixa de seleção

      ![criar banco de dados](./media/sql-database-get-started/create-database-s1.png)

4. Clique em **Criar** quando terminar. O provisionamento demora alguns minutos.
5. Após a conclusão da implantação do Banco de Dados SQL, selecione o **Bancos de Dados SQL** no painel ou selecione **Bancos de Dados SQL** no menu à esquerda e clique em seu novo banco de dados na página **Bancos de Dados SQL**. Uma página de visão geral de seu banco de dados é aberta, mostrando o nome totalmente qualificado do servidor (como **mynewserver20170313.database.windows.net**) e fornece opções para configurações adicionais.

      ![new-sql database](./media/sql-database-get-started/new-database-s1-overview.png) 

## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall no nível de servidor

O serviço Banco de Dados SQL cria um firewall impedindo aplicativos e ferramentas externas de se conectar ao seu novo servidor e banco de dados. Execute estas etapas para criar uma regra de firewall no nível do servidor do Banco de Dados SQL para o endereço IP a fim de habilitar a conectividade externa por meio do firewall do Banco de Dados SQL. 

1. Clique em **Definir firewall do servidor** na barra de ferramentas do banco de dados. A página **Configurações do firewall** do servidor de Banco de Dados SQL é aberta. 

      ![regra de firewall do servidor](./media/sql-database-get-started/server-firewall-rule.png) 

2. Clique em **Adicionar IP do cliente** na barra de ferramentas e clique em **Salvar**. Uma regra de firewall no nível do servidor é criada para seu endereço IP atual.

3. Clique em **OK** e clique no **X** para fechar a página de configurações do Firewall.

Agora você pode se conectar ao banco de dados e ao seu servidor usando o SQL Server Management Studio ou outra ferramenta de sua escolha.

## <a name="query-the-sql-database"></a>Consultar o Banco de Dados SQL

Execute estas etapas para consultar o banco de dados usando o editor de consultas no Portal do Azure. 

1. Na página do Banco de Dados SQL de seu banco de dados, clique em **Ferramentas** na barra de ferramentas. A página de visualização do **Editor de consultas** é aberta.

     ![menu ferramentas](./media/sql-database-get-started/tools-menu.png) 

2. Clique em **Editor de consultas (visualização)**, clique na caixa de seleção **Visualizar termos** e clique em **OK**. O editor de consulta é aberto.

3. Clique em **Login** e, quando receber solicitação, selecione **Autenticação do SQL Server** e forneça o logon de administrador do servidor e a senha.
4. Clique em **OK** para fazer logon.

5. Após a autenticação, digite uma consulta de sua escolha na janela de consulta, como a consulta a seguir:

   ```
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. Clique em **Executar** e reveja os resultados da consulta no painel **Resultados**.

    ![resultados do editor de consultas](./media/sql-database-get-started/query-editor-results.png)

7. Clique no **X** para fechar a página do Editor de consultas.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode optar por salvar os recursos criados neste início rápido para tutoriais posteriores. Ou, você pode usar as seguintes etapas para excluir todos os recursos criados por esse início rápido no Portal do Azure.

1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e clique em **myResourceGroup**. 
2. Em sua página de grupo de recursos, clique em **Excluir**, digite **myResourceGroup** na caixa de texto e clique **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- Para conectar e consultar usando SQL Server Management Studio, veja [Conectar e consultar com o SSMS](sql-database-connect-query-ssms.md)
- Para se conectar usando o Visual Studio, veja [Conectar e consultar com o Visual Studio](sql-database-connect-query.md).
- Para obter uma visão geral técnica do banco de dados SQL, veja [Sobre o serviço Banco de Dados SQL](sql-database-technical-overview.md).

