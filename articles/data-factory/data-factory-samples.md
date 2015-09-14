<properties 	
	pageTitle="Azure Data Factory - Exemplos"
	description="Fornece detalhes sobre os exemplos fornecidos com o serviço Azure Data Factory."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags 
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="spelluru"/>

# Azure Data Factory - Exemplos

## Exemplos no Portal do Azure
Você pode rapidamente implantar, analisar e testar um exemplo da Azure Data Factory usando a folha **Pipelines de exemplo** no Portal do Azure.

1. Crie uma nova data factory ou abra uma existente. Consulte [Introdução à Azure Data Factory][data-factory-get-started] para conhecer as etapas para criar uma data factory.
2. Na folha **DATA FACTORY** para a data factory em questão, clique no bloco **Pipelines de exemplo**.

	![Bloco Pipelines de exemplo](./media/data-factory-samples/SamplePipelinesTile.png)

2. Na folha **Pipelines de exemplo**, clique no **exemplo** que você deseja implantar.
	
	![Folha Pipelines de exemplo](./media/data-factory-samples/SampleTile.png)

3. Especifique definições de configuração para o exemplo. Por exemplo, sua chave e nome da conta de armazenamento do Azure, o nome do SQL Server do Azure, banco de dados, ID de usuário e senha, etc...

	![Folha Exemplo](./media/data-factory-samples/SampleBlade.png)

4. Depois de terminar a especificação de configurações, clique em **Criar** para criar/implantar os pipelines de exemplo e serviços vinculados/tabelas usados pelos pipelines.
5. Você verá o status da implantação do bloco de exemplo em que você clicou anteriormente na folha **Pipelines de exemplo**.

	![Status da implantação](./media/data-factory-samples/DeploymentStatus.png)

6. Quando você vir a mensagem **Implantação bem-sucedida** no bloco para o exemplo, feche a folha **Pipelines de exemplo**.
5. Na folha **DATA FACTORY**, você verá que os pipelines, conjuntos de dados e serviços vinculados são adicionados à sua data factory.  

	![Folha Data Factory](./media/data-factory-samples/DataFactoryBladeAfter.png)
   

A tabela a seguir fornece uma breve descrição dos exemplos disponíveis no bloco **Pipelines de exemplo**.

Nome do exemplo | description
----------- | -----------
Criação de perfil de cliente de jogos | A Contoso é uma empresa de jogos que cria os jogos para várias plataformas: PCs (computadores pessoais), dispositivos de mão e consoles de jogos. Cada um desses jogos produz toneladas de logs. O objetivo da Contoso é coletar e analisar os logs gerados por esses jogos para obter informações sobre o uso, identificar oportunidades de vendas e vendas cruzadas, desenvolver novos recursos atraentes etc., para melhorar os negócios e fornecer a melhor experiência para os clientes. Este exemplo coleta logs de exemplo, processa e enriquece-os com dados de referência, além de transformar os dados para avaliar a eficiência de uma campanha de marketing que a Contoso lançou recentemente.
 
## Exemplos no GitHub
O [repositório GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) contém vários exemplos que ajudam você a utilizar rapidamente o serviço Azure Data Factory ou modificar os scripts e usá-los no próprio aplicativo. A pasta Samples\\JSON contém trechos de código JSON para cenários comuns.

## Enviar comentários
Apreciamos muito seus comentários sobre este artigo. Reserve alguns minutos para enviar seus comentários por meio de [email](mailto:adfdocfeedback@microsoft.com?subject=data-factory-samples.md).

[data-factory-get-started]: data-factory-get-started.md#CreateDataFactory

<!---HONumber=September15_HO1-->