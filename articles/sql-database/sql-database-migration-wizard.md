<properties 
   pageTitle="Usar o SQL Azure Migration Wizard | Microsoft" 
   description="Banco de Dados SQL do Microsoft Azure, migração de banco de dados, importar banco de dados, exportar banco de dados, assistente de migração" 
   services="sql-database" 
   documentationCenter="" 
   authors="pehteh" 
   manager="jeffreyg" 
   editor="monicar"/>


<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="07/01/2015"
   ms.author="pehteh"/>


# Como usar o SQL Azure Migration Wizard


![texto alt](./media/sql-database-migration-wizard/01SAMWDiagram.png)


Essa Opção usa o SQL Azure Migration Wizard para gerar um script T-SQL de um banco de dados de origem que é transformado pelo assistente a fim de torná-lo compatível com o Banco de Dados SQL e conectá-lo ao Banco de Dados SQL do Azure para execução do script em um banco de dados SQL vazio do Azure. O script pode ser gerado apenas com esquemas ou pode incluir dados no formato BCP. O assistente também permite incluir ou excluir objetos específicos do banco de dados no script.


Observe que nem todos os esquemas incompatíveis que podem ser detectados pelo assistente podem ser processados por suas transformações internas. O script incompatível que não puder ser incluído será apontado como erro, com comentários injetados no script gerado. Se isso ocorrer, o script deverá ser salvo e editado manualmente para que possa ser enviado ao Banco de Dados SQL. Se forem necessárias alterações, o script poderá ser salvo e editado com o SSMS ou a ferramenta SQL Server no VS e, uma vez compatível, executado fora da banda no Banco de Dados SQL.


> **Observação**: como uma extensão dessa Opção, se muitos erros forem detectados e corrigi-los não for uma tarefa simples, você poderá importar o arquivo de script gerado em um projeto de banco de dados no Visual Studio. Se você definir o destino do projeto para o Banco de Dados SQL V12, será possível compilar o projeto e corrigir os erros progressivamente usando a ferramenta SQL Server no VS. Quando o esquema estiver sem erros, você poderá publicá-lo em uma cópia do banco de dados de origem e usar o SSMS para implantar ou exportar/importar o banco de dados no banco de dados SQL do Azure, conforme descrito na Opção 1.


## Baixar o SQLAzureMW.exe


Você pode baixar o SQL Azure Migration Wizard do CodePlex:


[Baixar o SQLAzureMW.exe](http://sqlazuremw.codeplex.com/)


## Etapas da migração


&nbsp;1. Provisione um novo banco de dados em um novo servidor no Banco de Dados SQL ou em um servidor existente atualizado, conforme descrito anteriormente. Você executará o script de migração criado nesta Opção nesse banco de dados como a etapa final.


&nbsp;2. Abra o assistente de migração e selecione a opção para **Analisar/Migrar um Banco de Dados** e defina o **Servidor de Destino** como Banco de Dados SQL do Azure V12 e clique em **Avançar**.


![texto alt](./media/sql-database-migration-wizard/02MigrationWizard.png)


&nbsp;3. Na próxima página, clique em **Conectar ao Servidor** e forneça as informações de conexão do servidor de origem. Especifique o banco de dados de origem na caixa de diálogo de conexão ou conecte-se ao servidor e selecione o banco de dados de origem na lista de bancos de dados.


![texto alt](./media/sql-database-migration-wizard/03MigrationWizard.png)


&nbsp;4. Na próxima página, Escolher Objetos, você pode opta por Gerar script de todos os objetos do banco de dados (o padrão) ou Selecionar objetos específicos do banco de dados a serem gerados com script. Talvez você ache melhor começar gerando em script todos os objetos e, posteriormente, voltar nessa etapa e excluir os objetos se o assistente apontar erros de script ou transformação. Primeiro, o assistente gera os objetos com script no banco de dados (usando o SMO) e, em seguida, pós-processa o script gerado para aplicar uma série de regras de validação e transformação com base no regex.


![texto alt](./media/sql-database-migration-wizard/04MigrationWizard.png)


&nbsp;5. Selecione Avançado e analise as opções de configuração usadas pelo assistente. Em primeiro lugar, especialmente no caso de um banco de dados grande, você deve definir **Gerar Script de Tabela/Dados** como Apenas Esquema de Tabela, garantir que **Servidor de Destino** seja definido como Banco de Dados SQL do Microsoft Azure V12 e que **Verificações de Compatibilidade** seja definida como Substituir: Executar Todas as Verificações de Compatibilidade.


![texto alt](./media/sql-database-migration-wizard/05MigrationWizard.png)


&nbsp;6. Clique em **Avançar** para analisar as opções e em **Avançar** novamente para gerar e transformar o script. É possível analisar o script na guia Script SQL.


&nbsp;7. A geração de script apontará erros se o esquema for incompatível com o Banco de Dados SQL e não puder ser transformado pelo assistente. No instantâneo abaixo, o processo de transformação encontrou problemas com um procedimento armazenado. Nesse caso, um procedimento foi escrito para usar pesquisa de texto completo, o que não tem suporte no V12 (mas que terá suporte em uma versão posterior).


![texto alt](./media/sql-database-migration-wizard/06MigrationWizard.png)


- Dependendo de sua avaliação dos erros, você poderá optar por voltar no assistente e excluir os objetos que estão causando problemas e gerar novamente o script. Ao considerar como planeja abordar os erros, considere o impacto em outros objetos no banco de dados, bem como nos aplicativos que usam o banco de dados.
- Se o script apresenta erros que precisam ser corrigidos, você pode salvar um arquivo de script somente de esquema na guia Script SQL e abrir e editar o script em seu editor favorito para corrigir os erros antes de executá-lo fora do SAMW no novo banco de dados criado na etapa 1. Se o script for grande ou houver muitos erros, você deverá usar a Opção 3. Observe que, embora seja possível importar o arquivo de script gerado no Visual Studio, a importação de um arquivo SQL pode demorar bem mais do que a importação de um banco de dados, conforme descrito na Opção 3. 


&nbsp;8. Se não houver erros ou você tiver eliminado a fonte de erros resultante da geração, você poderá clicar em **Avançar** e, na página seguinte, clicar em **Conectar ao Servidor** para se conectar ao servidor do Banco de Dados SQL do Azure criado na etapa 1.


![texto alt](./media/sql-database-migration-wizard/07MigrationWizard.png)


&nbsp;9. A próxima etapa é selecionar o banco de dados no qual o script será executado. Todos os bancos de dados existentes no servidor serão listados. Selecione o banco de dados que criou na etapa 1. O banco de dados deve estar vazio e ter uma camada de preços apropriada para esta operação. Se você preferir, poderá criar um banco de dados neste momento usando o assistente. Para isso, clique em Criar Banco de Dados para configurá-lo e criá-lo. Consulte a seção Introdução para ver diretrizes sobre como escolher o nível de desempenho a ser usado durante o processo de migração. Depois de ter criado o banco de dados, selecione-o e prossiga.


&nbsp;10. Com um banco de dados vazio selecionado, clique em **Avançar** e confirme que deseja executar o script para concluir a migração.

 

<!---HONumber=July15_HO4-->