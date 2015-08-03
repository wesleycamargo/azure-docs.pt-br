<properties 
   pageTitle="Migração usando o Visual Studio e SSDT" 
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
   ms.date="07/17/2015"
   ms.author="pehteh"/>

#Atualizar o banco de dados no local e implantar o Banco de Dados SQL do Azure

![texto alt](./media/sql-database-migrate-visualstudio-ssdt/01VSSSDTDiagram.png)

Use esta opção quando a migração de um banco de dados para o Banco de Dados SQL do Azure V12 requer alterações de esquema que não podem ser solucionadas usando o Assistente de Migração do SQL Azure (SAMW) porque o banco de dados usa recursos do SQL Server que não têm suporte no Banco de Dados SQL do Azure. Nessa opção, o Visual Studio é usado primeiro para criar um projeto de banco de dados do banco de dados de origem. Em seguida, a plataforma de destino do projeto é definida como o Banco de Dados SQL do Azure V12 e o projeto é criado a fim de identificar todos os problemas de compatibilidade. O SAMW pode corrigir muitos problemas de compatibilidade, mas não todos. Portanto, ele é usado para processar todos os scripts nos projetos como uma primeira passagem. O uso do SAMW é opcional, mas  
altamente recomendado. A compilação do projeto após o processamento dos arquivos de script com o SAMW identificará problemas restantes que deverão ser solucionados manualmente usando as ferramentas de edição de Transact-SQL no Visual Studio. Após a compilação do projeto, o esquema é publicado novamente em uma cópia (recomendada) do banco de dados de origem a fim de atualizar o esquema e os dados no local. Em seguida, o banco de dados atualizado é implantado no Azure, diretamente ou por exportação e importação de um arquivo BACPAC, usando as técnicas descritas na Opção nº 1.
 
Como essa opção envolve a atualização do esquema do banco de dados no local antes da implantação no Azure, é altamente recomendável que isso seja realizado em uma cópia do banco de dados. A ferramenta de Comparação de Esquema do Visual Studio pode ser usada para examinar o conjunto completo de alterações que serão aplicadas ao banco de dados antes de publicar o projeto.

O uso do SQL Azure Migration Wizard (SAMW) é opcional, mas recomendado. O SAMW detectará problemas de compatibilidade dentro do corpo de funções, procedimentos armazenados e disparadores que de outra forma não serão detectados até a implantação.

Caso seja necessária uma implantação somente de esquema, o esquema atualizado poderá ser publicado diretamente do Visual Studio para o Banco de Dados SQL do Azure.

## Etapas da migração

1.	Abra o **Pesquisador de Objetos do SQL Server** no Visual Studio. Use **Adicionar SQL Server** para se conectar à instância do SQL Server que contém o banco de dados que está sendo migrado. Localize o banco de dados no Pesquisador, clique com o botão direito nele e selecione **Criar Novo Projeto...** 

![texto alt](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)

2.	Defina as configurações de importação como **Importar somente objetos no escopo do aplicativo**. Desmarque as opções para importar configurações de banco de dados, permissões e logons referenciados.

![texto alt](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)

3.	Clique em **Iniciar** para importar o banco de dados e criar o projeto, que conterá um arquivo de script T-SQL para cada objeto no banco de dados. Os arquivos de script são aninhados em pastas dentro do projeto.

![texto alt](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)

4.	No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto do banco de dados e selecione Propriedades. Isso abrirá a página **Configurações do Projeto** na qual você deve configurar a Plataforma de Destino como o Banco de Dados SQL do Microsoft Azure V12.

![texto alt](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)

5.	Opcional: clique com botão direito do mouse no projeto e selecione **Compilar** para compilar o projeto (isso não é estritamente necessário neste ponto, mas fazer isso agora proporcionará a você uma base para o número de problemas de compatibilidade do projeto e para a eficácia das etapas a seguir.)

![texto alt](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)

6.	Opcional: clique com botão direito do mouse no projeto e selecione **Instantâneo do Projeto**. Ao obter um instantâneo no início e, possivelmente nos estágios posteriores durante a transformação, você pode usar a ferramenta de Comparação de Esquema para comparar o esquema em cada estágio.

![texto alt](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png) - A obtenção de instantâneos do projeto cria um arquivo .dacpac com carimbo de data e hora contendo o esquema completo do projeto. Você pode modificar o nome do arquivo a fim de indicar em qual estágio do processo esse instantâneo foi obtido. ![texto alt](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

7.	Processe os arquivos de script importados usando o SQL Azure Migration Wizard (SAMW). Use a opção de pasta e selecione a pasta raiz do projeto. ![texto alt](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)

8.	O assistente processará cada arquivo de script nessa pasta e em todas as subpastas. Um resumo dos resultados será exibido na próxima página. ![texto alt](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)
9.	Clique em avançar para ver uma lista resumida dos arquivos alterados. 

![texto alt](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)

> [AZURE.NOTE]Observe que foram feitas cópias temporárias dos arquivos originais antes do processamento e dos arquivos afetados após processamento nos locais indicados na parte superior da página.

10.	Clique em **Substituir ** e em **OK** na caixa de diálogo de confirmação e os arquivos originais serão substituídos pelos arquivos alterados. Apenas os arquivos realmente alterados serão substituídos.
11.	Opcional. Use a Comparação de Esquemas para comparar o projeto com um instantâneo anterior ou com o banco de dados original, a fim de entender quais alterações foram feitas pelo assistente. Convém também obter outro instantâneo agora. 

![texto alt](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)

12.	Compile o projeto novamente (confira a etapa anterior) para determinar quais erros permanecem.

13.	Percorra os erros sistematicamente para resolvê-los. Avalie o impacto de cada alteração nos aplicativos que usam o banco de dados.

14.	Quando o banco de dados estiver livre de erros, clique com o botão direito do mouse no projeto e selecione **Publicar** para compilar e publicar o banco de dados em uma cópia do banco de dados de origem (é altamente recomendável usar uma cópia, pelo menos a princípio). O objetivo desta etapa é atualizar o esquema do banco de dados de origem e fazer quaisquer alterações resultantes nos dados no banco de dados.
- Antes de publicar, dependendo da versão do SQL Server de origem, talvez seja necessário redefinir a plataforma de destino do projeto a fim de permitir a implantação. Se você estiver migrando um banco de dados SQL Server mais antigo, não será necessário introduzir recursos que não têm suporte no SQL Server de origem ao projeto, a menos que primeiro você migre o banco de dados para uma versão mais recente do SQL Server. 
- É necessário configurar a etapa de publicação a fim de habilitar as opções apropriadas de publicação. Por exemplo, se você tiver excluído ou comentado objetos sem suporte no projeto, eles deverão ser excluídos do banco de dados. Portanto, você deverá permitir que a publicação exclua dados do banco de dados de destino. 
- Se você antecipar repetidamente a publicação 

15.	Implante o banco de dados copiado no Banco de Dados SQL do Azure usando as técnicas descritas na Opção 1.

## Validando a migração

Depois de concluir a migração, convém comparar o esquema no banco de dados migrado com o esquema no banco de dados de origem para familiarizar-se com as alterações feitas, e para ter certeza de que elas foram realizadas conforme o esperado e não causarão problemas ao migrar aplicativos para o novo banco de dados. Você pode usar a ferramenta de Comparação de Esquemas incluída com as ferramentas de SQL Server no Visual Studio para executar a comparação. Você pode comparar o banco de dados no Banco de Dados SQL do Azure com o banco de dados SQL Server original ou com um instantâneo feito quando o banco de dados foi importado pela primeira vez no projeto.

1.	Conecte-se ao servidor no banco de dados SQL do Azure que contém o banco de dados migrado e localize o banco de dados. 

2.	Clique com o botão direito do mouse no banco de dados e selecione **Comparação de Esquema...**. Isso abrirá uma nova comparação de esquema com o banco de dados do Azure selecionado como a origem no lado esquerdo. Use a lista suspensa Selecionar Destino à direita para selecionar o banco de dados de destino ou o arquivo de instantâneo para a comparação.

3.	Com a origem e o destino selecionados, clique em Comparar para iniciar a comparação. O carregamento do esquema a partir de um banco de dados complexo no Banco de Dados SQL do Azure pode demorar um tempo considerável. Carregar o esquema e executar outras tarefas de metadados no banco de dados SQL do Azure levará menos tempo com uma camada de preços mais alta.

4.	Após o término da comparação, analise as diferenças. A menos que você tenha alguma preocupação, a regra é não aplicar alterações em qualquer um dos esquemas.

Na comparação de esquemas abaixo, o banco de dados Adventure Works 2014 no Banco de Dados SQL do Azure V12 à esquerda, transformado e migrado pelo SQL Azure Migration Wizard, é comparado com o banco de dados de origem no SQL Server à direita.

![texto alt](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)

<!---HONumber=July15_HO4-->