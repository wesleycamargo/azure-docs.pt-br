<properties
   pageTitle="Corrigir problemas de compatibilidade do banco de dados do SQL Server antes da migração para o Banco de Dados SQL"
   description="Banco de Dados SQL do Microsoft Azure, migração de banco de dados, compatibilidade, Assistente de Migração do SQL Azure, SSDT"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="05/31/2016"
   ms.author="carlrab"/>

# Migrar um Banco de Dados SQL Server para o Banco de Dados SQL do Azure usando as SQL Server Data Tools para Visual Studio 

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Supervisor de Atualização](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

Neste artigo, você aprenderá a detectar e corrigir problemas de compatibilidade do Banco de Dados SQL Server usando as SQL Server Data Tools para Visual Studio antes da migração para o Banco de Dados SQL do Azure.

## Como usar o SQL Server Data Tools para Visual Studio

Usar o SQL Server Data Tools para Visual Studio (“SSDT”) para importar o esquema de banco de dados em um projeto de banco de dados do Visual Studio para análise. Para analisar, especifique a plataforma de destino para o projeto como Banco de Dados SQL V12 e crie o projeto. Se a criação for bem-sucedida, o banco de dados é compatível. Se a compilação falhar, você poderá resolver os erros no SSDT (ou em uma das outras ferramentas discutidas neste tópico). Depois que o projeto for criado com êxito, você poderá publicá-lo como uma cópia do banco de dados de origem e usar o recurso de comparação de dados no SSDT para copiar os dados do banco de dados de origem no banco de dados compatível com o SQL V12 do Azure. Em seguida, você pode migrar esse banco de dados atualizado. Para usar essa opção, baixe a [versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx).

  ![Diagrama de migração do VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  > [AZURE.NOTE] Se a migração somente de esquema for necessária, o esquema poderá ser publicado diretamente do Visual Studio no Banco de Dados SQL do Azure. Use esse método quando o esquema de banco de dados requer mais alterações do que o Assistente de migração pode administrar sozinho.

## Detectando problemas de compatibilidade usando o SQL Server Data Tools para Visual Studio
   
1.	Abra o **Pesquisador de Objetos do SQL Server** no Visual Studio. Use **Adicionar SQL Server** para se conectar à instância do SQL Server que contém o banco de dados que está sendo migrado. Localize o banco de dados no Pesquisador, clique com o botão direito nele e selecione **Criar Novo Projeto...**     
    
	![Novo Projeto](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)
   
2.	Defina as configurações de importação como **Importar somente objetos no escopo do aplicativo**. Desmarque as opções para importar o seguinte: logons referenciados, permissões e configurações de banco de dados.

    ![texto alt](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)

3.	Clique em **Iniciar** para importar o banco de dados e criar o projeto, que conterá um arquivo de script T-SQL para cada objeto no banco de dados. Os arquivos de script são aninhados em pastas dentro do projeto.

    ![texto alt](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)

4.	No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto do banco de dados e selecione Propriedades. Isso abrirá a página **Configurações do Projeto** na qual você deve configurar a Plataforma de Destino como o Banco de Dados SQL do Microsoft Azure V12.
    
    ![texto alt](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)
    
5.	Clique com o botão direito no projeto e selecione **Criar** para compilar o projeto.
    
	![texto alt](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)
    
6.	A **Lista de Erros** exibe cada incompatibilidade. Nesse caso, o nome de usuário NT AUTHORITY\\NETWORK SERVICE é incompatível. Como é incompatível, você pode comentá-lo ou removê-lo (e cuidar das implicações da remoção desse logon e da função a partir da solução de banco de dados).
    
	![texto alt](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)
    
## Corrigindo problemas de compatibilidade usando o SQL Server Data Tools para Visual Studio        
  
1.	Clique duas vezes no primeiro script para abri-lo em uma janela de consulta e comente o script e, em seguida, execute-o. ![texto alt](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)    

2.	Repita esse processo para cada script que contiver incompatibilidades até que nenhum erro permaneça. ![texto alt](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
3.	Quando o banco de dados estiver livre de erros, clique com o botão direito do mouse no projeto e selecione **Publicar** para compilar e publicar o banco de dados em uma cópia do banco de dados de origem (é altamente recomendável usar uma cópia, pelo menos a princípio).     
 - Antes de publicar, dependendo da versão do SQL Server de origem (anterior ao SQL Server 2014), talvez seja necessário redefinir a plataforma de destino do projeto a fim de permitir a implantação.     
 - Se você estiver migrando um banco de dados SQL Server mais antigo, não será necessário introduzir recursos que não têm suporte no SQL Server de origem ao projeto, a menos que primeiro você migre o banco de dados para uma versão mais recente do SQL Server.     

    	![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)    
    
    	![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)    
    
4.	No Pesquisador de objetos do SQL Server, clique com o botão direito do mouse em seu banco de dados de origem e clique em **Comparação de Dados** para comparar o projeto no banco de dados original para entender quais alterações foram feitas pelo assistente. Selecione a versão V12 do SQL do Azure do banco de dados e, em seguida, clique em **Concluir**.    
    
	![texto alt](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)
    
	![texto alt](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)
    
5.	Revise as diferenças detectadas e, em seguida, clique em **Atualizar Destino** para migrar dados do banco de dados de origem para o banco de dados V12 do SQL do Azure.
    
	![texto alt](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)
    
6.	Escolha um método de implantação. Veja [Migrar um Banco de Dados SQL Server compatível para o Banco de Dados SQL.](sql-database-cloud-migrate.md)

## Próximas etapas

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## Saiba mais

- [Banco de Dados SQL V12](sql-database-v12-whats-new.md)
- [Funções como suporte parcial ou inexistente no Transact-SQL](sql-database-transact-sql-information.md)
- [Migrar bancos de dados não SQL Server usando o Assistente de Migração do SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0601_2016-->