---
title: 'Portal do Azure: Criar um banco de dados SQL | Microsoft Docs'
description: "Aprenda a criar um servidor lógico do Banco de Dados SQL, uma regra de firewall no nível de servidor e bancos de dados com o portal do Azure. Saiba também como consultar um Banco de Dados SQL do Azure usando o Portal do Azure."
keywords: tutorial do banco de dados SQL, criar um banco de dados SQL
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: be5839e04fae457b889db11dffe56f31afe723a5
ms.lasthandoff: 03/28/2017


---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>Criar um Banco de Dados SQL do Azure no portal do Azure

Este tutorial de início rápido percorre como criar um banco de dados SQL no Azure.  O Banco de Dados SQL do Azure é uma oferta de "banco de dados como serviço" que permite que você execute e dimensione os bancos de dados do SQL Server altamente disponíveis na nuvem.  Este guia rápido mostra como começar criando um novo Banco de Dados SQL no portal do Azure.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Criar um banco de dados SQL

Um banco de dados SQL do Azure é criado com um conjunto definido de [recursos de computação e armazenamento](sql-database-service-tiers.md). O banco de dados é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) e em um [servidor lógico de banco de dados SQL do Azure](sql-database-features.md). 

Execute estas etapas para criar um Banco de Dados SQL que contém os dados de exemplo do Adventure Works LT. 

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

2. Selecione **Bancos de Dados** na página **Novo** e **Banco de Dados SQL** na página **Bancos de Dados**.

    ![criar database-1](./media/sql-database-get-started/create-database-1.png)

3. Preencha o formulário do Banco de Dados SQL com as informações abaixo, conforme mostrado na imagem anterior: 
   - Nome do banco de dados: use **mySampleDatabase**
   - Grupo de recursos: use **myResourceGroup**
   - Fonte: Selecione **Exemplo (AdventureWorksLT)**

4. Clique em **Servidor** para criar e configurar um novo servidor para o novo banco de dados. Preencha o **formulário Novo servidor** especificando um nome de servidor exclusivo, forneça um nome para o logon de administrador do servidor e especifique a senha escolhida. 

    ![criar database-server](./media/sql-database-get-started/create-database-server.png)
5. Clique em **Selecionar**.

6. Clique em **Tipo de preço** para especificar o nível de desempenho e o tipo de serviço para o novo banco de dados. Para esse início rápido, selecione **20 DTUs** e **250** GB de armazenamento

    ![Criar database-s1](./media/sql-database-get-started/create-database-s1.png)

7. Clique em **Aplicar**.  

8. Clique em **Criar** para provisionar o banco de dados. O provisionamento demora alguns minutos. 

9. Na barra de ferramentas, clique em **Notificações** para monitorar o processo de implantação.

    ![notificação](./media/sql-database-get-started/notification.png)


## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall no nível de servidor

O serviço Banco de Dados SQL cria um firewall em aplicativos externos impedindo que aplicativos e ferramentas externas se conectem ao servidor ou banco de dados no servidor, a menos que uma regra de firewall seja criada para abrir o firewall para endereços IP específicos. Execute estas etapas a fim de criar uma [regra de firewall no nível do servidor do Banco de Dados SQL](sql-database-firewall-configure.md) para o endereço IP do seu cliente e habilitar a conectividade externa por meio do firewall do Banco de Dados SQL somente para seu endereço IP. 

1. Após a conclusão da implantação, clique em **Bancos de Dados SQL** no menu à esquerda e clique no novo banco de dados, **mySampleDatabase**na página **Bancos de Dados SQL**. A página de visão geral de seu banco de dados é aberta, mostrando o nome totalmente qualificado do servidor (como **mynewserver20170327.database.windows.net**) e fornece opções para configurações adicionais.

      ![regra de firewall do servidor](./media/sql-database-get-started/server-firewall-rule.png) 

2. Clique em **Definir o firewall do servidor** na barra de ferramentas, conforme mostrado na imagem anterior. A página **Configurações do firewall** do servidor de Banco de Dados SQL é aberta. 

3. Clique em **Adicionar IP do cliente** na barra de ferramentas e clique em **Salvar**. Uma regra de firewall no nível do servidor é criada para seu endereço IP atual.

      ![definir regra de firewall do servidor](./media/sql-database-get-started/server-firewall-rule-set.png) 

4. Clique em **OK** e clique no **X** para fechar a página **Configurações do firewall**.

Agora você pode se conectar ao banco de dados e ao seu servidor usando o SQL Server Management Studio ou outra ferramenta de sua escolha.

## <a name="query-the-sql-database"></a>Consultar o Banco de Dados SQL

Quando criamos nosso Banco de Dados SQL, preenchemos com o banco de dados de exemplo **AdventureWorksLT** (essa foi uma das opções que selecionamos na interface de usuário Criar anteriormente neste início rápido). Agora vamos usar a ferramenta de consulta interna no portal do Azure para consultar os dados. 

1. Na página do Banco de Dados SQL do seu banco de dados, clique em **Ferramentas** na barra de ferramentas. A página **Ferramentas** é aberta.

     ![menu ferramentas](./media/sql-database-get-started/tools-menu.png) 

2. Clique em **Editor de consultas (visualização)**, clique na caixa de seleção **Visualizar termos** e clique em **OK**. A página do Editor de consulta é aberta.

3. Clique em **Login** e, quando receber solicitação, selecione **Autenticação do SQL Server** e forneça o logon de administrador do servidor e a senha criada anteriormente.

    ![logon](./media/sql-database-get-started/login.png) 

4. Clique em **OK** para fazer logon.

5. Depois de autenticado, digite a consulta a seguir no painel do editor de consulta.

   ```
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. Clique em **Executar** e reveja os resultados da consulta no painel **Resultados**.

    ![resultados do editor de consultas](./media/sql-database-get-started/query-editor-results.png)

7. Clique em **X** para fechar a página **Editor de consultas** e clique em **X** novamente para fechar a página **Ferramentas**.

## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos nessa coleção aproveitam esse início rápido. Se você planeja continuar trabalhando com inícios rápidos subsequentes ou com os tutoriais, não limpe os recursos criados nesse início rápido. Caso contrário, siga estas etapas para excluir todos os recursos criados por esse início rápido no Portal do Azure.

1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e clique em **myResourceGroup**. 
2. Em sua página de grupo de recursos, clique em **Excluir**, digite **myResourceGroup** na caixa de texto e clique **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- Para conectar e consultar usando SQL Server Management Studio, veja [Conectar e consultar com o SSMS](sql-database-connect-query-ssms.md)
- Para se conectar usando o Visual Studio, veja [Conectar e consultar com o Visual Studio](sql-database-connect-query.md).
- Para obter uma visão geral técnica do banco de dados SQL, veja [Sobre o serviço Banco de Dados SQL](sql-database-technical-overview.md).

