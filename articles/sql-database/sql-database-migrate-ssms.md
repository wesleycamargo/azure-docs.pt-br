<properties
   pageTitle="Migrando para o Banco de Dados SQL usando SSMS"
   description="Banco de Dados SQL do Microsoft Azure, migrar banco de dados sql, migrar usando ssms"
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

#Migrando um banco de dados compatível usando SSMS

![Diagrama de migração do SSMS](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

Se um esquema de banco de dados é compatível com o Banco de dados SQL do Azure, a migração exige apenas que o banco de dados seja importado para o Azure. Isso pode ser feito em uma única etapa com o SSMS, implantando o banco de dados no Banco de Dados SQL do Azure, ou como um processo de duas etapas, primeiro exportando um BACPAC do banco de dados e, depois, importando esse BACPAC para um Azure SQL Server como um novo banco de dados.

Quando você exportar um BACPAC, pode exportá-lo em um arquivo local ou diretamente em um blob do Azure. Se você exportá-lo localmente, pode carregar o BACPAC exportado para um blob do Azure. Depois que o arquivo BACPAC for armazenado em um blob do Azure, você pode importar o BACPAC como um banco de dados usando o portal do Azure. A execução da importação no portal do Azure reduzirá a latência na etapa de importação, o que aprimorará o desempenho e a confiabilidade da migração com bancos de dados de grande porte.

Implantar diretamente do SSMS sempre implantará o esquema inteiro e todos os dados, enquanto a exportação usando um BACPAC permite que você selecione o subconjunto de objetos para exportar para o BACPAC. O BACPAC exportado sempre inclui o esquema de banco de dados completo e, por padrão, os dados de todas as tabelas. Se você implantar do SSMS ou exportar e importar do SSMS (ou do portal do Azure), a mesma tecnologia DAC será usada de forma oculta e o resultado será o mesmo.

Essa opção também é usada como a etapa final na opção nº 2 para migrar os bancos de dados depois que eles forem atualizados para torná-los compatíveis com o Banco de Dados SQL do Azure.

## Obtenha a versão mais recente do SQL Server Management Studio

Use a versão mais recente do Microsoft SQL Server Management Studio para SQL Server para garantir que você tenha as atualizações mais recentes para as ferramentas do SSMS e para interagir com o portal do Azure. Para obter a versão mais recente do Microsoft SQL Server Management Studio para SQL Server, [baixe-a](https://msdn.microsoft.com/library/mt238290.aspx) e instale-a em um computador cliente com conectividade com o banco de dados que você planeja migrar e com a Internet.

##Usando o SSMS para implantar no Banco de Dados SQL do Azure
1.	Provisione um servidor lógico usando o Portal de Gerenciamento do Azure.
2.	Localize o banco de dados de origem no Pesquisador de Objetos do SSMS e execute a tarefa **Exportar Aplicativo da Camada de Dados…**

	![Implantar para o Azure por meio do menu de Tarefas](./media/sql-database-migrate-ssms/02MigrateusingSSMS.png)

3.	No assistente de implantação, configure a conexão com o servidor lógico do Banco de Dados SQL do Azure que você forneceu na etapa 1.
4.	Forneça o **nome** do banco de dados e defina a **Edição** (camada de serviço) e o **Objetivo do Serviço** (nível de desempenho). Consulte [Camadas de serviço do Banco de Dados SQL do Azure](sql-database-service-tiers.md) para obter mais informações sobre como definir essas configurações.

	![Exportar configurações](./media/sql-database-migrate-ssms/03MigrateusingSSMS.png)

5.	Siga o assistente para migrar o banco de dados. Dependendo do tamanho e da complexidade do banco de dados, a implantação talvez demore de alguns minutos até algumas horas. Quando há incompatibilidades, as rotinas de validação de esquema detectarão rapidamente erros antes que qualquer implantação do Azure realmente ocorra. Se os erros indicarem que o esquema de banco de dados é incompatível com o Banco de Dados SQL, use a opção de migração nº 2. Consulte [Atualizar o banco de dados no local e implantar o Banco de Dados SQL do Azure.](sql-database-migrate-visualstudio-ssdt.md)

##Usar o SSMS para exportar um BACPAC e, em seguida, importá-lo no Banco de Dados SQL
O processo de implantação pode ser dividido em duas etapas: exportação e importação. Na primeira etapa, um arquivo BACPAC é criado e, em seguida, usado como informação na segunda etapa.

1.	Provisione um servidor lógico usando o Portal de Gerenciamento do Azure.
2.	Localize o banco de dados de origem no Pesquisador de Objetos do SSMS e selecione a tarefa **Implantar o Banco de dados no Banco de Dados SQL do Azure...**

	![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-migrate-ssms/04MigrateusingSSMS.png)

3. No assistente de exportação, configure a exportação para salvar o arquivo BACPAC em um disco local ou em um blob do Azure. O BACPAC exportado sempre inclui o esquema de banco de dados completo e, por padrão, os dados de todas as tabelas. Se você quiser excluir dados de algumas ou de todas as tabelas, use a guia Avançado. Por exemplo, você pode optar por exportar somente os dados de tabelas de referência.

	![Exportar configurações](./media/sql-database-migrate-ssms/05MigrateusingSSMS.png)

4.	Quando o BACPAC tiver sido criado, conecte-se ao servidor criado na etapa 1, clique com o botão direito do mouse na pasta **Bancos de Dados** e clique em **Importar Aplicativo da Camada de Dados...**
	
	>[AZURE.NOTE]Observação: também é possível importar o arquivo BACPAC armazenado em um blob do Azure de dentro do portal de gerenciamento do Azure.

	![Importar o item de menu do aplicativo da camada de dados](./media/sql-database-migrate-ssms/06MigrateusingSSMS.png)

5.	No assistente de importação, selecione o arquivo BACPAC recém-exportado para criar o novo Banco de Dados SQL do Azure.

	![Configurações de importação](./media/sql-database-migrate-ssms/07MigrateusingSSMS.png)

6.	Forneça o nome do banco de dados e defina a Edição (camada de serviço) e o Objetivo do Serviço (nível de desempenho).

7.	Conclua o assistente para importar o arquivo BACPAC e criar o banco de dados no Banco de Dados SQL do Azure.

	![Configurações de banco de dados](./media/sql-database-migrate-ssms/08MigrateusingSSMS.png)

##Alternativas
Também é possível usar o utilitário de linha de comando sqlpackage.exe para implantar o banco de dados ou exportar e importar um BACPAC. Sqlpackage.exe usa a mesma tecnologia DAC que o SSMS. Portanto, o resultado é o mesmo. Para obter mais informações, consulte [SqlPackage.exe no MSDN](https://msdn.microsoft.com/library/hh550080.aspx).

<!---HONumber=Oct15_HO3-->