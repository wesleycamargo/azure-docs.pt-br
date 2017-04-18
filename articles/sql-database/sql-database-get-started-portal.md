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
ms.custom: quick start create
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 04/03/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 58af25d90b419b3ddb986118a8c9ba3b42aa95a6
ms.lasthandoff: 04/12/2017


---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>Criar um Banco de Dados SQL do Azure no portal do Azure

Este tutorial de início rápido percorre como criar um banco de dados SQL no Azure. O Banco de Dados SQL do Azure é uma oferta de "Banco de Dados como Serviço" que permite executar e dimensionar os bancos de dados do SQL Server altamente disponíveis na nuvem. Este guia rápido mostra como começar criando um banco de dados SQL com o portal do Azure.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Criar um banco de dados SQL

Um banco de dados SQL do Azure é criado com um conjunto definido de [recursos de computação e armazenamento](sql-database-service-tiers.md). O banco de dados é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) e em um [servidor lógico de banco de dados SQL do Azure](sql-database-features.md). 

Execute estas etapas para criar um Banco de Dados SQL que contém os dados de exemplo do Adventure Works LT. 

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

2. Selecione **Bancos de Dados** na página **Novo** e **Banco de Dados SQL** na página **Bancos de Dados**.

    ![criar database-1](./media/sql-database-get-started-portal/create-database-1.png)

3. Preencha o formulário do Banco de Dados SQL com as informações abaixo, conforme mostrado na imagem anterior:     
   - Nome do banco de dados: **mySampleDatabase**
   - Grupo de recursos: **myResourceGroup**
   - Fonte: **Exemplo (AdventureWorksLT)**

4. Clique em **Servidor** para criar e configurar o servidor para seu novo banco de dados. Preencha o **formulário Novo servidor** especificando um nome de servidor exclusivo, forneça um nome para o logon de administrador do servidor e especifique a senha escolhida. 

    ![criar database-server](./media/sql-database-get-started-portal/create-database-server.png)
5. Clique em **Selecionar**.

6. Clique em **Tipo de preço** para especificar o nível de desempenho e o tipo de serviço para o novo banco de dados. Para esse início rápido, selecione **20 DTUs** e **250** GB de armazenamento

    ![Criar database-s1](./media/sql-database-get-started-portal/create-database-s1.png)

7. Clique em **Aplicar**.  

8. Clique em **Criar** para provisionar o banco de dados. O provisionamento demora alguns minutos. 

9. Na barra de ferramentas, clique em **Notificações** para monitorar o processo de implantação.

    ![notificação](./media/sql-database-get-started-portal/notification.png)


## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall no nível de servidor

O serviço do Banco de Dados SQL cria um firewall no nível do servidor impedindo que os aplicativos e ferramentas externos conectem o servidor ou os bancos de dados no servidor, a menos que uma regra de firewall seja criada para abrir o firewall para endereços IP específicos. Execute estas etapas a fim de criar uma [regra de firewall no nível do servidor do Banco de Dados SQL](sql-database-firewall-configure.md) para o endereço IP do seu cliente e habilitar a conectividade externa por meio do firewall do Banco de Dados SQL somente para seu endereço IP. 

1. Após a conclusão da implantação, clique em **Bancos de dados SQL** no menu à esquerda e clique em seu banco de dados na página **Bancos de dados SQL**. A página de visão geral de seu banco de dados é aberta, mostrando o nome totalmente qualificado do servidor (como **mynewserver20170327.database.windows.net**) e fornece opções para configurações adicionais.

      ![regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule.png) 

2. Clique em **Definir o firewall do servidor** na barra de ferramentas, conforme mostrado na imagem anterior. A página **Configurações do firewall** do servidor de Banco de Dados SQL é aberta. 

3. Clique em **Adicionar IP do cliente** na barra de ferramentas e clique em **Salvar**. Uma regra de firewall no nível do servidor é criada para seu endereço IP atual.

      ![definir regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

4. Clique em **OK**, em seguida, feche a página **Configurações do Firewall**.

Agora, você pode conectar o servidor do Banco de Dados SQL e seus bancos de dados usando o SQL Server Management Studio ou outra ferramenta de sua escolha neste endereço IP usando a conta do administrador do Servidor criada anteriormente.

> [!NOTE]
> O Banco de Dados SQL se comunica pela porta 1433. Se você estiver tentando conectar-se a partir de uma rede corporativa, o tráfego de saída pela porta 1433 poderá não ser permitido pelo firewall de sua rede. Se isto acontecer, você não conseguirá conectar seu servidor do Banco de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>

## <a name="query-the-sql-database"></a>Consultar o Banco de Dados SQL

Quando criamos nosso Banco de Dados SQL, preenchemos com o banco de dados de exemplo **AdventureWorksLT** (essa foi uma das opções que selecionamos na interface de usuário Criar anteriormente neste início rápido). Agora vamos usar a ferramenta de consulta interna no portal do Azure para consultar os dados. 

1. Na página do Banco de Dados SQL do seu banco de dados, clique em **Ferramentas** na barra de ferramentas. A página **Ferramentas** é aberta.

     ![menu ferramentas](./media/sql-database-get-started-portal/tools-menu.png) 

2. Clique em **Editor de consultas (visualização)**, clique na caixa de seleção **Visualizar termos** e clique em **OK**. A página do Editor de consulta é aberta.

3. Clique em **Login** e, quando receber solicitação, selecione **Autenticação do SQL Server** e forneça o logon de administrador do servidor e a senha criada anteriormente.

    ![logon](./media/sql-database-get-started-portal/login.png) 

4. Clique em **OK** para fazer logon.

5. Depois de autenticado, digite a consulta a seguir no painel do editor de consulta.

   ```
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. Clique em **Executar** e reveja os resultados da consulta no painel **Resultados**.

    ![resultados do editor de consultas](./media/sql-database-get-started-portal/query-editor-results.png)

7. Feche a página **Editor de consultas** e a página **Ferramentas**.

## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos nessa coleção aproveitam esse início rápido. Se você planeja continuar trabalhando com os inícios rápidos subsequentes, não limpe os recursos criados nesse início rápido. Caso contrário, siga estas etapas para excluir todos os recursos criados por esse início rápido no Portal do Azure.

1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e clique em **myResourceGroup**. 
2. Em sua página de grupo de recursos, clique em **Excluir**, digite **myResourceGroup** na caixa de texto e clique **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- Para conectar e consultar usando SQL Server Management Studio, veja [Conectar e consultar com o SSMS](sql-database-connect-query-ssms.md)
- Para conectar e consultar usando o Visual Studio Code, veja [Conectar e consultar com o Visual Studio Code](sql-database-connect-query-vscode.md).
- Para conectar e consultar usando o .NET, veja [Conectar e consultar com o .NET](sql-database-connect-query-dotnet.md).
- Para conectar e consultar usando o PHP, veja [Conectar e consultar com o PHP](sql-database-connect-query-php.md).
- Para conectar e consultar usando o Node.js, veja [Conectar e consultar com o Node.js](sql-database-connect-query-nodejs.md).
- Para conectar e consultar usando o Java, veja [Conectar e consultar com o Java](sql-database-connect-query-java.md).
- Para conectar e consultar usando o Python, veja [Conectar e consultar com o Python](sql-database-connect-query-python.md).
- Para conectar e consultar usando o Ruby, veja [Conectar e consultar com o Ruby](sql-database-connect-query-ruby.md).

