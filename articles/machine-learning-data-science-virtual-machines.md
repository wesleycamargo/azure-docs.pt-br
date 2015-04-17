<properties 
	pageTitle="Configurar uma máquina virtual ou uma máquina virtual do SQL Server no Azure para ciência de dados" 
	description="Configurar uma Máquina Virtual de ciência de dados" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mohabib;xibingao;bradsev" />

# Configurar uma máquina virtual ou uma máquina virtual SQL Server no Azure para ciência de dados

Vários tipos de máquinas virtuais do Azure podem ser provisionados e configurados para serem usados como parte de um ambiente de ciência de dados baseado em nuvem.  A decisão sobre qual tipo de máquina virtual usar depende do tipo e da quantidade de dados que serão modelados com o aprendizado de máquina e do destino de tais dados na nuvem.  Para obter orientação sobre as perguntas a serem consideradas ao tomar essa decisão, consulte [Planejar seu ambiente de ciência de dados do aprendizado de máquina do Azure](machine-learning-data-science-plan-your-environment.md).  Os dois cenários abordados aqui incluem as autoridades de certificação

São fornecidas instruções que descrevem como configurar uma VM do Azure e uma VM do Azure com o serviço do SQL como servidores IPython Notebook.  A máquina virtual é executada no Windows e é configurada com ferramentas como o Azure Storage Explorer e o AzCopy, bem como outros pacotes que são úteis para projetos de ciência de dados.  O Azure Storage Explorer e o AzCopy, por exemplo, fornecem maneiras convenientes para carregar dados no armazenamento do Azure em seu computador local ou baixá-lo em seu computador local por meio do armazenamento.  São fornecidos dois conjuntos de instruções:

* [Configurar uma máquina virtual do Azure para ciência de dados](machine-learning-data-science-setup-virtual-machine.md) mostra como provisionar uma máquina virtual do Azure com o IPython Notebook e outras ferramentas usadas para fazer a ciência de dados para casos em que uma forma de armazenamento do Azure que não seja SQL pode ser usada para armazenar os dados. 

* [Configurar uma máquina virtual do SQL Server do Azure para ciência de dados](machine-learning-data-science-setup-sql-server-virtual-machine.md) mostra como provisionar uma máquina virtual do SQL Server do Azure com o IPython Notebook e outras ferramentas usadas para fazer a ciência de dados para casos em que um banco de dados SQL armazenará dados.

Uma vez provisionadas e configuradas, essas máquinas virtuais estão prontas para uso como servidores IPython Notebook para a exploração e o processamento de dados e outras tarefas em conjunto com o aprendizado de máquina do Azure e o processo de ciência de dados de nuvem.  Esse processo pode incluir etapas que movem dados no HDInsight, o processam e exemplificam na preparação do aprendizado com os dados para o aprendizado de máquina do Azure.

* Para obter informações sobre como mover dados para HDInsight por meio do armazenamento de blob do Azure, consulte [Criar e carregar dados em tabelas de Hive do armazenamento de blob do Azure](machine-learning-data-science-hive-tables.md).
* Para obter informações sobre o processamento de dados no HDInsight com consultas de Hive, consulte [Enviar consultas Hive para os clusters do HDInsight Hadoop no processo de ciência de dados de nuvem](machine-learning-data-science-hive-queries.md).
* Para obter informações sobre os dados de amostragem no HDInsight, consulte [Dados de amostra nas tabelas do Azure HDInsight Hive](machine-learning-data-science-sample-data-hive.md).


> [AZURE.NOTE] As máquinas virtuais do Azure são cobradas como **pague somente pelo que usa**.  Para garantir que você não esteja sendo cobrado quando não estiver usando sua máquina virtual, ela deverá estar no estado **parado (desalocado)** do [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/).  Para obter instruções passo a passo ou como desalocar a sua máquina virtual, consulte [Desligar e desalocar máquina virtual quando não estiver em uso](machine-learning-data-science-setup-virtual-machine.md#shutdown) 




<!--HONumber=49-->