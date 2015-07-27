<properties 
	pageTitle="Visão geral sobre trabalhos de bancos de dados elásticos" 
	description="Ilustra o serviço do trabalho de banco de dados elástico" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/25/2015" 
	ms.author="sidneyh" />

# Visão geral de trabalhos de bancos de dados elásticos

**Trabalhos de banco de dados elásticos** (visualização) permitem que você execute scripts T-SQL (trabalhos) em todos os bancos de dados em um [pool de bancos de dados elásticos (visualização)](sql-database-elastic-pool.md). Por exemplo, você pode atualizar com facilidade o esquema em cada banco de dados a fim de incluir uma nova tabela. Normalmente, você deve se conectar a cada banco de dados de forma independente a fim de executar instruções T-SQL ou realizar outras tarefas administrativas. Um **trabalho de banco de dados elástico** lida com a tarefa de logon e execução confiável do script, enquanto registra o status da execução de cada banco de dados. Para obter instruções sobre como instalar a visualização, vá para [Instalando os componentes do Trabalho de Banco de Dados Elástico](sql-database-elastic-jobs-service-installation.md).

![Serviço do trabalho de banco de dados elástico][1]

## Benefícios

* Definir, manter e persistir em scripts T-SQL que serão executados em um pool de banco de dados elástico
* Executar scripts T-SQL de forma confiável com repetição automática e em grande escala
* Controlar o estado de execução do trabalho

## Cenários

* Executar tarefas administrativas, como a implantação de um novo esquema
* Atualizar dados de referência, por exemplo, informações sobre o produto comuns a todos os bancos de dados
* Recriar índices para melhorar o desempenho da consulta

## Como funciona o trabalho

1.	Instale os serviços usados pelos trabalhos de banco de dados elástico. Consulte [Instalando trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-service-installation.md). Se a instalação falhar, confira [como desinstalar](sql-database-elastic-jobs-uninstall.md).
2.	Configure o pool de bancos de dados elásticos para execução do trabalho [adicionando um usuário a cada banco de dados](sql-database-elastic-jobs-add-logins-to-dbs.md).
3.	Criar um script T-SQL itempotent que pode ser executado em cada banco de dados no pool.
4.	Siga estas etapas para executar o script: [Criando e gerenciando trabalhos de banco de dados elástico](sql-database-elastic-jobs-create-and-manage.md) 

## Componentes e preços 

Os seguintes componentes trabalham juntos para criar um Serviço de Nuvem do Azure que permite a execução ad hoc de trabalhos administrativos. Os componentes são instalados e configurados automaticamente durante a configuração, em sua assinatura. Você pode identificar os serviços, uma vez que todos têm o mesmo nome gerado automaticamente. O nome é exclusivo e é formado pelo prefixo "edj" seguido por 21 caracteres gerados aleatoriamente.

* **Serviço de Nuvem do Azure**: os trabalhos de banco de dados elástico (visualização) são fornecidos como um Serviço de Nuvem do Azure hospedado pelo cliente para a execução das tarefas solicitadas. No portal, o serviço é implantado e hospedado em sua assinatura do Microsoft Azure. O serviço implantado por padrão é executado com o mínimo de duas funções de trabalho a fim de manter a alta disponibilidade. O tamanho padrão de cada função de trabalho (ElasticDatabaseJobWorker) é executado em uma instância de A0. Para obter os preços, confira [preços dos serviços de Nuvem](http://azure.microsoft.com/pricing/details/cloud-services/). 
* **Banco de dados SQL do Azure**: O serviço usa um Banco de Dados SQL do Azure conhecido como o **banco de dados de controle** para armazenar todos os metadados de trabalho. A camada de serviço padrão é S0. Para obter os preços, confira [Preços de Banco de Dados SQL](http://azure.microsoft.com/pricing/details/sql-database/).
* **Barramento de Serviço do Azure**: um Barramento de Serviço do Azure serve para coordenação do trabalho no Serviço de Nuvem do Azure. Consulte [Preços de Barramento de Serviço](http://azure.microsoft.com/pricing/details/service-bus/).
* **Armazenamento do Azure**: uma conta de Armazenamento do Azure é usada para armazenar o log de saída do diagnóstico caso um problema exija uma depuração extra (uma prática comum para [Diagnóstico do Azure](../cloud-services-dotnet-diagnostics.md)). Para obter os preços, confira [Preços de Armazenamento do Azure](http://azure.microsoft.com/pricing/details/storage/).

## Próximas etapas
[Instalar os componentes](sql-database-elastic-jobs-service-installation.md) e, em seguida, [criar e adicionar um log a cada banco de dados no pool](sql-database-elastic-jobs-add-logins-to-dbs.md). Para entender mais a criação de trabalho e o gerenciamento, consulte [criar e gerenciar trabalhos do banco de dados elástico](sql-database-elastic-jobs-create-and-manage.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=July15_HO2-->