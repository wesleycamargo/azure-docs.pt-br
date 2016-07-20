<properties
	pageTitle="Guia de desempenho e ajuste da atividade de cópia | Microsoft Azure"
	description="Conheça os principais fatores que afetam o desempenho de movimentação de dados no Azure Data Factory por meio da atividade de cópia."
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
	ms.date="06/03/2016"
	ms.author="spelluru"/>


# Guia de desempenho e ajuste da atividade de cópia
Este artigo descreve os principais fatores que afetam o desempenho de movimentação de dados (atividade de cópia) no Azure Data Factory. Ele também lista o desempenho observado durante os testes internos e discute várias maneiras de otimizar o desempenho da Atividade de Cópia.

Usando a Atividade de Cópia, você pode obter alta taxa de transferência de movimentação de dados conforme mostrado nos exemplos a seguir:

- Consumir 1 TB de dados no Armazenamento de Blobs do Azure do Sistema de Arquivos local e do Armazenamento de Blobs do Azure em menos de 3 horas (ou seja, a 100 MBps)
- Consumir 1 TB de dados no Repositório Azure Data Lake do Sistema de Arquivos local e do Armazenamento de Blobs do Azure em menos de 3 horas (ou seja, a 100 MBps)
- Consumir 1 TB de dados no Azure SQL Data Warehouse do Armazenamento de Blobs do Azure em menos de 3 horas (ou seja, a 100 MBps)

Consulte as seções a seguir para saber mais sobre o desempenho da Atividade de Cópia e dicas de ajuste para melhorá-las ainda mais.

> [AZURE.NOTE] Se você não estiver familiarizado com a Atividade de Cópia em geral, consulte [Atividades de Movimentação de Dados](data-factory-data-movement-activities.md) antes de ler este artigo.

## Etapas de ajuste de desempenho
As etapas típicas que sugerimos que você faça para ajustar o desempenho de sua solução Azure Data Factory com atividade de cópia são listadas abaixo.

1.	**Estabelecer uma linha de base.** Durante a fase de desenvolvimento, teste seu pipeline com atividade de cópia em relação aos dados de uma amostra representativa. Você pode aproveitar o [modelo de divisão](data-factory-scheduling-and-execution.md#time-series-datasets-and-data-slices) do Azure Data Factory para limitar a quantidade de dados com que você está trabalhando.

	Colete o tempo de execução e características de desempenho usando o **Aplicativo de Monitoramento e Gerenciamento**: clique no bloco **Monitorar e Gerenciar** na home page da sua data factory, selecione o **conjunto de dados de saída** no modo de exibição de árvore e selecione a execução de atividade de cópia da lista **Janelas de Atividade**. Você deverá ver a duração da atividade de cópia na lista **Janelas de Atividade** e o tamanho dos dados que são copiados, bem como sua taxa de transferência, na janela **Gerenciador de Janela de Atividade** à direita. Consulte [Monitorar e gerenciar os pipelines do Azure Data Factory usando o Aplicativo de Monitoramento e Gerenciamento](data-factory-monitor-manage-app.md) para saber mais sobre o Aplicativo.
	
	![Detalhes da execução da atividade](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

	Você pode comparar o desempenho e as configurações do seu cenário com a [referência desempenho](#performance-reference) da atividade de cópia publicadas abaixo com base em observações internas.
2. **Diagnóstico e otimização de desempenho** Se o desempenho observado estiver abaixo das suas expectativas, você precisa identificar gargalos de desempenho e efetuar otimizações para remover ou reduzir o impacto de afunilamentos. Uma descrição completa do diagnóstico de desempenho está além do escopo deste artigo, mas estamos registrando algumas considerações comuns aqui da seguinte maneira.
	- [Fonte](#considerations-on-source)
	- [Coletor](#considerations-on-sink)
	- [Serialização/desserialização](#considerations-on-serializationdeserialization)
	- [Compactação](#considerations-on-compression)
	- [Mapeamento de coluna](#considerations-on-column-mapping)
	- [Gateway de gerenciamento de dados](#considerations-on-data-management-gateway)
	- [Outras considerações](#other-considerations)
	- [Cópia paralela](#parallel-copy)
	- [Unidades de Movimentação de Dados de Nuvem](#cloud-data-movement-units)

3. **Expandir a configuração para todos os dados** Quando você estiver satisfeito com os resultados e o desempenho da execução, poderá expandir a definição do conjunto de dados e o período ativo do pipeline para cobrir todos os dados na imagem.

## Referência de desempenho
> [AZURE.IMPORTANT] **Isenção de responsabilidade:** os dados abaixo foram publicados com o único propósito de orientação e planejamento alto nível. Eles pressupõem que a largura de banda, o hardware, a configuração, etc. são os melhores em sua classe. Use apenas como referência. A taxa de transferência de movimentação de dados que você observar será afetada por uma variedade de variáveis. Consulte as seções mais tarde para saber mais sobre como você pode ajustar e obter o melhor desempenho para suas necessidades de movimentação de dados. Esses dados serão atualizados como e quando forem adicionados recursos e melhorias de aumento de desempenho.

![Matriz de desempenho](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

Pontos a serem observados:

- A taxa de transferência é calculada usando a seguinte fórmula: [tamanho dos dados lidos da origem]/[duração da execução de atividade de cópia]
- O conjunto de dados [TPC-H](http://www.tpc.org/tpch/) foi aproveitado para calcular números acima.
- No caso de armazenamentos de dados do Microsoft Azure, origem e coletor estão na mesma região do Azure.
- **cloudDataMovementUnits** definido como 1 e **parallelCopies** não especificado.
- No caso da movimentação de dados híbrida (local para a nuvem ou da nuvem para local), o Gateway de gerenciamento de dados (instância única) estava hospedado em um computador diferente do armazenamento de os dados local usando a configuração a seguir. Observe que, com uma única execução atividade em andamento no gateway, a operação de cópia consumiu apenas uma pequena parte da largura de banda de rede e dos recursos de CPU/memória do computador.
	<table>
	<tr>
		<td>CPU</td>
		<td>32 núcleos de 2,20 GHz Intel Xeon® E5-2660 v2</td>
	</tr>
	<tr>
		<td>Memória</td>
		<td>128 GB</td>
	</tr>
	<tr>
		<td>Rede</td>
		<td>Interface da Internet: 10 Gbps; Interface de intranet: 40 Gbps</td>
	</tr>
	</table>

## Cópia paralela
Uma das maneiras de melhorar a taxa de transferência de uma operação de cópia e reduzir o tempo para mover os dados é ler os dados da origem e/ou gravar dados no destino **em paralelo dentro de uma execução de Atividade de Cópia**.
 
Observe que essa configuração é diferente da propriedade **concurrency** na definição da atividade. A propriedade de simultaneidade determina o número de **execuções de Atividade de Cópia simultâneas** que são executadas juntas no tempo de execução para processar dados de diferentes janelas de atividade (1h a 2h, 2h a 3h, 3h a 4h, etc...). Isso é muito útil ao executar uma carga histórica. Por outro lado, a capacidade de cópia paralela discutida aqui aplica-se a uma **única execução de atividade**.

Vamos examinar um **cenário de exemplo**: considere o exemplo a seguir, em que há várias fatias do passado e que precisam ser processadas. O serviço do Data Factory executa uma instância da Atividade de Cópia (execução de atividade) para cada fatia.

- fatia de dados da 1ª janela de atividade (1h a 2h) = = > Execução de atividade 1
- fatia de dados da 2ª janela de atividade (2h a 3h) = = > Execução de atividade 2
- fatia de dados da 2ª janela de atividade (3h a 4h) = = > Execução de atividade 3
- e assim por diante.

Ter a configuração de **simultaneidade** **2** neste exemplo permite que a **Execução de atividade 1** e a **Execução de atividade 2** copiem dados de duas janelas de atividade **simultaneamente** para melhorar o desempenho da movimentação de dados. No entanto, se houver vários arquivos associados à Execução de atividade 1, um arquivo será copiado da origem para o destino de cada vez.

### parallelCopies
Você pode usar a propriedade **parallelCopies** para indicar o paralelismo que deseja que a Atividade de Cópia use. Em termos simples, considere essa propriedade como o número máximo de threads dentro de uma atividade de cópia que lê da sua fonte e/ou grava para seus armazenamentos de dados do coletor em paralelo.

Para cada execução de atividade de cópia, o Azure Data Factory determina de forma inteligente o número de cópias paralelas a serem usadas para copiar dados do armazenamento de dados de origem para o de destino. O número padrão de cópias paralelas usadas depende dos tipos de fonte e do coletor:

Fonte e coletor |	Contagem de cópia paralela padrão determinada pelo serviço
------------- | -------------------------------------------------
Copiar dados entre **armazenamentos baseados em arquivos** (Blob do Azure, Azure Data Lake, Sistema de Arquivos local e HDFS local) | Em qualquer ponto entre **1 e 32** com base no **tamanho dos arquivos** e **número de unidades de movimentação de dados na nuvem** (consulte a próxima seção para ver a definição) usada para copiar dados entre dois armazenamentos de dados de nuvem (ou) a configuração física do computador de gateway usado para cópia híbrida (copiar dados para/de um armazenamento de dados local)
Copiar dados do **qualquer armazenamento de dados de origem para a Tabela do Azure** | 4
Todos os outros pares de origem e coletor | 1

Para a maioria dos casos, o comportamento padrão deve fornecer a melhor taxa de transferência. No entanto, você pode substituir o valor padrão, especificando um valor para a propriedade **parallelCopies** para controlar a carga em computadores com seus armazenamentos de dados ou ajustar o desempenho de cópia. Ele deve estar entre **1 e 32 (ambos inclusive)**. No momento de execução, a Atividade de Cópia escolherá um valor menor ou igual ao valor configurado para proporcionar o melhor desempenho.

	"activities":[  
	    {
	        "name": "Sample copy activity",
	        "description": "",
	        "type": "Copy",
	        "inputs": [{ "name": "InputDataset" }],
	        "outputs": [{ "name": "OutputDataset" }],
	        "typeProperties": {
	            "source": {
	                "type": "BlobSource",
	            },
	            "sink": {
	                "type": "AzureDataLakeStoreSink"
	            },
	            "parallelCopies": 8
	        }
	    }
	]

Observe o seguinte:

- Para copiar dados entre repositórios baseados em arquivos, ocorre um paralelismo no nível do arquivo; em outras palavras, não há nenhum agrupamento em um único arquivo. O número real de cópias paralelas usado para a operação de cópia no tempo de execução será no máximo o número de arquivos que você tem. Se o comportamento de cópia for mergeFile, o paralelismo não será aproveitado.
- Ao especificar um valor para a propriedade parallelCopies, considere o aumento da carga que será trazida para seus armazenamentos de dados da fonte e do coletor, bem como o gateway, se esta for uma cópia híbrida, especialmente quando você tiver várias atividades ou execuções simultâneas das mesmas atividades em execução no mesmo armazenamento de dados. Se você perceber que o armazenamento de dados ou o gateway estão sobrecarregados com a carga, diminua o valor de parallelCopies para aliviá-la.
- Ao copiar dados de armazenamentos não baseados em arquivos para outros baseados em arquivos, a propriedade parallelCopies será ignorada, mesmo se for especificada, e nenhum paralelismo será aproveitado.

> [AZURE.NOTE] Para tirar proveito do recurso parallelCopies ao fazer uma cópia híbrida, você deve usar o Gateway de Gerenciamento de Dados de versão >= 1.11.

### Unidades de Movimentação de Dados de Nuvem
A **unidade de movimentação de dados de nuvem** é uma medida que representa a potência (combinação de alocação de recurso de CPU, memória e rede) de uma única unidade no serviço do Azure Data Factory que é usada para executar uma operação de cópia de nuvem para a nuvem. Ela não entra em jogo ao fazer uma cópia híbrida. Por padrão, o serviço do Azure Data Factory usa uma unidade de movimentação de dados de nuvem única para realizar uma execução de atividade de cópia única. Você pode substituir esse padrão especificando um valor para a propriedade **cloudDataMovementUnits**. Neste momento, a configuração de cloudDataMovementUnits tem **suporte apenas** ao copiar dados **entre dois armazenamentos de blobs do Azure** ou um **armazenamento de Blobs do Azure para um Repositório Azure Data Lake** e ela entra em vigor quando você tem vários arquivos a serem copiados de tamanho >= 16 MB individualmente.

Se você estiver copiando um número de arquivos relativamente grandes, definir um valor alto para a propriedade **parallelCopies** poderá não melhorar o desempenho devido a limitações de recursos de uma única unidade de movimentação de dados de nuvem. Nesses casos, pode ser útil usar mais unidades de movimentação de dados de nuvem para copiar enormes quantidades de dados com alta taxa de transferência. Para especificar o número de unidades de movimentação de dados de nuvem que você deseja que a Atividade de Cópia use, defina um valor para a propriedade **cloudDataMovementUnits**, conforme mostrado abaixo:

	"activities":[  
	    {
	        "name": "Sample copy activity",
	        "description": "",
	        "type": "Copy",
	        "inputs": [{ "name": "InputDataset" }],
	        "outputs": [{ "name": "OutputDataset" }],
	        "typeProperties": {
	            "source": {
	                "type": "BlobSource",
	            },
	            "sink": {
	                "type": "AzureDataLakeStoreSink"
	            },
	            "cloudDataMovementUnits": 4
	        }
	    }
	]

Os **valores permitidos** para a propriedade cloudDataMovementUnits são: 1 (padrão), 2, 4 e 8. Se você precisar de mais unidades de movimentação de dados de nuvem para uma maior taxa de transferência, entre em contato com o [Suporte do Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). O **número real de unidades de movimentação de dados de nuvem** usado para a operação de cópia no tempo de execução será igual ou menor que o valor configurado, dependendo do número de arquivos a serem copiados da fonte que atendem os critérios de tamanho.

> [AZURE.NOTE] parallelCopies deve ser >= cloudDataMovementUnits se especificado; e, quando cloudDataMovementUnits for maior que 1, a movimentação de dados paralela é distribuída entre as cloudDataMovementUnits para essa execução de atividade de cópia e isso aumenta a taxa de transferência.

Ao copiar vários arquivos grandes com **cloudDataMovementUnits** configurada como 2, 4 e 8, o desempenho pode chegar a 2x (duas vezes), 4x e 7x os números de referência mencionados na seção de Referência de Desempenho.

Consulte os [casos de uso de exemplo](#case-study---parallel-copy) aqui para aproveitar melhor as duas prioridades acima para aprimorar sua taxa de transferência de movimentação de dados.
 
É **importante** lembrar que você será cobrado com base no tempo total da operação de cópia. Desta forma, se um trabalho de cópia levava uma hora com uma unidade de nuvem e agora leva 15 minutos com quatro unidades de nuvem, o total da fatura será quase o mesmo. Em outro cenário, vamos supor que você esteja usando quatro unidades de nuvem: a primeira gasta 10 minutos; a segunda, 10 minutos; a terceira, 5 minutos; e a quarta, 5 minutos com uma execução de atividade de cópia. Nesse cenário, você será cobrado pelo tempo total da cópia (movimentação de dados), que é de 10 + 10 + 5 + 5 = 30 minutos. O uso de **parallelCopies** não tem nenhum impacto sobre a cobrança.

## Cópia em etapas
Ao copiar dados de um armazenamento de dados de origem para um armazenamento de dados do coletor, você poderá usar um armazenamento de Blobs do Azure como um armazenamento de preparação provisório. Esse recurso de preparo é especialmente útil nos seguintes casos:

1.	**Às vezes, leva algum tempo para realizar a movimentação de dados híbridos (ou seja, no armazenamento de dados local para um repositório de dados de nuvem ou vice-versa) em uma conexão de rede lenta.** Para melhorar o desempenho de tal movimentação de dados, você poderá compactar os dados no local para que a movimentação de dados seja mais rápida durante a transmissão para o armazenamento de dados de preparo na nuvem e depois descompactar dados no armazenamento de preparo antes de carregá-lo no armazenamento de dados de destino.
2.	**Você não deseja abrir portas diferentes da 80 e da 443 em seu firewall devido a políticas de TI.** Por exemplo, ao copiar dados de um armazenamento de dados local para um coletor do Banco de Dados SQL do Azure ou o coletor do SQL Data Warehouse do Azure, a comunicação de saída TCP na porta 1433 para o firewall do Windows e o firewall corporativo precisam estar habilitados. Em tal cenário, você pode aproveitar os dados da primeira cópia do Gateway de Gerenciamento de Dados para um Armazenamento de Blobs do Azure de preparo, isto é, pela porta 443 e depois carregar os dados no Banco de Dados SQL ou o SQL Data Warehouse do armazenamento de blobs de preparo. Em um fluxo desses, a porta 1433 não precisa estar habilitada.
3.	**Inclua dados de vários armazenamentos de dados no SQL Data Warehouse do Azure via PolyBase.** O SQL Data Warehouse do Azure oferece o PolyBase como um mecanismo de alta taxa de transferência para carregar uma grande quantidade de dados no SQL Data Warehouse. No entanto, isso requer que os dados de origem estejam no Armazenamento de Blobs do Azure e que atendam a alguns critérios adicionais. Ao carregar dados de um armazenamento de dados diferente do Armazenamento de Blobs do Azure, você poderá habilitar a cópia de dados por meio de um Armazenamento de Blobs de preparo provisório do Azure e, nesse caso, o Azure Data Factory executará as transformações necessárias nos dados para garantir que eles atendam aos requisitos de formato de dados do PolyBase e então aproveitará o PolyBase para carregar dados no SQL Data Warehouse. Veja [Usar o PolyBase para carregar dados no SQL Data Warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para ver mais detalhes e exemplos.

### Como funciona a cópia em etapas
Quando você habilita o recurso de preparo, os dados são copiados primeiro do armazenamento de dados de origem para o armazenamento de dados de preparo (traga seu próprio) e, em seguida, copiados do armazenamento de dados de preparo para o armazenamento de dados do coletor. O Azure Data Factory gerenciará automaticamente o fluxo de dois estágios e também limpará os dados temporários do armazenamento de preparo após a conclusão da movimentação de dados.

No **cenário de cópia de nuvem**, onde os armazenamentos de dados de origem e do coletor estão na nuvem e não aproveitam o Gateway de Gerenciamento de Dados, as operações de cópia são executadas pelo **serviço Azure Data Factory**.

![Cópia em etapas - cenário de nuvem](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

Enquanto isso, no **cenário de cópia híbrida**, onde a origem é local e o coletor está na nuvem, a movimentação de dados do armazenamento de dados de origem para o armazenamento de dados de preparo é executada pelo **Data Management Gateway** e a movimentação de dados do armazenamento de dados de preparo para o armazenamento de dados do coletor dados de preparo é executada pelo **serviço Azure Data Factory**.

![Cópia em etapas - cenário híbrido](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Ao habilitar a movimentação de dados usando o armazenamento de preparo, você poderá especificar se deseja que os dados a ser compactado antes de mover dados de repositório de dados de origem para o armazenamento de dados provisório/de preparo e descompactados antes da movimentação de dados do armazenamento de dados provisório/de preparo para o armazenamento de dados do coletor.

A cópia de dados de um armazenamento de dados de nuvem para um armazenamento de dados local ou entre dois armazenamentos de dados locais com armazenamento de preparo não tem suporte neste ponto e deve ser habilitada em breve.

### Configuração
Você pode configurar a configuração **enableStaging** na Atividade de Cópia para especificar se deseja que os dados sejam preparados em um armazenamento de blobs do Azure antes de carregá-los em um armazenamento de dados de destino. Quando você define enableStaging como true, precisa especificar as propriedades adicionais listadas na tabela a seguir. E você precisa criar um Armazenamento do Azure ou o serviço vinculado SAS do Armazenamento do Azure como preparo se ainda não tiver um.

Propriedade | Descrição | Valor padrão | Obrigatório
--------- | ----------- | ------------ | --------
enableStaging | Especifique se você deseja copiar os dados por meio de um armazenamento de preparo provisório. | Falso | Não
linkedServiceName | Especifique o nome de um serviço vinculado [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service), que se refere ao armazenamento do Azure, que será usado como um armazenamento de preparo provisório. <br/><br/> Observe que um Armazenamento do Azure com SAS (Assinatura de Acesso Compartilhado) não pode ser usado para carregar dados no Azure SQL Data Warehouse via PolyBase. Ele pode ser usado em todos os outros cenários. | N/D | Sim, quando enableStaging estiver definido como true. 
path | Especifique o caminho no armazenamento de blobs do Azure que conterá os dados preparados. Se você não fornecer um caminho, o serviço criará um contêiner para armazenar os dados temporários. <br/><br/> Você não precisa especificar o caminho, a menos que esteja usando o Armazenamento do Azure com SAS ou se tiver um requisito rígido no qual os dados temporários devem residir. | N/D | Não
enableCompression | Especifique se os dados devem ser compactados quando movidos do armazenamento de dados de origem para o armazenamento de dados do coletor, para a redução do volume de dados transferidos durante a transmissão. | Falso | Não

Veja um exemplo de definição de uma Atividade de Cópia com as propriedades acima:

	"activities":[  
	{
		"name": "Sample copy activity",
		"type": "Copy",
		"inputs": [{ "name": "OnpremisesSQLServerInput" }],
		"outputs": [{ "name": "AzureSQLDBOutput" }],
		"typeProperties": {
			"source": {
				"type": "SqlSource",
			},
			"sink": {
				"type": "SqlSink"
			},
	    	"enableStaging": true,
			"stagingSettings": {
				"linkedServiceName": "MyStagingBlob",
				"path": "stagingcontainer/path",
				"enableCompression": true
			}
		}
	}
	]


### Impacto de cobrança
Observe que você será cobrado com base nos dois estágios da duração de cópia e seu tipo de cópia respectivamente, o que significa:

- Ao usar o preparo durante uma cópia de nuvem (cópia de dados de um armazenamento de dados em nuvem para outro armazenamento de dados em nuvem, por exemplo, do Azure Data Lake para o Azure SQL Data Warehouse), você será cobrado desta forma: [soma da duração de cópia para a etapa 1 e a etapa 2] x [preço da unidade de cópia de nuvem]
- Ao usar o preparo durante uma cópia híbrida (copiar dados de um armazenamento de dados local para um armazenamento de dados de nuvem; por exemplo, do banco de dados do SQL Server local para o Azure SQL Data Warehouse), você será cobrado desta forma: [duração da cópia híbrida] x [preço da unidade de cópia híbrida] + [duração da cópia de nuvem] x [preço da unidade de cópia de nuvem]


## Considerações sobre origem
### Geral
Certifique-se de que o armazenamento de dados subjacente não esteja sobrecarregado por outras cargas de trabalho em execução nele ou em relação a ele, incluindo, entre outras, a atividade de cópia.

Para armazenamentos de dados da Microsoft, consulte os [tópicos de monitoramento e ajuste](#appendix-data-store-performance-tuning-reference) específicos do armazenamento de dados que podem ajudá-lo a entender as características de desempenho do armazenamento, a minimizar os tempos de resposta e a maximizar a produtividade.

Se você estiver copiando dados do **Armazenamento de Blobs do Azure** para o **Azure SQL Data Warehouse**, considere habilitar o **PolyBase** para melhorar o desempenho. Veja [Usar o PolyBase para carregar dados para o Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md###use-polybase-to-load-data-into-azure-sql-data-warehouse) para ver mais detalhes.


### Armazenamentos de dados baseado em arquivo
*(Inclui Blob do Azure, Azure Data Lake, sistema de arquivos local)*

- **Média de tamanho do arquivo e a contagem de arquivos**: a atividade de cópia transfere dados arquivo por arquivo. Com a mesma quantidade de dados a ser movidos, a produtividade geral será mais lenta se os dados consistirem em um grande número de pequenos arquivos, em vez de um pequeno número de arquivos maiores devido à fase de inicialização para cada arquivo. Portanto, se possível, combine arquivos pequenos em arquivos maiores para obter uma maior taxa de transferência.
- **Formato do arquivo e compactação**: consulte as seções de [considerações sobre serialização/desserialização](#considerations-on-serializationdeserialization) e [considerações sobre compactação](#considerations-on-compression) para conhecer mais maneiras de melhorar o desempenho.
- Além disso, para o cenário **Sistema de Arquivos local** em que o uso de **Gateway de Gerenciamento de Dados** é obrigatório, consulte a seção de [Considerações sobre o gateway](#considerations-on-data-management-gateway).

### Armazenamentos de dados relacionais
*(Inclui o Banco de Dados SQL do Azure, o SQL Data Warehouse do Azure, o Banco de Dados SQL Server, o Oracle Database, o Banco de Dados MySQL, o Banco de Dados DB2, o Banco de Dados Teradata, o Banco de Dados Sybase, o Banco de Dados PostgreSQL)*

- **Padrão de dados**: o esquema de tabela afeta a taxa de transferência de cópia. Para copiar a mesma quantidade de dados, um tamanho de linha grande proporcionará um desempenho melhor do que um tamanho de linha pequeno porque o banco de dados poderá recuperar com mais eficiência menos lotes de dados contendo um menor número de linhas.
- **Consulta ou procedimento armazenado**: otimize a lógica da consulta ou o procedimento armazenado especificado na fonte de atividade de cópia para buscar os dados com mais eficiência.
- Além disso, para **bancos de dados relacionais locais**, como o SQL Server e Oracle, nos quais o uso do **Gateway de Gerenciamento de Dados** é obrigatório, consulte a seção de [Considerações sobre gateway](#considerations-on-data-management-gateway).

## Considerações sobre coletor

### Geral
Certifique-se de que o armazenamento de dados subjacente não esteja sobrecarregado por outras cargas de trabalho em execução nele ou em relação a ele, incluindo, entre outras, a atividade de cópia.

Para armazenamentos de dados da Microsoft, consulte os [tópicos de monitoramento e ajuste](#appendix-data-store-performance-tuning-reference) específicos do armazenamento de dados que podem ajudá-lo a entender as características de desempenho do armazenamento, a minimizar os tempos de resposta e a maximizar a produtividade.

Se você estiver copiando dados do **Armazenamento de Blobs do Azure** para o **Azure SQL Data Warehouse**, considere habilitar o **PolyBase** para melhorar o desempenho. Veja [Usar o PolyBase para carregar dados para o Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md###use-polybase-to-load-data-into-azure-sql-data-warehouse) para ver mais detalhes.


### Armazenamentos de dados baseado em arquivo
*(Inclui Blob do Azure, Azure Data Lake, sistema de arquivos local)*

- **Comportamento de cópia**: se você estiver copiando dados de outro armazenamento de dados baseado em arquivo, a atividade de cópia fornecerá três tipos de comportamento por meio da propriedade "copyBehavior": preservar hierarquia, mesclar hierarquia e mesclar arquivos. Preservar ou nivelar a hierarquia tem pouca ou nenhuma sobrecarga de desempenho, enquanto mesclar arquivos causa sobrecarga adicional de desempenho.
- **Formato do arquivo e compactação**: consulte as seções de [considerações sobre serialização/desserialização](#considerations-on-serializationdeserialization) e [considerações sobre compactação](#considerations-on-compression) para conhecer mais maneiras de melhorar o desempenho.
- Para **Blob do Azure**, atualmente oferecemos suporte apenas para Blobs de bloco para produtividade e transferência de dados otimizadas.
- Além disso, para cenários de **Sistema de Arquivos local** em que o uso de **Gateway de Gerenciamento de Dados** é obrigatório, consulte a seção de [considerações sobre o gateway](#considerations-on-data-management-gateway).

### Armazenamentos de dados relacionais
*(Inclui o Banco de Dados SQL do Azure, Azure SQL Data Warehouse, Banco de Dados do SQL Server)*

- **Comportamento de cópia**: dependendo das propriedades configuradas para "sqlSink", a atividade de cópia irá gravar os dados no banco de dados de destino de maneiras diferentes:
	- Por padrão, o serviço de movimentação de dados usa a API de cópia em massa para inserir dados no modo de anexação, o que fornece o melhor desempenho.
	- Se você configurar um procedimento armazenado no coletor, o banco de dados aplicará os dados linha por linha, em vez de realizar carregamento em massa, de modo que o desempenho cairá significativamente. Se o tamanho dos dados for grande, quando aplicável, considere alternar para usar a propriedade "sqlWriterCleanupScript" (veja abaixo) em vez disso.
	- Se você configurar a propriedade "sqlWriterCleanupScript", para cada execução de atividade de cópia, o serviço irá disparar o script primeiro e então usará a API de cópia em massa para inserir os dados. Por exemplo, para substituir a tabela inteira com os dados mais recentes, você pode especificar um script para excluir todos os registros primeiro, antes de carregar em massa os novos dados da origem.
- **Padrão de dados e tamanho do lote**:
	- O esquema da tabela afetará a taxa de transferência de cópia. Para copiar a mesma quantidade de dados, um tamanho de linha grande proporcionará um desempenho melhor do que um tamanho de linha pequeno, pois o banco de dados poderá confirmar com mais eficiência menos lotes de dados.
	- A atividade de cópia insere dados em uma série de lotes, em que o número de linhas contido em um lote podem ser definido usando a propriedade "writeBatchSize". Se os dados tiverem linhas de tamanho pequeno, você poderá definir a propriedade "writeBatchSize" com um valor mais alto para se beneficiar de menos sobrecarga de lote e aumentar a produtividade. Se o tamanho da linha dos seus dados for grande, tenha cuidado ao aumentar o writeBatchSize – um valor grande pode levar a uma falha de cópia devido à sobrecarga do banco de dados.
- Além disso, para **bancos de dados relacionais locais**, como o SQL Server e Oracle, nos quais o uso do **Gateway de Gerenciamento de Dados** é obrigatório, consulte a seção de [considerações sobre gateway](#considerations-on-data-management-gateway).


### Repositórios NoSQL
*(Incluindo Tabela do Azure, Banco de Dados de Documentos do Azure)*

- Para a **Tabela do Azure**:
	- **Partição**: gravar dados em partições intercaladas degrada drasticamente o desempenho. Você pode optar por solicitar seus dados de origem por chave de partição para que os dados sejam inseridos com eficiência partição após partição, ou você pode ajustar a lógica para gravar os dados em uma única partição.
- Para o **Banco de Dados de Documentos Azure**:
	- **Tamanho do lote**: a propriedade "writeBatchSize" indica o número de solicitações paralelas para o serviço de Banco de Dados de Documentos para criar documentos. Você pode esperar um melhor desempenho quando você aumenta “writeBatchSize”, pois mais solicitações paralelas para Banco de Dados de Documentos são enviadas. No entanto, tenha cuidado com a limitação ao escrever em Banco de Dados de Documentos (mensagem de erro "A taxa de solicitação é grande"). A limitação pode ocorrer devido a uma série de fatores, incluindo o tamanho dos documentos, o número de termos incluídos e a política de indexação da coleção de destino. Para obter maior taxa de transferência de cópia, considere usar uma coleção melhor (por exemplo, S3).

## Considerações sobre serialização/desserialização
Serialização e desserialização podem acontecer quando o conjunto de dados de entrada ou saída é um arquivo. Atualmente, a atividade de cópia tem suporte para os formatos de dados Avro e Texto (por exemplo, TSV e CSV).

**Comportamentos de cópia:**

- Ao copiar arquivos entre armazenamentos de dados baseados em arquivo:
	- Quando os conjuntos de dados de entrada e saída estiverem com as mesmas configurações de formato de arquivo ou não tiverem essas configurações, o serviço de movimentação de dados executará uma cópia binária sem executar qualquer serialização/desserialização. Portanto, você deve observar uma taxa de transferência melhor em comparação ao cenário quando as configurações de formato de arquivo de fonte/coletor forem diferentes.
	- Quando os conjuntos de dados de entrada e de saída estão em formato de texto enquanto apenas o tipo de codificação é diferente, o serviço de movimentação de dados apenas realiza a conversão de codificação sem executar qualquer serialização/desserialização, resultando em certa sobrecarga de desempenho em comparação à cópia binária.
	- Quando os conjuntos de dados de entrada e saída tiverem formatos de arquivo diferentes ou configurações diferentes, como delimitadores, o serviço de movimentação de dados irá desserializar os dados de origem para transmitir, transformar e então serializar para o formato de saída desejado. Isso resultará em uma sobrecarga desempenho muito mais significativa em comparação aos cenários anteriores.
- Ao copiar arquivos de/para um armazenamento de dados não baseado em arquivo (digamos, um armazenamento baseado em arquivos para um armazenamento relacional), a etapa de serialização ou desserialização será necessária e isso resultará em uma sobrecarga significativa no desempenho.

**Formato de arquivo:** a opção de formato de arquivo pode afetar o desempenho de cópia. Por exemplo, Avro é um formato binário compacto que armazena metadados com dados e tem amplo suporte no ecossistema do Hadoop para processamento e consulta. No entanto, Avro é mais caro para serialização/desserialização, o que resulta em menor taxa de transferência de cópia em comparação ao formato de texto. A escolha qual formato de arquivo usar ao longo do fluxo de processamento deve ser feita de maneira holística, começando com a forma como os dados são armazenados nos armazenamentos de dados de origem ou a serem extraídos de sistemas externos, o melhor formato para armazenamento, processamento analítico e consulta, e em qual formato os dados devem ser exportados para data marts para ferramentas de relatório e visualização. Às vezes, um formato de arquivo abaixo do é ideal para desempenho de leitura e gravação pode ser adequado considerando o processo analítico geral.

## Considerações sobre compactação
Quando o conjunto de dados de entrada ou saída é um arquivo, você pode configurar a atividade de cópia para executar a compactação ou a descompactação conforme ela grava dados no destino. Ao habilitar a compactação, você faz uma compensação entre E/S e CPU: a compactação dos dados consumirá mais recursos de computação, mas reduzirá E/S de rede e armazenamento, o que pode aumentar a produtividade geral de cópia, dependendo dos seus dados.

**Codec:** há suporte para tipos de compactação GZIP, BZIP2 e Deflate. Todos os três tipos podem ser consumidos pelo HDInsight do Azure para processamento. Cada codec de compactação tem sua particularidade. Por exemplo, BZIP2 tem a menor taxa de transferência de cópia, mas você obter o melhor desempenho de consulta de Hive, uma vez que ele pode ser dividido para processamento; GZIP fornece a opção mais equilibrada e é usado com mais frequência. Você deve escolher o codec mais adequado para seu cenário de ponta a ponta.

**Nível:** para cada codec de compactação, você pode escolher entre duas opções – compactação mais rápida e compactação ideal. A opção compactada mais rápida compacta os dados o mais rapidamente possível, mesmo que o arquivo resultante não tenha compactação ideal. A opção de compactação ideal levará mais tempo na compactação, produzindo a quantidade mínima de dados. Você pode testar as duas opções para qual fornece o melhor desempenho no seu caso.

**Uma consideração:** para copiar tamanhos grandes de dados entre o armazenamento local e a nuvem, onde a rede corporativa de largura de banda e o Azure costumam ser o fator limitante, e você deseja que os conjuntos de dados de entrada e de saída estejam em formato descompactado, pode considerar o uso de um **Blob do Azure provisório** com compactação. Mais especificamente, é possível dividir uma atividade de cópia única em duas atividades de cópia: a primeira atividade de cópia copia da origem para o blob provisório ou de preparação em formato compactado, e a segunda atividade de cópia copia dados compactados da preparação e descompacta durante a gravação de coletor.

## Considerações sobre mapeamento de coluna
A propriedade "columnMappings" na atividade de cópia pode ser usada para mapear todas as colunas de entrada para as colunas de saída ou um subconjunto dessas colunas. Depois de ler os dados de origem, o serviço de movimentação de dados precisa executar o mapeamento de coluna nos dados antes de gravá-los no coletor. Esse processamento extra reduz a taxa de transferência de cópia.

Se o armazenamento de dados de origem tiver capacidade de consulta, por exemplo, um armazenamento relacional, como o SQL/SQL Server do Azure ou o repositório NoSQL como Tabela do Azure/Banco de Dados de Documentos Azure, você pode considerar enviar a lógica de filtragem/reordenação de coluna para a propriedade de consulta, em vez de usar mapeamento de coluna, o que resulta em fazer a projeção durante a leitura de dados do armazenamento de dados de origem e é muito mais eficiente.

## Considerações sobre o Gateway de gerenciamento de dados
Para obter recomendações de configuração de gateway, consulte [Considerações para usar o Gateway de gerenciamento de dados](data-factory-move-data-between-onprem-and-cloud.md#Considerations-for-using-Data-Management-Gateway).

**Ambiente de máquina de gateway:** é recomendável que você use um computador dedicado para hospedar o Gateway de Gerenciamento de Dados. Use ferramentas como o PerfMon para examinar o uso de CPU, memória e largura de banda durante uma operação de cópia em sua máquina de gateway. Alterne para uma máquina mais potente se CPU, memória ou largura de banda da rede se tornar um gargalo.

**Execuções de atividade de cópia simultânea:** uma única instância do Gateway de gerenciamento de dados pode atender a várias execuções de atividade de cópia ao mesmo tempo, por exemplo, um gateway pode executar vários trabalhos de cópia simultaneamente (o número de trabalhos simultâneos é calculado com base na configuração de hardware do computador do gateway). Trabalhos de cópia adicionais são colocados na fila até que sejam capturados pelo gateway ou até que o trabalho expire, o que ocorrer primeiro. Para evitar contenção de recursos no gateway, você pode preparar o agendamento de suas atividades para reduzir a quantidade de trabalhos de cópia na fila de uma só vez, ou considere dividir a carga em vários gateways.


## Outras considerações
Se o tamanho dos dados a serem copiados for muito grande, você poderá ajustar sua lógica de negócios para particionar mais os dados usando o mecanismo de divisão do Azure Data Factory e agendar a atividade de cópia com mais frequência para reduzir o tamanho dos dados para cada execução da atividade de cópia.

Tenha cuidado com o número de conjuntos de dados e atividades de cópia que chegam ao mesmo armazenamento de dados em um determinado momento. Um grande número de trabalhos de cópia simultâneos pode restringir um armazenamento de dados e levar a degradação do desempenho, repetições internas do trabalho de cópia e, em alguns casos, falhas de execução.

## Estudo de caso – cópia do SQL Server local para Blob do Azure
**Cenário:** um pipeline é criado para copiar dados de um local do SQL Server para Blob do Azure no formato CSV. Para tornar a cópia mais rápida, é especificado que os arquivos CSV sejam compactados no formato BZIP2.

**Teste e análise:** é observado que a produtividade da atividade de cópia é menor que 2 MB/s, muito mais lento do que o parâmetro de comparação de desempenho.

**Análise e ajuste de desempenho:** para solucionar o problema de desempenho, vamos primeiro explicar passo a passo como os dados são processados e movidos:

1.	**Ler dados:** o gateway abre a conexão com o SQL Server e envia a consulta. O SQL Server responde enviando a transmissão de dados para o gateway pela intranet.
2.	O gateway **serializa** a transmissão de dados no formato CSV e **compacta** os dados para uma transmissão BZIP2.
3.	**Gravar dados:** o gateway carrega a transmissão BZIP2 para o Blob do Azure pela Internet.

Como você pode ver, os dados estão sendo processados e movidos de uma maneira de transmissão sequencial: SQL Server -> LAN -> Gateway -> WAN -> Blob do Azure, **o desempenho geral é restrito pela taxa de transferência mínima através do pipeline**.

![fluxo de dados](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Um ou mais dos seguintes fatores pode ser o gargalo de desempenho:

1.	**Origem:** o próprio SQL Server tem baixa taxa de transferência devido a cargas pesadas.
2.	**Gateway de Gerenciamento de Dados:**
	1.	**LAN:** o gateway está longe do SQL Server com uma conexão de baixa largura de banda
	2.	A **carga no computador do gateway** alcançou suas limitações para realizar o seguinte:
		1.	**Serialização:** a transmissão de dados de serialização para CSV tem a taxa de transferência lenta
		2.	**Compactação:** o codec de compactação lenta foi escolhido (por exemplo, BZIP2, que é de 2,8 MB/s com Core i7)
	3.	**WAN:** baixa largura de banda entre a rede corporativa e o Azure (ex.: T1 = 1.544 kbps, T2 = 6.312 kbps)
4.	**Coletor:** o Blob do Azure tem baixa taxa de transferência (embora muito improvável, já que seu SLA garante um mínimo de 60 MB/s).

Nesse caso, a compactação de dados BZIP2 poderia estar desacelerando todo o pipeline. Alternar para o codec de compactação GZIP pode aliviar esse gargalo.


## Estudo de caso - Cópia paralela  

**Cenário I:** copiar 1000 arquivos de 1 MB do Sistema de Arquivos local para o Armazenamento de Blobs do Azure

**Ajuste de análise e de desempenho:** suponha que você tenha instalado o Gateway de Gerenciamento de Dados em um computador com quatro núcleos, o Data Factory usará 16 cópias paralelas para mover os arquivos do Sistema de Arquivos para o Blob do Azure simultaneamente. Isso deve resultar em boa taxa de transferência. Você também pode especificar a contagem de cópias paralelas explicitamente, se desejar. Ao copiar um grande número de arquivos pequenos, cópias paralelas ajudarão enormemente a taxa de transferência, utilizando os recursos envolvidos com mais eficiência.

![Cenário 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Cenário II:** copiar 20 blobs de 500 MB de Armazenamento de Blobs do Azure para a Análise do Repositório Azure Data Lake e ajustar o desempenho.

**Ajuste de análise e de desempenho:** para este cenário, por padrão, o Data Factory copiará os dados do Blob do Azure para o Azure Data Lake usando uma cópia simples (parallelCopies como 1) e uma única unidade de movimentação de dados de nuvem. A taxa de transferência que você observará seria mais próxima do que é declarado na [seção de referência de desempenho](#performance-reference) acima.

![Cenário 2](./media/data-factory-copy-activity-performance/scenario-2.png)

Quando o tamanho do arquivo individual é maior que dezenas de MBs e o volume total for grande, aumentar parallelCopies não resultará em um melhor desempenho de cópia devido às limitações de recursos de uma única unidade de movimentação de dados de nuvem. Em vez disso, você deve especificar mais unidades de movimentação de dados de nuvem para obter mais recursos para realizar a movimentação de dados. Não especifique um valor para a propriedade parallelCopies de forma que o Data Factory manuseie o paralelismo para você. Nesse caso, especificar cloudDataMovementUnits como 4 resultará em uma taxa de transferência de cerca de 4 vezes.

![Cenário 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## Referência de ajuste de desempenho de armazenamento de dados
Aqui estão algumas referências de monitoramento e ajuste de desempenho para alguns dos armazenamentos de dados com suporte:

- Armazenamento do Azure (incluindo o Blob do Azure e a Tabela do Azure): [metas de escalabilidade do Armazenamento do Azure](../storage/storage-scalability-targets.md) e [Lista de verificação de escalabilidade e desempenho do Armazenamento do Azure](../storage//storage-performance-checklist.md)
- Banco de dados SQL Azure: é possível [monitorar o desempenho](../sql-database/sql-database-service-tiers.md#monitoring-performance) e verificar o percentual de DTU (Unidade de Transação de Banco de Dados).
- Azure SQL Data Warehouse: sua capacidade é medida por DWUs (Unidades de Data Warehouse). Consulte [Desempenho e escala elásticos com o SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
- Banco de Dados de Documentos do Azure: [nível de desempenho no Banco de Dados de Documentos](../documentdb/documentdb-performance-levels.md).
- SQL Server local: [monitoramento e ajuste de desempenho](https://msdn.microsoft.com/library/ms189081.aspx).
- Servidor de arquivos local: [ajuste de desempenho para servidores de arquivos](https://msdn.microsoft.com/library/dn567661.aspx)

<!---HONumber=AcomDC_0706_2016-->