<properties
   pageTitle="Migrando um banco de dados do SQL Server para o Banco de Dados SQL do Azure"
   description="Banco de Dados SQL do Microsoft Azure, implantação de banco de dados, migração de banco de dados, banco de dados de importação, exportação de banco de dados, assistente de migração"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="10/12/2015"
   ms.author="carlrab"/>

# Migrando um banco de dados do SQL Server para o Banco de Dados SQL do Azure

O Banco de Dados SQL V12 do Azure oferece compatibilidade de mecanismo quase completa com o SQL Server 2014 e o SQL Server 2016. Para os bancos de dados compatíveis, a migração para o Banco de Dados SQL do Azure é um esquema direto e uma operação de movimentação de dados que requer poucas (se houver) alterações no esquema e pouca ou nenhuma reengenharia de aplicativos. Onde os bancos de dados precisam ser alterados, o escopo dessas alterações é mais restrito do que o Banco de Dados SQL do Azure v11.

Desde o início, os recursos do SQL Server no escopo do servidor não têm suporte do Banco de Dados SQL V12 do Azure. Os bancos de dados e aplicativos que dependem desses recursos precisarão de alguma reengenharia para que possam ser migrados.

>[AZURE.NOTE]Para migrar outros tipos de bancos de dados, incluindo o Microsoft Access, Sybase, MySQL Oracle e DB2 para o Banco de Dados SQL do Azure, consulte [Assistente de Migração do SQL Server](http://blogs.msdn.com/b/ssma/).

O fluxo de trabalho para a migração de um banco de dados do SQL Server para o Banco de Dados SQL do Azure é:

 1. [Determinar se o seu banco de dados é compatível](#determine-if-your-database-is-compatible)
 2. [Se não for compatível, corrija problemas de compatibilidade do banco de dados](#fix-database-compatibility-issues)
 3. [Migrar um banco de dados compatível](#options-to-migrate-a-compatible-database-to-azure-sql-database)

## Determinar se o seu banco de dados é compatível
Há dois métodos principais a serem usados para determinar se o banco de dados de origem é compatível. -Exportar Aplicativo da Camada de Dados: esse método usa um assistente no Management Studio e exibe mensagens de erro no console. -SQLPackage.exe: [sqlpackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) é um utilitário de linha de comando que acompanha o Visual Studio e o SQL Server. Este método gerará um relatório.

> [AZURE.NOTE]Há um terceiro método, que também usará arquivos de rastreamento para testar a compatibilidade. Esse é o [assistente de Migração do SQL Azure](http://sqlazuremw.codeplex.com/), uma ferramenta gratuita do Codeplex. No entanto, atualmente essa ferramenta pode encontrar erros de compatibilidade que sejam problemas para o Banco de Dados SQL do Azure V11 e que não sejam problemas para o Banco de Dados SQL do Azure V12.

Se forem detectadas incompatibilidades de banco de dados, você precisará corrigir essas incompatibilidades antes de poder migrar seu banco de dados para o Banco de Dados SQL do Azure. Para obter diretrizes sobre como corrigir problemas de compatibilidade do banco de dados, acesse [corrigir problemas de compatibilidade do banco de dados](#fix-database-compatibility-issues).

> [AZURE.IMPORTANT]Estas opções não capturam todos os problemas de compatibilidade entre os diferentes níveis de bancos de dados do SQL Server (ou seja, nível 90, 100 e 110). Se você estiver migrando de um banco de dados mais antigo (nível 80, 90, 100 e 110), deverá passar pelo processo de atualização primeiro (pelo menos no ambiente de desenvolvimento) e uma vez no SQL Server 2014 ou posterior e então migrar para o Banco de Dados SQL do Azure.

## Determinar se seu banco de dados é compatível com o uso de sqlpackage.exe

1. Abra um prompt de comando e altere um diretório com a versão mais recente do sqlpackage.exe. Esse utilitário é fornecido com o Visual Studio e o SQL Server.
2. Execute o seguinte comando, substituindo os seguintes argumentos: < server_name >, < database_name >, < target_file >, < schema_name.table_name > e < output_file >. O motivo da presença do argumento /p:TableName é que só queremos testar a compatibilidade de banco de dados para exportar para o Banco de Dados SQL do Azure V12, em vez de exportar os dados de todas as tabelas. Infelizmente, o argumento de exportação para sqlpackage.exe não dá suporte à extração de tabelas e, portanto, será necessário especificar uma pequena tabela única. O < output_file > conterá o relatório de erros.

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1'

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Abra o arquivo de saída e examine os erros de compatibilidade, se houver. Para obter diretrizes sobre como corrigir problemas de compatibilidade do banco de dados, acesse [corrigir problemas de compatibilidade do banco de dados](#fix-database-compatibility-issues).

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## Determinar se seu banco de dados é compatível com o uso de Exportar Aplicativo de Camada de Dados

1. Verifique se você tem a versão 13.0.600.65 ou posterior do SQL Server Management Studio. As novas versões do Management Studio são atualizadas mensalmente para permanecerem em sincronia com as atualizações para o portal do Azure.

 	 >[AZURE.IMPORTANT]Baixe a versão [mais recente](https://msdn.microsoft.com/library/mt238290.aspx) do SQL Server Management Studio. É recomendável que você sempre use a versão mais recente do Management Studio.

2. Abra o Management Studio e conecte-se ao banco de dados de origem no Pesquisador de Objetos.
3. Clique com o botão direito do mouse no banco de dados de origem no Pesquisador de Objetos, aponte para **Tarefas** e clique em **Exportar Aplicativo de Camada de Dados...**

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. No assistente de exportação, na guia **Configurações**, configure a exportação para salvar o arquivo BACPAC em um disco local ou em um blob do Azure. Um arquivo BACPAC só será salvo se você não tiver nenhum problema de compatibilidade de banco de dados. Se houver problemas de compatibilidade, eles serão exibidos no console.

	![Exportar configurações](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. Clique na **guia Avançado** e desmarque a caixa de seleção **Selecionar Tudo** para ignorar a exportação de dados. Neste ponto, nosso objetivo será apenas testar a compatibilidade.

	![Exportar configurações](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. Clique em **Avançar** e então clique em **Concluir**. Os problemas de compatibilidade do banco de dados, se houver, serão exibidos depois que o assistente validar o esquema.

	![Exportar configurações](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. Se nenhum erro for exibido, o banco de dados é compatível e você está pronto para migrar. Se houver erros, você precisará corrigi-los. Para ver os erros, clique em **Erro** para **Validando esquema**. Para saber como corrigi-los, vá para [corrigir problemas de compatibilidade do banco de dados](#fix-database-compatibility-issues).

	![Exportar configurações](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

## Opções para migrar um banco de dados compatível com o Banco de Dados SQL do Azure

- No caso de bancos de dados pequenos a médios, migrar um banco de dados do SQL Server 2005 ou posterior [compatível](#determine-if-your-database-is-compatible) é tão simples quanto executar o [Assistente Implantar Banco de Dados no Banco de Dados do Microsoft Azure](#use-the-deploy-database-to-microsoft-azure-database-wizard) no SQL Server Management Studio. Se você tiver os desafios de conectividade (sem conectividade, baixa largura de banda ou problemas de tempo limite), poderá [usar o BACPAC para migrar](#use-a-bacpac-to-migrate-a-database-to-azure-sql-database) um banco de dados do SQL Server para o Banco de Dados SQL do Azure.
- Para bancos de dados médios a grandes ou quando você tiver desafios de conectividade, [use um BACPAC para migrar](#use-a-bacpac-to-migrate-a-database-to-azure-sql-database) um banco de dados do SQL Server para o Banco de Dados SQL do Azure. Com este método, você usa o SQL Server Management Studio para exportar os dados e o esquema para um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) (armazenado localmente ou em um blob do Azure) e, em seguida, importar o arquivo BACPAC para sua instância SQL do Azure. Se você armazenar o BACPAC em um blob do Azure, também será possível importar o arquivo BACPAC do [portal do Azure](sql-database-import.md) ou [usando o PowerShell](sql-database-import-powershell.md).
- Para bancos de dados maiores, você conseguirá o melhor desempenho migrando o esquema e os dados separadamente. Com este método, você cria um script do esquema usando o SQL Server Management Studio ou cria um projeto de banco de dados no Visual Studio e implante o esquema para o Banco de Dados SQL do Azure. Depois que o esquema tiver sido importado para o Banco de Dados SQL do Azure, você usará o [BCP](https://msdn.microsoft.com/library/ms162802.aspx) para extrair os dados em arquivos simples e importará esses arquivos para o Banco de Dados SQL do Azure.

 ![Diagrama de migração do SSMS](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

## Use o Assistente para Implantar Banco de Dados no Banco de Dados do Microsoft Azure

O assistente Implantar Banco de Dados para o Banco de Dados do Microsoft Azure no SQL Server Management Studio migra uma migração de um banco de dados SQL Server 2005 ou posterior [compatível](#determine-if-your-database-is-compatible) diretamente em sua instância de servidor lógico do Azure SQL.

> [AZURE.NOTE]As etapas abaixo supõem que você já tenha provisionado sua instância lógica do Azure SQL e tenha as informações sobre a conexão a seu alcance.

1. Verifique se você tem a versão 13.0.600.65 ou posterior do SQL Server Management Studio. As novas versões do Management Studio são atualizadas mensalmente para permanecerem em sincronia com as atualizações para o portal do Azure.

	 >[AZURE.IMPORTANT]Baixe a versão [mais recente](https://msdn.microsoft.com/library/mt238290.aspx) do SQL Server Management Studio. É recomendável que você sempre use a versão mais recente do Management Studio.

2. Abra o Management Studio e conecte-se ao banco de dados de origem no Pesquisador de Objetos.
3. Clique com o botão direito do mouse no banco de dados de origem no Pesquisador de Objetos, aponte para **Tarefas** e clique em **Implantar Banco de Dados no Banco de Dados SQL do Microsoft Azure...**

	![Implantar para o Azure por meio do menu de Tarefas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.	No assistente de implantação, configure a conexão ao servidor do Banco de Dados SQL do Azure.
5.	Forneça o **Novo nome de banco de dados** para o banco de dados no Banco de Dados SQL do Azure, defina a **Edição do Banco de Dados SQL do Microsoft Azure** (camada de serviço), **Tamanho máximo do banco de dados**, **Objetivo de Serviço** (nível de desempenho) e **Nome do arquivo temporário** para o arquivo BACPAC criado por esse assistente durante o processo de migração. Consulte [Camadas de serviço do Banco de Dados SQL do Azure](sql-database-service-tiers.md) para saber mais sobre camadas de serviço e níveis de desempenho.

	![Exportar configurações](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.	Siga o assistente para migrar o banco de dados. Dependendo do tamanho e da complexidade do banco de dados, a implantação talvez demore de alguns minutos até algumas horas.
7.	Usando o Pesquisador de Objetos, conecte-se ao banco de dados migrado em seu servidor de Banco de Dados SQL do Azure.
8.	Usando o Portal do Azure, exiba seu banco de dados e suas propriedades.

## Usar um BACPAC para migrar um banco de dados do SQL Server para o Banco de Dados SQL do Azure

Para bancos de dados médios a grandes ou quando você tiver desafios de conectividade, você poderá separar o processo de migração em duas etapas distintas. Você pode exportar o esquema e seus dados em um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) usando um ou dois métodos.

- [Exportar para um arquivo BACPAC usando o SQL Server Management Studio](#export-a-compatible-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio)
- [Exportar para um BACPAC usando o SqlPackage](#export-a-compatible-sql-server-database-to-a-bacpac-file-using-sqlpackage)

Você pode armazenar este BACPAC localmente ou em um blob do Azure. Em seguida, você poderá importar esse arquivo BACPAC para o Banco de Dados SQL do Azure usando um dos vários métodos.

- [Importando de um arquivo BACPAC para o Banco de Dados SQL do Azure usando o SQL Server Management Studio](#import-from-a-bacpac-file-into-azure-sql-database-using-sql-server-management-studio)
- [Importar de um arquivo BACPAC para o Banco de Dados SQL do Azure usando o SqlPackage](#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage)
- [Importar de um arquivo BACPAC para o Banco de Dados SQL do Azure usando o portal do Azure](sql-database-import.md)
- [Importar de um arquivo BACPAC para o Banco de Dados SQL do Azure usando o PowerShell](sql-database-import-powershell.md)

## Exportar um banco de dados do SQL Server compatível para um arquivo BACPAC usando o SQL Server Management Studio

Use as etapas abaixo para usar o Management Studio para exportar uma migração de um banco de dados do SQL Server [compatível](#determine-if-your-database-is-compatible) para um arquivo BACPAC.

1. Verifique se você tem a versão 13.0.600.65 ou posterior do SQL Server Management Studio. As novas versões do Management Studio são atualizadas mensalmente para permanecerem em sincronia com as atualizações para o portal do Azure.

	 >[AZURE.IMPORTANT]Baixe a versão [mais recente](https://msdn.microsoft.com/library/mt238290.aspx) do SQL Server Management Studio. É recomendável que você sempre use a versão mais recente do Management Studio.

2. Abra o Management Studio e conecte-se ao banco de dados de origem no Pesquisador de Objetos.

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. Clique com o botão direito do mouse no banco de dados de origem no Pesquisador de Objetos, aponte para **Tarefas** e clique em **Exportar Aplicativo de Camada de Dados...**

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. No assistente de exportação, configure a exportação para salvar o arquivo BACPAC em um disco local ou em um blob do Azure. O BACPAC exportado sempre inclui o esquema de banco de dados completo e, por padrão, os dados de todas as tabelas. Se você quiser excluir dados de algumas ou de todas as tabelas, use a guia Avançado. Por exemplo, você pode optar por exportar somente os dados das tabelas de referência em vez de todas as tabelas.

	![Exportar configurações](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)

## Exportar um banco de dados do SQL Server compatível para um arquivo BACPAC usando o SqlPackage

Use as etapas abaixo para usar o utilitário de linha de comando [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) para exportar uma migração de um banco de dados [compatível](#determine-if-your-database-is-compatible) para um arquivo BACPAC.

> [AZURE.NOTE]As etapas a seguir supõem que você já configurou um servidor de Banco de Dados SQL do Azure, tem as informações de conexão disponíveis e verificou se o banco de dados de origem é compatível.

1. Abra um prompt de comando e altere um diretório com o utilitário de linha de comando sqlpackage.exe - esse utilitário é fornecido com o Visual Studio e o SQL Server.
2. Execute o comando a seguir, substituindo estes argumentos: < server_name >, < database_name > e < target_file >.

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Importando de um arquivo BACPAC para o Banco de Dados SQL do Azure usando o SQL Server Management Studio

Use as etapas abaixo para importar de um arquivo BACPAC para o Banco de Dados SQL do Azure.

> [AZURE.NOTE]As etapas abaixo supõem que você já tenha provisionado sua instância lógica do Azure SQL e tenha as informações sobre a conexão a seu alcance.

1. Verifique se você tem a versão 13.0.600.65 ou posterior do SQL Server Management Studio. As novas versões do Management Studio são atualizadas mensalmente para permanecerem em sincronia com as atualizações para o portal do Azure.

	 >[AZURE.IMPORTANT]Baixe a versão [mais recente](https://msdn.microsoft.com/library/mt238290.aspx) do SQL Server Management Studio. É recomendável que você sempre use a versão mais recente do Management Studio.

2. Abra o Management Studio e conecte-se ao banco de dados de origem no Pesquisador de Objetos.

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

    Quando o BACPAC tiver sido criado, conecte-se ao seu servidor do Banco de Dados SQL do Azure, clique com o botão direito do mouse na pasta **Bancos de Dados** e clique em **Importar Aplicativo da Camada de Dados...**

    ![Importar o item de menu do aplicativo da camada de dados](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

3.	No assistente de importação, selecione o arquivo BACPAC recém-exportado para criar o novo Banco de Dados SQL do Azure.

    ![Configurações de importação](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

4.	Forneça o **Novo nome de banco de dados** para o banco de dados no Banco de Dados SQL do Azure, defina a **Edição do Banco de Dados SQL do Microsoft Azure** (camada de serviço), **Tamanho máximo do banco de dados**, **Objetivo de Serviço** (nível de desempenho).

    ![Configurações de banco de dados](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

5.	Clique em **Avançar** e clique em **Concluir** para importar o arquivo BACPAC para um novo banco de dados no servidor do Banco de Dados SQL do Azure.

6. Usando o Pesquisador de Objetos, conecte-se ao banco de dados migrado em seu servidor de Banco de Dados SQL do Azure.

7.	Usando o Portal do Azure, exiba seu banco de dados e suas propriedades.

## Importar de um arquivo BACPAC para o Banco de Dados SQL do Azure usando o SqlPackage

Use as etapas abaixo para usar o utilitário de linha de comando [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) para importar um banco de dados do SQL Server compatível (ou um banco de dados SQL do Azure) de um arquivo BACPAC.

> [AZURE.NOTE]As etapas abaixo supõem que você já tenha provisionado um servidor do Banco de Dados SQL do Azure e tenha as informações sobre a conexão a seu alcance.

1. Abra um prompt de comando e altere um diretório com o utilitário de linha de comando sqlpackage.exe - esse utilitário é fornecido com o Visual Studio e o SQL Server.
2. Execute o comando a seguir, substituindo estes argumentos: < server_name >, < database_name >, < user_name >, < password > e < source_file >.

	'sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< target_file >

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)


## Corrigir problemas de compatibilidade do banco de dados

Se você determinar que o banco de dados do SQL Server de origem não é compatível, terá várias opções para corrigir os problemas de compatibilidade do banco de dados [identificados anteriormente](#determine-if-your-database-is-compatible).

- Use o [assistente de Migração do SQL Azure](http://sqlazuremw.codeplex.com/). Você pode usar esta ferramenta Codeplex para gerar um script T-SQL de um banco de dados de origem incompatível que é transformado pelo assistente a fim de torná-lo compatível com o Banco de Dados SQL e conectá-lo ao Banco de Dados SQL do Azure para execução do script. Essa ferramenta também analisará arquivos de rastreamento para determinar problemas de compatibilidade. O script pode ser gerado apenas com esquemas ou pode incluir dados no formato BCP. Há uma documentação adicional, incluindo instruções passo a passo, disponível no Codeplex em [Assistente de Migração do SQL Azure](http://sqlazuremw.codeplex.com/).  

 ![Diagrama de migração do SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

 >[AZURE.NOTE]Observe que nem todos os esquemas incompatíveis que podem ser detectados pelo assistente podem ser processados por suas transformações internas. O script incompatível que não puder ser incluído será apontado como erro, com comentários injetados no script gerado. Se vários erros forem detectados, use o Visual Studio ou o SQL Server Management Studio para depurar e corrigir cada erro que não puder ser corrigido pelo Assistente de Migração do SQL Server.

- Use o Virtual Studio. Você pode usar o Visual Studio para importar o esquema de banco de dados para um projeto de banco de dados do Visual Studio para análise. Para analisar, especifique a plataforma de destino para o projeto como Banco de Dados SQL V12 e crie o projeto. Se a criação for bem-sucedida, o banco de dados é compatível. Se falhar, você poderá corrigir os erros no SQL Server Data Tools para Visual Studio ("SSDT"). Depois que o projeto for criado com êxito, você poderá publicá-lo como uma cópia do banco de dados de origem e usar o recurso de comparação de dados no SSDT para copiar os dados do banco de dados de origem no banco de dados compatível com o SQL V12 do Azure. Esse banco de dados atualizado é implantado no Banco de Dados SQL do Azure usando as opções [discutidas anteriormente](#options-to-migrate-a-compatible-database-to-azure-sql-database).

 ![Diagrama de migração do VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

 >[AZURE.NOTE]Se a migração somente de esquema for necessária, o esquema poderá ser publicado diretamente do Visual Studio no Banco de Dados SQL do Azure. Use esse método quando o esquema de banco de dados requer mais alterações do que o Assistente de migração pode administrar sozinho.

- SQL Server Management Studio. Você pode corrigir os problemas no Management Studio usando vários comandos Transact-SQL, como **ALTER DATABASE**.

<!---HONumber=Oct15_HO3-->