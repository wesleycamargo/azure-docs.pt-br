<properties
	pageTitle="Guia de desempenho e ajuste da atividade de cópia"
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
	ms.date="12/17/2015"
	ms.author="spelluru"/>


# Guia de desempenho e ajuste da atividade de cópia
Este artigo descreve os principais fatores que afetam o desempenho de movimentação de dados (atividade de cópia) no Azure Data Factory. Ele também lista o desempenho observado durante os testes internos e discute várias maneiras de otimizar o desempenho da Atividade de Cópia.

## Visão geral de movimentação de dados no Data Factory do Azure
A Atividade de Cópia executa a movimentação de dados no Azure Data Factory e é alimentada por um [serviço de movimentação de dados globalmente disponível](data-factory-data-movement-activities.md#global) que pode copiar dados entre [vários armazenamentos de dados](data-factory-data-movement-activities.md#supported-data-stores-for-copy-activity) de forma segura, confiável, escalonável e de bom desempenho. O serviço de movimentação de dados escolhe automaticamente a região ideal para executar a operação de movimentação de dados com base nos armazenamentos de dados de origem e coletor. Atualmente, a região usada é a mais próxima ao armazenamento de dados do coletor.

Vamos entender como a movimentação de dados ocorre em cenários diferentes.

### Copiando dados entre dois armazenamentos de dados em nuvem
Quando os armazenamentos de dados de origem e coletores (destino) residem na nuvem, a atividade de cópia passa pelas fases a seguir para copiar/mover os dados da origem para o coletor.

1.	Lê dados do armazenamento de dados de origem
2.	Executa a serialização/desserialização, a compactação/descompactação, o mapeamento de coluna e a conversão de tipo baseado nas configurações de conjuntos de dados de entrada e de saída e na atividade de cópia.
3.	Grava dados no armazenamento de dados de destino

![Copiando dados entre dois armazenamentos de dados em nuvem](./media/data-factory-copy-activity-performance/copy-data-between-two-cloud-stores.png)

**Observação:** formas de linha pontilhada (compactação, mapeamento de coluna, etc.) são recursos que podem ou não podem ser aproveitados em seu caso de uso.


### Copiando dados entre um armazenamento de dados local e um armazenamento de dados na nuvem
Para [mover dados entre armazenamentos de dados locais e armazenamento de dados de nuvem](data-factory-move-data-between-onprem-and-cloud.md), você precisará instalar o gateway de gerenciamento de dados, que é um agente que permite a movimentação e o processamento de dados híbridos em seu computador local. Neste cenário, a serialização/desserialização, compactação/descompactação, mapeamento de coluna e conversão de tipo com base nas configurações do conjunto de dados de entrada, conjunto de dados de saída e a atividade de cópia são executados pelo Gateway de gerenciamento de dados.

![Copiar dados entre um armazenamento de dados local e em nuvem](./media/data-factory-copy-activity-performance/copy-data-between-onprem-and-cloud.png)

## Etapas de ajuste de desempenho
As etapas típicas que sugerimos que você faça para ajustar o desempenho de sua solução Azure Data Factory com atividade de cópia são listadas abaixo.

1.	**Estabelecer uma linha de base.** Durante a fase de desenvolvimento, teste seu pipeline com atividade de cópia em relação aos dados de uma amostra representativa. Você pode aproveitar o [modelo de divisão](data-factory-scheduling-and-execution.md#time-series-datasets-and-data-slices) do Azure Data Factory para limitar a quantidade de dados com que você está trabalhando.

	Colete o tempo de execução e características de desempenho marcando a folha “fatia de dados” do conjunto de dados de saída e a folha “detalhes de execução de atividade” no portal de Visualização do Azure, que mostra a duração da atividade de cópia e o tamanho dos dados copiados.

	![Detalhes da execução da atividade](./media/data-factory-copy-activity-performance/activity-run-details.png)

	Você pode comparar o desempenho e as configurações do seu cenário com a [referência desempenho](#performance-reference) da atividade de cópia publicadas abaixo com base em observações internas.
2. **Diagnóstico e otimização de desempenho** Se o desempenho observado estiver abaixo das suas expectativas, você precisa identificar gargalos de desempenho e efetuar otimizações para remover ou reduzir o impacto de afunilamentos. Uma descrição completa do diagnóstico de desempenho está além do escopo deste artigo, mas estamos registrando algumas considerações comuns aqui da seguinte maneira.
	- [Fonte](#considerations-on-source)
	- [Coletor](#considerations-on-sink)
	- [Serialização/desserialização](#considerations-on-serializationdeserialization)
	- [Compactação](#considerations-on-compression)
	- [Mapeamento de coluna](#considerations-on-column-mapping)
	- [Gateway de gerenciamento de dados](#considerations-on-data-management-gateway)
	- [Outras considerações](#other-considerations)
3. **Expandir a configuração para todos os dados** Quando você estiver satisfeito com os resultados e o desempenho da execução, poderá expandir a definição do conjunto de dados e o período ativo do pipeline para cobrir todos os dados na imagem.

## Referência de desempenho
> [AZURE.IMPORTANT]**Isenção de responsabilidade:** os dados abaixo foram publicados com o único propósito de orientação e planejamento alto nível. Eles pressupõem que a largura de banda, o hardware, a configuração, etc. são os melhores em sua classe. Use apenas como referência. A taxa de transferência de movimentação de dados que você observar será afetada por uma variedade de variáveis. Consulte as seções mais tarde para saber mais sobre como você pode ajustar e obter o melhor desempenho para suas necessidades de movimentação de dados. Esses dados serão atualizados como e quando forem adicionados recursos e melhorias de aumento de desempenho.

![Matriz de desempenho](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

> [AZURE.NOTE]**Em breve:** estamos melhorando as características de desempenho de base e logo você verá números de taxa de transferência melhores e maiores na tabela acima.

Pontos a serem observados:

- A taxa de transferência é calculada usando a seguinte fórmula: [tamanho dos dados lidos da origem]/[duração da execução de atividade de cópia]
- O conjunto de dados [TPC-H](http://www.tpc.org/tpch/) foi aproveitado para calcular números acima.
- No caso de armazenamentos de dados do Microsoft Azure, origem e coletor estão na mesma região do Azure.
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

## Considerações sobre origem
### Geral
Certifique-se de que o armazenamento de dados subjacente não esteja sobrecarregado por outras cargas de trabalho em execução nele ou em relação a ele, incluindo, entre outras, a atividade de cópia.

Para armazenamentos de dados da Microsoft, consulte os [tópicos de monitoramento e ajuste](#appendix-data-store-performance-tuning-reference) específicos do armazenamento de dados que podem ajudá-lo a entender as características de desempenho do armazenamento, a minimizar os tempos de resposta e a maximizar a produtividade.

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

## Apêndice – referência de ajuste de desempenho de armazenamento de dados
Aqui estão algumas referências de monitoramento e ajuste de desempenho para alguns dos armazenamentos de dados com suporte:

- Armazenamento do Azure (incluindo o Blob do Azure e a Tabela do Azure): [metas de escalabilidade do Armazenamento do Azure](../storage/storage-scalability-targets.md) e [Lista de verificação de escalabilidade e desempenho do Armazenamento do Azure](../storage//storage-performance-checklist.md)
- Banco de dados SQL Azure: você pode [monitorar o desempenho](../sql-database/sql-database-service-tiers.md?rnd=1#monitoring-performance) e verificar a porcentagem de unidade de DTU (taxa de transferência do banco de dados).
- Azure SQL Data Warehouse: sua capacidade é medida por DWUs (Unidades de Data Warehouse). Consulte [Desempenho e escala elásticos com o SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-performance-scale/).
- Banco de Dados de Documentos do Azure: [nível de desempenho no Banco de Dados de Documentos](../documentdb/documentdb-performance-levels/).
- SQL Server local: [monitoramento e ajuste de desempenho](https://msdn.microsoft.com/library/ms189081.aspx).
- Servidor de arquivos local: [ajuste de desempenho para servidores de arquivos](https://msdn.microsoft.com/library/dn567661.aspx)

<!---HONumber=AcomDC_1223_2015-->