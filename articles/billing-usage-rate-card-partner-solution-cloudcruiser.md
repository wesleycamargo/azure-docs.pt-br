<properties
   pageTitle="Integração da API de Cobrança do Microsoft Azure e Cloud Cruiser"
   description="Fornece uma perspectiva exclusiva do parceiro de cobrança Cloud Cruiser do Microsoft Azure em suas experiências de integração das APIs de Cobrança do Azure em seus produtos. Isso é especialmente útil para clientes do Azure e do Cloud Cruiser que estejam interessados em usar/experimentar o Cloud Cruiser para o pacote do Microsoft Azure."
   services="billing"
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="06/17/2015"
   ms.author="mobandyo;sirishap;bryanla"/>

# Integração da API de Cobrança do Microsoft Azure e Cloud Cruiser 

Este artigo descreve como as informações coletadas nas novas APIs de Cobrança Microsoft Azure podem ser usadas no Cloud Cruiser para análise e simulação de custo do fluxo de trabalho.

## API RateCard do Azure
A API RateCard fornece informações de taxa do Azure. Depois de autenticar com as credenciais apropriadas, você pode consultar a API para reunir metadados sobre os serviços disponíveis no Azure, juntamente com as taxas associadas com a sua ID de Oferta.

Abaixo está um exemplo de resposta da API mostrando os preços para a instância A0 (Windows):

    {       
		"MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",       
		"MeterName": "Compute Hours",       
		"MeterCategory": "Virtual Machines",       
		"MeterSubCategory": "A0 VM (Windows)",       
		"Unit": "Hours",       
		"MeterRates": 
		{         
			"0": 0.029       
		},       
		"EffectiveDate": "2014-08-01T00:00:00Z",       
		"IncludedQuantity": 0.0     
	}, 

## Interface do Cliud Cruiser para a API RateCard do Azure
O Cloud Cruiser pode aproveitar as informações da API RateCard de diferentes maneiras. Neste artigo, mostraremos como ele pode ser usado para fazer a análise e a simulação de custo de fluxo de trabalho IaaS.

Para demonstrar este caso de uso, imagine uma carga de trabalho de várias instâncias em execução no Microsoft Azure Pack (WAP). O objetivo é simular essa mesma carga de trabalho no Azure e estimar os custos de fazer essa migração. Para criar esta simulação, há duas tarefas principais a serem executadas:

1. **Importar e processar as informações de serviço coletadas da API RateCard** - Essa tarefa também é executada em pastas de trabalho, onde a extração da API RateCard é transformada e publicada para um novo plano de taxa. Esse novo plano de taxa será usado em simulações para estimar os preços do Azure.

2. **Normalização de serviços WAP e serviços do Azure para IaaS** - Por padrão, os serviços WAP se baseiam em recursos individuais (CPU, tamanho da memória, tamanho do disco, etc.) enquanto os serviços do Azure baseiam-se no tamanho da instância (A0 A1, A2, etc.). A primeira tarefa pode ser executada pelo mecanismo ETL do Cloud Cruiser, chamado de pastas de trabalho, onde esses recursos podem ser incorporados em tamanhos de instância, semelhantes aos serviços de instância do Azure.

## Importar dados da API RateCard

As pastas de trabalho do Cloud Cruiser fornecem uma forma automatizada para coletar e processar informações da API RateCard. As pastas de trabalho do ETL (extrair, transformar e carregar) permitem configurar a coleta, transformação e publicação de dados no banco de dados do Cloud Cruiser.

Cada pasta de trabalho pode ter uma ou várias coleções. Isso permite que você correlacione informações de diferentes fontes para complementar ou aumentar os dados de uso. Nas duas capturas de tela abaixo, estamos mostrando a criação de uma novo *coleção* em uma pasta de trabalho existente e a importação de informações para a *coleção* da API RateCard:

![Figura 1 - Criação de uma nova coleção][1]

![Figura 2 - Importação de dados da nova coleção][2]

Depois de importar os dados na pasta de trabalho, é possível criar várias etapas e processos de transformação, para modificar e modelar os dados. Para este exemplo, como só estamos interessados em infraestrutura como um serviço (IaaS), podemos usar as etapas de transformação para remover linhas desnecessárias ou registros relacionados a serviços diferentes do IaaS.

A captura de tela abaixo mostra as etapas de transformação usadas para processar os dados coletados pela API RateCard:

![Figura 3 - Etapas de transformação para processar os dados coletados da API RateCard][3]

## Definindo novos serviços e a planos de taxa

Há diferentes maneiras de definir os serviços no Cloud Cruiser. Uma das opções é importar os serviços de dados de uso. Esse método é normalmente usado quando se trabalha com nuvens públicas, onde os serviços já estão definidos pelo provedor.

Um plano de taxa é um conjunto de taxas ou de preços que podem ser aplicados a serviços diferentes, com base nas datas reais ou grupo de clientes, entre outras opções. Os planos de taxa também podem ser usados em Cloud Cruiser para criar uma simulação ou cenários hipotéticos, para entender como as alterações nos serviços podem afetar o custo total de uma carga de trabalho ou simulação.

Neste exemplo, usaremos as informações do serviço da API RateCard para definir novos serviços no Cloud Cruiser. Da mesma forma, podemos usar as taxas associadas aos serviços para criar um novo plano de taxa no Cloud Cruiser.

No final do processo de transformação, é possível criar uma nova etapa e publicar os dados da API RateCard como novos serviços e taxas.

![Figura 4 - Publicação dos dados da API RateCard como novos Serviços e Taxas][4]

## Verificação das taxas e os serviços do Azure

Depois de publicar os serviços e as taxas, você pode verificar a lista de serviços importados na guia *Serviços* do Cloud Cruiser:

![Figura 5 - Verificando os novos serviços][5]

Na guia *Planos de taxa*, você pode verificar o novo plano de taxa chamado "AzureSimulation" com as taxas importadas da API RateCard.

![Figura 6 - Verificando o novo Plano de Taxas e as taxas associadas][6]

## Normalização WAP e Serviços do Azure

Por padrão, o WAP fornece informações de uso com base no uso de recursos de computação, memória e rede. No Cloud Cruiser, você pode definir os serviços com base diretamente na alocação ou uso limitado desses recursos. Por exemplo, você pode definir uma taxa básica de cada hora do uso da CPU ou cobrar o GB de memória alocada para uma instância.

Neste exemplo, para comparar os custos entre WAP e o Azure, precisamos agregar a utilização de recursos WAP em pacotes, que pode ser mapeada para os serviços do Azure. Essa transformação pode ser facilmente implementada nas pastas de trabalho:

![Figura 7 - Transformar dados WAP para normalizar os serviços][7]

A última etapa na pasta de trabalho é publicar os dados no banco de dados do Cloud Cruiser. Durante esta etapa, os dados de uso são empacotados em serviços (que mapeiam para os Serviços do Azure) e vinculados a taxas padrões para criar os encargos.

Depois de concluir a pasta de trabalho, você pode automatizar o processamento de dados, adicionando uma nova tarefa no agendador e especificando a freqüência e a hora para a pasta de trabalho ser executada.

![Figura 8 - Planejamento de pasta de trabalho][8]

## Criação de relatórios para análise de custo de simulação de carga de trabalho

Como o uso é coletado e os encargos são carregados no banco de dados do Cloud Cruiser, em seguida, podemos aproveitar o módulo Insights do Cloud Cruiser – uma ferramenta de relatório de análise avançada – para criar a simulação de custo de carga de trabalho desejado.

Para demonstrar esse cenário, criamos o relatório a seguir:

![Comparação de custos][9]

O gráfico superior mostra uma comparação de custo dividida por serviços e compara o preço da execução da carga de trabalho para cada serviço específico entre WAP (azul-escuro) e o Azure (azul-claro).

O gráfico inferior mostra os mesmos dados, mas dividido por departamento, demonstrando os custos de cada departamento para executar a carga de trabalho no WAP e o Azure, juntamente com a diferença entre esses dois – Barra de economia (verde).

## Próximas etapas

+ Para obter instruções detalhadas sobre como criar relatórios e pastas de trabalho do Cloud Cruiser, consulte a [documentação](http://docs.cloudcruiser.com/) online do Cloud Cruiser (logon válido necessário). Para obter mais informações sobre o Cloud Cruiser, entre em contato com [info@cloudcruiser.com](mailto:info@cloudcruiser.com).
+ Consulte [Obter informações sobre o consumo de recursos do Microsoft Azure](billing-usage-rate-card-overview.md) para uma visão geral das APIs RateCard e de Uso dos recursos do Azure. 
+ Confira a [referência da API REST de Cobrança do Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) para obter mais informações sobre as duas APIs, que fazem parte do conjunto de APIs fornecidas pelo Gerenciador de Recursos do Azure.
+ Se você gostaria de mergulhar no código de exemplo, confira nossos [exemplos de código da API do Microsoft Azure no Github](https://github.com/Azure/BillingCodeSamples).

## Saiba mais
+ Consulte o artigo [Visão geral do Gerenciador de Recursos do Azure](resource-group-overview.md) para saber mais sobre o Gerenciador de Recursos do Azure.

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Figura 1 - Criação de uma nova coleção"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Figura 2 - Importação de dados da nova coleção"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Figura 3 - Etapas de transformação para processar os dados coletados da API RateCard"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Figura 4 - Publicação dos dados da API RateCard como novos Serviços e Taxas"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Figura 5 - Verificando os novos serviços"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Figura 6 - Verificando o novo Plano de Taxas e as taxas associadas"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Figura 7 - Transformar dados WAP para normalizar os serviços"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Figura 8 - Planejamento de pasta de trabalho"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Figura 9 - Relatório de exemplo para o cenário de comparação de custo de carga de trabalho"

<!---HONumber=July15_HO3-->