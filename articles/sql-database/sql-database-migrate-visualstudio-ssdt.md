<properties 
   pageTitle="Migração usando o Visual Studio e SSDT" 
   description="Banco de Dados SQL do Microsoft Azure, migração de banco de dados, importar banco de dados, exportar banco de dados, assistente de migração" 
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
   ms.date="08/24/2015"
   ms.author="carlrab"/>

#Atualizar o banco de dados no local e implantar o Banco de Dados SQL do Azure

![texto alt](./media/sql-database-migrate-visualstudio-ssdt/01VSSSDTDiagram.png)

Use esta opção quando a migração de um banco de dados local para o Banco de Dados SQL do Azure V12 requerer alterações de esquema porque o banco de dados usa recursos do SQL Server que não são compatíveis com o Banco de Dados SQL do Azure, ou para testar se recursos não compatíveis estão presentes em um banco de dados local.

Use [o SQL Server Data Tools for Visual Studio mais recente](https://msdn.microsoft.com/library/mt204009.aspx)com o Visual Studio 2013 Update 4 ou posterior.

Com essa opção:

 - O SQL Server Data Tools for Visual Studio ("SSDT") é usado primeiro para criar um projeto de banco de dados do banco de dados de origem. 
 - Em seguida, a plataforma de destino do projeto é definida como o Banco de Dados SQL do Azure V12 e o projeto é criado a fim de identificar todos os problemas de compatibilidade. 
 - Após a compilação bem-sucedida do projeto, o esquema é publicado novamente em uma cópia do banco de dados de origem (não substitua o banco de dados de origem).
 - O recurso de comparação de dados no SSDT é então usado para comparar o banco de dados de origem com o recém-criado banco de dados compatível com o SQL do Azure e, em seguida, atualizar o novo banco de dados com dados do banco de dados de origem. 
 - Em seguida, o banco de dados atualizado é implantado no Azure usando SSMS, diretamente ou por exportação e importação de um arquivo BACPAC.
 
>[AZURE.NOTE]Observação: caso uma implantação somente de esquema seja necessária, o esquema atualizado poderá ser publicado diretamente do Visual Studio para o Banco de Dados SQL do Azure.

## Etapas da migração

1.	Abra o **Pesquisador de Objetos do SQL Server** no Visual Studio. Use **Adicionar SQL Server** para se conectar à instância do SQL Server que contém o banco de dados que está sendo migrado. Localize o banco de dados no Pesquisador, clique com o botão direito nele e selecione **Criar Novo Projeto...** 

	![texto alt](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)

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
7.	Clique duas vezes no primeiro script para abri-lo em uma janela de consulta e comente o script e, em seguida, execute-o. ![texto alt](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

8.	Repita esse processo para cada script que contiver incompatibilidades até que nenhum erro permaneça. ![texto alt](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
9.	Quando o banco de dados estiver livre de erros, clique com o botão direito do mouse no projeto e selecione **Publicar** para compilar e publicar o banco de dados em uma cópia do banco de dados de origem (é altamente recomendável usar uma cópia, pelo menos a princípio). 
 - Antes de publicar, dependendo da versão do SQL Server de origem (anterior ao SQL Server 2014), talvez seja necessário redefinir a plataforma de destino do projeto a fim de permitir a implantação. 
 - Se você estiver migrando um banco de dados SQL Server mais antigo, não será necessário introduzir recursos que não têm suporte no SQL Server de origem ao projeto, a menos que primeiro você migre o banco de dados para uma versão mais recente do SQL Server. 

	![texto alt](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)

	![texto alt](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)

10.	No Pesquisador de objetos do SQL Server, clique com o botão direito do mouse em seu banco de dados de origem e clique em **Comparação de Dados** para comparar o projeto no banco de dados original para entender quais alterações foram feitas pelo assistente. Selecione a versão V12 do SQL do Azure do banco de dados e, em seguida, clique em **Concluir**.

	![texto alt](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)

	![texto alt](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)

12.	Revise as diferenças detectadas e, em seguida, clique em **Atualizar Destino** para migrar dados do banco de dados de origem para o banco de dados V12 do SQL do Azure.

	![texto alt](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)

14.	Implante o esquema de banco de dados compatível com o V12 do SQL do Azure e os dados para o Banco de Dados SQL do Azure usando SSMS. Consulte [Migrar um banco de dados compatível usando SSMS.](sql-database-migrate-ssms.md)

<!---HONumber=Oct15_HO3-->