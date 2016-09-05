<properties
	pageTitle="Todos os tópicos sobre o serviço do Banco de Dados SQL | Microsoft Azure"
	description="Uma tabela de todos os tópicos sobre o serviço do Azure chamada Banco de Dados SQL existente em http://azure.microsoft.com/documentation/articles/, Título e descrição."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="genemi"/>


# Todos os tópicos sobre o serviço do Banco de Dados SQL do Azure

Este tópico lista todos os tópicos que se aplicam diretamente ao serviço do **Banco de Dados SQL** do Azure. Você pode pesquisar palavras-chave nesta página da Web usando **Ctrl + F** para encontrar os tópicos de seu interesse atual.




## Novo

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 1 | [Getting started with JSON features in Azure SQL Database](sql-database-json-features.md) (Introdução aos recursos do JSON no Banco de Dados SQL do Azure) | O Banco de Dados SQL do Azure permite que você analise, consulte e formate dados na notação JSON (JavaScript Object Notation). |
| 2 | [Suporte do SSMS para MFA do Azure AD com o Banco de Dados SQL e o SQL Data Warehouse](sql-database-ssms-mfa-authentication.md) | Use o Multi-Factor Authentication com SSMS para Banco de Dados SQL e SQL Data Warehouse. |


## Artigos atualizados

Esta seção lista os artigos que foram atualizados recentemente nos quais a atualização foi grande ou significativa. Para cada artigo atualizado, um trecho aproximado do texto markdown adicionado é exibido. Os artigos foram atualizados dentro do intervalo de datas de **26/07/2016** a **21/08/2016**.

| &nbsp; | Artigo | Texto atualizado, trecho |
| --: | :-- | :-- |
| 3 | [Arquivar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o PowerShell](sql-database-export-powershell.md) | $subscriptionId = "YOUR AZURE SUBSCRIPTION ID" Login-AzureRmAccount Set-AzureRmContext -SubscriptionId $subscriptionId Database to export $DatabaseName = "DATABASE-NAME" $ResourceGroupName = "RESOURCE-GROUP-NAME" $ServerName = "SERVER-NAME" $serverAdmin = "ADMIN-NAME" $serverPassword = "ADMIN-PASSWORD" $securePassword = ConvertTo-SecureString ΓÇôString $serverPassword ΓÇôAsPlainText -Force $creds = New-Object ΓÇôTypeName System.Management.Automation.PSCredential ΓÇôArgumentList $serverAdmin, $securePassword Generate a unique filename for the BACPAC $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac" Storage account info for the BACPAC $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/" $BacpacUri = $BaseStorageUri + $bacpacFilename $StorageKeytype = "StorageAccessKey" $StorageKey = "YOUR STORAGE KEY" $exportRequest = New-AzureRmSqlDatabaseExpo |
| 4 | [Opções e desempenho de Banco de Dados SQL: compreender o que está disponível em cada camada de serviço](sql-database-service-tiers.md) | ** Como escolher uma camada de serviço** Para decidir sobre uma camada de serviço, comece determinando se o banco de dados deverá ser um banco de dados autônomo ou se fará parte de um pool elástico. ** Como escolher uma camada de serviço para um banco de dados autônomo** Para decidir sobre uma camada de serviço para um banco de dados autônomo, comece determinando os recursos do banco de dados necessários para escolher sua edição do Banco de Dados SQL: / Tamanho do banco de dados (no máximo 5 GB para o Básico, 250 GB para o Standard e 500 GB a 1 TB para Premium, dependendo do nível de desempenho) / Período de retenção de backup do banco de dados (sete dias para Básico, 35 dias para Standard e 35 dias para Premium) Depois de determinar a edição do Banco de Dados SQL, você estará pronto para determinar o nível de desempenho do banco de dados (o número de DTUs). Você pode fazer uma suposição e depois escalar ou reduzir verticalmente ou horizontalmente de forma dinâmica com base na experiência real. Você também pode usar a Calculadora de DTU(http://dtucalculator.azurewebsites.net/) para ter uma ideia aproximada do número de DTUs necessários. ** C |





## Introdução

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 5 | [Compilar Aplicativos de Multilocatário com o Banco de Dados SQL com Isolamento e Eficiência](sql-database-build-multi-tenant-apps.md) | Saiba como o Banco de Dados SQL compila aplicativos multilocatário |
| 6 | [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md) | Saiba como se conectar a um banco de dados SQL no Azure usando o SSMS (SQL Server Management Studio). Em seguida, execute uma consulta de exemplo usando o Transact-SQL (T-SQL). |
| 7 | [Conectar-se ao Banco de Dados SQL usando .NET (C#)](sql-database-develop-dotnet-simple.md) | Use o código de exemplo neste início rápido para criar um aplicativo moderno com C# e sustentado por um poderoso banco de dados relacional na nuvem com o Banco de Dados SQL do Azure. |
| 8 | [Criar um Pool de Banco de Dados Elástico com o portal do Azure](sql-database-elastic-pool-create-portal.md) | Como adicionar um pool de banco de dados elástico escalonável à sua configuração do banco de dados SQL para facilitar a administração e o compartilhamento de recursos entre vários bancos de dados. |
| 9 | [Explorar Tutoriais do Banco de Dados SQL do Azure](sql-database-explore-tutorials.md) | Saiba mais sobre recursos e funcionalidades do Banco de Dados SQL |
| 10 | [Tutorial do Banco de Dados SQL: criar um banco de dados SQL em alguns minutos usando o Portal do Azure](sql-database-get-started.md) | Saiba como configurar um servidor lógico do Banco de Dados SQL, uma regra de firewall de servidor, um banco de dados SQL, dados de exemplo, conectar-se com ferramentas de cliente, configurar usuários e regras de firewall de banco de dados. |
| 11 | [Seguranças e Proteções do Banco de Dados SQL do Azure](sql-database-helps-secures-and-protects.md) | Saiba como o Banco de Dados SQL ajuda a defender e proteger |
| 12 | [O Banco de Dados SQL do Azure aprende e se adapta](sql-database-learn-and-adapt.md) | Saiba como o Banco de Dados SQL aprende e se adapta |
| 13 | [Escolher uma opção do SQL Server de nuvem: Banco de Dados do SQL Azure (PaaS) ou SQL Server em VMs do Azure (IaaS)](sql-database-paas-vs-sql-server-iaas.md) | Saiba qual opção do SQL Server da nuvem se adapta ao seu aplicativo: Banco de Dados SQL do Azure (PaaS) ou SQL Server na nuvem em Máquinas Virtuais do Azure. |
| 14 | [Escalas do Banco de Dados SQL do Azure em tempo real](sql-database-scale-on-the-fly.md) | Saiba como o Banco de Dados SQL é dimensionado em tempo real |
| 15 | [Explorar os Guias de Início Rápido da solução de Banco de Dados SQL do Azure](sql-database-solution-quick-starts.md) | Saiba mais sobre as Soluções de Banco de Dados SQL do Azure |
| 16 | [O Banco de Dados SQL do Azure funciona em seu ambiente](sql-database-works-in-your-environment.md) | Saiba como Banco de Dados SQL ajuda, defende e protege |



## Compilar um aplicativo

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 17 | [Solucionar problemas, diagnosticar e evitar erros de conexão SQL e erros transitórios para o Banco de Dados SQL](sql-database-connectivity-issues.md) | Saiba como solucionar problemas, diagnosticar e evitar um erro de conexão do SQL ou um erro transitório no Banco de Dados SQL. |
| 18 | [Conectar-se ao Banco de Dados SQL com o Visual Studio](sql-database-connect-query.md) | Escreva um programa em C# para consultar um Banco de Dados SQL e se conectar a ele Informações sobre endereços IP, cadeias de conexão, logon seguro e Visual Studio gratuito. |
| 19 | [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md) | Às vezes, as conexões de cliente do ADO.NET para o Banco de Dados SQL do Azure V12 ignoram o proxy e interagem diretamente com o banco de dados. Outras portas diferentes da 1433 se tornam importantes. |
| 20 | [Códigos de erro de SQL para aplicativos cliente do Banco de Dados SQL: erro de conexão de banco de dados e outros problemas](sql-database-develop-error-messages.md) | Saiba mais sobre os códigos de erro de SQL para aplicativos cliente do Banco de Dados SQL, como erros comuns de conexão de banco de dados, problemas de cópia de banco de dados e erros gerais. |
| 21 | [Conectar-se ao Banco de Dados SQL usando PHP no Windows](sql-database-develop-php-simple.md) | Apresenta um programa PHP de exemplo que se conecta ao Banco de Dados SQL a partir de um cliente do Windows e fornece links para os componentes de software necessários e exigidos pelo cliente. |
| 22 | [Experimentar o Banco de Dados SQL: Usar o C# para criar um Banco de Dados SQL com a Biblioteca do Banco de Dados SQL para .NET](sql-database-get-started-csharp.md) | Experimente o banco de dados SQL para o desenvolvimento de aplicativos SQL e C# e crie um banco de dados SQL do Azure com o C# usando a Biblioteca do Banco de Dados SQL para .NET. |
| 23 | [Solucionar problemas de conexão no Banco de Dados SQL do Azure](sql-database-troubleshoot-common-connection-issues.md) | Etapas para identificar e resolver erros comuns de conexão do Banco de Dados SQL do Azure. |
| 24 | [Erro "O banco de dados no servidor não está disponível atualmente" ao se conectar a um banco de dados sql](sql-database-troubleshoot-connection.md) | Erro "A solução de problemas de banco de dados no servidor não está disponível no momento" quando um aplicativo se conecta ao Banco de Dados SQL. |



## Desenvolver

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 25 | [Obter a ID do cliente e a chave para se conectar ao Banco de Dados SQL do código](sql-database-client-id-keys.md) | Obtenha a ID do cliente e a chave para acessar o Banco de Dados SQL do código. |
| 26 | [Conectar-se ao Banco de Dados SQL usando Java com JDBC no Windows](sql-database-develop-java-simple.md) | Apresenta um exemplo de código Java que pode ser usado para se conectar ao Banco de Dados SQL do Azure. O exemplo usa o JDBC, e é executado em um computador cliente com Windows. |
| 27 | [Conectar-se ao Banco de Dados SQL usando Node.js](sql-database-develop-nodejs-simple.md) | Apresenta um exemplo de código Node.js que pode ser usado para se conectar ao Banco de Dados SQL do Azure. |
| 28 | [Visão geral do desenvolvimento de Banco de Dados SQL](sql-database-develop-overview.md) | Saiba mais sobre bibliotecas de conectividade disponíveis e práticas recomendadas para aplicativos que se conectam ao Banco de Dados SQL. |
| 29 | [Conectar-se ao Banco de Dados SQL usando Phyton](sql-database-develop-python-simple.md) | Apresenta um exemplo de código Phyton que pode ser usado para se conectar ao Banco de Dados SQL do Azure. |
| 30 | [Conectar-se ao Banco de Dados SQL usando o Ruby](sql-database-develop-ruby-simple.md) | Dê um exemplo de código Ruby que você pode executar para se conectar ao Banco de Dados SQL do Azure. |
| 31 | [Introdução às Tabelas Temporais no Banco de Dados SQL do Azure](sql-database-temporal-tables.md) | Saiba como começar a usar as Tabelas Temporais no Banco de Dados SQL do Azure. |



## Desempenho e escala

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 32 | [Advisor do Banco de Dados SQL](sql-database-advisor.md) | O Advisor do Banco de Dados SQL do Azure fornece recomendações para seus Bancos de Dados SQL existentes que podem melhorar o desempenho de consulta atual. |
| 33 | [Advisor do Banco de Dados SQL](sql-database-advisor-portal.md) | Você pode usar o Advisor do Banco de Dados SQL do Azure no Portal do Azure para analisar e implementar recomendações para seus Bancos de Dados SQL existentes que podem melhorar o desempenho de consulta atual. |
| 34 | [Visão geral do parâmetro de comparação do Banco de Dados SQL do Azure](sql-database-benchmark-overview.md) | Este tópico descreve o Parâmetro de Comparação do Banco de Dados SQL do Azure usado para medir o desempenho do Banco de Dados SQL do Azure. |
| 35 | [Quando um Pool de Banco de Dados Elástico deve ser usado?](sql-database-elastic-pool-guidance.md) | Um pool de bancos de dados elástico é um conjunto de recursos disponíveis compartilhados por um grupo de bancos de dados elásticos. Este documento fornece diretrizes para ajudar a avaliar a adequação do uso de um pool de banco de dados elástico para um grupo de bancos de dados. |
| 36 | [Introdução às ferramentas do Banco de Dados Elástico](sql-database-elastic-scale-get-started.md) | Explicação básica do recurso das ferramentas de banco de dados elástico do Banco de Dados SQL do Azure, incluindo um aplicativo de amostra fácil de executar. |
| 37 | [Perguntas frequentes sobre o Banco de Dados SQL](sql-database-faq.md) | Respostas a perguntas comuns feitas por clientes sobre bancos de dados de nuvem e o Banco de Dados SQL do Azure, o sistema de gerenciamento de bancos de dados relacionais da Microsoft (RDBMS) e o banco de dados como um serviço na nuvem. |
| 38 | [Introdução a Na Memória (visualização) no Banco de Dados SQL](sql-database-in-memory.md) | As tecnologias Na Memória do SQL melhoram muito o desempenho de cargas de trabalho transacionais e analíticas. Saiba como aproveitar as vantagens dessas tecnologias. |
| 11,8 | [Usar In-Memory OLTP (visualização) para melhorar o desempenho do aplicativo no Banco de Dados SQL](sql-database-in-memory-oltp-migration.md) | Adote o OLTP Na Memória para melhorar o desempenho transacional em um banco de dados SQL existente. |
| 40 | [Monitorar o armazenamento OLTP na memória](sql-database-in-memory-oltp-monitoring.md) | Estimar e monitorar o uso do armazenamento na memória XTP, capacidade; resolver o erro de capacidade 41823 |
| 41 | [Operando o Repositório de Consultas no Banco de Dados SQL do Azure](sql-database-operate-query-store.md) | Saiba como operar o Repositório de Consultas no Banco de Dados SQL do Azure |
| 42 | [Análise de Desempenho de Banco de Dados SQL](sql-database-performance.md) | O Banco de Dados SQL do Azure fornece ferramentas de desempenho para ajudá-lo a identificar áreas em que é possível melhorar o desempenho de consulta atual. |
| 43 | [Diretrizes de desempenho do Banco de Dados SQL do Azure para bancos de dados únicos](sql-database-performance-guidance.md) | Este tópico oferece diretrizes para ajudar você a determinar a camada de serviço adequada para seu aplicativo e oferece recomendações para ajustar seu aplicativo para obter o máximo proveito de seu Banco de Dados SQL do Azure. |
| 44 | [Visão do desempenho de consulta de Banco de Dados SQL do Azure](sql-database-query-performance.md) | O monitoramento do desempenho de consulta identifica as consultas que consumem mais CPU de um Banco de Dados SQL do Azure. |
| 45 | [Alterar a camada de serviços e o nível de desempenho (tipo de preço) de um banco de dados SQL](sql-database-scale-up.md) | Alterar a camada de serviço e o nível de desempenho de um banco de dados SQL do Azure mostra como escalar verticalmente seu banco de dados SQL. Alterando o tipo de preço de um banco de dados SQL do Azure. |
| 46 | [Monitorar o desempenho do banco de dados no Banco de Dados SQL do Azure](sql-database-single-database-monitor.md) | Saiba mais sobre as opções de monitoramento de seu banco de dados com as ferramentas do Azure e exibições de gerenciamento dinâmico. |
| 47 | [Dicas de ajuste de desempenho do Banco de Dados SQL](sql-database-troubleshoot-performance.md) | Dicas de ajuste de desempenho no Banco de Dados SQL por meio de avaliação e melhoria. |
| 48 | [Como usar o envio em lote para melhorar o desempenho do aplicativo Banco de Dados SQL](sql-database-use-batching-to-improve-performance.md) | O tópico fornece provas de que as operações de banco de dados em lote melhoram consideravelmente a velocidade e a escalabilidade dos aplicativos do Banco de Dados SQL do Azure. Embora essas técnicas de envio em lote funcionem para qualquer banco de dados do SQL Server, o foco do artigo é no Azure. |



## Ferramentas para escalar horizontalmente

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 49 | [Padrões de design para aplicativos SaaS multilocatários e o Banco de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md) | Este artigo aborda os requisitos e padrões de arquitetura de dados comuns que aplicativos de banco de dados multilocatários em execução em um ambiente de nuvem precisam considerar e as várias compensações associadas a esses padrões. Ele também explica como o Banco de Dados SQL do Azure com seus pools de banco de dados elástico e ferramentas elásticas ajudam a atender a esses requisitos sem comprometimento. |
| 50 | [Migrar bancos de dados existentes para escala horizontal](sql-database-elastic-convert-to-use-elastic-tools.md) | Converter bancos de dados fragmentados para usar ferramentas de banco de dados elástico criando um gerenciador de mapa de fragmentos |
| 51 | [Criando bancos de dados de nuvem escalonáveis](sql-database-elastic-database-client-library.md) | Crie aplicativos de banco de dados .NET escalonáveis com a biblioteca de cliente do banco de dados elástico |
| 52 | [Contadores de desempenho do gerenciador de mapa de fragmentos](sql-database-elastic-database-perf-counters.md) | Classe do ShardMapManager e contadores de desempenho de roteamento dependente de dados |
| 53 | [Adicionando um fragmento usando ferramentas do Banco de Dados Elástico](sql-database-elastic-scale-add-a-shard.md) | Como usar APIs de Escala Elástica para adicionar novos fragmentos para um fragmento de conjunto. |
| 54 | [Implantar um serviço de divisão e mesclagem](sql-database-elastic-scale-configure-deploy-split-and-merge.md) | Divisão e mesclagem com ferramenta de banco de dados elástico |
| 55 | [Roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) | Como usar a classe ShardMapManager em aplicativos .NET para o roteamento dependente de dados, um recurso dos bancos de dados elásticos para o Banco de Dados SQL do Azure |
| 56 | [Pergunta Frequentes das ferramentas de banco de dados elástico](sql-database-elastic-scale-faq.md) | Perguntas frequentes sobre a Escala Elástico do banco de dados SQL do Azure. |
| 57 | [Glossário de ferramentas do banco de dados elástico](sql-database-elastic-scale-glossary.md) | Explicação dos termos usados para as ferramentas de banco de dados elástico |
| 58 | [Escalando horizontalmente com o Banco de Dados SQL do Azure](sql-database-elastic-scale-introduction.md) | Os desenvolvedores de SaaS (Software como um serviço) podem criar com facilidade bancos de dados elásticos e escalonáveis na nuvem usando estas ferramentas |
| 59 | [Credenciais usadas para acessar a biblioteca de cliente do Banco de Dados Elástico](sql-database-elastic-scale-manage-credentials.md) | Como definir o nível certo de credenciais de administrador para somente leitura em aplicativos de banco de dados elástico. |
| 60 | [Consulta de vários fragmentos](sql-database-elastic-scale-multishard-querying.md) | Execute consultas entre fragmentos usando a biblioteca de cliente de banco de dados elástico. |
| 61 | [Mover dados entre bancos de dados na nuvem escalados horizontalmente](sql-database-elastic-scale-overview-split-and-merge.md) | Explica como manipular fragmentos e mover os dados por meio de um serviço auto-hospedado usando APIs de banco de dados elástico. |
| 62 | [Escale horizontalmente os bancos de dados com o gerenciador do mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md) | Como usar o ShardMapManager, a biblioteca de cliente do banco de dados elástico |
| 63 | [Configuração de segurança da divisão e mesclagem](sql-database-elastic-scale-split-merge-security-configuration.md) | Configurar certificados x409 para criptografia |
| 64 | [Atualizar um aplicativo para usar a biblioteca de cliente de banco de dados elástico mais recente](sql-database-elastic-scale-upgrade-client-library.md) | Atualizar aplicativos e bibliotecas usando o Nuget |
| 65 | [Biblioteca cliente do Banco de Dados Elástico com Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) | Usar a biblioteca de cliente do Banco de Dados Elástico e o Entity Framework para bancos de dados de codificação |
| 66 | [Usando a biblioteca de cliente do banco de dados elástico com Dapper](sql-database-elastic-scale-working-with-dapper.md) | Usar a biblioteca de cliente de banco de dados elástico com Dapper. |
| 67 | [Aplicativos multilocatários com ferramentas de banco de dados elástico e segurança em nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md) | Saiba como usar as ferramentas de banco de dados elástico com segurança em nível de linha para criar um aplicativo com uma camada de dados altamente dimensionável no Banco de Dados SQL que dá suporte a fragmentos multilocatários. |



## Trabalhos elásticos

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 68 | [Criar e gerenciar Bancos de Dados SQL do Azure com escala horizontal usando trabalhos elásticos (preview)](sql-database-elastic-jobs-create-and-manage.md) | Explore a criação e o gerenciamento de um trabalho de banco de dados elástico. |
| 69 | [Introdução a trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-getting-started.md) | como usar trabalhos de banco de dados elástico |
| 70 | [Gerenciando bancos de dados de nuvem com escalonamento horizontal](sql-database-elastic-jobs-overview.md) | Ilustra o serviço do trabalho de banco de dados elástico |
| 71 | [Criar e gerenciar trabalhos de banco de dados elástico de Banco de Dados SQL usando o PowerShell (visualização)](sql-database-elastic-jobs-powershell.md) | PowerShell usado para gerenciar pools do Banco de Dados SQL do Azure |
| 72 | [Visão geral de Instalando trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-service-installation.md) | Percorra a instalação do recurso de trabalho elástico. |
| 73 | [Desinstalar componentes de trabalhos de banco de dados elástico](sql-database-elastic-jobs-uninstall.md) | Como desinstalar a ferramenta de trabalho de banco de dados elástico |



## Pools elásticos

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 74 | [O que é um Pool de Banco de Dados Elástico do Azure?](sql-database-elastic-pool.md) | Gerencie centenas ou milhares de bancos de dados usando um pool. Um preço para um conjunto de unidades de desempenho pode ser distribuído pelo pool. Mova bancos de dados para dentro ou para fora à vontade. |
| 75 | [Criar um pool de banco de dados elástico com C#](sql-database-elastic-pool-create-csharp.md) | Use as técnicas de desenvolvimento de bancos de dados C# para criar um pool de banco de dados elástico e escalonável no Banco de Dados SQL do Azure para que você possa compartilhar recursos entre vários bancos de dados. |
| 76 | [Criar um Pool de Banco de Dados Elástico com o PowerShell](sql-database-elastic-pool-create-powershell.md) | Saiba como usar o PowerShell para expandir recursos do Banco de Dados SQL do Azure criando um pool de banco de dados elástico escalonável para gerenciar vários bancos de dados. |
| 77 | [Desenvolvimento de banco de dados em C#: criar e configurar um pool de banco de dados elástico para o banco de dados SQL](sql-database-elastic-pool-csharp.md) | Use técnicas de desenvolvimento de banco de dados em C# para criar um Pool de Banco de Dados Elástico para o Banco de Dados SQL, de modo que você possa compartilhar recursos entre vários bancos de dados. |
| 78 | [Script do PowerShell para identificar os bancos de dados adequados para um pool de banco de dados elástico](sql-database-elastic-pool-database-assessment-powershell.md) | Um pool de bancos de dados elástico é um conjunto de recursos disponíveis compartilhados por um grupo de bancos de dados elásticos. Este documento fornece um script do PowerShell para ajudar a avaliar a adequação do uso de um pool de banco de dados elástico para um grupo de bancos de dados. |
| 79 | [Monitorar e gerenciar um pool de banco de dados elástico com C#](sql-database-elastic-pool-manage-csharp.md) | Use técnicas de desenvolvimento de banco de dados C# para gerenciar um pool de banco de dados elástico do Banco de Dados SQL do Azure. |
| 80 | [Monitorar e gerenciar um pool de banco de dados elástico com o Portal do Azure](sql-database-elastic-pool-manage-portal.md) | Saiba como usar o Portal do Azure e a inteligência interna do Banco de Dados SQL para gerenciar, monitorar e dimensionar corretamente um pool de banco de dados elástico escalonável para otimizar o desempenho do banco de dados e gerenciar o custo. |
| 81 | [Monitorar e gerenciar um pool de banco de dados elástico com o PowerShell](sql-database-elastic-pool-manage-powershell.md) | Saiba como usar o PowerShell para gerenciar um pool de banco de dados elástico. |
| 82 | [Monitorar e gerenciar um pool de banco de dados elástico com Transact-SQL](sql-database-elastic-pool-manage-tsql.md) | Use o T-SQL para criar um banco de dados Azure SQL em um pool elástico. Ou use o T-SQL para mover o banco de dados para dentro ou fora dos pools. |
| 83 | [Informações sobre preços e cobrança do Pool de Banco de Dados Elástico](sql-database-elastic-pool-price.md) | Informações sobre preços específicas para Pools de Banco de Dados Elástico. |



## Consulta elástica

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 84 | [Relatórios entre bancos de dados expandidos na nuvem (preview)](sql-database-elastic-query-getting-started.md) | como usar consultas de banco de dados entre bancos de dados |
| 85 | [Introdução às consultas entre bancos de dados (particionamento vertical) (preview)](sql-database-elastic-query-getting-started-vertical.md) | como usar a consulta de banco de dados elástico com bancos de dados particionados verticalmente |
| 86 | [Relatórios entre bancos de dados em nuvem expandidos (visualização)](sql-database-elastic-query-horizontal-partitioning.md) | como configurar consultas elásticas em partições horizontais |
| 87 | [Visão geral da consulta de banco de dados elástico do Banco de Dados SQL do Azure (visualização)](sql-database-elastic-query-overview.md) | Visão geral do recurso de consulta elástica |
| 88 | [Consultar entre bancos de dados na nuvem com esquemas diferentes (visualização)](sql-database-elastic-query-vertical-partitioning.md) | como configurar consultas entre bancos de dados em partições verticais |



## Transação elástica

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 89 | [Transações distribuídas entre bancos de dados na nuvem](sql-database-elastic-transactions-overview.md) | Visão Geral das Transações de Banco de Dados Elástico com o Banco de Dados SQL do Azure |



## Backup e recuperação

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 90 | [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md) | Saiba mais sobre os backups internos do Banco de Dados SQL que permitem a você reverter um Banco de Dados SQL do Azure para um momento anterior ou copiar um banco de dados para um novo banco de dados em uma região geográfica (por até 35 dias). |
| 91 | [Visão geral da continuidade dos negócios com o Banco de Dados SQL do Azure](sql-database-business-continuity.md) | Saiba como o Banco de Dados SQL do Azure dá suporte para a continuidade dos negócios em nuvem e para a recuperação de banco de dados, além de ajudar a manter os aplicativos em nuvem críticos em execução. |
| 92 | [Como restaurar uma única tabela de um backup do Banco de Dados SQL do Azure](sql-database-cloud-migrate-restore-single-table-azure-backup.md) | Saiba como restaurar uma única tabela do backup do Banco de Dados SQL do Azure. |
| 93 | [Criar um aplicativo para recuperação de desastre na nuvem usando a Replicação geográfica ativa no Banco de Dados SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) | Aprenda a criar soluções de recuperação de desastre em nuvem para planejamento de continuidade de negócios usando a replicação geográfica para backup de dados de aplicativo com o banco de dados SQL. |
| 94 | [Restaurar um Banco de Dados SQL ou fazer failover para um secundário](sql-database-disaster-recovery.md) | Saiba como recuperar um banco de dados de uma interrupção do datacenter regional ou de uma falha com os recursos de Restauração geográfica e Replicação geográfica Ativa do Banco de Dados SQL do Azure. |
| 95 | [Executar análise de recuperação de desastres](sql-database-disaster-recovery-drills.md) | Obtenha orientação e as práticas recomendadas para usar o Banco de Dados SQL do Azure para executar os análises de recuperação de desastres que ajudarão a manter seus aplicativos comerciais importantes resilientes a falhas e interrupções. |
| 96 | [Estratégias de recuperação de desastres para aplicativos que usam o Pool Elástico do Banco de Dados SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) | Aprenda a projetar sua solução de nuvem para recuperação de desastres escolhendo o padrão de failover correto. |
| 97 | [Usando a classe RecoveryManager para corrigir problemas do mapa de fragmentos](sql-database-elastic-database-recovery-manager.md) | Use a classe RecoveryManager para resolver problemas com mapas de fragmentos |
| 98 | [Importar um arquivo BACPAC para criar um novo banco de dados SQL do Azure](sql-database-import.md) | Crie um novo banco de dados SQL do Azure importando um arquivo BACPAC existente. |
| 99 | [Restaurar um Banco de Dados SQL do Azure para um determinado momento anterior com o Portal do Azure](sql-database-point-in-time-restore-portal.md) | Restaure um Banco de Dados SQL do Azure para um determinado momento anterior. |
| 100 | [Restaurar um Banco de Dados SQL do Azure para um determinado momento anterior com o PowerShell](sql-database-point-in-time-restore-powershell.md) | Restaurar um Banco de Dados SQL do Azure para um determinado momento anterior |
| 101 | [Finalizar seu Banco de Dados SQL do Azure recuperado](sql-database-recovered-finalize.md) | Restauração pontual, Banco de Dados SQL do Microsoft Azure, banco de dados de restauração, banco de dados de recuperação, Portal Clássico do Azure, portal Clássico do Azure |
| 102 | [Recuperar um banco de dados SQL do Azure usando backups de banco de dados automatizados](sql-database-recovery-using-backups.md) | Saiba mais sobre a Restauração Pontual, que permite que você reverta um Banco de Dados SQL do Azure para um ponto anterior no tempo (até 35 dias). |
| 103 | [Restaurar um Banco de Dados SQL do Azure excluído usando o Portal do Azure](sql-database-restore-deleted-database-portal.md) | Restaure um Banco de Dados SQL do Azure (Portal do Azure). |
| 104 | [Restaurar um Banco de Dados SQL do Azure usando o PowerShell](sql-database-restore-deleted-database-powershell.md) | Restaure um Banco de Dados SQL do Azure (PowerShell). |
| 105 | [Restaurar um banco de dados para um ponto anterior no tempo, restaurar um banco de dados excluído ou recuperar-se de uma interrupção do datacenter](sql-database-troubleshoot-backup-and-restore.md) | Saiba como recuperar um banco de dados de nuvem que enfrentou erros e falhas usando réplicas e backups no Banco de Dados SQL. |



## Migrar

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 106 | [Exportar um banco de dados do SQL Server para um arquivo BACPAC usando SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Banco de Dados SQL do Microsoft Azure, migração de banco de dados, exportar banco de dados, exportar arquivo BACPAC, sqlpackage |
| 107 | [Exportar um banco de dados do SQL Server para um arquivo BACPAC usando o SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Banco de Dados SQL do Microsoft Azure, migração do banco de dados, exportar banco de dados, exportar arquivo BACPAC, Assistente para Exportação de Aplicativo de Camada de Dados |
| 108 | [Importação de um arquivo BACPAC para o Banco de Dados SQL usando o SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md) | Banco de Dados SQL do Microsoft Azure, migração de banco de dados, importar banco de dados, importar arquivo BACPAC, sqlpackage |
| 109 | [Importar do BACPAC para o Banco de Dados SQL usando SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Banco de Dados SQL do Microsoft Azure, implantação de banco de dados, migração de banco de dados, banco de dados de importação, exportação de banco de dados, assistente de migração |
| 110 | [Migrar o banco de dados do SQL Server para o Banco de Dados SQL usando o Assistente para implantação de Banco de Dados no Banco de Dados do Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) | Banco de Dados SQL do Microsoft Azure, migração de banco de dados, Assistente de Banco de Dados do Microsoft Azure |
| 111 | [Migrar o banco de dados do SQL Server para o Banco de Dados SQL do Azure usando a replicação transacional](sql-database-cloud-migrate-compatible-using-transactional-replication.md) | Banco de Dados SQL do Microsoft Azure, migração de banco de dados, importar banco de dados, replicação transacional |
| 112 | [Determinar a compatibilidade com o Banco de Dados SQL usando SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) | Banco de Dados SQL do Microsoft Azure, migração de banco de dados, compatibilidade com o Banco de Dados SQL, SqlPackage |
| 113 | [Use o SQL Server Management Studio para Determinar a compatibilidade do Banco de Dados SQL antes da migração para o Banco de Dados SQL do Azure](sql-database-cloud-migrate-determine-compatibility-ssms.md) | Banco de Dados SQL do Microsoft Azure, migração do banco de dados, compatibilidade com o Banco de Dados SQL, Assistente para Exportação de Aplicativo de Camada de Dados |
| 114 | [Use o SQL Azure Migration Wizard para corrigir problemas de compatibilidade de banco de dados SQL Server antes da migração para o Banco de dados SQL do Azure](sql-database-cloud-migrate-fix-compatibility-issues.md) | Banco de Dados SQL do Microsoft Azure, migração de banco de dados, compatibilidade, Assistente de Migração do SQL Azure |
| 115 | [Migrar um Banco de Dados SQL Server para o Banco de Dados SQL do Azure usando as SQL Server Data Tools para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) | Banco de Dados SQL do Microsoft Azure, migração de banco de dados, compatibilidade, Assistente de Migração do SQL Azure, SSDT |
| 116 | [Corrigir problemas de compatibilidade do banco de dados SQL Server usando o SQL Server Management Studio antes da migração para o Banco de Dados SQL](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) | Banco de Dados SQL do Microsoft Azure, migração de banco de dados, compatibilidade, Assistente de Migração do SQL Azure |
| 117 | [Arquivar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o PowerShell](sql-database-export-powershell.md) | Arquivar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o PowerShell |
| 118 | [Importar um arquivo BACPAC para criar um novo banco de dados SQL do Azure usando o PowerShell](sql-database-import-powershell.md) | Importar um arquivo BACPAC para criar um novo banco de dados SQL do Azure usando o PowerShell |
| 119 | [Carregar dados do CSV no Azure SQL Data Warehouse (arquivos simples)](sql-database-load-from-csv-with-bcp.md) | Para um tamanho de dados pequeno, use bcp para importar dados para o Banco de Dados SQL. |
| 120 | [Mover bancos de dados entre servidores, entre as assinaturas e para dentro e para fora do Azure](sql-database-troubleshoot-moving-data.md) | Passos rápidos para copiar, mover e migrar dados e bancos de dados no Banco de Dados SQL. |



## Mover dados para dentro e para fora

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 121 | [Migração de banco de dados do SQL Server para o Banco de Dados SQL na nuvem](sql-database-cloud-migrate.md) | Saiba mais sobre a migração de banco de dados do SQL Server local para o Banco de Dados SQL do Azure na nuvem. Use ferramentas de migração do banco de dados para testar a compatibilidade antes da migração do banco de dados. |
| 122 | [Copiar um Banco de Dados SQL do Azure](sql-database-copy.md) | Criar uma cópia de um Banco de Dados SQL do Azure |
| 123 | [Arquivar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o portal do Azure](sql-database-export.md) | Arquivar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o portal do Azure |



## Segurança

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 124 | [Conexão ao Banco de Dados SQL ou ao SQL Data Warehouse usando a autenticação do Azure Active Directory](sql-database-aad-authentication.md) | Saiba como se conectar ao Banco de Dados SQL Usando a Autenticação do Active Directory do Azure. |
| 125 | [Always Encrypted: proteger dados confidenciais no Banco de Dados SQL e armazenar suas chaves de criptografia no repositório de certificados do Windows](sql-database-always-encrypted.md) | Proteja dados confidenciais no banco de dados SQL em minutos. |
| 126 | [Always Encrypted: proteger dados confidenciais no Banco de Dados SQL e armazenar suas chaves de criptografia no Cofre de Chaves do Azure](sql-database-always-encrypted-azure-key-vault.md) | Proteja dados confidenciais no banco de dados SQL em minutos. |
| 127 | [Banco de Dados SQL - Suporte a clientes de versão anterior e alterações ponto de extremidade IP para Auditoria](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) | Saiba mais sobre o suporte a clientes de versão anterior do Banco de Dados SQL e alterações ponto de extremidade IP para Auditoria. |
| 128 | [Configurar regras de firewall no nível de servidor do Banco de Dados SQL do Azure usando o PowerShell](sql-database-configure-firewall-settings-powershell.md) | Saiba como configurar o firewall para endereços IP que acessam bancos de dados SQL do Azure. |
| 129 | [Configurar regras de firewall no nível de servidor do Banco de Dados SQL do Azure usando a API REST](sql-database-configure-firewall-settings-rest.md) | Saiba como configurar o firewall para endereços IP que acessam bancos de dados SQL do Azure. |
| 130 | [Como configurar um firewall do Banco de Dados SQL do Azure usando TSQL](sql-database-configure-firewall-settings-tsql.md) | Saiba como configurar o firewall para endereços IP que acessam bancos de dados SQL do Azure. |
| 131 | [Introdução ao Mascaramento de dados dinâmicos no banco de dados SQL (Portal do Azure)](sql-database-dynamic-data-masking-get-started.md) | Como começar a usar a Máscara de Dados Dinâmicos do Banco de Dados SQL no Portal do Azure |
| 132 | [Introdução à Máscara de Dados Dinâmicos do Banco de Dados SQL (Portal clássico do Azure)](sql-database-dynamic-data-masking-get-started-portal.md) | Como começar a usar a Máscara de Dados Dinâmicos do Banco de Dados SQL no Portal Clássico do Azure |
| 133 | [Configurar regras de firewall do Banco de Dados SQL – visão geral](sql-database-firewall-configure.md) | Saiba como configurar um firewall de banco de dados SQL com regras de firewall no nível de servidor e banco de dados para gerenciar o acesso. |
| 134 | [Tutorial do Banco de Dados SQL: criar contas de usuário do Banco de Dados SQL para acessar e gerenciar um banco de dados usando o Portal do Azure](sql-database-get-started-security.md) | Saiba como criar contas de usuário para acessar e gerenciar um banco de dados. |
| 135 | [Autenticação e Autorização do Banco de Dados SQL: Concessão de Acesso](sql-database-manage-logins.md) | Saiba mais sobre o gerenciamento de segurança de banco de dados SQL, especialmente sobre como gerenciar o acesso ao banco de dados e a segurança de logon por meio da conta de entidade de segurança no nível do servidor. |
| 136 | [Limitações e diretrizes de segurança de Banco de Dados SQL do Azure](sql-database-security-guidelines.md) | Saiba mais sobre as Limitações e diretrizes de Banco de dados SQL do Microsoft Azure relacionadas à segurança. |
| 137 | [Introdução à Detecção de Ameaças do Banco de Dados SQL](sql-database-threat-detection-get-started.md) | Como começar com a Detecção de Ameaças do Banco de Dados SQL no Portal do Azure |
| 138 | [Como executar tarefas administrativas comuns, como a redefinição de senha de administrador no Banco de Dados SQL](sql-database-troubleshoot-permissions.md) | Descreve como executar tarefas administrativas comuns no banco de dados SQL. Por exemplo, redefinir a senha do administrador, conceder e remover acesso. |



## Gerenciar o banco de dados

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 139 | [Introdução à auditoria do banco de dados SQL](sql-database-auditing-get-started.md) | Introdução à auditoria do banco de dados SQL |
| 140 | [Configurar uma regra de firewall no nível de servidor do Banco de Dados SQL do Azure usando o Portal do Azure](sql-database-configure-firewall-settings.md) | Saiba como configurar o firewall para endereços IP que acessam o Azure SQL Server. |
| 141 | [Copiar um Banco de Dados SQL do Azure usando o Portal do Azure](sql-database-copy-portal.md) | Criar uma cópia de um Banco de Dados SQL do Azure |
| 142 | [Gerenciando bancos de dados SQL do Azure usando a Automação do Azure](sql-database-manage-automation.md) | Saiba mais sobre como o serviço de Automação do Azure pode ser usado para gerenciar bancos de dados SQL do Azure em grande escala. |
| 143 | [Visão geral: ferramentas de gerenciamento para o Banco de Dados SQL](sql-database-manage-overview.md) | Compara as ferramentas e opções de gerenciamento de Banco de Dados SQL do Azure |
| 144 | [Monitoramento de Banco de Dados SQL usando exibições de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md) | Saiba como detectar e diagnosticar problemas de desempenho comuns usando exibições de gerenciamento dinâmico para monitorar o Banco de Dados SQL do Microsoft Azure. |
| 145 | [Protegendo o Banco de Dados SQL](sql-database-security.md) | Saiba mais sobre a segurança do Banco de Dados SQL do Azure e SQL Server, incluindo as diferenças entre a nuvem e o SQL Server local quando se trata de autenticação, autorização, segurança de conexão, criptografia e conformidade. |



## Eventos estendidos

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 146 | [Código de destino do Arquivo de evento para eventos estendidos no Banco de Dados SQL](sql-database-xevent-code-event-file.md) | Fornece PowerShell e Transact-SQL para um exemplo de código de duas fases que demonstra o destino de Arquivo de evento em um evento estendido no Banco de Dados SQL do Azure. O Armazenamento do Azure é uma parte obrigatória deste cenário. |
| 147 | [Código de destino do Buffer de Anéis para eventos estendidos no Banco de Dados SQL](sql-database-xevent-code-ring-buffer.md) | Fornece um exemplo de código Transact-SQL que se torna fácil e rápido por meio do uso do destino do Buffer de Anéis, no Banco de Dados SQL do Azure. |
| 148 | [Eventos estendidos no Banco de Dados SQL](sql-database-xevent-db-diff-from-svr.md) | Descreve eventos estendidos (XEvents) no Banco de Dados SQL do Azure e como as sessões de eventos diferem ligeiramente das sessões de eventos no Microsoft SQL Server. |



## Replicação geográfica

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 149 | [Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure com o portal do Azure](sql-database-geo-replication-failover-portal.md) | Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure usando o portal do Azure |
| 150 | [Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure com o PowerShell](sql-database-geo-replication-failover-powershell.md) | Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure usando o PowerShell |
| 151 | [Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure com o Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure usando o Transact-SQL |
| 152 | [Visão geral: Replicação Geográfica Ativa para o Banco de Dados SQL](sql-database-geo-replication-overview.md) | A Replicação Geográfica Ativa permite configurar 4 réplicas de seu banco de dados em qualquer um dos datacenters do Azure. |
| 153 | [Configurar a Replicação Geográfica para o Banco de Dados SQL do Azure com o portal do Azure](sql-database-geo-replication-portal.md) | Configurar a Replicação Geográfica para o Banco de Dados SQL do Azure usando o portal do Azure |
| 154 | [Configurar a Replicação Geográfica para o Banco de Dados SQL do Azure com o PowerShell](sql-database-geo-replication-powershell.md) | Configurar a Replicação Geográfica Ativa para o Banco de Dados SQL do Azure usando o PowerShell |
| 155 | [Como gerenciar a segurança do Banco de Dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md) | Este tópico explica considerações de segurança para gerenciar a segurança após uma restauração ou failover de um banco de dados. |
| 156 | [Configurar a Replicação Geográfica para o Banco de Dados SQL do Azure com o Transact-SQL](sql-database-geo-replication-transact-sql.md) | Configurar a Replicação Geográfica para o Banco de Dados SQL do Azure usando o Transact-SQL |
| 157 | [Restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica usando o Portal do Azure](sql-database-geo-restore-portal.md) | Restaurar geograficamente um Banco de Dados SQL do Azure de um backup com redundância geográfica (Portal do Azure). |
| 158 | [Restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica usando o PowerShell](sql-database-geo-restore-powershell.md) | Restaurar um Banco de Dados SQL para um novo servidor de um backup com redundância geográfica |



## Atualizar

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 159 | [Desempenho aprimorado de consultas com nível de compatibilidade 130 no Banco de Dados SQL do Azure](sql-database-compatibility-level-query-performance-130.md) | Etapas e ferramentas para determinar qual é o melhor nível de compatibilidade para seu banco de dados no Banco de Dados SQL do Azure ou no Microsoft SQL Server |
| 160 | [Gerenciando a rolagem de atualizações de aplicativos na nuvem usando a Replicação Geográfica Ativa do Banco de Dados SQL](sql-database-manage-application-rolling-upgrade.md) | Saiba como usar a replicação geográfica do Banco de Dados SQL do Azure para dar suporte a atualizações online de seu aplicativo na nuvem. |
| 161 | [Atualização para o Banco de Dados SQL V12 do Azure usando o portal do Azure](sql-database-upgrade-server-portal.md) | Explica como atualizar para o Banco de Dados SQL V12 do Azure incluindo como atualizar bancos de dados da Web e Empresarial e como atualizar um servidor V11 migrando seus bancos de dados diretamente para um pool de banco de dados elástico usando o portal do Azure. |
| 162 | [Atualizar para o Banco de Dados SQL V12 usando o PowerShell](sql-database-upgrade-server-powershell.md) | Explica como atualizar para o Banco de Dados SQL V12 do Azure, incluindo como atualizar bancos de dados da Web e Business e como atualizar um servidor V11 migrando seus bancos de dados diretamente para um pool de banco de dados elástico usando o PowerShell. |
| 163 | [Planejar e se preparar para atualizar para o Banco de Dados SQL V12](sql-database-v12-plan-prepare-upgrade.md) | Descreve as preparações e limitações envolvidas na atualização para a versão V12 do Banco de Dados SQL do Azure. |
| 164 | [Novidades no Banco de Dados SQL V12](sql-database-v12-whats-new.md) | Descreve por que os sistemas de negócios que estão usando o Banco de Dados SQL do Azure na nuvem terão benefícios com a atualização para a versão V12 agora. |
| 165 | [Últimas perguntas frequentes de Web e Business Edition ](sql-database-web-business-sunset-faq.md) | Descubra quando os bancos SQL Web e Business do Azure e Business serão desativados e saiba mais sobre os recursos e funcionalidades das novas camadas de serviço. |



## Ferramentas

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 166 | [Gerenciar o Banco de Dados SQL do Azure com o PowerShell](sql-database-command-line-tools.md) | Gerenciamento de banco de dados SQL do Azure com o PowerShell. |
| 167 | [Tutorial do Banco de Dados SQL: Conectar o Excel a um banco de dados SQL do Azure e criar um relatório](sql-database-connect-excel.md) | Saiba como conectar o Microsoft Excel ao banco de dados SQL do Azure na nuvem. Importar dados para o Excel para exploração de dados e geração de relatórios. |
| 168 | [Criar um Banco de Dados SQL e executar tarefas comuns de instalação de banco de dados com cmdlets do PowerShell](sql-database-get-started-powershell.md) | Saiba como criar um Banco de Dados SQL com o PowerShell. As tarefas comuns de configuração de banco de dados podem ser gerenciadas por meio de cmdlets do PowerShell. |
| 169 | [Introdução à Sincronização de Dados do SQL do Azure (visualização)](sql-database-get-started-sql-data-sync.md) | Esse tutorial ajudará você a começar a Sincronização de Dados do SQL do Azure (visualização). |
| 170 | [Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio](sql-database-manage-azure-ssms.md) | Aprenda a usar o SQL Server Management Studio para gerenciar bancos de dados e servidores de banco de dados SQL. |
| 171 | [Gerenciando Bancos de Dados SQL do Azure usando o Portal do Azure](sql-database-manage-portal.md) | Saiba como usar o Portal do Azure para gerenciar um banco de dados relacional na nuvem usando o Portal do Azure. |
| 172 | [Alterar a camada de serviços e o nível de desempenho (tipo de preço) de um banco de dados SQL com o PowerShell](sql-database-scale-up-powershell.md) | Alterar a camada de serviço e o nível de desempenho de um banco de dados SQL do Azure mostra como escalar verticalmente seu banco de dados SQL com o PowerShell. Alterando o tipo de preço de um banco de dados SQL do Azure com o PowerShell. |
| 173 | [Usar o SQL Server Management Studio no Azure RemoteApp para conectar-se ao Banco de dados SQL](sql-database-ssms-remoteapp.md) | Use este tutorial para aprender a usar o SQL Server Management Studio no Azure RemoteApp para segurança e desempenho ao se conectar ao Banco de Dados SQL |



## Referência

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 174 | [Bibliotecas de conexão para Banco de Dados SQL e SQL Server](sql-database-libraries.md) | Lista o número de versão mínimo para cada driver que os programas clientes podem usar para se conectarem ao Banco de Dados SQL do Azure ou ao Microsoft SQL Server. Foi fornecido um link para obtenção de informações de versão sobre drivers lançados pela comunidade, e não pela Microsoft. |
| 175 | [Limites de recursos do Banco de Dados SQL do Azure](sql-database-resource-limits.md) | Esta página descreve alguns limites de recurso comuns para o Banco de Dados SQL do Azure. |
| 176 | [Diferenças de Transact-SQL de Banco de Dados SQL do Azure](sql-database-transact-sql-information.md) | Instruções Transact-SQL que têm suporte menor que o total pelo Banco de Dados SQL |



## Diversos

| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 177 | [Copiar um banco de dados SQL do Azure usando o PowerShell](sql-database-copy-powershell.md) | Criar cópia de um Banco de Dados SQL do Azure usando o PowerShell |
| 178 | [Copiar um banco de dados SQL do Azure usando o Transact-SQL](sql-database-copy-transact-sql.md) | Criar cópia de um Banco de Dados SQL do Azure usando o Transact-SQL |
| 179 | [Diretrizes e limitações gerais do Banco de Dados SQL do Azure](sql-database-general-limitations.md) | Esta página descreve algumas limitações gerais para o Banco de Dados SQL do Azure, bem como áreas de interoperabilidade e suporte. |
| 180 | [Recomendações de camada de preços do Banco de Dados SQL](sql-database-service-tier-advisor.md) | Ao alterar as camadas de preços no portal do Azure, são fornecidas recomendações de camada de preços que apontam a camada mais adequada para executar a carga de trabalho do Banco de Dados SQL do Azure. As camadas de preços descrevem o nível de serviço e o nível de desempenho de um banco de dados SQL. |


&nbsp;

<hr/>

<a name="extras_append"></a>

## Extras

<a name="extra_related_articles"></a>

### Artigos relacionados de outros serviços do Azure

- [Fazer backup do aplicativo de Serviços de Aplicativos do Azure no Azure](../app-service-web/web-sites-backup.md)

<a name="extra_documentation_tools"></a>

### Ferramentas de documentação

- [Atualizações anunciadas para o Banco de Dados SQL do Azure](http://azure.microsoft.com/updates/?service=sql-database)

- [Pesquisar todos os tipos de documentação do Microsoft Azure, com filtros](http://azure.microsoft.com/search/documentation/)

<a name="extra_learning_path_graphics"></a>

### Gráficos do roteiro de aprendizagem

- [Gráficos do roteiro de aprendizagem para todos os serviços do Microsoft Azure](http://azure.microsoft.com/documentation/learning-paths/)

- [Roteiro de aprendizagem: conheça o Banco de Dados SQL do Azure](http://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)

- [Roteiro de aprendizagem: escala elástica do Banco de Dados SQL](http://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)


<!--
AzIxAA.ExtraAppend.sql-database.txt
C:\_MainW\VS15\AzureIndexAllArticles\AzureIndexAllArticles\In-Out\In\

This bullet link is improperly disallowed by publishing automation due to presence of string 'docuXXmentation/arXXticles':
- [Search SQL Database documentation, with filters](http://azure.microsoft.com/docuXXmentation/arXXticles/?service=sql-database)
-->

<!---HONumber=AcomDC_0824_2016-->