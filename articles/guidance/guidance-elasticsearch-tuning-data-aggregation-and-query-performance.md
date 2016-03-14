<properties
   pageTitle="Ajuste do desempenho de consulta e agregação de dados com o Elasticsearch no Azure | Microsoft Azure"
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
   ms.date="02/29/2016"
   ms.author="masimms"/>
   
# Ajuste de desempenho de consulta e de agregação de dados com o Elasticsearch no Azure


Este artigo faz [parte de uma série](guidance-elasticsearch.md).

Um grande motivo para usar o Elasticsearch é dar suporte a pesquisas por dados. Os usuários devem ser capazes de localizar rapidamente as informações que estão procurando. Além disso, o sistema deve permitir que os usuários façam perguntas sobre dados, busquem correlações e cheguem a conclusões que podem gerar decisões de negócios. Esse processamento é o que diferencia dados de informações.

Este documento resume as opções que você pode considerar ao determinar a melhor maneira de otimizar seu sistema em relação ao desempenho de consulta e pesquisa.

Todas as recomendações de desempenho dependem principalmente dos cenários que se aplicam à sua situação, do volume de dados que você estiver indexando e da velocidade em que seus aplicativos e usuários consultam os dados. Você deve testar cuidadosamente os resultados de qualquer alteração na configuração ou na estrutura de indexação usando seus próprios dados e cargas de trabalho a fim de avaliar os benefícios para seus cenários específicos. Para esse fim, este documento também descreve um número de benchmarks que foram executados para um cenário específico implementado usando configurações diferentes. Você pode adaptar a abordagem usada para avaliar o desempenho de seus próprios sistemas. Os detalhes desses testes são descritos no [apêndice](#appendix-the-query-and-aggregation-performance-test).

## Índice e considerações de desempenho de consulta

Esta seção descreve alguns fatores comuns que você deve avaliar ao criar índices que precisam dar suporte à consulta e à pesquisa rápida.

### Armazenando vários tipos em um índice

Um índice de Elasticsearch pode conter vários tipos. É melhor evitar essa abordagem e criar um índice separado para cada tipo. Considere os seguintes pontos:

- Tipos diferentes podem especificar analisadores diferentes e nem sempre fica claro qual analisador o Elasticsearch deve usar se uma consulta é executada no nível do índice em vez de no nível do tipo. Confira [Como evitar armadilhas de tipos](https://www.elastic.co/guide/en/elasticsearch/guide/current/mapping.html#_avoiding_type_gotchas) para obter detalhes.

- Fragmentos de índices que contêm vários tipos provavelmente serão maiores do que aqueles para os índices que contêm um único tipo. Quanto maior um fragmento, mais esforço é exigido do Elasticsearch para filtrar os dados ao executar consultas.

- Se houver uma diferença significativa entre os volumes de dados para os tipos, as informações de um tipo podem ficar distribuídas esporadicamente entre vários fragmentos, reduzindo a eficiência de pesquisas que recuperam esses dados.

    ![](./media/guidance-elasticsearch/query-performance1.png)

    ***Figura 1. Os efeitos do compartilhamento de um índice entre tipos***

    A Figura 1 ilustra essa abordagem. Na parte superior do diagrama, o mesmo índice é compartilhado por documentos do tipo A e do tipo B. Há mais documentos do tipo A que do tipo B. As pesquisas pelo tipo A envolverão a consulta de todos os quatro fragmentos. A parte inferior do diagrama mostra o efeito no caso de índices separados serem criados para cada tipo. Nesse caso, a busca pelo tipo A somente exigirá acesso a dois fragmentos.

- Pequenos fragmentos podem ter distribuição mais uniforme que os grandes fragmentos, tornando mais fácil para Elasticsearch distribuir a carga entre os nós.

- Tipos diferentes podem ter diferentes períodos de retenção. Pode ser difícil arquivar dados antigos que compartilham fragmentos com dados ativos.


No entanto, em algumas circunstâncias, o compartilhamento de um índice entre tipos pode ser eficiente se:

- As pesquisas abrangem regularmente tipos mantidos no mesmo índice.

- Cada tipo tem apenas um pequeno número de documentos. A manutenção de um conjunto separado de fragmentos para cada tipo pode se tornar uma sobrecarga significativa nesse caso.


### Otimizando os tipos de índice

Um índice de Elasticsearch contém uma cópia dos documentos JSON originais que foram usados para preenchê-lo. Essa informação é mantida no campo [*\_source*](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-source-field.html#mapping-source-field) de cada item indexado. Esses dados não são pesquisáveis, mas são retornados pelas solicitações *get* e *search* por padrão. No entanto, esse campo gera sobrecarga e ocupa o armazenamento, aumentando os fragmentos e o volume de E/S realizadas. Você pode desabilitar o campo *\_source* de acordo com o tipo:

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

- Atualizar dados no índice usando a API *update*.

- Executando pesquisas que retornam dados realçados.

- Reindexando de um índice do Elasticsearch diretamente para outro.

- Alterando mapeamentos ou configurações de análise.

- Depurando consultas com a exibição do documento original.


### Reindexando dados

Por fim, o número de fragmentos disponíveis para um índice determina a capacidade dele. Você pode usar uma estimativa inicial (e ponderada) de quantos fragmentos serão necessários, mas deve sempre considerar a estratégia de reindexação de documento antecipadamente. Em muitos casos, a reindexação pode ser uma tarefa planejada, conforme os dados vão crescendo. Não convém alocar um grande número de fragmentos para um índice inicialmente, para fins de otimização de pesquisa, mas aloque novos fragmentos conforme o volume de dados vai se expandindo. Em outros casos, talvez haja a necessidade de reindexação de forma avulsa se suas estimativas sobre o crescimento do volume de dados simplesmente não puderem ser confiáveis.

> [AZURE.NOTE] A reindexação pode não ser necessária para dados que ficam ultrapassados rapidamente. Nesse caso, um aplicativo pode criar um novo índice para cada período de tempo. Exemplos incluem logs de desempenho ou dados de auditoria que podem ser armazenados em um índice atualizado diariamente.

<!-- -->

A reindexação envolve efetivamente a criação de um novo índice dos dados de um antigo e a remoção deste. Se um índice for grande, esse processo poderá levar tempo e talvez seja necessário garantir que os dados permaneçam pesquisáveis durante esse período. Por esse motivo, você deve criar um [alias para cada índice](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html) e as consultas devem recuperar dados por meio desses aliases. Durante a reindexação, mantenha o alias apontando para o índice antigo e alterne-o para referenciar o novo índice após a conclusão da reindexação. Essa abordagem também é útil para acessar os dados baseados em tempo, que cria um novo índice a cada dia. Para acessar os dados atuais, use um alias que muda para o novo índice quando ele é criado.

### Gerenciando mapeamentos

O Elasticsearch usa mapeamentos para determinar como interpretar os dados que ocorrem em cada campo em um documento. Cada tipo tem seu próprio mapeamento, que efetivamente define um esquema para ele. O Elasticsearch usa essas informações para gerar índices invertidos para cada campo nos documentos em um tipo. Em qualquer documento, cada campo tem um tipo de dados (como *string*, *date* ou *long*) e um valor. Você pode especificar os mapeamentos para um índice quando o índice é criado pela primeira vez, ou isso pode ser inferido pelo Elasticsearch quando novos documentos são adicionados a um tipo. No entanto, considere os seguintes pontos:

- Os mapeamentos gerados dinamicamente podem causar erros dependendo de como os campos são interpretados quando os documentos são adicionados a um índice. Por exemplo, o documento 1 pode conter um campo A com um número e faz com que o Elasticsearch adicione um mapeamento que especifica que esse campo é um *long*. Se um documento subsequente for adicionado no campo que contém dados não numéricos, ele falhará. Nesse caso, o campo A provavelmente foi interpretado como uma cadeia de caracteres quando o primeiro documento foi adicionado. As especificação desse mapeamento quando o índice é criado pode ajudar a evitar esses problemas.

- Crie documentos para evitar a geração de mapeamentos excessivamente grandes, pois isso pode criar uma sobrecarga significativa durante as pesquisas, consumir muita memória e também fazer com que as consultas falhem na localização dos dados. Adote uma convenção de nomenclatura consistente para os campos nos documentos que compartilham o mesmo tipo. Por exemplo, não use nomes de campo como "nome", "Nome" e "PrimeiroNome" em diferentes documentos. Use o mesmo nome de campo em cada documento. Além disso, não tente usar valores como chaves (essa é uma abordagem comum em bancos de dados coluna-família, mas pode causar falhas e ineficiências com o Elasticsearch.) Para saber mais, confira [Detalhamento do mapeamento](https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion).

- Use *not\_analyzed* para evitar a geração de tokens quando for apropriado. Por exemplo, se um documento contiver um campo de cadeia de caracteres chamado *dados* que contém o valor "ABC-DEF", você poderá tentar executar uma pesquisa para todos os documentos que correspondem a esse valor da seguinte maneira:

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

    However, this search will fail to return the expected results due to the way in which the string ABC-DEF is tokenized when it is indexed; it will be effectively split into two tokens, ABC and DEF, by the hyphen. This feature is designed to support full text searching, but if you want the string to be interpreted as a single atomic item you should disable tokenization when the document is added to the index. You can use a mapping such as this:

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

  Para saber mais, confira [Localização de valores exatos](https://www.elastic.co/guide/en/elasticsearch/guide/current/_finding_exact_values.html#_term_filter_with_text).


### Usando valores de documento

Muitas consultas e agregações exigem que os dados sejam classificados como parte da operação de pesquisa. A classificação exige a capacidade de mapear um ou mais termos para uma lista de documentos. Para ajudar nesse processo, o Elasticsearch pode carregar todos os valores de um campo usado como uma chave de classificação na memória. Essas informações são conhecidas como *fielddata*. A intenção é que o caching de fielddata na memória resulte em menos E/S e possa ser mais rápido do que ler repetidamente os mesmos dados no disco. No entanto, se um campo tem alta cardinalidade, o armazenamento de fielddata na memória pode consumir muito espaço de heap, possivelmente afetando a capacidade de executar outras operações simultâneas ou até mesmo esgotando o armazenamento, fazendo com que o Elasticsearch venha a falhar.

Como uma abordagem alternativa, o Elasticsearch também dá suporte a *valores de documento*. Um valor de documento é semelhante a um item de fielddata na memória, exceto pelo fato de ser armazenado em disco e criado quando os dados são armazenados em um índice (os fielddata são criados dinamicamente quando uma consulta é executada). Os valores de doc não consomem espaço de heap e, portanto, são úteis para consultas que classificam ou agregam dados em campos que podem conter um número muito grande de valores exclusivos. Além disso, a redução da pressão na pilha pode ajudar a compensar as diferenças de desempenho entre a recuperação de dados do disco e a leitura da memória. A coleta de lixo deve ocorrer com menos frequência e outras operações simultâneas que utilizam memória devem ser menos afetadas.

Habilite ou desabilite os valores do documento baseado na propriedade em um índice usando o atributo *doc\_values*, como mostrado no exemplo abaixo:

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

O impacto exato de usar valores de documento provavelmente será altamente específico para seus próprios cenários de dados e consulta; portanto, esteja preparado para realizar testes de desempenho para estabelecer sua utilidade. Você também deve observar que os valores de documento não funcionam com campos de cadeia de caracteres analisados. Para saber mais, confira [Valores de documento](https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html#doc-values).

### Usando réplicas para reduzir a contenção de consulta

Uma estratégia comum para melhorar o desempenho das consultas é criar várias réplicas de cada índice. As operações de recuperação de dados podem ser atendidas com a busca de dados de uma réplica. No entanto, essa estratégia pode afetar gravemente o desempenho das operações de ingestão de dados e, portanto, ela precisa ser usada com cuidado em cenários que envolvem cargas de trabalho mistas. Além disso, essa estratégia só funciona se as réplicas são distribuídas entre os nós e não competem por recursos com os principais fragmentos que fazem parte do mesmo índice. Lembre-se de que é possível aumentar ou diminuir o número de réplicas de um índice dinamicamente.

### Usando o cache de solicitação de fragmento

O Elasticsearch pode armazenar em cache os dados locais solicitados por consultas em cada fragmento na memória. Isso permite que as pesquisas que recuperam os mesmos dados sejam executadas mais rapidamente. Os dados podem ser lidos da memória em vez de no armazenamento em disco. O armazenamento em cache dessa forma pode melhorar o desempenho de algumas operações de pesquisa, com o custo de reduzir a memória disponível para outras tarefas em execução simultânea. Também há o risco de que os dados fornecidos pelo cache estejam desatualizados. Os dados no cache só serão invalidados quando o fragmento for atualizado e os dados tiverem sido alterados. A frequência de atualizações é controlada pelo valor da configuração *refresh\_interval* do índice.

O cache de solicitação para um índice está desabilitado por padrão, mas você pode habilitá-lo da seguinte maneira:

```http
PUT /myindex/_settings
{
  "index.requests.cache.enable": true
}
```

O cache de solicitação de fragmento é mais adequado para informações que permanecem relativamente estáticas, como dados históricos ou registro em log.

### Usando nós de cliente

Todas as consultas são processadas pelo nó que recebe a solicitação primeiro. Esse nó envia solicitações adicionais para todos os outros nós que contêm fragmentos dos índices que estão sendo consultados e acumula os resultados para retornar a resposta. Se uma consulta envolve a agregação de dados ou a execução de cálculos complexos, o nó inicial é responsável por executar o processamento apropriado. Se o sistema tem que dar suporte a um número relativamente pequeno de consultas complexas, considere criar um pool de nós cliente para aliviar a carga dos nós de dados. Por outro lado, se seu sistema tiver que lidar com um grande número de consultas simples, envie essas solicitações diretamente para os nós de dados e use um balanceador de carga para distribuir as solicitações uniformemente.

### Ajuste de consultas

Os pontos a seguir resumem as dicas para maximizar o desempenho das consultas do Elasticsearch:

- Sempre que possível, evite as consultas que envolvam caracteres curinga.

- Se o mesmo campo estiver sujeito à pesquisa de texto completo e à correspondência exata, considere armazenar os dados do campo nas formas analisada e não analisada. Execute as pesquisas de texto completo no campo analisado e as correspondências exatas no campo não analisado.

- Retorne somente os dados necessários. Se você tiver documentos grandes, mas se um aplicativo precisar apenas das informações mantidas em um subconjunto dos campos, retorne esse subconjunto das consultas em vez de documentos inteiros. Essa estratégia pode reduzir os requisitos de largura de banda de rede do cluster.

- Sempre que possível, use os filtros em vez das consultas durante a pesquisa de dados. Um filtro simplesmente determina se um documento atende a um determinado critério, enquanto uma consulta também calcula qual a proximidade de correspondência de um documento (pontuação). Internamente, os valores gerados por um filtro são armazenados como um bitmap que indica a correspondência/não correspondência de cada documento. Além disso, eles podem ser armazenados em cache pelo Elasticsearch. Se o mesmo critério de filtro ocorrer posteriormente, o bitmap poderá ser recuperado do cache e usado para buscar rapidamente os documentos correspondentes. Para saber mais, confira [Operação de filtro interno](https://www.elastic.co/guide/en/elasticsearch/guide/current/_finding_exact_values.html#_internal_filter_operation).

- Use os filtros *bool* para executar comparações estáticas e use os filtros *and*, *or* e *not* apenas para filtros calculados dinamicamente, como aqueles que envolvem a criação de scripts ou os filtros *geo-**.

- Se uma consulta combinar os filtros *bool* com *and*, *or* ou *not* com filtros *geo-**, coloque os filtros *and*/*or*/*not geo-** por último para que eles operem no menor conjunto de dados possível.

    Da mesma forma, use um *post\_filter* para executar operações de filtro dispendiosas. Esses filtros serão executados por último.

- Use agregações em vez de facetas. Evite calcular agregações analisadas ou que possuem muitos valores possíveis.

    > **Observação**: as facetas foram removidas do Elasticsearch versão 2.0.0.

- É preferível usar a agregação *cardinalidade* em vez da *value\_count*, a menos que seu aplicativo exija uma contagem exata de itens correspondentes. Uma contagem exata pode ficar desatualizada rapidamente, e muitos aplicativos exigem apenas uma aproximação razoável.

- Evite a geração de scripts. Os scripts em consultas e em filtros podem ser dispendiosos, e os resultados não são armazenados em cache. Os scripts de execução longa podem consumir os threads de pesquisa indefinidamente, fazendo com que as solicitações subsequentes sejam colocadas em fila. Se a fila for preenchida, as solicitações posteriores serão rejeitadas.

## Testando e analisando o desempenho da pesquisa e da agregação

Esta seção descreve os resultados de uma série de testes que foram executados em relação a várias configurações de cluster e índice. Dois tipos de testes foram executados, como a seguir:

- **O teste de *ingestão e consulta***. Esse teste foi iniciado com um índice vazio, preenchido conforme o teste ia progredindo pela execução de operações de inserção em massa (cada operação adicionou 1000 documentos). Ao mesmo tempo, várias consultas projetadas para pesquisar documentos adicionados durante os últimos 15 minutos e para gerar agregações foram repetidas em intervalos de cinco segundos. Esse teste foi executado durante 24 horas a fim de reproduzir os efeitos de uma carga de trabalho desafiadora formada pela ingestão de dados em larga escala com consultas quase em tempo real.

- **O teste *somente consulta***. Esse teste é semelhante ao teste de *ingestão e consulta*, exceto pela omissão da parte de ingestão, e o índice em cada nó é preenchido previamente com 100 milhões de documentos. Um conjunto corrigido de consultas é executado; o elemento de tempo que limitava os documentos àqueles adicionados nos últimos 15 minutos foi removido, pois os dados agora são estáticos. Os testes foram executados durante 90 minutos; menos tempo será necessário para estabelecer um padrão de desempenho devido à quantidade fixa de dados.

---

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
 
As consultas a seguir foram executadas como um lote por cada iteração dos testes. Os nomes em itálico são usados para se referir a essas consultas no restante deste documento. Observe que o critério de tempo (documentos adicionados nos últimos 15 minutos) foi omitido do teste *somente consulta*:

- Quantos documentos com cada valor de *Classificação* foram inseridos nos últimos 15 minutos (*Contagem por Classificação*)?

- Quantos documentos foram adicionados em cada intervalo de 5 minutos durante os últimos 15 minutos (*Contagem ao Longo do Tempo*)?

- Quantos documentos de cada valor *Classificação* foram adicionados para cada país nos últimos 15 minutos (*Ocorrências por País*)?

- Quais são as 15 organizações que ocorrem com mais frequência nos documentos adicionados nos últimos 15 minutos (*15 Principais Organizações*)?

- Quantas organizações diferentes ocorrem nos documentos adicionados nos últimos 15 minutos (*Organizações com Contagem Exclusiva*)?

- Quantos documentos foram adicionados nos últimos 15 minutos (*Contagem Total de Ocorrências*)?

- Quantos valores *SourceIp* diferentes ocorrem nos documentos adicionados nos últimos 15 minutos (*Contagem de IP Exclusiva*)?


A definição do índice e os detalhes das consultas estão descritos no [apêndice](#appendix-the-query-and-aggregation-performance-test).

Os testes foram projetados para entender os efeitos das seguintes variáveis:

- **Tipo de disco**. Os testes foram executados em um cluster de seis nós em VMs D4 usando HDDs (armazenamento padrão) e repetidos em um cluster de seis nós de VMs DS4 usando SSDs (armazenamento premium).

- **Tamanho da máquina – dimensionamento**. Os testes foram executados em um cluster de seis nós que consiste em VMs DS3 (designado como cluster *pequeno*), repetido em um cluster de VMs DS4 (o cluster *médio*) e repetidos novamente em um cluster de máquinas DS14 (o cluster *grande*). A tabela a seguir resume as principais características do SKU de cada VM:

 HDInsight | SKU da VM | Número de núcleos | Número de discos de dados | RAM (GB) |
---------|---------------|-----------------|----------------------|----------|
 Pequena | DS3 Standard | 4 | 8 | 14 |
 Média | DS4 padrão | 8 | 16 | 28 |
 Grande | DS14 Standard | 16 | 32 | 112 |

- **Tamanho do cluster - expansão**. Os testes foram executados em clusters de VMs DS14 compostas por um, três e seis nós.

- **Número de réplicas do índice**. Os testes foram executados usando índices configurados com uma e duas réplicas.

- **Valores de documento**. Inicialmente, os testes foram executados com a configuração do índice *doc\_values* definida como *true* (o valor padrão). Os testes selecionados foram repetidos com *doc\_values* definidos como *false*.

- **Cache**. Os testes foram conduzidos com o cache de solicitação de fragmento habilitado no índice.

- **Número de fragmentos**. Os testes foram repetidos usando números variados de fragmentos a fim de estabelecer se consultas eram executadas com mais eficiência em índices contendo menos fragmentos de maior porte, ou mais fragmentos de menor porte.


## Resultados de desempenho – Tipo de disco

O desempenho do disco foi avaliado por meio da execução do teste de *ingestão e consulta* no cluster de seis nós das VMs D4 (usando HDDs) e no cluster de seis nós das VMs DS4 (usando SSDs). A configuração do Elasticsearch nos dois clusters era a mesma. Os dados se espalhavam entre 16 discos em cada nó, e cada nó tinha 14GB de RAM alocados para o JVM que executava o Elasticsearch; a memória restante (também 14GB) foi deixada para uso do sistema operacional. Cada teste foi executado por 24 horas. Esse período foi selecionado para permitir que os efeitos do crescente volume de dados ficassem aparentes e para permitir que o sistema se estabilizasse: A tabela a seguir resume os resultados, realçando os tempos de resposta de várias operações que formam o teste.

 HDInsight | Operação/consulta | Tempo Médio de Resposta (ms) |
---------|----------------------------|----------------------------|
 D4 | Ingestão | 978 |
 | Contagem por classificação | 103 |
 | Contagem no tempo | 134 |
 | Ocorrências por país | 199 |
 | 15 principais organizações | 137 |
 | Organizações com contagem exclusiva | 139 |
 | Contagem de IP exclusiva | 510 |
 | Número total de ocorrências | 89
 DS4 | Ingestão | 511 |
 | Contagem por classificação | 187 |
 | Contagem no tempo | 411 |
 | Ocorrências por país | 402 |
 | 15 principais organizações | 307 |
 | Organizações com contagem exclusiva | 320 |
 | Contagem de IP exclusiva | 841 |
 | Número total de ocorrências | 236 |

À primeira vista, parece que o cluster DS4 executava consultas pior que o cluster D4, às vezes com o dobro (ou pior) do tempo de resposta. Isso não mostra a história completa. A tabela a seguir mostra o número de operações de ingestão executadas por cada cluster (lembre-se de que cada operação carrega mil documentos):

 HDInsight | Nº de Operações de Ingestão |
---------|-------------------------|
 D4 | 264769 |
 DS4 | 503157 |

O cluster DS4 foi capaz de carregar quase duas vezes mais dados que o cluster D4 durante o teste. Portanto, ao analisar os tempos de resposta para cada operação, você também precisará considerar quantos documentos a consulta tem que varrer e quantos documentos são retornados Eles são valores dinâmicos, já que o volume de documentos no índice aumenta continuamente. Você não pode simplesmente dividir 503137 por 264769 (o número de operações de ingestão executadas por cada cluster) e multiplicar o resultado pelo tempo médio de resposta para cada consulta executada pelo cluster D4 para ter uma informação comparativa, já que isso ignora a quantidade de E/S que estão sendo executadas simultaneamente pela operação de ingestão. Em vez disso, você deve medir a quantidade física de dados que estão sendo gravados e lidos no disco conforme o teste prossegue. O plano de teste JMeter captura essas informações para cada nó. Os resultados resumidos são:

 HDInsight | Média de bytes gravados/lidos por cada operação |
---------|----------------------------------------------|
 D4 | 13471557 |
 DS4 | 24643470 |

Esses dados mostram que o cluster DS4 foi capaz de manter uma taxa de E/S aproximadamente 1,8 vezes a do cluster D4. Considerando que, tirando a natureza dos discos, todos os outros recursos são iguais, a diferença deve se dar pelo uso de SSDs em vez de HDDs.

Para ajudar a justificar essa conclusão, os gráficos a seguir ilustram como a E/S foi executada ao longo do tempo por cada cluster:

![](./media/guidance-elasticsearch/query-performance2.png)

<!-- -->

***Figura 2. Atividade de disco dos clusters D4 e DS4***

O gráfico para o cluster D4 mostra uma variação significativa, especialmente durante a primeira metade do teste. Isso foi provavelmente devido à limitação para reduzir a taxa de E/S. Nos estágios iniciais do teste, as consultas podem ser executadas rapidamente, pois há poucos dados para analisar. A probabilidade, portanto, é que os discos no cluster D4 operem próximos de sua capacidade IOPS, embora cada operação E/S não esteja retornando muitos dados. O cluster DS4 é capaz de dar suporte a uma maior taxa IOPS e não tem o mesmo grau de limitação; as taxas de E/S são mais regulares. Para apoiar essa teoria, o próximo par de gráficos mostra como a CPU foi bloqueada por E/S de disco ao longo do tempo (os tempos de espera de disco exibidos no gráfico são proporcionais ao tempo de CPU gasto aguardando E/S):

![](./media/guidance-elasticsearch/query-performance3.png)

***Figura 3. Tempos de espera de E/S de disco da CPU para os clusters D4 e DS4***

É importante entender que há dois motivos predominantes que levam as operações de E/S a bloquear a CPU:

- O subsistema de E/S pode ser ler ou gravar dados do e para o disco.

- O subsistema de E/S pode ser limitado pelo ambiente de host. Os discos do Azure implementados usando HDDs têm uma taxa de transferência máxima de 500 IOPS, e SSDs têm uma taxa de transferência máxima de 5000 IOPS.


Para o cluster D4, a quantidade de tempo gasto aguardando E/S durante a primeira metade do teste está intimamente relacionada, de maneira inversa, ao gráfico que mostra as taxas de E/S. Os períodos de baixa E/S correspondem aos períodos de tempo significativo que a CPU efetivamente perde ao ficar bloqueada; isso indica que a E/S está sofrendo limitação. Conforme mais dados são adicionados ao cluster, a situação se altera e, na segunda metade do teste, os picos de tempo de espera de E/S correspondem aos picos da taxa de transferência de E/S. Nesse momento, a CPU fica bloqueada durante a execução de E/S real. Mais uma vez, com o cluster DS4, o tempo gasto aguardando E/S é muito mais uniforme, e cada pico corresponde a um pico equivalente no desempenho de E/S em vez de uma descida. Isso significa que há pouca ou nenhuma limitação.

Há um outro fator a considerar. Durante o teste, o cluster D4 gerou 10584 erros de ingestão e 21 erros de consulta. O teste no cluster DS4 não produziu erros.

## Resultados de desempenho - expansão

Testes de expansão foram executados pela execução de testes nos clusters de seis nós das VMs DS14, DS4 e DS3. Esses SKUS foram selecionados porque uma VM DS4 fornece o dobro de núcleos de CPU e de memória que uma DS3, e uma máquina DS14 dobra novamente os recursos de CPU fornecendo quatro vezes a quantidade de memória. A tabela a seguir compara os principais aspectos de cada SKU:

 SKU | Nº de Núcleos de CPU | Memória (GB) | IOPS máximo do disco | Largura de banda máxima (MB/s)|
------|-------------|-------------|---------------|--------------|
 DS3 | 4 | 14 | 12\.800| 128 |
 DS4 | 8 | 28 | 25\.600| 256 |
 DS14 | 16 | 112 | 50\.000| 512 |

A tabela a seguir resume os resultados da execução de testes nos clusters pequeno (DS3), médio (DS4) e grande (DS14). Cada VM usou SSDs para manter os dados. Cada teste foi executado por 24 horas:

> **Observação**: a tabela informa o número de solicitações bem-sucedidas para cada tipo de consulta (as falhas não estão incluídas). O número de tentativas de solicitação para cada tipo de consulta é aproximadamente o mesmo durante uma execução do teste. Isso ocorre porque o plano de teste JMeter executa uma única ocorrência de cada consulta (Contagem por Classificação, Contagem ao Longo do Tempo, Ocorrências por País, 15 Principais Organizações, Organizações com Contagem Exclusiva, Contagem de IP Exclusiva e Número Total de Ocorrências) junto em uma única unidade, conhecida como *transação do teste* (essa transação é independente da tarefa que executa a operação de ingestão, que é executada por um thread separado). Cada iteração do plano de teste executa uma única transação de teste. O número de transações de teste concluídas é, portanto, uma medida do tempo de resposta da consulta mais lenta em cada transação.

| HDInsight | Operação/consulta | Número de solicitações | Tempo Médio de Resposta (ms) |
|--------------|----------------------------|--------------------|----------------------------|
| Pequeno (DS3) | Ingestão | 207284 | 3328 |
| | Contagem por classificação | 18444 | 268 |
| | Contagem no tempo | 18444 | 340 |
| | Ocorrências por país | 18445 | 404 |
| | 15 principais organizações | 18439 | 323 |
| | Organizações com contagem exclusiva | 18437 | 338 |
| | Contagem de IP exclusiva | 18442 | 468 |
| | Número total de ocorrências | 18428 | 294   
|||||
| Médio (DS4) | Ingestão | 503157 | 511 |
| | Contagem por classificação | 6958 | 187 |
| | Contagem no tempo | 6958 | 411 |
| | Ocorrências por país | 6958 | 402 |
| | 15 principais organizações | 6958 | 307 |
| | Organizações com contagem exclusiva | 6956 | 320 |
| | Contagem de IP exclusiva | 6955 | 841 |
| | Número total de ocorrências | 6958 | 236 |
|||||
| Grande (DS14) | Ingestão | 502714 | 511 |
| | Contagem por classificação | 7041 | 201 |
| | Contagem no tempo | 7040 | 298 |
| | Ocorrências por país | 7039 | 363 |
| | 15 principais organizações | 7038 | 244 |
| | Organizações com contagem exclusiva | 7037 | 283 |
| | Contagem de IP exclusiva | 7037 | 681 |
| | Número total de ocorrências | 7038 | 200 |

Esses números mostram que, para este teste, o desempenho dos clusters DS4 e DS14 eram razoavelmente semelhantes. Os tempos de resposta das operações de consulta para o cluster DS3 também parecem favoráveis a princípio, e o número de operações de consulta executadas é muito maior do que os valores dos cluster DS4 e DS14. No entanto, também é necessário levar em consideração a taxa de ingestão e o número resultante de documentos pesquisados. No cluster DS3, a ingestão é muito mais restrita e, ao final do teste, o banco de dados continha apenas cerca de 40% dos documentos lidos por cada um dos outros dois clusters. Isso pode ter ocorrido devido aos recursos de processamento, rede e largura de banda de disco disponíveis para uma VM DS3 em comparação com uma VM DS14 ou DS4. Considerando que uma VM DS4 tem duas vezes mais recursos disponíveis que uma VM DS3, e uma DS14 tem duas vezes mais recursos (quatro vezes a memória) que uma VM DS4, resta uma dúvida: por que a diferença nas taxas de ingestão entre os clusters DS4 e DS14 é consideravelmente menor do que o que ocorre entre os clusters DS3 e DS4? Isso pode ocorrer devido à utilização da rede e aos limites de largura de banda das VMs do Azure. Os gráficos abaixo mostram esses dados para todos os três clusters:

![](./media/guidance-elasticsearch/query-performance4.png)

***Figura 4. Utilização de rede dos clusters DS3, DS4 e DS14 que estão executando o teste *ingestão e consulta****

<!-- -->

Os limites de largura de banda de rede disponível nas VMs do Azure não são publicados e podem variar, mas o fato de a atividade de rede parecer ter se firmado em uma média de aproximadamente 2,75 GBps para os testes de DS4 e DS14, sugere que esse limite foi atingido e tornou-se o principal fator de restrição da taxa de transferência. No caso do cluster DS3, a atividade de rede foi consideravelmente menor, portanto, desempenho mais baixo ocorre provavelmente devido a restrições na disponibilidade de outros recursos.

Para isolar os efeitos das operações de ingestão e ilustrar como o desempenho da consulta varia à medida que os nós são escalados verticalmente, foi realizado um conjunto de testes somente consulta usando os mesmos nós. A tabela a seguir resume os resultados obtidos em cada cluster:

> [AZURE.NOTE] Você não deve comparar o desempenho e o número de solicitações executadas por consultas no teste *somente consulta* com aquelas executadas pelo teste de *ingestão e consulta*. Isso ocorre porque as consultas foram modificadas, e o volume de documentos envolvidos é diferente.

| HDInsight | Operação/consulta | Número de solicitações | Tempo Médio de Resposta (ms) |
|--------------|----------------------------|--------------------|----------------------------|
| Pequeno (DS3) | Contagem por classificação | 464 | 11758 |
| | Contagem no tempo | 464 | 14699 |
| | Ocorrências por país | 463 | 14075 |
| | 15 principais organizações | 464 | 11856 |
| | Organizações com contagem exclusiva | 462 | 12314 |
| | Contagem de IP exclusiva | 461 | 19898 |
| | Número total de ocorrências | 462 | 8882  
|||||
| Médio (DS4) | Contagem por classificação | 1045 | 4489 |
| | Contagem no tempo | 1045 | 7292 |
| | Ocorrências por país | 1053 | 7564 |
| | 15 principais organizações | 1055 | 5066 |
| | Organizações com contagem exclusiva | 1051 | 5231 |
| | Contagem de IP exclusiva | 1051 | 9228 |
| | Número total de ocorrências | 1051 | 2180 |
|||||
| Grande (DS14) | Contagem por classificação | 1842 | 1927 |
| | Contagem no tempo | 1839 | 4483 |
| | Ocorrências por país | 1838 | 4761 |
| | 15 principais organizações | 1842 | 2117 |
| | Organizações com contagem exclusiva | 1837 | 2393 |
| | Contagem de IP exclusiva | 1837 | 7159 |
| | Número total de ocorrências | 1837 | 642 |

Desta vez, as tendências nos tempos médios de resposta nos diferentes clusters são mais claras. A utilização de rede está bem abaixo dos 2,75 GBps exigidos anteriormente pelos clusters DS4 e DS14 (que provavelmente saturavam a rede nos testes de ingestão e consulta) e do 1,5 GBps do cluster DS3. Na verdade, fica mais próximo de 200 MBps em todos os casos, como mostram os gráficos abaixo:

![](./media/guidance-elasticsearch/query-performance5.png)

***Figura 5. Utilização de rede dos clusters DS3, DS4 e DS14 que estão executando o teste de *ingestão e consulta****

Agora, o fator limitante dos clusters DS3 e DS4 parece ser a utilização da CPU, que é quase 100% na maior parte do tempo. No cluster DS14, o uso da CPU apresenta uma média um pouco acima de 80%. Continua sendo alto, mas claramente destaca as vantagens de ter mais núcleos de CPU disponíveis. A imagem a seguir retrata os padrões de uso da CPU para os clusters DS3, DS4 e DS14.

![](./media/guidance-elasticsearch/query-performance6.png)

***Figura 6. A utilização da CPU dos clusters DS3 e DS14 que estão executando o teste *somente consulta****

## Resultados de desempenho – Escala horizontal

Para ilustrar como o sistema escala horizontalmente com o número de nós, os testes foram executados usando clusters DS14 compostos por 1, 3 e 6 nós. Desta vez, apenas o teste *somente consulta* foi executado, usando 100 milhões de documentos e com duração de 90 minutos:

> [AZURE.NOTE] Para obter informações detalhadas sobre como a expansão horizontal pode afetar o comportamento das operações de ingestão de dados, consulte o documento [Maximizar o desempenho da ingestão de dados com o Elasticsearch no Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md).

| HDInsight | Operação/consulta | Número de solicitações | Tempo Médio de Resposta (ms) |
|---------|----------------------------|--------------------|----------------------------|
| 1 Nó | Contagem por classificação | 288 | 6216 |
| | Contagem no tempo | 288 | 28933 |
| | Ocorrências por país | 288 | 29455 |
| | 15 principais organizações | 288 | 9058 |
| | Organizações com contagem exclusiva | 287 | 19916 |
| | Contagem de IP exclusiva | 284 | 54203 |
| | Número total de ocorrências | 287 | 3333 |
|||||
| 3 Nós | Contagem por classificação | 1194 | 3427 |
| | Contagem no tempo | 1194 | 5381 |
| | Ocorrências por país | 1191 | 6840 |
| | 15 principais organizações | 1196 | 3819 |
| | Organizações com contagem exclusiva | 1190 | 2938 |
| | Contagem de IP exclusiva | 1189 | 12516 |
| | Número total de ocorrências | 1191 | 1272 |
|||||
| 6 Nós | Contagem por classificação | 1842 | 1927 |
| | Contagem no tempo | 1839 | 4483 |
| | Ocorrências por país | 1838 | 4761 |
| | 15 principais organizações | 1842 | 2117 |
| | Organizações com contagem exclusiva | 1837 | 2393 |
| | Contagem de IP exclusiva | 1837 | 7159 |
| | Número total de ocorrências | 1837 | 642 |

O número de nós faz uma diferença considerável no desempenho de consulta do cluster, embora de uma forma não linear; o cluster de três nós conclui aproximadamente quatro vezes mais consultas que o cluster de nó único, enquanto o cluster de seis nó executa seis vezes mais. Para ajudar a explicar essa falta de linearidade, os gráficos a seguir mostram como a CPU estava sendo consumida pelos três clusters:

![](./media/guidance-elasticsearch/query-performance7.png)

***Figura 7. Utilização da CPU dos clusters de um, três e seis nós que estão executando o teste *somente consulta****

Os clusters de nó único e de três nós são limitados pela CPU, ao passo que embora a utilização da CPU seja alta no cluster de seis nós, há capacidade de processamento de sobra disponível. Nesse caso, provavelmente há outros fatores limitando a taxa de transferência. Isso pode ser confirmado pelo teste com 9 e 12 nós, que provavelmente mostraria ainda mais capacidade de processamento sobressalente.

Os dados na tabela acima também mostram como os tempos médios de resposta das consultas variam. Este é o item que proporciona mais informações ao testar como um sistema pode ser dimensionado para tipos específicos de consulta. Algumas pesquisas são claramente mais eficientes quando envolvem mais nós do que outras. Isso pode ocorrer devido ao aumento do número de nós e do número de documentos no cluster; cada cluster continha 100 milhões de documentos. Ao realizar pesquisas que envolvem a agregação de dados, o Elasticsearch processa e armazena em buffer os dados recuperados como parte do processo de agregação em memória em cada nó. Se houver mais nós, haverá menos dados para recuperar, armazenar em buffer e processar em cada nó.

## Resultados de desempenho - número de réplicas

Os testes de *ingestão e consulta* foram executados em um índice com uma única réplica. Os testes foram repetidos nos clusters DS4 e DS14 de seis nós usando um índice configurado com duas réplicas. Todos os testes foram executados por 24 horas. A tabela a seguir mostra os resultados comparativos de uma e duas réplicas:

| HDInsight | Operação/consulta | Tempo Médio de Resposta (ms)- Uma Réplica | Tempo Médio de Resposta (ms)- Duas Réplicas | % de Diferença no Tempo de Resposta |
|---------|----------------------------|----------------------------------------|-----------------------------------------|-------------------------------|
| DS4 | Ingestão | 511 | 655 | +28% |
| | Contagem por classificação | 187 | 168 | -10% |
| | Contagem no tempo | 411 | 309 | -25% |
| | Ocorrências por país | 402 | 562 | +40% |
| | 15 principais organizações | 307 | 366 | +19% |
| | Organizações com contagem exclusiva | 320 | 378 | +18% |
| | Contagem de IP exclusiva | 841 | 987 | +17% |
| | Número total de ocorrências | 236 | 236 | +0% |
||||||
| DS14 | Ingestão | 511 | 618 | +21% |
| | Contagem por classificação | 201 | 275 | +37% |
| | Contagem no tempo | 298 | 466 | +56% |
| | Ocorrências por país | 363 | 529 | +46% |
| | 15 principais organizações | 244 | 407 | +67% |
| | Organizações com contagem exclusiva | 283 | 403 | +42% |
| | Contagem de IP exclusiva | 681 | 823 | +21% |
| | Número total de ocorrências | 200 | 221 | +11% |

A taxa de ingestão diminuía conforme o número de réplicas aumentava. Isso deve ser esperado, pois o Elasticsearch está gravando mais cópias de cada documento, gerando mais E/S em disco. Isso é refletido nos gráficos do cluster DS14 para índices com uma e duas réplicas mostradas na imagem abaixo. No caso do índice com uma réplica, a taxa média de E/S era de 16896573 bytes por segundo. Para o índice com duas réplicas, a taxa média de E/S era de 33986843 bytes por segundo; um pouco mais do que o dobro.

![](./media/guidance-elasticsearch/query-performance8.png)

***Figura 8. Taxas de E/S de disco para nós com uma e duas réplicas executando o teste de *ingestão e consulta****

| HDInsight | Consultar | Tempo Médio de Resposta (ms)- Uma Réplica | Tempo Médio de Resposta (ms)- Duas Réplicas |
|---------|----------------------------|----------------------------------------|-----------------------------------------|
| DS4 | Contagem por classificação | 4489 | 4079 |
| | Contagem no tempo | 7292 | 6697 |
| | Ocorrências por país | 7564 | 7173 |
| | 15 principais organizações | 5066 | 4650 |
| | Organizações com contagem exclusiva | 5231 | 4691 |
| | Contagem de IP exclusiva | 9228 | 8752 |
| | Número total de ocorrências | 2180 | 1909 |
|||||
| DS14 | Contagem por classificação | 1927 | 2330 |
| | Contagem no tempo | 4483 | 4381 |
| | Ocorrências por país | 4761 | 5341 |
| | 15 principais organizações | 2117 | 2560 |
| | Organizações com contagem exclusiva | 2393 | 2546 |
| | Contagem de IP exclusiva | 7159 | 7048 |
| | Número total de ocorrências | 642 | 708 |

Esses resultados mostram um aprimoramento no tempo médio de resposta para o cluster DS4, mas um aumento para o cluster DS14. Para ajudar a interpretar esses resultados, você também deve considerar o número de consultas executadas por cada teste:

| HDInsight | Consultar | Quantidade executada - 1 réplica | Quantidade executada - 2 réplicas |
|---------|----------------------------|------------------------------|-------------------------------|
| DS4 | Contagem por classificação | 1054 | 1141 |
| | Contagem no tempo | 1054 | 1139 |
| | Ocorrências por país | 1053 | 1138 |
| | 15 principais organizações | 1055 | 1141 |
| | Organizações com contagem exclusiva | 1051 | 1136 |
| | Contagem de IP exclusiva | 1051 | 1135 |
| | Número total de ocorrências | 1051 | 1136 |
|||||
| DS14 | Contagem por classificação | 1842 | 1718 |
| | Contagem no tempo | 1839 | 1716 |
| | Ocorrências por país | 1838 | 1714 |
| | 15 principais organizações | 1842 | 1718 |
| | Organizações com contagem exclusiva | 1837 | 1712 |
| | Contagem de IP exclusiva | 1837 | 1712 |
| | Número total de ocorrências | 1837 | 1712 |

Esses dados mostram o aumento no número de consultas executadas pelo cluster DS4 acompanhando a redução no tempo médio de resposta, mas, novamente, o inverso é verdadeiro para o cluster DS14. Um fator importante é que a utilização de CPU no cluster DS4 nos testes com uma e duas réplicas estava distribuída de forma desigual; alguns nós mostravam uma utilização próxima de 100%, enquanto outros tinham a capacidade de processamento sobressalente. Provavelmente, o aprimoramento no desempenho ocorreu devido ao aumento da capacidade de distribuir o processamento entre os nós do cluster. A imagem a seguir mostra a variação no processamento de CPU entre as VMs com maior e menor utilização (nós 4 e 3):

![](./media/guidance-elasticsearch/query-performance9.png)

***Figura 9. Utilização da CPU para os nós mais e menos usados no cluster DS4 executando o teste *somente consulta****

Para o cluster DS14 esse não era o caso. A utilização da CPU para ambos os testes foi inferior em todos os nós, e a disponibilidade de uma segunda réplica tornou-se menos uma vantagem e mais uma sobrecarga:

![](./media/guidance-elasticsearch/query-performance10.png)

***Figura 10. Utilização da CPU para os nós mais e menos usados no cluster DS14 executando o teste *somente consulta****

Esses resultados mostram a necessidade para avaliar com cuidado o desempenho do sistema ao decidir se usa várias réplicas. Você deve ter sempre pelo menos uma réplica de cada índice (a menos que você esteja disposto a perder dados se um nó falhar), mas réplicas adicionais podem impor uma carga no sistema em troca de pouco benefício, dependendo de suas cargas de trabalho e dos recursos de hardware disponíveis para o cluster.

## Resultados de Desempenho - Valores de Documento

Os testes de *ingestão e consulta* foram realizados com valores de documento habilitados, fazendo com que o Elasticsearch armazene os dados usados na classificação de campos no disco. Os testes foram repetidos com valores de doc desabilitados; portanto, o Elasticsearch criou fielddata dinamicamente e armazenou em cache na memória. Todos os testes foram executados por 24 horas. A tabela a seguir compara os tempos de resposta para testes executados em clusters de seis nós criados com as VMs D4, DS4 e DS14 (o cluster D4 usa discos rígidos regulares, enquanto os clusters DS4 e DS14 usam SSDs).

| HDInsight | Operação/consulta | Tempo Médio de Resposta (ms) - Valores de Documento Habilitados | Tempo Médio de Resposta (ms) - Valores de Documento Desabilitados | % de Diferença no Tempo de Resposta |
|---------|----------------------------|-------------------------------------------------|--------------------------------------------------|-------------------------------|
| D4 | Ingestão | 978 | 835 | -15% |
| | Contagem por classificação | 103 | 132 | +28% |
| | Contagem no tempo | 134 | 189 | +41% |
| | Ocorrências por país | 199 | 259 | +30% |
| | 15 principais organizações | 137 | 184 | +34% |
| | Organizações com contagem exclusiva | 139 | 197 | +42% |
| | Contagem de IP exclusiva | 510 | 604 | +18% |
| | Número total de ocorrências | 89 | 134 | +51% |
||||||
| DS4 | Ingestão | 511 | 581 | +14% |
| | Contagem por classificação | 187 | 190 | +2% |
| | Contagem no tempo | 411 | 409 | -0.5% |
| | Ocorrências por país | 402 | 414 | +3% |
| | 15 principais organizações | 307 | 284 | -7% |
| | Organizações com contagem exclusiva | 320 | 313 | -2% |
| | Contagem de IP exclusiva | 841 | 955 | +14% |
| | Número total de ocorrências | 236 | 281 | +19% |
||||||
| DS14 | Ingestão | 511 | 571 | +12% |
| | Contagem por classificação | 201 | 232 | +15% |
| | Contagem no tempo | 298 | 341 | +14% |
| | Ocorrências por país | 363 | 457 | +26% |
| | 15 principais organizações | 244 | 338 | +39% |
| | Organizações com contagem exclusiva | 283 | 350 | +24% |
| | Contagem de IP exclusiva | 681 | 909 | +33% |
| | Número total de ocorrências | 200 | 245 | +23% |

A tabela a seguir compara o número de operações de ingestão executadas pelos testes:

| HDInsight | Nº de Operações de Ingestão - Valores de Documento Habilitados | Nº de Operações de Ingestão - Valores de Documento Desabilitados | % de Diferença no Nº de Operações de Ingestão |
|---------|----------------------------------------------|-----------------------------------------------|-----------------------------------------|
| D4 | 264769 | 408690 | +54% |
| DS4 | 503137 | 578237 | +15% |
| DS14 | 502714 | 586472 | +17% |

As taxas de ingestão aprimoradas ocorreram com valores de documento desabilitados, pois menos dados são gravados no disco à medida que os documentos são inseridos. O desempenho aprimorado é particularmente perceptível com a VM D4 usando HDDs para armazenar dados. Nesse caso, o tempo de resposta para operações de ingestão também diminuiu em 15% (consulte a primeira tabela nesta seção). Isso pode ocorrer devido à redução da pressão sobre os HDDs, que provavelmente estavam executando próximo ao limite de IOPS no teste com valores de documento habilitados. Consulte o teste de Tipo de disco para saber mais. O gráfico a seguir compara o desempenho de E/S das VMs D4 com valores de documento habilitados (valores mantidos em disco) e valores de documento desabilitado (valores mantidos na memória):

![](./media/guidance-elasticsearch/query-performance11.png)

***Figura 11. Atividade de disco para o cluster D4 com valores de documento habilitados e desabilitados***

Em comparação, os valores de ingestão para as VMs usando SSDs mostram um pequeno aumento no número de documentos, mas também um aumento no tempo de resposta das operações de ingestão. Com uma ou duas pequenas exceções, os tempos de resposta de consulta também foram piores. Os SSDs apresentam uma probabilidade menor de executarem próximos ao limite de IOPS com valores de documento habilitados. Portanto, as alterações no desempenho ocorrem provavelmente devido à maior atividade de processamento e à sobrecarga de gerenciamento de heap de JVM. Isso fica evidente ao comparar a utilização da CPU com valores de documento habilitados e desabilitados. O próximo gráfico destaca esses dados para o cluster DS4, onde a maioria da utilização da CPU muda da faixa entre 30% e 40% com valores de documento habilitados para a faixa entre 40% e 50% com valores de documento desabilitados (o cluster DS14 mostrou uma tendência semelhante):

![](./media/guidance-elasticsearch/query-performance12.png)

***Figura 12. Utilização de CPU para o cluster DS4 com valores de documento habilitados e desabilitados***

Para distinguir os efeitos dos valores de documento sobre o desempenho da consulta na ingestão de dados, foram executados pares de testes somente consulta para os clusters DS4 e DS14 com valores de documento habilitados e desabilitados. A tabela abaixo resume os resultados desses testes:

| HDInsight | Operação/consulta | Tempo Médio de Resposta (ms) - Valores de Documento Habilitados | Tempo Médio de Resposta (ms) - Valores de Documento Desabilitados | % de Diferença no Tempo de Resposta |
|---------|----------------------------|-------------------------------------------------|--------------------------------------------------|-------------------------------|
| DS4 | Contagem por classificação | 4489 | 3736 | -16% |
| | Contagem no tempo | 7293 | 5459 | -25% |
| | Ocorrências por país | 7564 | 5930 | -22% |
| | 15 principais organizações | 5066 | 3874 | -14% |
| | Organizações com contagem exclusiva | 5231 | 4483 | -2% |
| | Contagem de IP exclusiva | 9228 | 9474 | +3% |
| | Número total de ocorrências | 2180 | 1218 | -44% |
||||||
| DS14 | Contagem por classificação | 1927 | 2144 | +11% |
| | Contagem no tempo | 4483 | 4337 | -3% |
| | Ocorrências por país | 4761 | 4840 | +2% |
| | 15 principais organizações | 2117 | 2302 | +9% |
| | Organizações com contagem exclusiva | 2393 | 2497 | +4% |
| | Contagem de IP exclusiva | 7159 | 7639 | +7% |
| | Número total de ocorrências | 642 | 633 | -1% |

Lembre-se de que os valores de documento estão habilitados por padrão na versão 2.0 e posteriores do Elasticsearch. Nos testes que envolvem o cluster DS4, desabilitar os valores de documento parece ter um efeito geral positivo, enquanto o inverso é geralmente verdadeiro para o cluster DS14 (os dois casos nos quais o desempenho é melhor com valores de documento desabilitados são muito pequenos).

Para o cluster DS4, a utilização de CPU nos dois casos estava próxima a 100% durante os dois testes, indicando que o cluster foi associado à CPU. No entanto, o número de consultas processadas diminuiu de 7369 para 5894 (20%)? Consulte a tabela a seguir. Lembre-se de que se os valores de documento estiverem desabilitados, o Elasticsearch gerará dinamicamente fielddata na memória, e isso consome recursos da CPU. Essa configuração reduziu a taxa de E/S de disco, mas aumentou a sobrecarga em CPUs já próximas de sua capacidade máxima. Nesse caso, as consultas são mais rápidas com valores de documento desabilitados, mas há menos delas.

Nos testes com DS14, com e sem valores de documento, a atividade da CPU foi alta, mas não 100%. O número de consultas executadas foi um pouco maior (cerca de %4) em testes com valores de documento habilitados:

| HDInsight | Consultar | Quantidade Realizada - Valores de Documento Habilitados | Quantidade Realizada - Valores de Documento Desabilitados |
|---------|----------------------------|---------------------------------------|----------------------------------------|
| DS4 | Contagem por classificação | 1054 | 845 |
| | Contagem no tempo | 1054 | 844 |
| | Ocorrências por país | 1053 | 842 |
| | 15 principais organizações | 1055 | 846 |
| | Organizações com contagem exclusiva | 1051 | 839 |
| | Contagem de IP exclusiva | 1051 | 839 |
| | Número total de ocorrências | 1051 | 839  
||||| |
| DS14 | Contagem por classificação | 1772 | 1842 |
| | Contagem no tempo | 1772 | 1839 |
| | Ocorrências por país | 1770 | 1838 |
| | 15 principais organizações | 1773 | 1842 |
| | Organizações com contagem exclusiva | 1769 | 1837 |
| | Contagem de IP exclusiva | 1768 | 1837 |
| | Número total de ocorrências | 1769 | 1837 |

## Resultados de desempenho - Cache de Solicitação de Fragmento

Para demonstrar como os dados de índice de cache na memória de cada nó podem afetar o desempenho, o teste de *consulta e ingestão* foi conduzido em um cluster DS4 e DS14 com seis nós e com cache de índice habilitado. Consulte a seção [Como usar o cache de solicitação de fragmento](#using-the-shard-request-cache) para saber mais. Os resultados foram comparados àqueles gerados pelos testes anteriores usando o mesmo índice, mas com o cache de índice desabilitado. A tabela abaixo resume os resultados: Observe que os dados foram reduzidos para abranger somente os 90 minutos iniciais do teste; nesse momento, a tendência comparativa era aparente e a continuação do teste provavelmente não geraria quaisquer informações adicionais:

| HDInsight | Operação/consulta | Tempo Médio de Resposta (ms) - Cache de Índice Desabilitado | Tempo Médio de Resposta (ms) - Cache de Índice Habilitado | % de Diferença no Tempo de Resposta |
|---------|----------------------------|---------------------------------------------------|--------------------------------------------------|-------------------------------|
| DS4 | Ingestão | 504 | 3260 | +547% |
| | Contagem por classificação | 218 | 273 | +25% |
| | Contagem no tempo | 450 | 314 | -30% |
| | Ocorrências por país | 447 | 397 | -11% |
| | 15 principais organizações | 342 | 317 | -7% |
| | Organizações com contagem exclusiva | 370 | 324 | -12%% |
| | Contagem de IP exclusiva | 760 | 355 | -53% |
| | Número total de ocorrências | 258 | 291 | +12% |
||||||
| DS14 | Ingestão | 503 | 3365 | +569% |
| | Contagem por classificação | 234 | 262 | +12% |
| | Contagem no tempo | 357 | 298 | -17% |
| | Ocorrências por país | 416 | 383 | -8% |
| | 15 principais organizações | 272 | 324 | -7% |
| | Organizações com contagem exclusiva | 330 | 321 | -3% |
| | Contagem de IP exclusiva | 674 | 352 | -48% |
| | Número total de ocorrências | 227 | 292 | +29% |

Esses dados mostram dois pontos interessantes:

-  As taxas de ingestão de dados parecem diminuir bastante com a habilitação do cache de índice, e

-  O cache de índice não melhora necessariamente o tempo de resposta de todos os tipos de consulta e pode ter um efeito adverso em determinadas operações de agregação, como aquelas executadas pelas consultas Contagem por Classificação e Número Total de Ocorrências.
 

Para entender o motivo de o sistema exibir esse comportamento, você deve considerar o número de consultas executadas com êxito em cada caso, durante as execuções de teste. A tabela a seguir resume esses dados:

| HDInsight | Operação/consulta | Nº de Operações/Consultas - Cache de Índice Desabilitado | Nº de Operações/Consultas - Cache de Índice Habilitado |
|---------|----------------------------|-------------------------------------------------|------------------------------------------------|
| DS4 | Ingestão | 38611 | 13232 |
| | Contagem por classificação | 524 | 18704 |
| | Contagem no tempo | 523 | 18703 |
| | Ocorrências por país | 522 | 18702 |
| | 15 principais organizações | 521 | 18706 |
| | Organizações com contagem exclusiva | 521 | 18700 |
| | Contagem de IP exclusiva | 521 | 18699 |
| | Número total de ocorrências | 521 | 18701  
|||| |
| DS14 | Ingestão | 38769 | 12835 |
| | Contagem por classificação | 528 | 19239 |
| | Contagem no tempo | 528 | 19239 |
| | Ocorrências por país | 528 | 19238 |
| | 15 principais organizações | 527 | 19240 |
| | Organizações com contagem exclusiva | 524 | 19234 |
| | Contagem de IP exclusiva | 524 | 19234 |
| | Número total de ocorrências | 527 | 19236 |

Você pode ver que, embora a taxa de ingestão com o cache habilitado seja aproximadamente 1/3 de quando o cache estava desabilitado, o número de consultas executadas aumentou com um fator de 34. As consultas não geram mais E/S de disco e não precisam competir por recursos de disco. Isso se reflete nos gráficos da Figura 13 abaixo, que compara a atividade de E/S para todos os quatro casos:

![](./media/guidance-elasticsearch/query-performance13.png)

***Figura 13. Atividade de E/S de disco para o teste de *ingestão e consulta* com o cache de índice desabilitado e habilitado***

A redução de E/S de disco também significa que a CPU gastou menos tempo aguardando a conclusão da E/S. Isso está realçado na Figura 14:

![](./media/guidance-elasticsearch/query-performance14.png)

***Figura 14. Tempo gasto da CPU aguardando a conclusão da E/S do disco para o teste de *ingestão e consulta* com o cache de índice desabilitado e habilitado***

A redução de E/S de disco significa que o Elasticsearch poderia gastar uma proporção muito maior de seu tempo atendendo a consultas de dados armazenados na memória. Isso aumentou a utilização da CPU, o que se torna aparente se você examinar a utilização da CPU para todos os quatro casos. Os gráficos a seguir mostram como o uso da CPU ficou mais estável com o cache habilitado:

![](./media/guidance-elasticsearch/query-performance15.png)

***Figura 15. Utilização de CPU para o teste de *ingestão e consulta* com o cache de índice desabilitado e habilitado***

O volume de E/S de rede nos dois cenários durante os testes foi bastante semelhante. Os testes sem cache mostraram uma redução gradual durante o período de teste, mas as execuções mais longas, de 24 horas, dos testes mostrou que essa estatística ficou nivelada em aproximadamente 2,75 GBps. A imagem a seguir mostra esses dados para os clusters DS4 (os dados para os clusters DS14 eram muito semelhantes):

![](./media/guidance-elasticsearch/query-performance16.png)

***Figura 16. Volumes de tráfego de rede para o teste de *ingestão e consulta* com o cache de índice desabilitado e habilitado***

Conforme descrito no teste [Escalar verticalmente](#performance-results-scaling-up), as restrições de largura de banda com VMs do Azure não são publicadas e podem variar, mas os níveis moderados de atividade da CPU e do disco sugerem que a utilização da rede pode ser o fator limitante nesse cenário.

O cache é mais adequado em cenários nos quais os dados mudam com pouca frequência. Para realçar o impacto do armazenamento em cache nesse cenário, os testes *somente consulta* foram executados com o cache habilitado. Os resultados estão abaixo (esses testes foram executados durante 90 minutos, e os índices em teste continham 100 milhões de documentos):

| HDInsight | Consultar | Tempo Médio de Resposta (ms) | Nº de Consultas Executadas |
|---------|----------------------------|----------------------------|-------------------------|
| | | **Cache Desabilitado** | **Cache Habilitado** |
| DS4 | Contagem por classificação | 4489 | 210 |
| | Contagem no tempo | 7292 | 211 |
| | Ocorrências por país | 7564 | 231 |
| | 15 principais organizações | 5066 | 211 |
| | Organizações com contagem exclusiva | 5231 | 211 |
| | Contagem de IP exclusiva | 9228 | 218 |
| | Número total de ocorrências | 2180 | 210  
|||| |
| DS14 | Contagem por classificação | 1927 | 211 |
| | Contagem no tempo | 4483 | 219 |
| | Ocorrências por país | 4761 | 236 |
| | 15 principais organizações | 2117 | 212 |
| | Organizações com contagem exclusiva | 2393 | 212 |
| | Contagem de IP exclusiva | 7159 | 220 |
| | Número total de ocorrências | 642 | 211 |

A variação no desempenho dos testes não armazenados em cache ocorre devido à diferença nos recursos disponíveis entre as VMs DS4 e DS14. Em ambos os casos do teste em cache o tempo médio de resposta caiu consideravelmente, pois os dados eram recuperados diretamente da memória. Também vale a pena observar que os tempos de resposta para os testes nos clusters DS4 e DS14 em cache eram muito semelhantes, apesar da disparidade com os resultados não armazenados em cache. Também há pouca diferença entre os tempos de resposta para cada consulta dentro de cada teste; todos eles levam aproximadamente 220ms. As taxas de E/S de disco e a utilização da CPU para os dois clusters foram muito baixas, pois uma vez que todos os dados estão na memória, há a necessidade de pouco processamento de E/S. A taxa de E/S de rede foi semelhante a dos testes sem cache, confirmando que largura de banda de rede pode ser um fator limitante nesse teste. Os gráficos a seguir apresentam essas informações para o cluster DS4. O perfil do cluster DS14 era bastante semelhante:

![](./media/guidance-elasticsearch/query-performance17.png)

***Figura 17. E/S de disco, utilização da CPU e a utilização de rede para o teste *somente consulta* com cache de índice habilitado***

Os valores na tabela acima sugerem que o uso da arquitetura DS14 apresenta pouco benefício com relação ao DS4. Na verdade, o número de amostras gerado pelo cluster DS14 era aproximadamente 5% abaixo com relação ao cluster DS4, mas isso também pode ser devido a restrições de rede que podem variar um pouco ao longo do tempo.

## Resultados de Desempenho - Número de Fragmentos

O objetivo desse teste era determinar se o número de fragmentos criados para um índice tinha qualquer influência sobre o desempenho de consulta do índice.

Testes separados realizados anteriormente mostraram que a configuração de fragmento de um índice pode afetar a taxa de ingestão de dados. Esses testes são descrito no documento [Maximizar o desempenho da ingestão de dados com o Elasticsearch no Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md). Os testes conduzidos para avaliar o desempenho da consulta seguiram uma metodologia parecida, mas eram restritos a um cluster de 6 nós em execução no hardware DS14. Essa abordagem ajuda a minimizar o número de variáveis. Portanto, quaisquer diferenças de desempenho devem ser devido ao volume de fragmentos.

O teste *somente consulta* foi realizado em cópias do mesmo índice configuradas com 7, 13, 23, 37 e 61 fragmentos primários. O índice continha 100 milhões de documentos e tinha uma única réplica, dobrando o número de fragmentos no cluster. Cada teste foi executado durante 90 minutos. A tabela a seguir resume os resultados: O tempo médio de resposta mostrado é o tempo de resposta da transação de teste JMeter que envolve o conjunto completo de consultas executadas por cada iteração do teste. Consulte a observação na seção [Resultados de desempenho - expansão](#performance-results-scaling-up) para saber mais:

| Número de fragmentos | Layout de fragmentos (fragmentos por nó, incluindo réplicas) | Número de consultas executadas | Tempo médio de resposta (ms) |
|---------------------------|----------------------------------------------------|-----------------------------|------------------------|
| 7 (14 incluindo réplicas) | 3-2-2-2-2-3 | 7461 | 40524 |
| 13 (26) | 5-4-5-4-4-4 | 7369 | 41055 |
| 23 (46) | 7-8-8-7-8-8 | 14193 | 21283 |
| 37 (74) | 13-12-12-13-12-12 | 13399 | 22506 |
| 61 (122) | 20-21-20-20-21-20 | 14743 | 20445 |

Esses resultados indicam que há uma diferença considerável de desempenho entre o cluster de fragmento de 13(26) e o cluster de fragmento 23(46); a taxa de transferência praticamente dobra e os tempos de resposta são reduzidos pela metade. Isso ocorre provavelmente devido à configuração das VMs e às estruturas usadas pelo Elasticsearch para processar solicitações de pesquisa. As solicitações de pesquisa são enfileiradas, e cada solicitação de pesquisa é manipulada por um único thread de pesquisa. O número de threads de pesquisa criados por um nó do Elasticsearch é uma função do número de processadores disponíveis na máquina que hospeda o nó. Os resultados sugerem que com apenas quatro ou cinco fragmentos em um nó, os recursos de processamento não são totalmente utilizados. Isso é comprovado pela observação da utilização da CPU durante a execução desse teste. A imagem a seguir é um instantâneo tirado do Marvel durante a execução do teste de fragmento 13(26):

![](./media/guidance-elasticsearch/query-performance18.png)

***Figura 18. Utilização da CPU para o teste *somente consulta* no cluster de fragmento 7(14)***

Compare esses valores àqueles do teste do fragmento 23(46):

![](./media/guidance-elasticsearch/query-performance19.png)

***Figura 19. Utilização da CPU para o teste *somente consulta* no cluster de fragmento 23(46)***

No teste do fragmento 23(46), a utilização de CPU foi muito maior. Cada nó contém sete ou oito fragmentos. A arquitetura do DS14 oferece 16 processadores e o Elasticsearch pode explorar melhor esse número de núcleos com fragmentos adicionais. Os valores na tabela acima sugerem que aumentar o número de fragmentos além desse ponto pode melhorar um pouco o desempenho, mas você deve compensar esses números com base na sobrecarga adicional de manutenção de um volume alto de fragmentos. O ponto ideal desses testes implica em que o número ideal de fragmentos por nó seja metade do número de núcleos de processador disponíveis em cada nó. No entanto, lembre-se de que esses resultados foram obtidos apenas durante a execução de consultas. Se o seu sistema importar dados, considere também como a fragmentação pode afetar o desempenho das operações de ingestão de dados. Para saber mais sobre esse aspecto, confira o documento [Maximizar o desempenho da ingestão de dados com o Elasticsearch no Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md).

## Resumo

O Elasticsearch fornece muitas opções para estruturar índices e ajustá-los a fim de dar suporte a operações de consulta em larga escala. Este documento resumiu algumas configurações e técnicas comuns que você pode usar para ajustar seu banco de dados para fins de consulta. No entanto, você deve reconhecer que há uma compensação entre a otimização de um banco de dados para dar suporte à recuperação rápida, em vez de oferecer suporte à ingestão de grandes volumes de dados. Às vezes, o que é bom para a consulta pode ter um impacto negativo sobre operações de inserção e vice-versa. Em um sistema que é exposto a cargas de trabalho variadas, você precisa avaliar onde está o equilíbrio e ajustar os parâmetros do sistema adequadamente.

Além disso, a aplicabilidade de configurações e técnicas diferentes pode variar dependendo da estrutura dos dados e das limitações (ou outro fator) do hardware no qual o sistema foi construído. Muitos testes mostrados neste documento ilustram como a seleção da plataforma de hardware pode afetar a taxa de transferência e como algumas estratégias podem ser vantajosas em certos casos, mas prejudiciais em outros. O importante é entender as opções disponíveis e executar estudos de parâmetros de comparação usando seus próprios dados para determinar a combinação ideal.

Finalmente, lembre-se de que um banco de dados Elasticsearch não é necessariamente um item estático. Provavelmente, ele aumentará ao longo do tempo, e talvez seja necessário revisar regularmente as estratégias usadas para estruturar os dados. Por exemplo, pode ser necessário escalar verticalmente, horizontalmente ou reindexar os dados com fragmentos adicionais. À medida que o sistema aumenta de tamanho e complexidade, esteja preparado para testar continuamente o desempenho a fim de garantir que você ainda esteja atendendo a quaisquer SLAs garantidos aos seus clientes.

## Apêndice: O teste de desempenho de consulta e agregação

Este apêndice descreve o teste de desempenho executado no cluster Elasticsearch. Os testes foram realizados usando o JMeter executado em um conjunto separado de VMs. Os detalhes da configuração do ambiente de teste foram descritos no documento Como criar um ambiente de teste de desempenho para o Elasticsearch. Para executar seus próprios testes, você pode criar manualmente seu próprio plano de teste do JMeter seguindo a orientação neste apêndice, ou pode usar os scripts de teste automatizados disponíveis separadamente. Veja o documento Como executar os testes de consulta automatizados do Elasticsearch para saber mais.

A carga de trabalho de consulta de dados executou o conjunto de consultas descrito abaixo durante a execução simultânea de um carregamento em larga escala de documentos (os dados foram carregados por meio de um teste JUnit, seguindo a mesma abordagem dos testes de ingestão de dados descritos no documento [Maximizar o desempenho de ingestão de dados com o Elasticsearch no Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md)). A finalidade dessa carga de trabalho era simular um ambiente de produção no qual novos dados são constantemente adicionados durante a execução das pesquisas. As consultas eram estruturadas para recuperar apenas os dados mais recentes de documentos adicionados nos últimos 15 minutos.

Cada documento foi armazenado em um único índice chamado *idx* com o tipo *doc*. Você pode usar a solicitação HTTP a seguir para criar o índice. Os valores das configurações *number\_of\_replicas*, *number\_of\_shards* variaram em comparação aos valores mostrados abaixo em muitos dos testes. Além disso, para os testes que usaram fielddata em vez de valores de documento, cada propriedade recebeu o atributo *"doc\_values": false*.

> **Importante**. O índice foi removido e recriado antes de cada execução de teste.

``` http
PUT /idx
{  
    "settings" : {
        "number_of_replicas": 1,
        "refresh_interval": "30s",
        "number_of_shards": "5",
        "index.translog.durability": "async"    
    },
    "doc": {
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

As consultas a seguir foram executadas pelo teste:
* Quantos documentos com cada valor Classificação foram inseridos nos últimos 15 minutos?

  ```http
  GET /idx/doc/_search
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

* Quantos documentos foram adicionados em cada intervalo de 5 minutos durante os últimos 15 minutos?

  ```http
  GET /idx/doc/_search
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

* Quantos documentos de cada valor Classificação foram adicionados para cada país nos últimos 15 minutos?

  ```HTTP
  GET /idx/doc/_search
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

* Quais são as 15 organizações que ocorrem com mais frequência nos documentos adicionados nos últimos 15 minutos?

  ```http
  GET /idx/doc/_search
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

* Quantas organizações diferentes ocorrem nos documentos adicionados nos últimos 15 minutos?

  ```http
  GET /idx/doc/_search
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

* Quantos documentos foram adicionados nos últimos 15 minutos?

  ```http
  GET /idx/doc/_search
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

* Quantos valores de SourceIp diferentes ocorrem nos documentos adicionados nos últimos 15 minutos?

  ```http
  GET /idx/doc/_search
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

<!---HONumber=AcomDC_0302_2016-->