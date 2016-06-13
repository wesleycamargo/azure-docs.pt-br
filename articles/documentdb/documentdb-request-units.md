<properties 
	pageTitle="Unidades de solicitação no Banco de Dados de Documentos | Microsoft Azure" 
	description="Saiba mais sobre como entender, especificar e estimar os requisitos de unidades de solicitação no Banco de Dados de Documentos." 
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="mimig" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/31/2016" 
	ms.author="stbaro"/>

#Unidades de Solicitação no Banco de Dados de Documentos
Agora disponível: [calculadora de unidade de solicitação](https://www.documentdb.com/capacityplanner) do Banco de Dados de Documentos. Saiba mais em [Estimativa das necessidades de produção](documentdb-request-units.md#estimating-throughput-needs).

![Calculadora de produtividade][5]

##Introdução
Este artigo fornece uma visão geral das unidades de solicitação no [Banco de Dados de Documentos do Microsoft Azure](https://azure.microsoft.com/services/documentdb/).

Após ler este artigo, você poderá responder as perguntas a seguir:

-	O que são unidades de solicitação e solicitações de encargos?
-	Como especificar a capacidade da unidade de solicitação para uma coleção?
-	Como estimar as necessidades de unidades de solicitação de meu aplicativo?
-	O que acontecerá se eu exceder a capacidade da unidade de solicitação de uma coleção?


##Unidades de solicitação e solicitações de encargos
O Banco de Dados de Documentos fornece desempenho rápido e previsível *reservando* recursos para atender às necessidades de produtividade do aplicativo. Como os padrões de carga e acesso do aplicativo mudam com o tempo, o Banco de Dados de Documentos permite que você aumente ou diminua facilmente a quantidade da taxa de transferência reservada disponível para o aplicativo.

Com o Banco de Dados de Documentos, a taxa de transferência reservada é especificada em termos de unidades de solicitação processadas por segundo. Você pode considerar as unidades de solicitação como a moeda de produtividade, com as quais você *reserva* uma quantidade de unidades de solicitação garantidas disponíveis para o aplicativo por segundo. Cada operação no Banco de Dados de Documentos (gravar um documento, fazer uma consulta, atualizar um documento) consome CPU, memória e IOPS. Ou seja, cada operação resulta em um *encargo de solicitação*, que é expressa em *unidades de solicitação*. Ao entender os fatores que afetam os encargos de unidade de solicitação e os requisitos de taxa de transferência do aplicativo, você pode executar o aplicativo da maneira mais econômica possível.

##Especificação da capacidade da unidade de solicitação
Ao criar uma coleção do Banco de Dados de Documentos, você pode especificar o número de RUs (unidades de solicitação) por segundo que deseja reservar para a coleção. Quando a coleção é criada, a alocação total de RUs especificada é reservada para uso da coleção. Cada coleção tem a garantia de ter características de taxa de transferência dedicadas e isoladas.

É importante observar que o Banco de Dados de Documentos opera em um modelo de reserva. Ou seja, você será cobrado pela quantidade de produtividade *reservada* para a coleção, independentemente da quantidade dessa produtividade que é *usada* ativamente. No entanto, lembre-se de que, à medida que a carga, os dados e os padrões de uso do aplicativo forem alterados, você poderá escalar vertical e horizontalmente com facilidade a quantidade de RUs reservadas por meio de SDKs do Banco de Dados de Documentos ou usando o [Portal do Azure](https://portal.azure.com). Para obter mais informações sobre como escalar vertical e horizontalmente a produtividade, confira [Níveis de desempenho no Banco de Dados de Documentos](documentdb-performance-levels.md).

##Considerações sobre unidades de solicitação
Ao estimar o número de unidades de solicitação a serem reservadas para a coleção do Banco de Dados de Documentos, é importante considerar as seguintes variáveis:

- **Tamanho do documento**. À medida que os tamanhos de documento aumentam, as unidades consumidas para ler ou gravar os dados também aumentam.
- **Contagem de propriedades do documento**. Pressupondo a indexação padrão de todas as propriedades, as unidades consumidas para gravar um documento aumentarão conforme a contagem de propriedades aumentar.
- **Consistência de dados**. Ao serem usados níveis de consistência de dados Strong ou Bounded Staleness, unidades adicionais serão consumidas para ler documentos.
- **Propriedades indexadas**. Uma política de indexação em cada coleção determina quais propriedades são indexadas por padrão. Você pode reduzir o consumo de unidades de solicitação limitando o número de propriedades indexadas ou habilitando a indexação lenta.
- **Indexação de documentos**. Por padrão, cada documento é indexado automaticamente. Você consumirá menos unidades de solicitação se optar por não indexar alguns de seus documentos.
- **Padrões de consulta**. A complexidade de uma consulta afeta a quantidade de Unidades de Solicitação que são consumidas para uma operação. O número de predicados, a natureza dos predicados, as projeções, o número de UDFs e o tamanho do conjunto de dados de origem influenciam o custo das operações de consulta.
- **Uso de scripts**. Assim como ocorre com as consultas, os procedimentos armazenados e os gatilhos consomem unidades de solicitação com base na complexidade das operações que estão sendo executadas. À medida que desenvolver seu aplicativo, inspecione o cabeçalho de solicitação de carga para entender melhor como cada operação está consumindo a capacidade de unidades de solicitação.

##Estimativa das necessidades de produção
Uma unidade de solicitação é uma medida normalizada de custo de processamento de solicitação. Uma única unidade de solicitação representa a capacidade de processamento necessária para ler (por meio de self link ou id) um único documento JSON de 1 KB que consiste em 10 valores de propriedade exclusivos (excluindo as propriedades do sistema). Uma solicitação para criar (inserir), substituir ou excluir o mesmo documento consumirá mais processamento do serviço e, assim, mais unidades de solicitação.

> [AZURE.NOTE] A linha de base de uma unidade de solicitação para um documento de 1 KB corresponde a um GET simples por self link ou id do documento.

###Usar a calculadora de unidade de solicitação
Para ajudar os clientes a ajustar as estimativas de produtividade, há uma [calculadora de unidade de solicitação](https://www.documentdb.com/capacityplanner) baseada na Web que ajuda a fazer uma estimativa dos requisitos de unidade de solicitação para operações comuns, incluindo:

- Criações de documento (gravações)
- Leituras de documento
- Exclusões de documento

Usar a ferramenta é simples:

1. Carregue um ou mais documentos JSON representativos.

	![Carregar documentos na calculadora de unidade de solicitação][2]

2. Insira o número necessário de operações criar, ler e excluir documentos (por segundo).

	![Inserir requisitos de produtividade na calculadora de unidade de solicitação][3]

3. Clique em Calcular e examinar os resultados.

	![Resultados da calculadora de unidade de solicitação][4]

>[AZURE.NOTE]Se você tiver tipos de documento que serão muito diferentes em termos de tamanho e número de propriedades indexadas, carregue uma amostra da cada *tipo* do documento típico na ferramenta e calcule os resultados.

###Usar o cabeçalho de resposta do encargo de solicitação do Banco de Dados de Documentos
Todas as respostas do serviço Banco de Dados de Documentos incluem um cabeçalho personalizado (x-ms-request-charge) que contém as unidades de solicitação consumidas para a solicitação. Esse cabeçalho também está acessível por meio dos SDKs do Banco de Dados de Documentos. No SDK .NET, RequestCharge é uma propriedade do objeto ResourceResponse. Para consultas, o Gerenciador de Consultas do Banco de Dados de Documentos no portal do Azure fornece informações sobre solicitações de encargo para consultas executadas.

![Análise de encargos de RU no Gerenciador de Consultas][1]

Tendo isso em mente, um método para estimar a quantidade de taxa de transferência reservada exigida pelo aplicativo é registrar o encargo de unidade de solicitação associado à execução de operações típicas em relação a um documento representativo usado pelo aplicativo e, em seguida, estimar o número de operações que você prevê que executará a cada segundo. Também meça e inclua consultas típicas e o uso de scripts do Banco de Dados de Documentos.

>[AZURE.NOTE]Se você tiver tipos de documento que serão muito diferentes em termos de tamanho e número de propriedades indexadas, registre o encargo de unidade de solicitação de operação aplicável associado a cada *tipo* de documento típico.

Por exemplo:

1. Registre o encargo de unidade de solicitação para a criação (inserção) de um documento típico. 
2. Registre o encargo de unidade de solicitação para a leitura de um documento típico.
3. Registre o encargo de unidade de solicitação para a atualização de um documento típico.
3. Registre o encargo de unidade de solicitação de consultas de documento comuns e típicos.
4. Registre o encargo de unidade de solicitação de quaisquer scripts personalizados (procedimentos armazenados, gatilhos, funções definidas pelo usuário) utilizados pelo aplicativo
5. Calcule as unidades de solicitação necessárias, dado o número estimado de operações que você prevê que executará por segundo.

##Um exemplo de estimativa de unidade de solicitação
Considere o seguinte documento de aproximadamente 1 KB:

	{
	 "id": "08259",
  	"description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  	"tags": [
    	{
      	"name": "cereals ready-to-eat"
    	},
    	{
      	"name": "kellogg"
    	},
    	{
      	"name": "kellogg's crispix"
    	}
	],
  	"version": 1,
  	"commonName": "Includes USDA Commodity B855",
  	"manufacturerName": "Kellogg, Co.",
  	"isFromSurvey": false,
  	"foodGroup": "Breakfast Cereals",
  	"nutrients": [
    	{
      	"id": "262",
      	"description": "Caffeine",
      	"nutritionValue": 0,
      	"units": "mg"
    	},
    	{
      	"id": "307",
      	"description": "Sodium, Na",
      	"nutritionValue": 611,
      	"units": "mg"
    	},
    	{
      	"id": "309",
      	"description": "Zinc, Zn",
      	"nutritionValue": 5.2,
      	"units": "mg"
    	}
  	],
  	"servings": [
    	{
      	"amount": 1,
      	"description": "cup (1 NLEA serving)",
      	"weightInGrams": 29
    	}
  	]
	}

>[AZURE.NOTE]Os documentos são reduzidos no Banco de Dados de Documentos, assim, o tamanho do documento acima calculado pelo sistema é um pouco menor que 1 KB.


A tabela a seguir mostra os encargos de unidade de solicitação aproximados para operações típicas nesse documento (o encargo de unidade de solicitação aproximado pressupõe que o nível de consistência de conta seja definido como "Sessão" e que todos os documentos estejam indexados automaticamente):

Operação|Encargo de Unidade de Solicitação 
---|---
Criar documento|Cerca de 15 RUs 
Ler documento|Cerca de 1 RU
Consultar documento por id|Cerca de 2,5 RUs

Adicionalmente, a tabela mostra os encargos de unidade de solicitação aproximados para consultas típicas usadas no aplicativo:

Consultar|Encargo de Unidade de Solicitação|Número de Documentos Retornados
---|---|--- 
Selecionar alimentos por id|Cerca de 2,5 RUs|1 
Selecionar alimentos por fabricante|Cerca de 7 RUs|7
Selecionar por grupo de alimentos e solicitar por peso|Cerca de 70 RUs|100
Selecionar os 10 alimentos principais em um grupo de alimentos|Cerca de 10 RUs|10

>[AZURE.NOTE]Os encargos de RU variam com base no número de documentos retornados.

Com essas informações, podemos estimar os requisitos de RU para o aplicativo, dado o número de operações e consultas que esperamos por segundo:

Operação/consulta|Número estimado por segundo|RUs necessárias 
---|---|--- 
Criar documento|10|150 
Ler documento|100|100 
Selecionar alimentos por fabricante|25|175 
Selecionar por grupo de alimentos|10|700 
Selecionar os 10 principais|15|Total de 150|155|1275

Nesse caso, esperamos um requisito de taxa de transferência médio de 1.275 RUs/s. Arredondando para a centena mais próxima, vamos provisionar 1.300 RUs/s para a coleção desse aplicativo.

##Exceder os limites de taxa de transferência reservada
Lembre-se de que o consumo de unidades de solicitação é avaliado como uma taxa por segundo. Para aplicativos que ultrapassam a taxa de unidades solicitação provisionada para uma coleção, as solicitações a essa coleção são limitadas até que a taxa caia para baixo do nível reservado. Quando ocorre uma restrição, o servidor encerra preventivamente a solicitação com RequestRateTooLarge (código de status HTTP 429) e retorna o cabeçalho x-ms-retry-after-ms, indicando a quantidade de tempo, em milissegundos, que o usuário deve aguardar antes de tentar novamente a solicitação.

	HTTP Status 429
	Status Line: RequestRateTooLarge
	x-ms-retry-after-ms :100

Se estiver usando as consultas do SDK do cliente .NET e LINQ, em seguida, na maioria das vezes, você nunca precisará lidar com essa exceção, pois a versão atual do SDK do Cliente .NET captura implicitamente essa resposta, respeita o cabeçalho retry-after especificado pelo servidor e tenta novamente a solicitação. A menos que sua conta esteja sendo acessada simultaneamente por vários clientes, a próxima tentativa será bem-sucedida.

Se você tiver mais de um cliente operando cumulativamente acima da taxa de solicitação, o comportamento de repetição padrão poderá não ser suficiente e o cliente lançará uma DocumentClientException com o código de status 429 para o aplicativo. Em casos como esse, você pode considerar a manipulação do comportamento de repetição e a lógica nas rotinas de tratamento de erro do aplicativo ou o aumento da taxa de transferência reservada para a coleção.

##Próximas etapas

Para saber mais sobre a taxa de transferência reservada com o Banco de Dados de Documentos do Azure, explore estes recursos:
 
- [Definição de preços no Banco de Dados de Documentos](https://azure.microsoft.com/pricing/details/documentdb/)
- [Gerenciando a capacidade no Banco de Dados de Documentos](documentdb-manage.md) 
- [Modelando dados no Banco de Dados de Documentos](documentdb-modeling-data.md)
- [Níveis de desempenho do Banco de Dados de Documentos](documentdb-partition-data.md)

Para saber mais sobre o Banco de Dados de Documentos, veja a [documentação](https://azure.microsoft.com/documentation/services/documentdb/) do Banco de Dados de Documentos do Azure.

Para começar com os testes de desempenho e escala com o Banco de Dados de Documentos, confira [Performance and Scale Testing with Azure DocumentDB](documentdb-performance-testing.md) (Teste de desempenho e escala com o Banco de Dados de Documentos do Azure).


[1]: ./media/documentdb-request-units/queryexplorer.png
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png

<!---HONumber=AcomDC_0601_2016-->