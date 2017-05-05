---
title: Migrar o banco de dados SQL Server para o Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como migrar seu banco de dados do SQL Server para o banco de dados SQL do Azure.
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: c6d965351f6f131ee342cea672fc4fa8771f8ede
ms.lasthandoff: 04/21/2017


---

# <a name="migrate-your-sql-server-database-to-azure-sql-database"></a>Migrar seu banco de dados do SQL Server para o banco de dados SQL do Azure

Neste tutorial, você pode migrar um banco de dados existente do SQL Server para o banco de dados SQL do Azure usando o Assistente de migração de dados da Microsoft e percorrer as etapas necessárias de preparação para migração para executar a migração de dados reais e conectar-se ao banco de dados migrado depois de concluída a migração. 

> [!IMPORTANT]
> Para corrigir problemas de compatibilidade, use as [Ferramentas de dados do Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt). 
>

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Para concluir este tutorial, verifique se você tem:

- A versão mais recente do [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Instalar o SSMS também instala a versão mais recente do SQLPackage, um utilitário de linha de comando que pode ser usado para automatizar uma variedade de tarefas de desenvolvimento de banco de dados. 
- O [Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595) (DMA).
- Um banco de dados a migrar. Este tutorial usa o banco de dados [SQL Server 2008R2 AdventureWorks OLTP](https://msftdbprodsamples.codeplex.com/releases/view/59211) em uma instância do SQL Server 2008R2 ou mais recente, mas você pode usar qualquer banco de dados de sua escolha. 

## <a name="prepare-for-migration"></a>Preparar para a migração

Você está pronto para preparar para a migração. Siga estas etapas para usar o  **[Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595)**  para avaliar a preparação do banco de dados para migração para o banco de dados SQL do Azure.

1. Abra o **Assistente de migração de dados**. Você pode executar DMA em qualquer computador com conectividade para a instância do SQL Server que contenha o banco de dados que você planeja migrar, você não precisa instalá-lo no computador que hospeda a instância do SQL Server.

     ![abra o assistente de migração de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-open.png)

2. No menu à esquerda, clique em **+ Novo** para criar um projeto de **Avaliação**. Preencha o formulário com um **Nome do projeto** (todos os outros valores devem ser deixados com seus valores padrão) e clique em **Criar**. A página **Opções** se abre.

     ![novo projeto do assistente de migração de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-new-project.png)

3. Na página **Opções**, clique em **Próxima**. A página **Selecionar fontes** será aberta.

     ![novas opções de migração de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-options.png) 

4. Na página **Selecionar fontes**, insira o nome da instância do SQL Server que contém o servidor que você planeja migrar. Altere os outros valores dessa página, se necessário. Clique em **Conectar**.

     ![nova seleção de fontes da migração de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-sources.png)

5. Na parte **Adicionar fontes** da página **Selecionar fontes**, marque as caixas de seleção para os bancos de dados a serem testadas para compatibilidade. Clique em **Adicionar**.

     ![nova seleção de fontes da migração de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-sources-add.png)

6. Clique em **Iniciar avaliação**.

     ![nova avaliação de início da migração de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-start-assessment.png)

7. Quando a avaliação for concluída, primeiro verifique se o banco de dados é suficientemente compatível para migrar. Procure a marca de seleção em um círculo verde.

     ![novos resultados da avaliação de migração de dados compatíveis](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-compatible.png)

8. Reveja os resultados, começando com **paridade de recursos do SQL Server**. Especificamente, examine as informações sobre os recursos sem suporte e com suporte parcial e as informações fornecidas sobre as ações recomendadas. 

     ![nova paridade da avaliação de migração de dados](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-parity.png)

9. Clique em **Problemas de compatibilidade**. Especificamente, examine as informações sobre bloqueadores de migração, alterações de comportamento e recursos preteridos para cada nível de compatibilidade. Para o banco de dados AdventureWorks2008R2, reveja as alterações para pesquisa de texto completo desde o SQL Server 2008 e as alterações para SERVERPROPERTY('LCID') desde o SQL Server 2000. Para obter detalhes sobre essas alterações, são fornecidos links para obter mais informações. Muitas opções de pesquisa e as configurações de pesquisa de texto completo foram alteradas 

   > [!IMPORTANT] 
   > Depois de migrar seu banco de dados para o Banco de Dados SQL do Azure, é possível operar o banco de dados em seu nível de compatibilidade atual (nível 100 para o banco de dados AdventureWorks2008R2) ou em um nível superior. Para obter mais informações sobre as implicações e as opções para a operação de um banco de dados em um nível de compatibilidade específico, consulte [nível de compatibilidade de ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Consulte também [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para obter informações sobre configurações de nível de banco de dados adicionais relacionadas aos níveis de compatibilidade.
   >

10. Opcionalmente, clique em **Exportar relatório** para salvar o relatório como um arquivo JSON.
11. Feche o Assistente de migração de dados.

## <a name="export-to-bacpac-file"></a>Exportar para um arquivo BACPAC 

Um arquivo BACPAC é um arquivo ZIP com uma extensão BACPAC que contém os metadados e dados de um banco de dados do SQL Server. Um arquivo BACPAC pode ser armazenado no armazenamento de blobs do Azure ou no armazenamento local de arquivamento ou para a migração – como do SQL Server para o banco de dados SQL do Azure. Para uma exportação transacionalmente consistente, você deve garantir que nenhuma atividade de gravação está ocorrendo durante a exportação.

Siga estas etapas para usar o utilitário de linha de comando SQLPackage para exportar o banco de dados AdventureWorks2008R2 para armazenamento local.

1. Abra um prompt de comando do Windows e altere o diretório para uma pasta em que você tenha a versão **130** do SQLPackage – como **C:\Program Files (x86) \Microsoft SQL Server\130\DAC\bin**.

2. Execute o seguinte comando no prompt de comando do SQLPackage para exportar o banco de dados **AdventureWorks2008R2** do **localhost** para **AdventureWorks2008R2.bacpac**. Altere quaisquer desses valores conforme apropriado para seu ambiente.

    ```SQLPackage
    sqlpackage.exe /Action:Export /ssn:localhost /sdn:AdventureWorks2008R2 /tf:AdventureWorks2008R2.bacpac
    ```

    ![exportação de sqlpackage](./media/sql-database-migrate-your-sql-server-database/sqlpackage-export.png)

Depois que a execução for concluída, o arquivo BCPAC gerado é armazenado no diretório no qual o executável do sqlpackage está localizado. Neste exemplo, C:\Program Files (x86)\Microsoft SQL Server\130\DAC\bin. 

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com/). Logon do computador do qual você estiver executando o utilitário de linha de comando do SQLPackage facilita a criação de regra de firewall na etapa 5.

## <a name="create-a-sql-database-logical-server"></a>Criar um servidor lógico do Banco de Dados SQL

Um [servidor lógico do Banco de Dados SQL do Azure](sql-database-features.md) atua como um ponto administrativo central para vários bancos de dados. Siga estas etapas para criar um servidor lógico do Banco de Dados SQL para conter o banco de dados migrado do Adventure Works OLTP SQL Server. 

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

2. Tipo de **servidor** na janela de pesquisa na página **Nova** e selecione **banco de dados SQL (servidor lógico)** na lista filtrada.

    ![selecionar servidor lógico](./media/sql-database-migrate-your-sql-server-database/logical-server.png)

3. Na página **Tudo**, clique em **SQL Server (servidor lógico)** e, em seguida, clique em **Criar** na página **SQL Server (servidor lógico)**.

    ![criar um servidor lógico](./media/sql-database-migrate-your-sql-server-database/logical-server-create.png)

4. Preencha o formulário do SQL Server (servidor lógico) com as informações abaixo, conforme mostrado na imagem anterior:     

   - Nome do servidor: Especifique um nome do servidor exclusivo
   - Logon de administrador do servidor: forneça um nome para o servidor de logon de administrador
   - Senha: Especifique a senha de sua escolha
   - Grupo de recursos: selecione **Criar novo** e especifique **myResourceGroup**
   - Local: Selecione um local do data center

    ![criar formulário de servidor lógico concluído](./media/sql-database-migrate-your-sql-server-database/logical-server-create-completed.png)

5. Clique em **Criar** para provisionar o servidor lógico. O provisionamento demora alguns minutos. 

## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall no nível de servidor

O serviço do Banco de Dados SQL cria um [firewall no nível do servidor](sql-database-firewall-configure.md) impedindo que os aplicativos e ferramentas externos conectem o servidor ou os bancos de dados no servidor, a menos que uma regra de firewall seja criada para abrir o firewall para endereços IP específicos. Siga estas etapas para criar uma regra de firewall de nível de servidor do banco de dados SQL para o endereço IP do computador do qual você estiver executando o utilitário de linha de comando SQLPackage. Isso permite que o SQLPackage se conecte ao servidor lógico do Banco de Dados SQL por meio do firewall do banco de dados SQL do Azure. 

1. Clique em **todos os recursos** no menu à esquerda e clique em seu novo servidor na página **todos os recursos**. A página de visão geral de seu servidor é aberta, mostrando o nome totalmente qualificado do servidor (como **mynewserver20170403.database.windows.net**) e fornece opções para configurações adicionais.

     ![visão geral do servidor lógico](./media/sql-database-migrate-your-sql-server-database/logical-server-overview.png)

2. Clique em **Firewall** no menu esquerdo sob **Configurações** da página Visão geral. A página **Configurações do firewall** do servidor de Banco de Dados SQL é aberta. 

3. Clique em **Adicionar IP do cliente** na barra de ferramentas para adicionar o endereço IP do computador que você está usando no momento e clique em **Salvar**. Uma regra de firewall no nível do servidor é criada para o endereço IP atual.

     ![definir regra de firewall do servidor](./media/sql-database-migrate-your-sql-server-database/server-firewall-rule-set.png)

4. Clique em **OK**.

Agora você pode se conectar a todos os bancos de dados e em seu servidor usando o SQL Server Management Studio ou outra ferramenta de sua escolha por meio deste endereço IP usando a conta do administrador do Servidor criada anteriormente.

> [!NOTE]
> O Banco de Dados SQL se comunica pela porta 1433. Se você estiver tentando conectar-se a partir de uma rede corporativa, o tráfego de saída pela porta 1433 poderá não ser permitido pelo firewall de sua rede. Se isto acontecer, você não conseguirá conectar seu servidor do Banco de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>

## <a name="import-bacpac-file-to-azure-sql-database"></a>Importar o arquivo BACPAC para o banco de dados SQL do Azure 

As versões mais recentes do utilitário de linha de comando SQLPackage oferecem suporte à criação de um banco de dados SQL do Azure em determinado [nível de desempenho e da camada de serviço](sql-database-service-tiers.md). Para melhor desempenho durante a importação, selecione um alto nível de desempenho e da camada de serviço e, então, reduza após a importação, se o nível de desempenho e da camada de serviço for maior do que o necessário, imediatamente.

Siga essas etapas, use o utilitário de linha de comando SQLPackage para importar o banco de dados AdventureWorks2008R2 para o banco de dados SQL do Azure.

- Execute o seguinte comando no prompt de comando SQLPackage para importar o banco de dados **AdventureWorks2008R2** do armazenamento local para o servidor lógico do banco de dados SQL do Azure que você criou anteriormente com um nome de banco de dados de **myMigratedDatabase**, uma camada de serviço de **Premium** e o objetivo de serviço de **P6**. Altere quaisquer desses três valores conforme apropriado para seu ambiente.

    ```
    SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<change_to_your_admin_user_account>;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
    ```

   ![importação de sqlpackage](./media/sql-database-migrate-your-sql-server-database/sqlpackage-import.png)

> [!IMPORTANT]
> Um servidor lógico do Banco de Dados SQL do Azure escuta na porta 1433. Se você estiver tentando conectar um servidor lógico do Banco de Dados SQL do Azure de dentro de um firewall corporativo, essa porta deverá estar aberta no firewall corporativo para que você possa conectar-se com êxito.
>

## <a name="connect-using-sql-server-management-studio-ssms"></a>Conectar-se usando o SQL Server Management Studio (SSMS)

Use o SQL Server Management Studio para estabelecer uma conexão com seu servidor do Banco de Dados SQL do Azure e o novo banco de dados migrado. Se você estiver executando o SQL Server Management Studio em um computador diferente do qual você executou o SQLPackage, crie uma regra de firewall para este computador usando as etapas no procedimento anterior.

1. Abra o SQL Server Management Studio.

2. Na caixa de diálogo **Conectar ao Servidor**, insira as informações a seguir:
   - **Tipo de servidor**: especifique mecanismo de banco de dados
   - **Nome do servidor**: insira seu nome do servidor totalmente qualificado, como **mynewserver20170403.database.windows.net**
   - **Autenticação**: especifique a Autenticação do SQL Server
   - **Logon**: insira a conta do administrador do servidor
   - **Senha**: insira a senha para sua conta do administrador do servidor
 
    ![conectar com SSMS](./media/sql-database-migrate-your-sql-server-database/connect-ssms.png)

3. Clique em **Conectar**. A janela Pesquisador de Objetos se abre. 

4. No Pesquisador de Objetos, expanda **Bancos de Dados** e expanda **myMigratedDatabase** para exibir os objetos no banco de dados de exemplo.

## <a name="change-database-properties"></a>Alterar as propriedades de banco de dados

Você pode alterar a camada de serviço, o nível de desempenho e o nível de compatibilidade usando o SQL Server Management Studio.

1. No Pesquisador de Objetos, clique com o botão direito do mouse em **myMigratedDatabase** e clique em **Nova Consulta**. Uma janela de consulta se abre conectada ao banco de dados.

2. Execute o seguinte comando para definir a camada de serviço como **Standard** e o nível de desempenho para **S1**.

    ```
    ALTER DATABASE myMigratedDatabase 
    MODIFY 
        (
        EDITION = 'Standard'
        , MAXSIZE = 250 GB
        , SERVICE_OBJECTIVE = 'S1'
    );
    ```

    ![alterar camada de serviço](./media/sql-database-migrate-your-sql-server-database/service-tier.png)

3. Execute o seguinte comando para alterar o nível de compatibilidade do banco de dados para **130**.

    ```
    ALTER DATABASE myMigratedDatabase  
    SET COMPATIBILITY_LEVEL = 130;
    ```

   ![alterar nível de compatibilidade](./media/sql-database-migrate-your-sql-server-database/compat-level.png)

## <a name="next-steps"></a>Próximas etapas 

- Para obter uma visão geral de migração, consulte [migração de banco de dados](sql-database-cloud-migrate.md).
- Para uma discussão sobre as diferenças de T-SQL, consulte [diferenças de resolução de Transact-SQL durante a migração para o banco de dados SQL](sql-database-transact-sql-information.md).
- Para conectar e consultar usando o Visual Studio Code, veja [Conectar e consultar com o Visual Studio Code](sql-database-connect-query-vscode.md).
- Para conectar e consultar usando o .NET, veja [Conectar e consultar com o .NET](sql-database-connect-query-dotnet.md).
- Para conectar e consultar usando o PHP, veja [Conectar e consultar com o PHP](sql-database-connect-query-php.md).
- Para conectar e consultar usando o Node.js, veja [Conectar e consultar com o Node.js](sql-database-connect-query-nodejs.md).
- Para conectar e consultar usando o Java, veja [Conectar e consultar com o Java](sql-database-connect-query-java.md).
- Para conectar e consultar usando o Python, veja [Conectar e consultar com o Python](sql-database-connect-query-python.md).
- Para conectar e consultar usando o Ruby, veja [Conectar e consultar com o Ruby](sql-database-connect-query-ruby.md).


