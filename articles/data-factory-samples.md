<properties 	
	pageTitle="Fábrica de dados do Azure - exemplos" 
	description="Fornece detalhes sobre os exemplos fornecidos com o serviço de fábrica de dados do Azure." 
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
	ms.date="04/25/2015" 
	ms.author="spelluru"/>

# Fábrica de dados do Azure - exemplos

## Exemplos no Portal do Azure
Rapidamente implantar, analisar e testar um exemplo de fábrica de dados do Azure usando o **pipelines de exemplo** lâmina no Portal do Azure.

1. Crie uma nova fábrica de dados ou abra uma fábrica de dados existente. Consulte [Introdução ao Azure Data Factory][data-factory-get-started] para conhecer as etapas criar uma fábrica de dados.
2. No **DATA FACTORY** blade da fábrica de dados, clique o **pipelines de exemplo** lado a lado.

	![Bloco de pipelines de exemplo](./media/data-factory-samples/SamplePipelinesTile.png)

2. No **pipelines de exemplo** lâmina, clique o **exemplo** que você deseja implantar.
	
	![Blade de pipelines de amostra](./media/data-factory-samples/SampleTile.png)

3. Especifica definições de configuração para o exemplo. Por exemplo, sua chave de nome e uma conta da conta de armazenamento do Azure, nome do servidor SQL Azure, banco de dados, ID de usuário e senha, etc...

	![Blade de exemplo](./media/data-factory-samples/SampleBlade.png)

4. Depois de terminar com a especificação de configurações, clique em **criar** criar/implantar os pipelines de exemplo e usadas pelos pipelines de serviços/tabelas vinculadas.
5. Você verá o status da implantação da imagem de exemplo você clicou no **pipelines de exemplo** lâmina.

	![Status da implantação](./media/data-factory-samples/DeploymentStatus.png)

6. Quando você vir a **implantação bem-sucedida** mensagem no bloco para o exemplo, feche o **pipelines de exemplo** lâmina.
5. Em **DATA FACTORY** lâmina, você verá que os pipelines, conjuntos de dados e serviços vinculados são adicionados à sua fábrica de dados.  

	![Blade de fábrica de dados](./media/data-factory-samples/DataFactoryBladeAfter.png)
   

A tabela a seguir fornece uma descrição breve dos exemplos disponíveis no **pipelines de exemplo** lado a lado.

Exemplo de nome | description
----------- | -----------
Criação de perfil de cliente de jogos | A Contoso é uma empresa de jogos que cria os jogos para várias plataformas: computadores pessoais (PCs), dispositivos de mão mantido e consoles de jogos. Cada um desses jogos produz toneladas de logs. Objetivo da Contoso é coletar e analisar os logs gerados por esses jogos para obter informações de uso, identificar oportunidades de venda e vendas cruzadas, desenvolver novos recursos atraentes etc... para melhorar os negócios e fornecer a melhor experiência para os clientes. Este exemplo coleta logs de exemplo, processos e enriquece-los com dados de referência e transforma os dados para avaliar a eficiência de uma campanha de marketing Contoso lançou recentemente.
 
## Amostras no GitHub
O [repositório GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) contém vários exemplos que ajudam você rapidamente aprimore-se com o serviço de fábrica de dados do Azure (ou) modifique os scripts e usá-lo no próprio aplicativo. A pasta Samples\JSON contém trechos JSON para cenários comuns.

[data-factory-get-started]: data-factory-get-started.md#CreateDataFactory

<!---HONumber=GIT-SubDir-->