<properties
   pageTitle="Ajustando o desempenho de qQuery e a agregação de dados com o Elasticsearch no Azure | Microsoft Azure"
   description="Um resumo de considerações sobre a otimização do desempenho de consulta e pesquisa para o Elasticsearch."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Ajustando o desempenho de qQuery e a agregação de dados com o Elasticsearch no Azure

Este artigo faz [parte de uma série](guidance-elasticsearch.md).

Um grande motivo para usar o Elasticsearch é dar suporte a pesquisas por dados. Os usuários devem ser capazes de localizar rapidamente as informações que estão procurando. Além disso, o sistema deve permitir que os usuários façam perguntas sobre dados, busquem correlações e cheguem a conclusões que podem gerar decisões de negócios. Esse processamento é o que diferencia dados de informações.

Este documento resume as opções que você pode considerar ao determinar a melhor maneira de otimizar seu sistema em relação ao desempenho de consulta e pesquisa.

Todas as recomendações de desempenho dependem principalmente dos cenários que se aplicam à sua situação, do volume de dados que você estiver indexando e da velocidade em que seus aplicativos e usuários consultam os dados. Você deve testar cuidadosamente os resultados de qualquer alteração na configuração ou na estrutura de indexação usando seus próprios dados e cargas de trabalho a fim de avaliar os benefícios para seus cenários específicos. Para esse fim, este documento também descreve um número de benchmarks que foram executados para um cenário específico implementado usando configurações diferentes. Você pode adaptar a abordagem usada para avaliar o desempenho de seus próprios sistemas.

## Índice e considerações de desempenho de consulta

Esta seção descreve alguns fatores comuns que você deve avaliar ao criar índices que precisam dar suporte à consulta e à pesquisa rápida.

### Armazenando vários tipos em um índice

Um índice de Elasticsearch pode conter vários tipos. É melhor evitar essa abordagem e criar um índice separado para cada tipo. Considere os seguintes pontos:

- Tipos diferentes podem especificar analisadores diferentes e nem sempre fica claro qual analisador o Elasticsearch deve usar se uma consulta é executada no nível do índice em vez de no nível do tipo. Confira [Evitando armadilhas de tipo](https://www.elastic.co/guide/en/elasticsearch/guide/current/mapping.html#_avoiding_type_gotchas) para obter detalhes.

- Fragmentos de índices que contêm vários tipos provavelmente serão maiores do que aqueles para os índices que contêm um único tipo. Quanto maior um fragmento, mais esforço é exigido do Elasticsearch para filtrar os dados ao executar consultas.

- Se houver uma diferença significativa entre os volumes de dados para os tipos, as informações de um tipo podem ficar distribuídas esporadicamente entre vários fragmentos, reduzindo a eficiência de pesquisas que recuperam esses dados.

![](./media/guidance-elasticsearch/query-performance1.png)

***Figura 1. Os efeitos do compartilhamento de um índice entre tipos***

A Figura 1 ilustra essa abordagem. Na parte superior do diagrama, o mesmo índice é compartilhado por documentos do tipo A e do tipo B. Há mais documentos do tipo A que do tipo B. As pesquisas pelo tipo A envolverão a consulta de todos os quatro fragmentos. A parte inferior do diagrama mostra o efeito no caso de índices separados serem criados para cada tipo. Nesse caso, a busca pelo tipo A somente exigirá acesso a dois fragmentos.

Pequenos fragmentos podem ter distribuição mais uniforme que os grandes fragmentos, tornando mais fácil para Elasticsearch distribuir a carga entre os nós.

Tipos diferentes podem ter diferentes períodos de retenção. Pode ser difícil arquivar dados antigos que compartilham fragmentos com dados ativos.

No entanto, em algumas circunstâncias, o compartilhamento de um índice entre tipos pode ser eficiente se:

- As pesquisas abrangem regularmente tipos mantidos no mesmo índice.

- Cada tipo tem apenas um pequeno número de documentos. A manutenção de um conjunto separado de fragmentos para cada tipo pode se tornar uma sobrecarga significativa nesse caso.

### Otimizando os tipos de índice

Um índice de Elasticsearch contém uma cópia dos documentos JSON originais que foram usados para preenchê-lo. Essa informação é mantida no campo [*Source*](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-source-field.html#mapping-source-field) de cada item indexado. Esses dados não são pesquisáveis, mas por padrão são retornados pelas solicitações *get* e *search*. No entanto, esse campo gera sobrecarga e ocupa o armazenamento, aumentando os fragmentos e o volume de E/S realizadas. Você pode desabilitar o campo *Source* baseado em tipo:

```http
PUT my_index
{
    "mappings": {
		"my_type": {
			"_source": {
				"enabled": false
			}
		}
	}
}
```

Desabilitar este campo também remove a capacidade de executar as seguintes operações:

- Atualizando dados no índice usando a API *de atualização*.
- Executando pesquisas que retornam dados realçados.
- Reindexando de um índice do Elasticsearch diretamente para outro.
- Alterando mapeamentos ou configurações de análise.
- Depurando consultas com a exibição do documento original.
 
### Reindexando dados

Por fim, o número de fragmentos disponíveis para um índice determina a capacidade dele. Você pode usar uma estimativa inicial (e ponderada) de quantos fragmentos serão necessários, mas deve sempre considerar a estratégia de reindexação de documento antecipadamente. Em muitos casos, a reindexação pode ser uma tarefa planejada, conforme os dados vão crescendo. Não convém alocar um grande número de fragmentos para um índice inicialmente, para fins de otimização de pesquisa, mas aloque novos fragmentos conforme o volume de dados vai se expandindo. Em outros casos, talvez haja a necessidade de reindexação de forma avulsa se suas estimativas sobre o crescimento do volume de dados simplesmente não puderem ser confiáveis.

> [AZURE.NOTE] A reindexação pode não ser necessária para dados que ficam ultrapassados rapidamente. Nesse caso, um aplicativo pode criar um novo índice para cada período de tempo. Exemplos incluem logs de desempenho ou dados de auditoria que podem ser armazenados em um índice atualizado diariamente.

A reindexação envolve efetivamente a criação de um novo índice dos dados de um antigo e a remoção deste. Se um índice for grande, esse processo poderá levar tempo e talvez seja necessário garantir que os dados permaneçam pesquisáveis durante esse período. Por esse motivo, você deve criar um [alias para cada índice](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html) e as consultas devem recuperar dados por meio desses aliases. Durante a reindexação, mantenha o alias apontando para o índice antigo e alterne-o para referenciar o novo índice após a conclusão da reindexação. Essa abordagem também é útil para acessar os dados baseados em tempo, que cria um novo índice a cada dia. Para acessar os dados atuais, use um alias que muda para o novo índice quando ele é criado.

### Gerenciando mapeamentos

O Elasticsearch usa mapeamentos para determinar como interpretar os dados que ocorrem em cada campo em um documento. Cada tipo tem seu próprio mapeamento, que efetivamente define um esquema para ele. O Elasticsearch usa essas informações para gerar índices invertidos para cada campo nos documentos em um tipo. Em qualquer documento, cada campo tem um tipo de dados (como *string*, *date* ou *long*) e um valor. Você pode especificar os mapeamentos para um índice quando o índice é criado pela primeira vez, ou isso pode ser inferido pelo Elasticsearch quando novos documentos são adicionados a um tipo. No entanto, considere os seguintes pontos:

- Os mapeamentos gerados dinamicamente podem causar erros dependendo de como os campos são interpretados quando os documentos são adicionados a um índice. Por exemplo, o documento 1 pode conter um campo A que contém um número e faz com que o Elasticsearch adicione um mapeamento que especifica que esse campo é um *long*. Se um documento subsequente for adicionado no campo que contém dados não numéricos, ele falhará. Nesse caso, o campo A provavelmente foi interpretado como uma cadeia de caracteres quando o primeiro documento foi adicionado. As especificação desse mapeamento quando o índice é criado pode ajudar a evitar esses problemas.

- Crie documentos para evitar a geração de mapeamentos excessivamente grandes, pois isso pode criar uma sobrecarga significativa durante as pesquisas, consumir muita memória e também fazer com que as consultas falhem na localização dos dados. Adote uma convenção de nomenclatura consistente para os campos nos documentos que compartilham o mesmo tipo. Por exemplo, não use nomes de campo como "nome", "Nome" e "PrimeiroNome" em diferentes documentos. Use o mesmo nome de campo em cada documento. Além disso, não tente usar valores como chaves (essa é uma abordagem comum em bancos de dados coluna-família, mas pode causar falhas e ineficiências com o Elasticsearch.) Para saber mais, confira [Mapping Explosion](https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion).

- Use *not\_analyzed* para evitar a geração de tokens quando apropriado. Por exemplo, se um documento contiver um campo de cadeia de caracteres chamado *dados* que contém o valor "ABC-DEF", você poderá tentar executar uma pesquisa para todos os documentos que correspondem a esse valor da seguinte maneira:

```http
GET /myindex/mydata/_search
{
	"query" : {
		"filtered" : {
			"filter" : {
				"term" : {
					"data" : "ABC-DEF"
				}
			}
		}
	}
}
```

No entanto, essa pesquisa não retornará os resultados esperados devido à maneira como a cadeia de caracteres ABC-DEF gera token quando indexada; ele será efetivamente dividida em dois tokens, ABC e DEF, por um hífen. Esse recurso foi projetado para dar suporte à pesquisa de texto completo, mas se você quiser que a cadeia de caracteres seja interpretada como um único item atômico, deverá desabilitar a geração de tokens quando o documento for adicionado ao índice. Você pode usar um mapeamento como este:

```http
PUT /myindex
{
	"mappings" : {
		"mydata" : {
			"properties" : {
				"data" : {
					"type" : "string",
					"index" : "not_analyzed"
				}
			}
		}
	}
}
```

Para saber mais, confira [Localizando valores exatos](https://www.elastic.co/guide/en/elasticsearch/guide/current/_finding_exact_values.html#_term_filter_with_text).

### Usando valores de documento

Muitas consultas e agregações exigem que os dados sejam classificados como parte da operação de pesquisa. A classificação exige a capacidade de mapear um ou mais termos para uma lista de documentos. Para ajudar nesse processo, o Elasticsearch pode carregar todos os valores de um campo usado como uma chave de classificação na memória. Essas informações são conhecidas como *fielddata*. A intenção é que o caching de fielddata na memória resulte em menos E/S e possa ser mais rápido do que ler repetidamente os mesmos dados no disco. No entanto, se um campo tem alta cardinalidade, o armazenamento de fielddata na memória pode consumir muito espaço de heap, possivelmente afetando a capacidade de executar outras operações simultâneas ou até mesmo esgotando o armazenamento, fazendo com que o Elasticsearch venha a falhar.

Como uma abordagem alternativa, o Elasticsearch também dá suporte a *valores doc*. Um valor de documento é semelhante a um item de fielddata na memória, exceto pelo fato de ele ser armazenado em disco e criado quando os dados são armazenados em um índice (os fielddata são criados dinamicamente quando uma consulta é executada.) Os valores de doc não consomem espaço de heap e, portanto, são úteis para consultas que classificam ou agregam dados em campos que podem conter um número muito grande de valores exclusivos. Além disso, a redução da pressão na pilha pode ajudar a compensar as diferenças de desempenho entre a recuperação de dados do disco e a leitura da memória. A coleta de lixo deve ocorrer com menos frequência e outras operações simultâneas que utilizam memória devem ser menos afetadas.

Habilite ou desabilite os valores do documento baseado na propriedade em um índice usando o atributo *doc\_values*, conforme mostrado no exemplo abaixo:

```http
PUT /myindex
{
	"mappings" : {
		"mydata" : {
			"properties" : {
				"data" : {
					...
				"doc_values": true
				}
			}
		}
	}
}
```

> [AZURE.NOTE] Os valores de documento são habilitados por padrão com a versão 2.0.0 e posteriores do Elasticsearch.

O impacto exato de usar valores de documento provavelmente será altamente específico para seus próprios cenários de dados e consulta; portanto, esteja preparado para realizar testes de desempenho para estabelecer sua utilidade. Você também deve observar que os valores de documento não funcionam com campos de cadeia de caracteres analisados. Para saber mais, confira [Valores doc](https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html#doc-values).

### Usando nós de cliente

Todas as consultas são processadas pelo nó que recebe a solicitação primeiro. Esse nó envia solicitações adicionais para todos os outros nós que contêm fragmentos dos índices que estão sendo consultados e acumula os resultados para retornar a resposta. Se uma consulta envolve a agregação de dados ou a execução de cálculos complexos, o nó inicial é responsável por executar o processamento apropriado. Se o sistema tem que dar suporte a um número relativamente pequeno de consultas complexas, considere criar um pool de nós cliente para aliviar a carga dos nós de dados. Por outro lado, se seu sistema tiver que lidar com um grande número de consultas simples, envie essas solicitações diretamente para os nós de dados e use um balanceador de carga para distribuir as solicitações uniformemente.

### Usando réplicas para reduzir a contenção de consulta

Uma estratégia comum para melhorar o desempenho das consultas é criar várias réplicas de cada índice. As operações de recuperação de dados podem ser atendidas com a busca de dados de uma réplica. No entanto, essa estratégia pode afetar gravemente o desempenho das operações de ingestão de dados e, portanto, ela precisa ser usada com cuidado em cenários que envolvem cargas de trabalho mistas. Além disso, essa estratégia só funciona se as réplicas são distribuídas entre os nós e não competem por recursos com os principais fragmentos que fazem parte do mesmo índice. Lembre-se de que é possível aumentar ou diminuir o número de réplicas de um índice dinamicamente.

### Usando o cache de solicitação de fragmento

O Elasticsearch pode armazenar em cache os dados locais solicitados por consultas em cada fragmento na memória. Isso permite que as consultas que acessam os mesmos dados sejam executadas mais rapidamente. Os dados podem ser recuperados da memória em vez do armazenamento em disco. Os dados no cache são invalidados quando o fragmento é atualizado e os dados foram alterados. A frequência de atualizações é controlada pelo valor da configuração *refresh\_interval* do índice. O cache de solicitação de fragmento para um índice é desabilitado por padrão, mas você pode habilitá-lo da seguinte maneira:

```http
PUT /myindex/_settings
{
	"index.requests.cache.enable": true
}
```

O cache de solicitação de fragmento é mais adequado para informações que permanecem relativamente estáticas, como dados históricos ou registro em log.

## Testando e analisando o desempenho da pesquisa e da agregação
Esta seção descreve os resultados de uma série de testes que foram executados em relação a várias configurações de cluster e índice. Cada teste foi iniciado com um índice vazio que foi preenchido conforme o teste ia progredindo pela execução de operações de inserção em massa (cada operação adicionou mil documentos). Ao mesmo tempo, um número de consultas projetadas para pesquisar dados específicos e para gerar agregações foi repetido em intervalos de cinco segundos. O objetivo dos testes era estabelecer como o desempenho da consulta foi afetado pelo volume de dados.

Cada documento no índice tinha o mesmo esquema. Esta tabela resume os campos no esquema:

 Nome | Tipo | Observações |
  ----------------------------- | ------------ | -------------------------------------------------------- |
 Organização | Cadeia de caracteres | O teste gera 200 organizações exclusivas. |
 CustomField1 - CustomField5 |Cadeia de caracteres |Esses são os cinco campos de cadeia de caracteres definidos para a cadeia de caracteres vazia.|
 DateTimeRecievedUtc |Timestamp |A data e a hora em que o documento foi adicionado.|
 Host |Cadeia de caracteres |Esse campo é definido como cadeia de caracteres vazia.|
 HttpMethod |Cadeia de caracteres |Esse campo é definido como um dos seguintes valores: "POST", "GET", "PUT".|
 HttpReferrer |Cadeia de caracteres |Esse campo é definido como cadeia de caracteres vazia.|
 HttpRequest |Cadeia de caracteres |Esse campo é preenchido com texto aleatório entre 10 e 200 caracteres.|
 HttpUserAgent |Cadeia de caracteres |Esse campo é definido como cadeia de caracteres vazia.|
 HttpVersion |Cadeia de caracteres |Esse campo é definido como cadeia de caracteres vazia.|
 OrganizationName |Cadeia de caracteres |Esse campo é definido como o mesmo valor que o campo Organização.|
 SourceIp |IP |Esse campo contém um endereço IP que indica "origem" dos dados. |
 SourceIpAreaCode |Longa |Esse campo é definido como 0.|
 SourceIpAsnNr |Cadeia de caracteres |Esse campo é definido como "AS#####".|
 SourceIpBase10 |Longa |Esse campo é definido como 500.|
 SourceIpCountryCode |Cadeia de caracteres |Esse campo contém um código de país de 2 caracteres. |
 SourceIpCity |Cadeia de caracteres |Esse campo contém uma cadeia de caracteres que identifica uma cidade em um país. |
 SourceIpLatitude |Duplo |Esse campo contém um valor aleatório.|
 SourceIpLongitude |Duplo |Esse campo contém um valor aleatório.|
 SourceIpMetroCode |Longa |Esse campo é definido como 0.|
 SourceIpPostalCode |Cadeia de caracteres |Esse campo é definido como cadeia de caracteres vazia.|
 SourceLatLong |Ponto de localização geográfica |Esse campo é definido como um ponto de localização geográfica aleatório.|
 SourcePort |Cadeia de caracteres |Esse campo é preenchido com a representação de cadeia de caracteres de um número aleatório|
 TargetIp |IP |Esse campo é preenchido com um endereço IP aleatório no intervalo entre 0.0.100.100 e 255.9.100.100|
 SourcedFrom |Cadeia de caracteres |Esse campo é definido como a cadeia de caracteres "MonitoringCollector".|
 TargetPort |Cadeia de caracteres |Esse campo é preenchido com a representação de cadeia de caracteres de um número aleatório|
 Classificação |Cadeia de caracteres |Esse campo é preenchido com um dos 20 valores de cadeia de caracteres diferentes selecionados aleatoriamente.|
 UseHumanReadableDateTimes |Booliano |Este campo é definido como false.|

As consultas a seguir foram executadas como um lote por cada iteração do teste (os nomes em itálico são usados para se referir a essas consultas no restante deste documento):

- Quantos documentos com cada valor *Classificação* foram inseridos nos últimos 15 minutos (*Contagem por classificação*)?

- Quantos documentos foram adicionados em cada intervalo de 5 minutos durante os últimos 15 minutos (*Contagem ao longo do tempo*)?

- Quantos documentos de cada valor *classificação* foram adicionados para cada país nos últimos 15 minutos (*Ocorrências por país*)?

- Quais são as 15 organizações que ocorrem com mais frequência nos documentos adicionados nos últimos 15 minutos (*15 principais organizações*)?

- Quantas organizações diferentes ocorram nos documentos adicionados nos últimos 15 minutos (*Organizações com contagem exclusiva*)?

- Quantos documentos foram adicionados nos últimos 15 minutos (*Contagem total de acertos*)?

- Quantos valores *SourceIp* diferentes ocorrem nos documentos adicionados nos últimos 15 minutos (*Contagem de IP exclusiva*)?

Os testes foram executados para entender os efeitos das seguintes variáveis:

- **Tipo de disco**. O teste foi executado em um cluster de seis nós em VMs D4 usando HDDs (armazenamento padrão) e repetido em um cluster de seis nós de VMs DS4 usando SSDs (armazenamento premium).

- **Tamanho da máquina –escalando verticalmente**. O teste foi executado em um cluster de seis nós que consiste em VMs DS3 (designado como cluster *pequeno*), repetido em um cluster de VMs DS4 (o cluster *médio*) e repetido novamente em um cluster de máquinas DS14 (o cluster *grande*). A tabela a seguir resume as principais características do SKU de cada VM:

HDInsight |SKU da VM |Número de núcleos |Número de discos de dados |RAM (GB)|
--------- |--------------- |----------------- |---------------------- |--------|
Pequena |DS3 Standard |4 |8 |14 |
Média |DS4 padrão |8 |16 |28 |
Grande |DS14 Standard |16 |32 |112 |

- **Valores doc**. Inicialmente, os testes foram executados com a configuração do índice *doc\_values* definida como *true*. Os testes selecionados foram repetidos com *doc\_values* definidos como *false*.

## Resultados de desempenho – tipo de disco

A tabela a seguir resume os tempos de resposta do trabalho realizado pela execução do teste no cluster de seis nós de VMs D4 (usando HDDs) e no cluster de seis nós de VMs DS4 (usando o SSDs). A configuração do Elasticsearch nos dois clusters era a mesma. Os dados se espalhavam entre 16 discos em cada nó, e cada nó tinha 14GB de RAM alocados para o JVM que executava o Elasticsearch; a memória restante (também 14GB) foi deixada para uso do sistema operacional. Cada teste foi executado por 24 horas. Esse período foi selecionado para permitir que os efeitos do crescente volume de dados ficassem aparentes e para permitir que o sistema se estabilizasse:

 HDInsight |Operação/consulta |Tempo Médio de Resposta (ms)|
 -----------|---------------------------- |--------------------------|
 D4 |Ingestão |978 |
 |Contagem por classificação |103 |
 |Contagem no tempo |134 |
 |Ocorrências por país |199 |
 |15 principais organizações |137 |
 |Organizações com contagem exclusiva |139 |
 |Contagem de IP exclusiva |510 |
 |Número total de ocorrências |89 |
 DS4 |Ingestão |511 |
 |Contagem por classificação |187 |
 |Contagem no tempo |411 |
 |Ocorrências por país |402 |
 |15 principais organizações |307 |
 |Organizações com contagem exclusiva |320 |
 |Contagem de IP exclusiva |841 |
 |Número total de ocorrências |236 |

À primeira vista, parece que o cluster DS4 executava consultas pior que o cluster D4, às vezes com o dobro (ou pior) do tempo de resposta. Isso não mostra a história completa. A tabela a seguir mostra o número de operações de ingestão executadas por cada cluster (lembre-se de que cada operação carrega mil documentos):

 HDInsight | Operações de ingestão
 ----------|---------------------
 D4 | 264769              
 DS4 | 503157              

O cluster DS4 foi capaz de carregar quase duas vezes mais dados que o cluster D4 durante o teste. Portanto, ao analisar os tempos de resposta para cada operação, você também precisará considerar quantos documentos a consulta tem que varrer e quantos documentos são retornados

Eles são valores dinâmicos, já que o volume de documentos no índice aumenta continuamente. Você não pode simplesmente dividir 503137 por 264769 (o número de operações de ingestão executadas por cada cluster) e multiplicar o resultado pelo tempo médio de resposta para cada consulta executada pelo cluster D4 para ter um valor comparativo, já que isso ignora a quantidade de E/S que estão sendo executadas simultaneamente pela operação de ingestão.

Em vez disso, você deve medir a quantidade física de dados que estão sendo gravados e lidos no disco conforme o teste prossegue. O plano de teste JMeter captura essas informações para cada nó. Os resultados resumidos foram:

 HDInsight |Média de bytes gravados/lidos por cada operação|
  --------- |--------------------------------------------|
 D4 |13471557 |
 DS4 |24643470 |

Essas figuras mostram que o cluster DS4 foi capaz de manter uma taxa de E/S aproximadamente 1,8 vezes a do cluster D4. Considerando que, tirando a natureza dos discos, todos os outros recursos são iguais, a diferença deve se dar pelo uso de SSDs em vez de HDDs.

Para ajudar a justificar essa conclusão, os gráficos a seguir ilustram como a E/S foi executada ao longo do tempo por cada cluster:

![](./media/guidance-elasticsearch/query-performance2.png)

O gráfico para o cluster D4 mostra uma variação significativa, especialmente durante a primeira metade do teste. Isso foi provavelmente devido à limitação para reduzir a taxa de E/S. Nos estágios iniciais do teste, as consultas podem ser executadas rapidamente, pois há poucos dados para analisar. A probabilidade, portanto, é que os discos no cluster D4 operem próximos de sua capacidade IOPS, embora cada operação E/S não esteja retornando muitos dados. O cluster DS4 é capaz de dar suporte a uma maior taxa IOPS e não tem o mesmo grau de limitação; as taxas de E/S são mais regulares.

Para ilustrar essa teoria, o próximo par de gráficos mostra como a CPU foi bloqueada por E/S de disco ao longo do tempo (os tempos de espera de disco exibidos no gráfico são proporcionais ao tempo de CPU gasto aguardando E/S):

![](./media/guidance-elasticsearch/query-performance3.png)

É importante entender que, neste cenário de teste, há dois motivos predominantes que levam as operações de E/S a bloquear a CPU:

- O subsistema de E/S pode ser ler ou gravar dados do e para o disco.

- O subsistema de E/S pode ser limitado pelo ambiente de host. Os discos do Azure com apoio de armazenamento padrão têm uma taxa de transferência máxima de 500 IOPS, enquanto que aqueles com apoio de armazenamento premium têm uma taxa de transferência máxima de 5000 IOPS.

Para o cluster D4, a quantidade de tempo gasto aguardando E/S durante a primeira metade do teste está intimamente relacionada, de maneira inversa, ao gráfico que mostra as taxas de E/S. Os períodos de baixa E/S correspondem aos períodos de tempo significativo que a CPU efetivamente perde bloqueada.

Isso indica que a E/S está sendo limitada. Conforme mais dados são adicionados ao cluster, a situação se altera e, na segunda metade do teste, os picos de tempo de espera de E/S correspondem aos picos da taxa de transferência de E/S. Nesse momento, a CPU fica bloqueada durante a execução de E/S real. Mais uma vez, com o cluster DS4, o tempo gasto aguardando E/S é muito mais uniforme, e cada pico corresponde a um pico equivalente no desempenho de E/S em vez de uma descida. Isso significa que há pouca ou nenhuma limitação.

Há um outro fator a considerar. Durante o teste, o cluster D4 gerou 10584 erros de ingestão e 21 erros de consulta. O teste no cluster DS4 não produziu erros.

## Resultados de desempenho – escalonamento vertical

A tabela a seguir resume os resultados da execução de testes nos clusters médios (DS4) e grandes (DS14). Cada VM usou SSDs para manter os dados. Cada teste foi executado por 24 horas:

| HDInsight |Operação/consulta |Número de solicitações |Tempo Médio de Resposta (ms)|
|  -------------- |---------------------------- |-------------------- |--------------------------|
| Médio (DS4) |Ingestão |503157 |511 |
| |Contagem por classificação |6958 |187 |
| |Contagem no tempo |6958 |411 |
| |Ocorrências por país |6958 |402 |
| |15 principais organizações |6958 |307 |
| |Organizações com contagem exclusiva |6956 |320 |
| |Contagem de IP exclusiva |6955 |841 |
| |Número total de ocorrências |6958 |236 |
| Grande (DS14) |Ingestão |502714 |511 |
| |Contagem por classificação |7041 |201 |
| |Contagem no tempo |7040 |298 |
| |Ocorrências por país |7039 |363 |
| |15 principais organizações |7038 |244 |
| |Organizações com contagem exclusiva |7037 |283 |
| |Contagem de IP exclusiva |7037 |681 |
| |Número total de ocorrências |7038 |200 |

<!-- 
DISCUSSION POINTS:

Similar volume of data ingested – same disk configuration for each cluster, and ingestion rate is 
constrained by I/O performance?

Average response time for queries decreases with SKU.

Show CPU graphs

Show memory utilization – more data cached, fewer GCs, etc.

-->

<!--
To isolate the effects of the ingestion operations and show how query performance varies as nodes scale up, a second set of tests was performed using the same nodes. The ingestion part of the test was omitted, and the index on each node was pre-populated with 100 million documents. An amended set of queries was performed; the time element limiting documents to those added in the last 15 minutes was removed as the data was now static. The tests ran for 90 minutes; there is less need to allow the system to stabilize due to the fixed amount of data. The following table summarizes the results obtained on each cluster:

> [AZURE.NOTE] The amended version of the test that omits the data ingestion process and that uses a set of indexes containing 100 million documents is referred to as the *query-only* test in the remainder of this document. You should not compare the performance of the queries in this test with that of the tests that perform ingestion and query operations because the queries have been modified and the volume of documents involved is different.

 |Cluster        |Operation/Query              |Number of Requests   Average Response Time (ms)
 | --------------| ----------------------------| -------------------- ----------------------------
 | Small (DS3)   | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      
 | Medium (DS4)  | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      
 | Large (DS14)  | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      

### Performance Results – Scaling Out
-->


<!--
To show the system scales out with the number of nodes, tests were run using DS14 clusters comprising 1, 3, and 6 nodes. This time, only the query-only test was performed, using 100 million documents and running for 90 minutes:

> [AZURE.NOTE] For detailed information on how scaling out can affect the behavior of data ingestion operations, see the document [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md).

|  Cluster   |Operation/Query              |Number of Requests   |Average Response Time (ms)
|  --------- |---------------------------- |-------------------- |----------------------------
|  1 Node    |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
|  3 Nodes   |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
|  6 Nodes   |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
-->

<!--
### Performance Results – Number of Replicas

The ingestion and query tests were run against an index with a single replica. The tests were repeated on the 6-node DS4 and DS14 clusters using an index configured with two replicas. All tests ran for 24 hours. The table below shows the comparative results for one and two replicas:

|  Cluster|  Operation/Query            | response time 1 Replica (ms)| response time 2 Replicas (ms) | % Difference in Response Time
|  -------| ----------------------------| ----------------------------|----------- -------------------|--------------------- --------
|  DS4    |   Ingestion                 |   511                       |               655             |                          +28%
|         |   Count By Rating           |   187                       |               168             |                          -10%
|         |   Count Over Time           |   411                       |               309             |                          -25%
|         |   Hits By Country           |   402                       |               562             |                          +40%
|         |   Top 15 Organizations      |   307                       |               366             |                          +19%
|         |   Unique Count Organizations|   320                       |               378             |                          +18%
|         |   Unique IP Count           |   841                       |               987             |                          +17%
|         |   Total Hits Counts         |   236                       |               236             |                          +0%
|  DS14   |   Ingestion                 |   511                       |               618             |                          +21%
|         |   Count By Rating           |   201                       |               275             |                          +37%
|         |   Count Over Time           |   298                       |               466             |                          +56%
|         |   Hits By Country           |   363                       |               529             |                          +46%
|         |   Top 15 Organizations      |   244                       |               407             |                          +67%
|         |   Unique Count Organizations|   283                       |               403             |                          +42%
|         |   Unique IP Count           |   681                       |               823             |                          +21%
|         |   Total Hits Counts         |   200                       |               221             |                          +11%


NEED ## OF DOCUMENTS RETURNED TO JUSTIFY THIS DATA, OTHERWISE PERF FOR 2 REPLICAS LOOKS OFF!

PRESENT QUERY-ONLY TEST RESULTS TO SHOW BETTER RESULTS
-->

## Resultados de desempenho – valores doc

Os testes de ingestão e consulta foram realizados com valores de doc habilitados, fazendo com que o Elasticsearch armazene os dados usados na classificação de campos no disco. Os testes foram repetidos com valores de doc desabilitados; portanto, o Elasticsearch criou fielddata dinamicamente e armazenou em cache na memória. Todos os testes foram executados por 24 horas.
 
 A tabela a seguir compara os tempos de resposta para testes executados em clusters de seis nós criados usando VMs D4, DS4 e DS14.

| HDInsight |Operação/consulta |Valores de doc habilitados (ms) | Valores de doc desabilitados (ms) | % de diferença |
|  --------- |---------------------------| -----------------------|--------------------------  |--------------------|
| D4 |Ingestão | 978 | 835 | -15% |
| |Contagem por classificação | 103 | 132 | +28% |
| |Contagem no tempo | 134 | 189 | +41% |
| |Ocorrências por país | 199 | 259 | +30% |
| |15 principais organizações | 137 | 184 | +34% |
| |Organizações com contagem exclusiva | 139 | 197 | +42% |
| |Contagem de IP exclusiva | 510 | 604 | +18% |
| |Número total de ocorrências | 89 | 134 | +51% |
| DS4 |Ingestão | 511 | 581 | +14% |
| |Contagem por classificação | 187 | 190 | +2% |
| |Contagem no tempo | 411 | 409 | -0.5% |
| |Ocorrências por país | 402 | 414 | +3% |
| |15 principais organizações | 307 | 284 | -7% |
| |Organizações com contagem exclusiva | 320 | 313 | -2% |
| |Contagem de IP exclusiva | 841 | 955 | +14% |
| |Número total de ocorrências | 236 | 281 | +19% |
| DS14 |Ingestão | 511 | 571 | +12% |
| |Contagem por classificação | 201 | 232 | +15% |
| |Contagem no tempo | 298 | 341 | +14% |
| |Ocorrências por país | 363 | 457 | +26% |
| |15 principais organizações | 244 | 338 | +39% |
| |Organizações com contagem exclusiva | 283 | 350 | +24% |
| |Contagem de IP exclusiva | 681 | 909 | +33% |
| |Número total de ocorrências | 200 | 245 | +23% |


<!--
The next table compares the number of ingestion operations performed by the tests:

  Cluster   Ingestion Operations – Doc Values Enabled   ## Ingestion Operations – Doc Values Disabled   % Difference in ## Ingestion Operations
  --------- ---------------------------------------------- ----------------------------------------------- -----------------------------------------
  D4        264769                                         408690                                          +54%
  DS4       503137                                         578237                                          +15%
  DS14      502714                                         586472                                          +17%

The improved ingestion rates occur with doc values disabled as less data is being written to disk as documents are inserted. The improved performance is especially noticeable with the D4 VM using HDDs to store data. In this case, the response time for ingestion operations also decreased by 15% (see the first table in this section). This could be due to the reduced pressure on the HDDs which were likely running close to their IOPS limits in the test with doc values enabled; see the [Disk Type](#performance-results-disk-type) test for more information. The following graph compares the I/O performance of the D4 VMs with doc values enabled (values held on disk) and doc values disabled (values held in memory):

![](./media/guidance-elasticsearch/query-performance4.png)

In contrast, the ingestion figures for the VMs using SSDs show a small increase in the number of documents but also an increase in the response time of the ingestion operations. With one or two small exceptions, the query response times were also worse. The SSDs are less likely to be running close to their IOPS limits with doc values enabled, so changes in performance are more likely due to increased processing activity and the overhead of managing the JVM heap. This is evident by comparing the CPU utilization with doc values enabled and disabled. The next graph highlights this data for the DS4 cluster, where most of the CPU utilization moves from the 30%-40% band with doc values enabled, to the 40%-50% band with doc values disabled (the DS14 cluster showed a similar trend):

![](./media/guidance-elasticsearch/query-performance5.png)

To isolate the effects that doc values on query performance from data ingestion, a pair of query-only tests were performed for each cluster as follows:

In the first test, the Elasticsearch index was created with doc values enabled, the index was pre-populated with 100 million documents, and then modified set of queries used by the replica tests were performed repeatedly for 90 minutes while performance statistics were gathered.

In the second test, the Elasticsearch index was created with doc values disabled, populated, and then subjected to the same query load for the same period as the first test.
-->

<!--
## Appendix: The Query and Aggregation Performance Test

This appendix describes the performance test performed against the Elasticsearch cluster. The tests were run by using JMeter running on a separate set of VMs. Details the configuration of the test environment are described in [Creating a Performance Testing Environment for Elasticsearch on Azure][]. To perform your own testing, you can create your own JMeter test plan manually following the guidance in this appendix, or you can use the automated test scripts available separately. See [Running the Automated Elasticsearch Performance Tests][] for further information.

The data query workload performed the set of queries described below while performing a large-scale upload of documents at the same time (the data was uploaded by using a JUnit test, following the same approach for the data ingestion tests described in the document Maximizing Data Ingestion Performance with Elasticsearch on Azure.) The purpose of this workload was to simulate a production environment where new data is constantly being added while searches are performed. The queries were structured to retrieve only the most recent data from documents added in the last 15 minutes.

Each document was stored in a single index named *sample*, and had the type *ctip*. You can use the following HTTP request to create the index. The *number\_of\_replicas* and *number\_of\_shards* settings varied from the values shown below in many of the tests. Additionally, for the tests that used fielddata rather than doc values, each property was annotated with the attribute *"doc\_values" : false*.

**Important**. The index was dropped and recreated prior to each test run.

```http
PUT /sample
{  
    "settings" : {
        "number_of_replicas": 1,
        "refresh_interval": "30s",
        "number_of_shards": "5",
        "index.translog.durability": "async"    
    },
    "ctip": {
        "mappings": {
            "event": {
                "_all": {
                    "enabled": false
                },
                "_timestamp": {
                    "enabled": true,
                    "store": true,
                    "format": "date_time"
                },
                "properties": {
                    "Organization": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField1": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField2": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField3": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField4": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField5": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "DateTimeReceivedUtc": {
                        "type": "date",
                        "format": "dateOptionalTime"
                    },
                    "Host": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpMethod": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpReferrer": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpRequest": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpUserAgent": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpVersion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "OrganizationName": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIp": {
                        "type": "ip"
                    },
                    "SourceIpAreaCode": {
                        "type": "long"
                    },
                    "SourceIpAsnNr": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpBase10": {
                        "type": "long"
                    },
                    "SourceIpCity": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpCountryCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpLatitude": {
                        "type": "double"
                    },
                    "SourceIpLongitude": {
                        "type": "double"
                    },
                    "SourceIpMetroCode": {
                        "type": "long"
                    },
                    "SourceIpPostalCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpRegion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceLatLong": {
                        "type": "geo_point",
                        "doc_values": true,
                        "lat_lon": true,
                        "geohash": true
                    },
                    "SourcePort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourcedFrom": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "TargetIp": {
                        "type": "ip"
                    },
                    "TargetPort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "Rating": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "UseHumanReadableDateTimes": {
                        "type": "boolean"
                    }
                }
            }
        }
    }
}

```

The following queries were performed by the test:

- How many documents with each *Rating* value have been entered in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "DateTimeReceivedUtc": {
              "gte": "now-15m",
              "lte": "now"
            }
          }
        }
      ],
      "must_not": [],
      "should": []
    }
  },
  "from": 0,
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Rating",
        "size": 5,
        "order": {
          "_count": "desc"
        }
      }
    }
  }
}

```

- How many documents have been added in each 5 minute interval during the last 15 minutes?

```http
GET /sample/ctip/_search 
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "DateTimeReceivedUtc": {
              "gte": "now-15m",
              "lte": "now"
            }
          }
        }
      ],
      "must_not": [],
      "should": []
    }
  },
  "from": 0,
  "size": 0,
  "sort": [],
  "aggs": {
    "2": {
      "date_histogram": {
        "field": "DateTimeReceivedUtc",
        "interval": "5m",
        "time_zone": "America/Los_Angeles",
        "min_doc_count": 1,
        "extended_bounds": {
          "min": "now-15m",
          "max": "now"
        }
      }
    }
  }
}

```

- How many documents of each *Rating* value have been added for each country in the last 15 minutes?

```http
GET /sample/ctip/_search 
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Rating",
        "size": 5,
        "order": {
          "_count": "desc"
        }
      },
      "aggs": {
        "3": {
          "terms": {
            "field": "SourceIpCountryCode",
            "size": 15,
            "order": {
              "_count": "desc"
            }
          }
        }
      }
    }
  }
}

```

- Which 15 organizations occur most frequently in documents added in the last 15 minutes?

``` http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Organization",
        "size": 15,
        "order": {
          "_count": "desc"
        }
      }
    }
  }
}

```

- How many different organizations occur in documents added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "cardinality": {
        "field": "Organization"
      }
    }
  }
}

```

- How many documents have been added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "analyze_wildcard": true,
                  "query": "*"
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {}
}

```

- How many different *SourceIp* values occur in documents added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "cardinality": {
        "field": "SourceIp"
      }
    }
  }
} 

```

[Running the Automated Elasticsearch Performance Tests]: guidance-elasticsearch-running-automated-performance-tests.md
[Creating a Performance Testing Environment for Elasticsearch on Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
-->

<!---HONumber=AcomDC_0224_2016-->