<properties
   pageTitle="Migrar: Utilitário de Migração do Data Warehouse | Microsoft Azure"
   description="Migrar para o SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="lodipalm"/>


#Utilitário de Migração do Data Warehouse (visualização)
O Utilitário de Migração do Data Warehouse é uma ferramenta criada para migrar o esquema e os dados do SQL Server e do Banco de Dados SQL do Azure para o SQL Data Warehouse do Azure. Durante a migração do esquema, a ferramenta mapeia automaticamente o esquema correspondente da origem para o destino. Depois de migrar o esquema, os usuários também verão a opção de mover dados por meio de scripts gerados automaticamente.

Além da migração de esquema e de dados, essa ferramenta fornece aos usuários a opção de gerar relatórios de compatibilidade que resumem as incompatibilidades entre as instâncias de origem e de destino que impediriam uma migração simplificada.

##Introdução
O Utilitário de Migração do Data Warehouse pode ser baixado [aqui][]. Como um pré-requisito para a instalação, você precisará do utilitário de linha de comando BCP para executar scripts de migração e do Office para exibir o relatório de compatibilidade. Depois de iniciar o arquivo executável baixado, você deverá aceitar o Contrato de Licença de Usuário Final padrão antes de instalar a ferramenta.

###Iniciando a ferramenta e conectando-se
A ferramenta pode ser iniciada de maneira fácil clicando no ícone da área de trabalho que aparecerá após a instalação. Ao abrir a ferramenta, você verá uma página de conexão inicial, na qual você poderá escolher a origem e o destino da ferramenta de migração. No momento, há suporte para o SQL Server e o Banco de Dados SQL do Azure como origens e para o SQL Data Warehouse como um destino. Depois de selecionar essa opção, você será solicitado a se conectar ao seu servidor de origem, preenchendo o nome do servidor, autenticando e clicando em “Conectar”.
 
Após a autenticação, a ferramenta mostrará uma lista de bancos de dados que estão presentes no servidor ao qual você se conectou. Você pode começar a migração selecionando um banco de dados que deseja migrar e depois clicando em “Migrar selecionado”.
 
##Relatório de migração
Selecionar a opção “Verificar a compatibilidade do banco de dados” na ferramenta gerará um relatório que resume todas as incompatibilidades nos objetos do banco de dados que você pretende migrar. Uma lista mais abrangente de algumas das funcionalidades do SQL Server que não estão presentes no SQL Data Warehouse pode ser encontrada em nosso [documentação de migração][]. Depois de gerar o relatório, você poderá salvar e abri-lo no Excel.

Vale lembrar que, ao gerar o esquema de migração, a maioria dos problemas identificados como “Objeto” será ajustada para permitir a migração imediata desses dados. Reveja as alterações para garantir que você não deseje fazer ajustes adicionais antes de aplicar o esquema.

##Migrar o esquema

Depois de se conectar, a seleção de “Migrar esquema” gerará um script de migração de esquema para as tabelas selecionadas. Esse script compatibiliza a estrutura da tabela, mapeia os tipos de dados incompatíveis para formulários mais compatíveis e cria o esquema e as credenciais de segurança, caso isso seja indicado pelo usuário nas configurações de migração. Este código pode ser executado na instância do SQL Data Warehouse de destino, salvo em um arquivo, copiado para a área de transferência ou até mesmo editado em linha antes de realizar outras ações.
 
Como mencionamos acima, ao fazer a migração, o esquema analisa as alterações de migração feitas pela ferramenta para garantir um entendimento completo sobre elas.

##Migrar dados

Ao clicar na opção “Migrar dados”, é possível gerar scripts BCP que moverão os dados primeiro para arquivos simples no servidor e depois diretamente para o SQL Data Warehouse. Recomendamos realizar esse processo para mover pequenas quantidades de dados e, como não temos repetições internas, lembre-se de que podem ocorrer desde falhas até interrupções de rede. Para executá-lo, você precisará ter o utilitário de linha de comando BCP instalado e o esquema para os dados já deve ter sido criado.
 
Depois de preencher os parâmetros acima, basta clicar em “Executar migração” para que um conjunto de dois pacotes seja gerado no local especificado. Execute o arquivo de exportação para exportar os dados da origem de migração para arquivos simples, e execute o arquivo de importação para importar os dados para o SQL Data Warehouse.

## Próximas etapas
Agora que você já migrou alguns dados, confira como [desenvolvê-los][].

<!--Image references-->

<!--Article references-->
[documentação de migração]: https://azure.microsoft.com/PT-BR/documentation/articles/sql-data-warehouse-overview-migrate/
[desenvolvê-los]: https://azure.microsoft.com/PT-BR/documentation/articles/sql-data-warehouse-overview-develop/
[aqui]: https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip

<!---HONumber=Sept15_HO4-->