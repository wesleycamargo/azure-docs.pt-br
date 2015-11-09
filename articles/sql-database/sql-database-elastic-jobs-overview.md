<properties 
	title="Elastic database jobs overview" 
	pageTitle="Visão geral sobre trabalhos de bancos de dados elásticos" 
	description="Ilustra o serviço do trabalho de banco de dados elástico" 
	metaKeywords="azure sql database elastic databases" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="ddove; sidneyh" />

# Visão geral de trabalhos de bancos de dados elásticos

O recurso de **trabalhos de Banco de Dados Elástico** (visualização) permite que você, de modo confiável, execute um script T-SQL (Transact-SQL) ou aplique um DACPAC em um grupo de bancos de dados, incluindo uma coleção de bancos de dados definida de modo personalizado, todos os bancos de dados em um [Pool de Banco de Dados Elástico (visualização)](sql-database-elastic-pool.md) ou um conjunto de fragmentos (criado usando a [biblioteca de cliente de Banco de Dados Elástico](sql-database-elastic-database-client-library.md)). Na visualização, **trabalhos de banco de dados elástico** é atualmente um serviço de nuvem do Azure hospedado pelo cliente, que permite a execução de tarefas administrativas ad hoc e agendadas, as quais são chamadas de trabalhos. Usando este recurso, você pode gerenciar de modo fácil e confiável um Banco de Dados SQL do Azure em escala em um grupo inteiro de bancos de dados, executando scripts Transact-SQL para realizar operações administrativas como alterações de esquema, gerenciamento de credenciais, atualizações de dados de referência, coleta de dados de desempenho ou coleta de telemetria do locatário (cliente). Normalmente, você deve se conectar a cada banco de dados de forma independente a fim de executar instruções Transact-SQL ou realizar outras tarefas administrativas. Os **trabalhos de banco de dados elástico** lidam com a tarefa de logon e executam o script de modo confiável, enquanto registram o status de execução de cada banco de dados. Para obter instruções sobre a instalação, vá para [Instalando os componentes do trabalho de banco de dados elástico](sql-database-elastic-jobs-service-installation.md).

![Serviço do trabalho de banco de dados elástico][1]

## Benefícios
* Definir grupos personalizados de Bancos de Dados SQL do Azure
* Definir, manter e persistir em scripts T-SQL que serão executados em um grupo de Bancos de Dados SQL do Azure 
* Implantar um aplicativo da camada de dados (DACPAC)
* Executar scripts Transact-SQL ou o aplicativo de DACPACs de modo confiável com repetição automática e em grande escala
* Controlar o estado de execução do trabalho
* Definir agendas de execução
* Executar a coleta de dados em um conjunto de Bancos de Dados SQL do Azure salvando os resultados em uma única tabela de destino

> [AZURE.NOTE]A funcionalidade **trabalhos de banco de dados elástico** no Portal do Azure revela um conjunto reduzido de recursos, limitado também aos pools Elásticos do SQL Azure. Use as APIs do PowerShell para acessar o conjunto completo de funcionalidades atuais.

## Cenários

* Executar tarefas administrativas, como a implantação de um novo esquema
* Atualizar dados de referência, como informações sobre o produto comuns a todos os bancos de dados e até mesmo usar agendas para automatizar as atualizações de todos os dias úteis após o expediente.
* Recompilar índices para melhorar o desempenho da consulta. A recriação pode ser configurada para executar em um conjunto de bancos de dados de modo recorrente, como fora dos horários de pico.
* Coletar resultados de consulta de um conjunto de bancos de dados em uma tabela central em uma base contínua. Consultas de desempenho podem ser executadas continuamente e configuradas para disparar tarefas adicionais a serem executadas.
* Executar consultas de processamento de dados mais longas em um grande conjunto de bancos de dados, por exemplo, a coleta de telemetria do cliente. Resultados são coletados em uma única tabela de destino para análise posterior.

## Análise simples de ponta a ponta de trabalhos do banco de dados elástico
1.	Instalar os componentes de **trabalhos de banco de dados elástico**. Para saber mais informações, consulte [Instalando trabalhos de banco de dados elástico](sql-database-elastic-jobs-service-installation.md). Se a instalação falhar, confira [como desinstalar](sql-database-elastic-jobs-uninstall.md).
2.	Use as APIs do PowerShell para acessar mais funcionalidades, por exemplo, criando coleções de bancos de dados personalizadas, adicionando agendas e/ou coletando conjuntos de resultados. Usar o Portal para instalação simples e criação/monitoramento de trabalhos limitado para execução com um **Pool de banco de dados elástico**. 
3.	Criar credenciais criptografadas para execução do trabalho e [adicionar o usuário (ou função) para cada banco de dados no grupo](sql-database-elastic-jobs-add-logins-to-dbs.md).
4.	Criar um script T-SQL idempotente que pode ser executado em cada banco de dados no grupo.
5.	Siga estas etapas para executar o script: [Criando e gerenciando trabalhos de banco de dados elástico](sql-database-elastic-jobs-create-and-manage.md) 

## Componentes e preços 
Os seguintes componentes trabalham juntos para criar um Serviço de Nuvem do Azure que permite a execução ad hoc de trabalhos administrativos. Os componentes são instalados e configurados automaticamente durante a configuração, em sua assinatura. Você pode identificar os serviços, uma vez que todos têm o mesmo nome gerado automaticamente. O nome é exclusivo e é formado pelo prefixo "edj" seguido por 21 caracteres gerados aleatoriamente.

* **Serviço de Nuvem do Azure**: os trabalhos de banco de dados elástico (visualização) são fornecidos como um Serviço de Nuvem do Azure hospedado pelo cliente para a execução das tarefas solicitadas. No portal, o serviço é implantado e hospedado em sua assinatura do Microsoft Azure. O serviço implantado por padrão é executado com o mínimo de duas funções de trabalho a fim de manter a alta disponibilidade. O tamanho padrão de cada função de trabalho (ElasticDatabaseJobWorker) é executado em uma instância de A0. Para obter os preços, confira [preços dos serviços de Nuvem](http://azure.microsoft.com/pricing/details/cloud-services/). 
* **Banco de dados SQL do Azure**: O serviço usa um Banco de Dados SQL do Azure conhecido como o **banco de dados de controle** para armazenar todos os metadados de trabalho. A camada de serviço padrão é S0. Para obter os preços, confira [Preços de Banco de Dados SQL](http://azure.microsoft.com/pricing/details/sql-database/).
* **Barramento de Serviço do Azure**: um Barramento de Serviço do Azure serve para coordenação do trabalho no Serviço de Nuvem do Azure. Consulte [Preços de Barramento de Serviço](http://azure.microsoft.com/pricing/details/service-bus/).
* **Armazenamento do Azure**: uma conta de Armazenamento do Azure é usada para armazenar o log de saída do diagnóstico caso um problema exija uma depuração extra (uma prática comum para [Diagnóstico do Azure](../cloud-services-dotnet-diagnostics.md)). Para obter os preços, confira [Preços de Armazenamento do Azure](http://azure.microsoft.com/pricing/details/storage/).

## Como os trabalhos de banco de dados elástico funcionam
1.	Um banco de dados de controle, que armazena todos os dados de estado e metadados, é atribuído a um banco de dados SQL do Azure.
2.	O banco de dados de controle for acessado por **trabalhos de banco de dados elástico** para iniciar e acompanhar os trabalhos a executar.
3.	Duas funções diferentes se comunicam com o banco de dados de controle: 
	* Controlador: determina quais trabalhos necessitam de tarefas para executar o trabalho solicitado e faz novas tentativas com aqueles trabalhos com falha, criando novas tarefas de trabalho.
	* Execução de tarefas de trabalho: executa as tarefas de trabalho.

### Tipos de tarefa de trabalho
Há vários tipos de tarefas de trabalho que realizarão a execução de trabalhos:

* ShardMapRefresh: consulta o mapa do fragmentos para determinar todos os bancos de dados usados como fragmentos
* ScriptSplit: divide o script contido em instruções “GO” em lotes
* ExpandJob: cria trabalhos filho para cada banco de dados de um trabalho que se destina a um grupo de bancos de dados
* ScriptExecution: executa um script em um banco de dados específico usando credenciais definidas
* Dacpac: aplica um DACPAC a um banco de dados específico usando credenciais específicas

## Fluxo de trabalho de execução de trabalho de ponta a ponta
1.	Usando o Portal ou a API do PowerShell, um trabalho é inserido no **banco de dados de controle**. O trabalho solicita a execução de um script Transact-SQL em um grupo de bancos de dados usando credenciais específicas.
2.	O controlador identifica o novo trabalho. Tarefas de trabalho são criadas e executadas para dividir o script e para atualizar os bancos de dados do grupo. Por fim, um novo trabalho é criado e executado para expandir o trabalho e criar novos trabalhos filho; cada um desses trabalhos filho é especificado para executar o script Transact-SQL em um banco de dados individual no grupo.
3.	O controlador identifica os trabalhos filho criados. Para cada trabalho, o controlador cria e dispara uma tarefa de trabalho para executar o script em um banco de dados. 
4.	Depois de concluir todas as tarefas de trabalho, o controlador atualiza os trabalhos para um estado concluído. A qualquer momento durante a execução do trabalho, a API do PowerShell pode ser usada para exibir o estado atual dessa execução. Todos os tempos retornados pelas APIs do PowerShell são representados em formato UTC. Se desejado, uma solicitação de cancelamento pode ser iniciada para interromper um trabalho. 

## Próximas etapas
[Instalar os componentes](sql-database-elastic-jobs-service-installation.md) e, em seguida, [criar e adicionar um log a cada banco de dados no grupo de bancos de dados](sql-database-elastic-jobs-add-logins-to-dbs.md). Para entender mais a criação de trabalho e o gerenciamento, consulte [criar e gerenciar trabalhos do banco de dados elástico](sql-database-elastic-jobs-create-and-manage.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=Nov15_HO1-->