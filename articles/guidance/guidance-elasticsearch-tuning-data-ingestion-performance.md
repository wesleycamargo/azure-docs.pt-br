<properties
   pageTitle="Ajuste de desempenho da ingestão de dados com o Elasticsearch no Azure | Microsoft Azure"
   description="Como maximizar o desempenho da ingestão de dados com o Elasticsearch no Azure."
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

# Ajustando o desempenho da ingestão de dados com o Elasticsearch no Azure

Este artigo faz [parte de uma série](guidance-elasticsearch.md).

## Visão geral

Um aspecto importante da criação de qualquer banco de dados de pesquisa é determinar a melhor maneira de estruturar o sistema para ingerir dados pesquisáveis de maneira rápida e eficiente. As considerações sobre esse requisito dizem respeito não apenas à opção de infraestrutura na qual você pode implementar o sistema, mas também às várias otimizações que você pode usar para ajudar a garantir que o sistema possa acompanhar os níveis esperados de influxo de dados.

Este documento descreve as opções de implantação e de configuração que devem ser consideradas para a implementação de um cluster Elasticsearch que espera uma alta taxa de ingestão de dados. Para fornecer dados sólidos para fins ilustrativos, este documento também mostra os resultados do parâmetro de avaliação de várias configurações usando uma carga de trabalho de ingestão de grandes volumes de dados simples. Os detalhes da carga de trabalho são descritos no [Apêndice](#appendix-the-bulk-load-data-ingestion-performance-test) ao final deste documento.

A finalidade dos parâmetros de configuração não era gerar números absolutos de desempenho para a execução do Elasticsearch ou até mesmo recomendar uma topologia específica, mas ilustrar os métodos que você pode usar para avaliar o desempenho, para dimensionar os nós de dados e para implementar os clusters que podem atender a seus requisitos de desempenho.

Ao dimensionar seus próprios sistemas, é importante testar minuciosamente o desempenho com base em suas próprias cargas de trabalho. Colete a telemetria que permite que você obtenha informações sobre a configuração de hardware ideal a ser usada e os fatores de dimensionamento horizontais que você deve considerar. Em particular, você deve:

- Considerar o tamanho geral da carga enviada e não apenas o número de itens em cada solicitação de inserção em massa. Um número menor de grandes itens em massa em cada solicitação poderia ser melhor do que um número maior, dependendo do recurso disponível para processar cada solicitação.

Você pode monitorar os efeitos da variação de solicitação de inserção em massa usando o Marvel, usando os contadores de E/S *readbytes*/*writebytes* com o JMeter e ferramentas do sistema operacional, como o *iostat* e o *vmstat* no Ubuntu.

- Conduza testes de desempenho e obtenha telemetria para o processamento de medida da CPU e de espera de E/S, latência de disco, taxa de transferência e tempos de resposta. Essa informação pode ajudar a identificar possíveis afunilamentos e a avaliar os custos e os benefícios do uso do armazenamento premium. Tenha em mente que a utilização de CPU e de disco pode não ser igual em todos os nós, dependendo do modo como fragmentos e réplicas são distribuídos entre o cluster (alguns nós podem conter mais fragmentos do que outros).

- Considere como o número de solicitações simultâneas para sua carga de trabalho será distribuído entre o cluster e avaliará o impacto de usar números diferentes de nós para lidar com essa carga de trabalho.

- Considere como as cargas de trabalho podem crescer conforme os negócios se expandem. Avalie o impacto desse aumento nos custos das VMs e do armazenamento usados pelos nós.

- Reconheça que o uso de um cluster com um grande número de nós com discos regulares poderá ser mais econômico se seu cenário exigir um alto número de solicitações e se a infraestrutura de disco mantiver uma taxa de transferência que satisfaça seus SLAS. No entanto, aumentar o número de nós pode introduzir uma sobrecarga na forma de comunicações e de sincronização entre os nós.

- Entenda que um número maior de núcleos por nó pode gerar mais tráfego de disco já que mais documentos poderão ser processados. Nesse caso, meça a utilização de disco para avaliar se o subsistema de E/S pode se tornar um afunilamento e determinar os benefícios do uso do armazenamento premium.

- Teste e anlise as compensações de um maior número de nós com menos núcleos versus menos nós com mais núcleos. Tenha em mente que o aumento do número de réplicas aumenta as demandas no cluster e pode exigir a adição de nós.

- Considere que o uso de discos efêmeros pode exigir que os índices tenham de ser recuperados com mais frequência.

- Meça o uso do volume de armazenamento para avaliar a capacidade e a subutilização de armazenamento. Por exemplo, em nosso cenário, armazenamos 1,5 bilhão de documentos usando 350 GB de armazenamento.

- Meça as taxas de transferência para suas cargas de trabalho e considere a probabilidade de você atingir o limite total de transferência de taxa de E/S para qualquer conta de armazenamento na qual você tenha criado os discos virtuais.

## Design de índice e de nó

Em um sistema que tiver de oferecer suporte à ingestão de dados em larga escala, faça as seguintes perguntas:

- **Os dados são relativamente estáticos ou ágeis?** Quanto mais dinâmicos os dados, maior a sobrecarga de manutenção do Elasticsearch. Se os dados forem replicados, cada réplica será mantida de forma síncrona. Os dados ágeis com uma vida limitada ou que possam ser facilmente reconstruídos podem se beneficiar da desabilitação total da replicação. Essa opção será explicada em mais detalhes na seção [Ajuste da ingestão de dados em larga escala.](#tuning-large-scale-data-ingestion)

- **Qual é o grau de atualização que você exige para os dados descobertos por meio de pesquisa?** Para manter o desempenho, o Elasticsearch armazena em buffers o máximo possível de dados na memória. Isso significa que nem todas as alterações ficarão imediatamente disponíveis para solicitações de pesquisa. O processo pelo qual o Elasticsearch mantém as alterações e as torna visíveis está descrito online no documento [Tornando alterações persistentes](https://www.elastic.co/guide/en/elasticsearch/guide/current/translog.html#translog).

    A taxa na qual os dados se tornam visíveis é regida pela configuração *refresh\_interval* do índice relevante. Por padrão, esse intervalo é definido em um segundo. No entanto, nem toda situação requer que as atualizações ocorram tão rápido. Por exemplo, os índices que registram dados de log podem precisar lidar com um influxo rápido e contínuo de informações que precisem ser ingeridas rapidamente, mas que não exijam que as informações estejam imediatamente disponíveis para consultas. Nesse caso, considere a redução da frequência de atualizações. Esse recurso também será descrito na seção [Ajuste da ingestão de dados em larga escala.](#tuning-large-scale-data-ingestion)

- **Com que rapidez os dados provavelmente crescerão?** A capacidade de índice é determinada pelo número de fragmentos especificado quando o índice é criado. Para permitir o crescimento, especifique um número adequado de fragmentos (o padrão é cinco). Se o índice for criado inicialmente em um único nó, todos os cinco fragmentos estarão localizados nesse nó, mas à medida que o volume de dados crescer, outros nós poderão ser adicionados e o Elasticsearch distribuirá dinamicamente os fragmentos pelos nós. No entanto, cada fragmento tem uma sobrecarga; todas as pesquisas em um índice consultarão todos os fragmentos e, portanto, a criação de um grande número de fragmentos para uma pequena quantidade de dados pode degradar as recuperações de dados (evitar o cenário de [fragmentos Kagillion](https://www.elastic.co/guide/en/elasticsearch/guide/current/kagillion-shards.html)).

    Algumas cargas de trabalho (como o registro em log) podem criar um novo índice a cada dia, e se você observar que o número de fragmentos é insuficiente para o volume de dados, deverá alterá-lo antes de criar o próximo índice (índices existentes serão afetados). Se for necessário distribuir os dados existentes por mais fragmentos, então uma opção será reindexar as informações; crie um novo índice com a configuração apropriada e copie os dados para ele. Esse processo poderá se tornar transparente para os aplicativos caso os [aliases de índice](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html) sejam usados.

- **Os dados precisam ser particionados entre usuários em um cenário de multilocação?** Você pode criar índices separados para cada usuário, mas isso poderá ser caro, se cada usuário tiver apenas uma quantidade moderada de dados. Em vez disso, considere a criação de [índices compartilhados](https://www.elastic.co/guide/en/elasticsearch/guide/current/shared-index.html) e use [aliases baseados em filtros](https://www.elastic.co/guide/en/elasticsearch/guide/current/faking-it.html) para direcionar solicitações para os dados por usuário. Para manter os dados de um usuário juntos no mesmo fragmento, substitua a configuração de roteamento padrão pelos dados de índice e de rota com base em um atributo de identificação do usuário.

- **Os dados têm curta ou longa duração?** Se você estiver usando um conjunto de máquinas virtuais do Azure para implementar um cluster Elasticsearch, poderá armazenar dados efêmeros em um disco do sistema do recurso local em vez de uma unidade conectada. Usar um SKU de VM que utilize um SSD para o disco de recurso pode melhorar o desempenho de E/S. No entanto, todas as informações mantidas no disco de recursos são temporárias e poderão ser perdidas se a VM for reiniciada (confira a seção Quando os dados em uma unidade temporária serão perdidos no documento [Noções básicas sobre a unidade temporária em máquinas virtuais do Microsoft Azure](http://blogs.msdn.com/b/mast/archive/2013/12/07/understanding-the-temporary-drive-on-windows-azure-virtual-machines.aspx) para obter mais detalhes). Se você precisa manter dados entre as reinicializações, crie discos de dados para armazenar essas informações e os anexe à máquina virtual.

- **Qual é o grau de atividade dos dados?** Os VHDs do Azure estarão sujeitos à limitação caso a quantidade de atividade exceda os parâmetros especificados (atualmente, 500 IOPS para um disco conectado a uma VM da Camada Standard e 5000 IOPs para um disco de Armazenamento Premium).

    Para reduzir as chances de limitação e aumentar o desempenho de E/S, considere a criação de vários discos de dados para cada VM e configure o Elasticsearch para dividir os dados entre esses discos, como descrito em [Requisitos de disco e do sistema de arquivos](guidance-elasticsearch-running-on-azure.md#disk-and-file-system-requirements).

    Você deve selecionar uma configuração de hardware que ajude a minimizar o número de operações de leitura de E/S de disco, garantindo que memória suficiente esteja disponível para os dados acessados com frequência do cache. Isso é descrito na seção [Requisitos de memória](guidance-elasticsearch-running-on-azure.md#memory-requirements) do documento Implementando o Elasticsearch no Azure.

- **Para que tipo de carga de trabalho cada nó deverá dar suporte?** O Elasticsearch se beneficia da memória disponível no qual os dados são armazenados em cache (em forma de cache do sistema de arquivos) e para o heap da JVM, como descrito na seção [Requisitos de memória](guidance-elasticsearch-running-on-azure.md#memory-requirements) do documento Implementando o Elasticsearch no Azure.

    A quantidade de memória, o número de núcleos de CPU e a quantidade de discos disponíveis são definidos pelo SKU da máquina virtual. Para saber mais, confira a página [Preços de máquinas virtuais](http://azure.microsoft.com/pricing/details/virtual-machines/) no site do Azure.

### Opções de máquina virtual

Você pode provisionar as VMs no Azure usando alguns SKUs diferentes. Os recursos disponíveis para uma VM do Azure dependerão do SKU selecionado. Cada SKU oferece uma combinação diferente de núcleos, memória e armazenamento. Você precisa selecionar um tamanho apropriado da VM que irá manipular a carga de trabalho esperada, mas que também se prove econômica. Inicie com uma configuração que atendará às suas necessidades atuais (execute parâmetros de comparação para testar, como descrito mais adiante neste documento). Você pode dimensionar um cluster mais tarde, adicionando mais VMs que executem nós Elasticsearch.

A página [Tamanhos das Máquinas Virtuais](virtual-machines-size-specs/) no site do Azure documenta as várias opções e os SKUs disponíveis para as máquinas virtuais.

Você deve corresponder o tamanho e os recursos de uma VM à função que os nós em execução na VM executarão.

Para um nó de dados:

- Aloque até 30 GB ou 50% da memória RAM disponível para o heap Java, o que for menor. Deixe o restante para o sistema operacional usar para armazenar em cache os arquivos. Se você estiver usando o Linux, poderá especificar a quantidade de memória a ser alocada no heap Java, definindo a variável de ambiente ES\_HEAP\_SIZE antes da execução do Elasticsearch. Como alternativa, se você estiver usando o Windows ou o Linux, poderá estipular o tamanho da memória com os parâmetros *Xmx* e *Xms* ao iniciar o Elasticsearch.

    Dependendo da carga de trabalho, ter menos VMs grandes pode não ser tão eficiente para o desempenho quanto usar um número maior de VMs de tamanhos moderados; você deve realizar testes que possam medir as compensações entre o tráfego de rede adicional e a manutenção envolvida versus os custos do aumento do número de núcleos disponíveis e a contenção de disco reduzida em cada nó.

- Use o armazenamento premium para armazenar dados do Elasticsearch. Isso será discutido mais detalhadamente na seção [Opções de armazenamento](#storage-options).

- Use vários discos (do mesmo tamanho) e divida os dados entre esses discos. O SKU das suas VMs ditará o número máximo de discos de dados que você pode anexar. Para saber mais, confira [Requisitos de disco e do sistema de arquivos](guidance-elasticsearch-running-on-azure.md#disk-and-file-system-requirements).

- Use uma SKU de CPU com vários núcleos. Pelo menos dois núcleos, preferencialmente quatro ou mais.

Para um nó cliente:

- Não aloque armazenamento em disco para os dados do Elasticsearch; os clientes dedicados não armazenam dados em disco.

- Certifique-se de que a memória adequada esteja disponível para lidar com cargas de trabalho. As solicitações de inserção em massa são lidas na memória antes do envio dos dados a vários nós de dados, e os resultados de consultas e de agregações são acumulados na memória antes de serem retornados para o aplicativo cliente. Avalie o desempenho de suas próprias cargas de trabalho e monitore o uso de memória usando uma ferramenta como o Marvel ou as [informações da JVM](https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_jvm_section) ao usar a API *node/stats* (`GET _nodes/stats`) para avaliar os requisitos ideais. Mais especificamente, monitore a métrica *heap\_used\_percent* para cada nó e busque manter o tamanho do heap abaixo de 75% do espaço disponível.

- Certifique-se de que haja núcleos de CPU suficientes disponíveis para receber e para processar o volume esperado de solicitações. As solicitações são enfileiradas conforme são recebidas antes do processamento e o volume de itens que podem ser enfileirados é uma função do número de núcleos de CPU em cada nó. Você pode monitorar os comprimentos de fila usando os dados das [informações do Threadpool](https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_threadpool_section) retornadas usando a API node/stats.

    Se a contagem de *rejeitadas* de uma fila indicar que as solicitações estão sendo recusadas, isso indica que o cluster está começando a causar um afunilamento. Isso pode estar acontecendo por causa da largura de banda de CPU, mas pode também ser causado por outros fatores, como falta de memória ou de desempenho lento de E/S e, portanto, use essas informações em conjunto com outras estatísticas para ajudar a determinar a causa raiz.

    Os nós de cliente podem ou não ser necessários, dependendo de suas cargas de trabalho. As cargas de trabalho de ingestão de dados não costumam se beneficiar do uso de clientes dedicados, enquanto algumas pesquisas e agregações podem ser executadas mais rapidamente; esteja preparado para avaliar o desempenho de seus próprios cenários.

    Os nós clientes são úteis principalmente para aplicativos que usam a API Cliente de Transporte para se conectar ao cluster. Você também pode usar a API Cliente de Nó, que cria dinamicamente um cliente dedicado para o aplicativo usando os recursos do ambiente de host do aplicativo. Se seus aplicativos usarem a API Cliente de Nó, não será necessário que o cluster contenha nós clientes dedicados pré-configurados .
    
    No entanto, lembre-se de que um nó criado usando a API Cliente de Nó é um membro de primeira classe do cluster e, como tal, participa das conversas da rede com outros nós; com frequência, iniciar e parar nós clientes com frequência pode criar um ruído desnecessário em todo o cluster.

Para um nó mestre:

- Não aloque armazenamento em disco para os dados do Elasticsearch; os nós mestres dedicados não armazenam dados em disco.

- Os requisitos de CPU devem ser mínimos.

- Os requisitos de memória dependem do tamanho do cluster. As informações sobre o estado do cluster são mantidas na memória. Para os clusters pequenos, a quantidade de memória necessária é mínima, mas para um cluster grande e altamente ativo onde os índices são criados com frequência e os fragmentos estão sempre em movimento, a quantidade de informações de estado pode aumentar significativamente. Monitore o tamanho do heap da JVM para determinar se você precisa adicionar mais memória.

> [AZURE.NOTE]  Para obter confiabilidade de cluster, sempre crie vários nós mestres e configure os nós restantes para evitar que ocorra uma dupla personalidade. Idealmente, deve haver um número ímpar de nós mestres. Este tópico é descrito em mais detalhes no documento [Configurando resiliência e a recuperação do Elasticsearch no Azure][].

### Opções de armazenamento

Existem algumas opções de armazenamento disponíveis nas VMs do Azure com diferentes compensações que afetam o custo, o desempenho, a disponibilidade e a recuperação e que você precisa considerar cuidadosamente.

Observe que você deve armazenar dados do Elasticsearch em discos de dados dedicados. Isso ajudará a reduzir a contenção com o sistema operacional e a garantir que os grandes volumes de E/S do Elasticsearch não concorram com as funções do sistema operacional pelos recursos de E/S.

Os discos do Azure estão sujeitos a restrições de desempenho. Se você descobrir que um cluster está passando por picos de atividade periódicos, então as solicitações de E/S deverão ser limitadas. Para evitar isso, ajuste o design para equilibrar o tamanho do documento no Elasticsearch em relação ao volume de solicitações que provavelmente serão recebidas por cada disco.

Discos baseados em armazenamento padrão dão suporte a uma taxa de solicitação máxima de 500 IOPS; já discos baseados em armazenamento premium podem operar com até 5.000 IOPS. Os discos de armazenamento premium estão disponíveis apenas para a série DS e GS de VMs. As taxas máximas de IOPS de disco para as [VMs do Azure estão documentadas online](virtual-machines-size-specs/).

**Discos de dados persistentes**

Os discos de dados persistentes são VHDs que contam com o Armazenamento do Azure. Se for necessário recriar a VM após uma falha grave, os VHDs existentes poderão ser anexados com facilidade à nova VM. Os VHDs podem ser criados com base em armazenamento padrão (mídia de rotação) ou em armazenamento premium (SSDs). Se você quiser usar os SSDs, deverá criar VMs usando a série DS ou uma melhor. As máquinas DS têm o mesmo preço das VMs da série D, mas haverá uma cobrança extra pelo uso do armazenamento premium.

Em casos em que a taxa de transferência máxima por disco seja insuficiente para dar suporte à carga de trabalho esperada, considere a criação de vários discos de dados e permita que o Elasticsearch [distribua dados entre esses discos](guidance-elasticsearch-running-on-azure.md#disk-and-file-system-requirements) ou implemente a [distribuição RAID 0 usando discos virtuais](virtual-machines-linux-configure-raid/) no nível do sistema.

> [AZURE.NOTE] A experiência da Microsoft mostrou que usar o RAID 0 é particularmente útil para suavizar os efeitos de E/S de cargas de trabalho *com picos de uso* que geram picos de atividade frequentes.

Use armazenamento premium localmente redundante (ou localmente redundante para cargas de trabalho de QA ou de baixo nível) para a conta de armazenamento que contém discos. A replicação em regiões geográficas e em zonas não é necessária para a alta disponibilidade do Elasticsearch HA.

**Discos efêmeros**

O uso de discos persistentes com base em SSDs requer a criação de VMs que oferecem suporte ao armazenamento premium. Isso afeta o preço. O uso do disco efêmero local para armazenar dados do Elasticsearch pode ser uma solução econômica para nós moderadamente dimensionados que exigem até cerca de 800 GB de armazenamento. Na série D padrão de VMs, discos efêmeros são implementados usando SSDs que fornecem desempenho muito superior e latência muito menor que discos comuns

Ao usar o Elasticsearch, o desempenho pode ser equivalente a usar o armazenamento premium sem incorrer no custo. Confira a seção [Abordando problemas de latência de disco](#addressing-disk-latency-issues) para saber mais.

O tamanho da VM limita a quantidade de espaço disponível em armazenamento efêmero, como descrito pelo documento [Expectativas de desempenho da série D](https://azure.microsoft.com/blog/d-series-performance-expectations/).

Por exemplo, uma VM Standard\_D1 fornece 50 GB de armazenamento efêmero, uma VM Standard\_D2 tem 100 GB de armazenamento efêmero e uma VM Standard\_D14 fornece 800 GB de espaço efêmero. Para clusters em que os nós exigem apenas essa quantidade de espaço, o uso de uma VM série D com armazenamento efêmero pode ser econômico.

Você deve equilibrar a maior taxa de transferência disponível com o armazenamento efêmero em relação ao tempo e aos custos envolvidos na recuperação de dados após a reinicialização de um computador. O conteúdo do disco efêmero será perdido se a VM for movida para um servidor de host diferente, se o host for atualizado ou se o host apresentar uma falha de hardware. Se os próprios dados tiverem um tempo de vida limitado, essa perda de dados poderá ser tolerável. Para dados com a vida mais longa, é possível recriar um índice ou recuperar as informações de um backup. É possível minimizar o potencial de perda usando réplicas mantidas em outras VMs.

> [AZURE.NOTE] Não use uma **única** VM para armazenar os dados críticos de produção. Se o nó falhar, todos os dados ficarão indisponíveis. Para obter as informações essenciais, certifique-se de que os dados sejam replicados em pelo menos um nó diferente.

**Arquivos do Azure**

O [Serviço de Arquivos do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx) oferece acesso a arquivos compartilhados usando o Armazenamento do Azure. Você pode criar compartilhamentos de arquivos que poderão então ser montados em VMs do Azure. Várias VMs podem montar o mesmo compartilhamento de arquivos, permitindo que eles acessem os mesmos dados.

Por motivos de desempenho, não se recomenda que você use compartilhamentos de arquivos para armazenar dados do Elasticsearch que não precisem ser compartilhados entre os nós; os discos de dados regulares são mais adequados para essa finalidade. Os compartilhamentos de arquivos podem ser usados para a criação de [índices de réplica de sombra](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-shadow-replicas.html) do Elasticsearch. No entanto, atualmente esse recurso é experimental e não deve ser implementado em um ambiente de produção. Por esse motivo, os índices de sombra não serão mais considerados neste guia.

**Opções de rede**

O Azure implementa um esquema de rede compartilhado. As VMs que utilizam os mesmos racks de hardware concorrem por recursos de rede. Dessa forma, a largura de banda de rede disponível pode variar de acordo com o momento do dia e com o ciclo diário do trabalho em execução em VMs que compartilham a mesma infraestrutura de rede física. Você tem pouco controle sobre esses fatores. É importante entender que o desempenho da rede provavelmente flutuará ao longo do tempo, então defina adequadamente as expectativas dos usuários.

## Escala vertical de nós para dar suporte à ingestão de dados em larga escala

Você pode criar clusters do Elasticsearch usando hardware razoavelmente moderado e, em seguida, escalar verticalmente ou horizontalmente conforme o volume de dados cresce e o número de solicitações aumenta. Com o Azure, você escala verticalmente ao executar em VMs maiores e mais caras, ou pode escalar horizontalmente usando outras VMs menores e mais baratas.

Você também pode executar uma combinação de ambas as estratégias. Não há uma solução única para todos os cenários e, para avaliar a melhor abordagem para qualquer situação, você precisará estar preparado para realizar uma série de testes de desempenho.

Esta seção trata da abordagem que escala verticalmente. O escalonamento horizontal é discutido na seção [Escalonamento horizontal: conclusões](#scaling-out-conclusions). Esta seção descreve os resultados de uma série de parâmetros de comparação executada em um conjunto de clusters do Elasticsearch, que consiste em VMs com tamanhos variados. Os clusters foram designados como pequeno, médio e grande. A tabela a seguir resume os recursos alocados para as VMs em cada cluster.

| HDInsight | SKU da VM | Número de núcleos | Número de discos de dados | RAM |
|---------|-------------|-----------------|----------------------|------|
| Pequena | Standard D2 | 2 | 4 | 7 GB |
| Média | Standard D3 | 4 | 8 | 14 GB |
| Grande | Standard D4 | 8 | 16 | 28 GB |

Cada cluster do Elasticsearch continha três nós de dados. Esses nós de dados lidavam com as solicitações do cliente, bem como com o processamento de dados; os nós clientes separados não eram utilizados porque ofereciam pouco benefício para o cenário de ingestão de dados usado pelos testes. O cluster também continha três nós mestres, um dos quais eleito pelo Elasticsearch para coordenar o cluster.

Os testes foram realizados usando o Elasticsearch 1.7.3. Inicialmente, os testes foram executados em clusters que executavam o Ubuntu Linux 14.0.4 e então foram repetidos usando o Windows Server 2012. Os detalhes da carga de trabalho executada pelos testes são descritos no [Apêndice](#appendix-the-bulk-load-data-ingestion-performance-test).

### Desempenho de ingestão de dados – Ubuntu Linux 14.0.4

A tabela a seguir resume os resultados gerais da execução dos testes por duas horas para cada configuração:

| Configuração | Nº de amostras | Tempo Médio de Resposta (ms) | Taxa de Transferência (Operações/s) |
|---------------|-----------|----------------------------|---------------------------|
| Pequena | 67057 | 636 | 9,3 |
| Média | 123482 | 692 | 17,2 |
| Grande | 197085 | 839 | 27,4 |

A proporção entre a taxa de transferência e o número de amostras processadas para as três configurações é de aproximadamente 1:2:3. No entanto, os recursos disponíveis em termos de memória, de núcleos de CPU e de discos têm a proporção 1:2:4. Foi percebido que vale a pena investigar os detalhes de desempenho de baixo nível dos nós no cluster para avaliar porque esse pode ser o caso. Essas informações podem ajudar a determinar se há limites para a escala vertical e quando será melhor considerar a escala horizontal.

### Determinando os fatores de limitação: utilização de rede

O Elasticsearch depende de ter largura de banda suficiente para dar suporte ao influxo de solicitações do cliente, bem como às informações de sincronização entre os nós no cluster. Como realçado anteriormente, você tem controle limitado sobre a disponibilidade de largura de banda, pois ela depende de muitas variáveis, como o datacenter em uso e a carga de rede atual de outras VMs que compartilham a mesma infraestrutura de rede. No entanto, ainda vale a pena examinar a atividade de rede para cada cluster se você quiser apenas verificar se o volume de tráfego não é excessivo. O gráfico abaixo mostra uma comparação do tráfego de rede recebido pelo nó 2 em cada um dos clusters (os volumes para os outros nós em cada cluster era muito semelhante).

![](media/guidance-elasticsearch/data-ingestion-image1.png)

A média de bytes recebidos por segundo para o nó 2 em cada configuração de cluster durante o período de duas horas foi a seguinte:

| Configuração | Número Médio de Bytes Recebidos/s |
|---------------|--------------------------------------|
| Pequena | 3993640\.3 |
| Média | 7311689\.9 |
| Grande | 11893874,2 |

Os testes foram realizados enquanto o sistema estava em execução em um **estado estável**. Em situações onde o rebalanceamento de índice ou a recuperação de nó estiver ocorrendo, as transmissões de dados entre os nós que armazenam os fragmentos de réplica e principal podem gerar um tráfego de rede significativo. Os efeitos deste processo são descritos em mais detalhes no documento [Configurando a resiliência e a recuperação do Elasticsearch no Azure][].

### Determinando os fatores de limitação: utilização de CPU

A taxa na qual as solicitações são manipuladas é pelo menos parcialmente regida pela capacidade de processamento disponível. O Elasticsearch aceita solicitações de inserção em massa na fila de inserção em massa. Cada nó tem um conjunto de filas de inserção em massa determinado pelo número de processadores disponíveis. Por padrão, há uma fila para cada processador e cada fila pode conter até 50 solicitações pendentes antes que elas comecem a ser rejeitadas.

Os aplicativos devem enviar solicitações de forma que as filas não se excedam. O número de itens em cada fila em qualquer momento dependerá da taxa na qual as solicitações são enviadas por aplicativos clientes e da taxa na qual essas mesmas solicitações são recuperadas e processadas pelo Elasticsearch. Por esse motivo, uma estatística importante capturada está relacionada à taxa de erro resumida na tabela a seguir.

| Configuração | Total de amostras | Nº de erros | Taxa de erros |
|---------------|---------------|-----------|------------|
| Pequena | 67057 | 0 | 0,00% |
| Média | 123483 | 1 | 0,0008% |
| Grande | 200702 | 3617 | 1,8 % |

Todos esses erros foram causados pela seguinte exceção de Java:

```
org.elasticsearch.action.support.replication.TransportShardReplicationOperationAction$PrimaryPhase$1@75a30c1b]; ]
[219]: index [systembase], type [logs], id [AVEAioKb2TRSNcPa_8YG], message [RemoteTransportException[[esdatavm2][inet[/10.0.1.5:9300]][indices:data/write/bulk[s]]]; nested: EsRejectedExecutionException[rejected execution (queue capacity 50)
```

O aumento do número de filas e/ou do comprimento de cada fila pode reduzir o número de erros, mas essa abordagem só pode lidar com picos de curta duração. Fazer isso durante a execução de uma série prolongada de tarefas de ingestão de dados simplesmente atrasará o ponto em que os erros começam a ocorrer. Além disso, essa alteração não aumentará a taxa de transferência e provavelmente prejudicará o tempo de resposta dos aplicativos cliente, já que as solicitações serão enfileiradas por mais tempo antes de serem processadas.

A estrutura de índice padrão de cinco fragmentos com uma réplica (10 fragmentos ao todo) resulta em um pequeno desequilíbrio de carga entre os nós em um cluster; dois nós conterão três fragmentos, enquanto o outro nó conterá quatro. O nó mais ocupado provavelmente será o item que mais restringirá a taxa de transferência e, consequentemente, esse é o motivo da seleção desse nó em cada caso.

O conjunto de gráficos a seguir ilustram a utilização de CPU para o nó mais visitado em cada cluster.

![](media/guidance-elasticsearch/data-ingestion-image2.png)

![](media/guidance-elasticsearch/data-ingestion-image3.png)

![](media/guidance-elasticsearch/data-ingestion-image4.png)

Para os clusters pequeno, médio e grande, a utilização média de CPU desses nós foi de 75,01%, de 64,93% e de 64,64%. A utilização raramente atinge os 100%, e ela cai à medida que o tamanho dos nós e a potência de CPU disponível aumentam. Portanto, é improvável que a potência da CPU seja um fator limitador do desempenho do cluster grande.

### Determinando os fatores de limitação: memória

O uso de memória é outro aspecto importante que pode influenciar o desempenho. Para os testes, o Elasticsearch foi alocado em 50% da memória disponível. Isso está de acordo com as [recomendações documentadas](https://www.elastic.co/guide/en/elasticsearch/guide/current/heap-sizing.html#_give_half_your_memory_to_lucene). Durante a execução dos testes, a atividade de coleta de lixo em excesso foi monitorada na JVM (como uma indicação de falta de memória heap). Em todos os casos, o tamanho do heap ficou estável e a JVM apresentou a atividade de coleta de lixo baixa. A captura de tela abaixo mostra um instantâneo do Marvel, destacando as principais estatísticas da JVM por um curto período enquanto o teste esteve em execução no cluster grande.

![](media/guidance-elasticsearch/data-ingestion-image5.png)

***Atividade de memória e de coleta de lixo JVM no cluster grande.***

### Determinando os fatores de limitação: taxas de E/S de disco

O recurso físico restante no lado servidor que poderá restringir o desempenho é o desempenho do subsistema de E/S de disco. O gráfico abaixo compara a atividade de disco em termos de bytes gravados para os nós mais ocupados em cada cluster.

![](media/guidance-elasticsearch/data-ingestion-image6.png)

A tabela a seguir mostra a média de bytes gravados por segundo para o nó 2 em cada configuração de cluster durante o período de duas horas:

| Configuração | Número Médio de Bytes Gravados/s |
|---------------|-------------------------------------|
| Pequena | 25502361,94 |
| Média | 48856124,5 |
| Grande | 88137675,46 |

O volume de dados gravados aumenta com o número de solicitações processadas por um cluster, mas as taxas de E/S estão dentro dos limites de armazenamento do Azure (os discos criados usando o armazenamento do Azure podem dar suporte a taxas sustentadas de 10s a 100s de MB/s, dependendo do armazenamento usado, o Standard ou o Premium). Examinando a quantidade de tempo gasto aguardando a E/S de disco ajuda a explicar por que a taxa de transferência de disco está abaixo do máximo teórico. Os gráficos e a tabela a seguir mostram essas estatísticas para os mesmos três nós:

> [AZURE.NOTE] O tempo de espera do disco é medido pelo monitoramento da porcentagem de tempo de CPU durante o qual os processadores estão bloqueados porque aguardam a conclusão de operações de E/S.

![](media/guidance-elasticsearch/data-ingestion-image7.png)

![](media/guidance-elasticsearch/data-ingestion-image8.png)

![](media/guidance-elasticsearch/data-ingestion-image9.png)

| Configuração | Tempo Médio de CPU de Espera de Disco (%) |
|---------------|--------------------------------|
| Pequena | 21,04 |
| Média | 14,48 |
| Grande | 15,84 |

Esses dados indicam que uma parte significativa do tempo da CPU (entre aproximadamente 16% e % 21) é gasta aguardando a conclusão da E/S de disco. Isso está restringindo a capacidade do Elasticsearch de processar solicitações e de armazenar dados.

Durante a execução do teste, o cluster grande inseriu **quinhentos milhões de documentos** a mais. Permitir a continuação do teste mostrou que os tempos de espera aumentaram significativamente quando o banco de dados continha mais de seiscentos milhões de documentos. Os motivos para esse comportamento não foram totalmente investigados, mas isso pode ter sido causado pela fragmentação de disco, o que levou ao aumento da latência de disco.

O aumento do tamanho do cluster usando mais nós pode ajudar a minimizar os efeitos desse comportamento. Em casos extremos, pode ser necessário desfragmentar um disco que esteja mostrando tempos de E/S excessivos. No entanto, desfragmentar um disco grande pode levar um tempo considerável (possivelmente mais de 48 horas para uma unidade VHD de 2 TB) e simplesmente reformatar a unidade e permitir que o Elasticsearch recupere os dados ausentes de fragmentos de réplica pode ser uma abordagem mais econômica.

### Tratando problemas de latência de disco

Os testes foram inicialmente executados usando as máquinas virtuais configuradas com discos padrão. Um disco padrão baseia-se em mídia de rotação e consequentemente está sujeito à latência rotacional e a outros afunilamentos que podem restringir as taxas de E/S. O Azure também fornece armazenamento premium em que os discos são criados usando dispositivos SSD. Esses dispositivos não têm nenhuma latência rotacional e, como resultado, devem fornecer maior velocidade de E/S.

A tabela a seguir compara os resultados da substituição de discos padrão por discos premium no cluster grande (as VMs Standard D4 do cluster grande foram substituídas por VMs Standard DS4; o número de núcleos, de memória e de discos é igual em ambos os casos, a única diferença é que as VMs DS4 usavam SSDs).

| Configuração | Nº de amostras | Tempo Médio de Resposta (ms) | Taxa de Transferência (Operações/s) |
|------------------|-----------|----------------------------|---------------------------|
| Grande - Standard | 197085 | 839 | 27,4 |
| Grande - Premium | 255985 | 581 | 35,6 |

Os tempos de resposta foram consideravelmente melhores, resultando em uma taxa de transferência média quase quatro vezes maior do que a do cluster pequeno. Isso está mais alinhado aos recursos disponíveis na VM Standard DS4. A utilização média da CPU no nó mais ocupado do cluster (neste caso, o nó 1) aumentou, pois ela gastou menos tempo aguardando a conclusão de E/S:

![](media/guidance-elasticsearch/data-ingestion-image10.png)

A redução no tempo de espera de disco se torna aparente quando você considera o gráfico a seguir, que mostra que, para o nó mais ocupado, essa estatística caiu para cerca de 1% em média:

![](media/guidance-elasticsearch/data-ingestion-image11.png)

No entanto, há um preço a pagar por essa melhoria. O número de erros de ingestão aumentou em um fator de 10 para 35797 (12,3%). Novamente, a maioria desses erros foi o resultado do estouro da fila de inserção em massa. Considerando que o hardware parece estar executando próximo à capacidade, pode ser necessário adicionar mais nós ou reduzir a taxa de inserções em massa para reduzir o volume de erros. Esses problemas serão discutidos posteriormente neste documento.

### Testando com o armazenamento efêmero

Os mesmos testes foram repetidos em um cluster de VMs D4 com o armazenamento efêmero. Em VMs D4, o armazenamento efêmero é implementado como um único SSD de 400 GB. O número de amostras processadas, o tempo de resposta e a taxa de transferência foram bem parecidos para os números relatados para o cluster baseado em VMs DS14 com armazenamento premium.

| Configuração | Nº de amostras | Tempo Médio de Resposta (ms) | Taxa de Transferência (Operações/s) |
|-----------------------------------|-----------|----------------------------|---------------------------|
| Grande - Premium | 255985 | 581 | 35,6 |
| Grande – Standard (disco Efêmero) | 255626 | 585 | 35,5 |

A taxa de erro também era semelhante (33862 falhas em 289488 solicitações no total – 11,7%).

Os gráficos a seguir mostram a utilização da CPU e as estatísticas de espera do disco para o nó mais ocupado no cluster (nó 2 neste momento):

![](media/guidance-elasticsearch/data-ingestion-image12.png)

![](media/guidance-elasticsearch/data-ingestion-image13.png)

Nesse caso, somente em termos de desempenho, o uso do armazenamento efêmero poderia ser considerado como uma solução mais econômica do que usar o armazenamento premium.

### Desempenho de ingestão de dados – Windows Server 2012

Os mesmos testes foram repetidos usando um conjunto de clusters Elasticsearch com nós executando o Windows Server 2012. A finalidade desses testes era estabelecer os efeitos, se houver, que a escolha do sistema operacional pode causar no desempenho do cluster.

Para ilustrar a escalabilidade do Elasticsearch no Windows, a tabela a seguir mostra os taxa de transferência e os tempos de resposta obtidos para as configurações dos clusters pequeno, médio e grande. Observe que todos esses testes foram executados com o Elasticsearch configurado para usar o armazenamento efêmero SSD, já que os testes com o Ubuntu mostraram que a latência de disco foi provavelmente um fator crítico para alcançar o desempenho máximo:

| Configuração | Nº de amostras | Tempo Médio de Resposta (ms) | Taxa de Transferência (Operações/s) |
|---------------|-----------|----------------------------|---------------------------|
| Pequena | 90295 | 476 | 12,5 |
| Média | 169243 | 508 | 23,5 |
| Grande | 257115 | 613 | 35,6 |

Esses resultados indicam como o Elasticsearch dimensiona o tamanho da VM e dos recursos disponíveis no Windows.

As tabelas a seguir comparam os resultados para o cluster grande no Ubuntu e no Windows:

| Sistema operacional | Nº de amostras | Tempo Médio de Resposta (ms) | Taxa de Transferência (Operações/s) | Taxa de Erros (%) |
|------------------|-----------|----------------------------|---------------------------|----------------|
| Ubuntu | 255626 | 585 | 35,5 | 11,7 |
| Windows | 257115 | 613 | 35,6 | 7,2 |

A taxa de transferência estava consistente com a dos clusters grandes do Ubuntu, embora o tempo de resposta tenha sido ligeiramente superior, isso pode ser creditado pela taxa de erros inferior (os erros são relatados com mais rapidez do que as operações bem-sucedidas e, portanto, têm um tempo de resposta menor).

A utilização de CPU relatada pelas ferramentas de monitoramento do Windows foi um pouco maior que a do Ubuntu. No entanto, você deve tratar essas comparações diretas de medidas entre sistemas operacionais com muito cuidado devido à maneira como os diferentes sistemas operacionais relatam essas estatísticas. Além disso, as informações sobre a latência de disco em termos S não estão disponíveis da mesma maneira como no Ubuntu. O ponto importante é que a utilização da CPU foi alta, indicando que tempo gasto aguardando E/S era baixa:

![](media/guidance-elasticsearch/data-ingestion-image14.png)

### Escalando verticalmente: conclusões

O desempenho do Elasticsearch para um cluster bem ajustado provavelmente será equivalente no Windows e no Ubuntu, e ele é escalado verticalmente em um padrão semelhante em ambos os sistemas operacionais. Para obter o melhor desempenho, **use armazenamento premium para armazenar dados do Elasticsearch**.

## Escala horizontal de nós para dar suporte à ingestão de dados em larga escala

A escala horizontal é a abordagem complementar à escala vertical investigada na seção anterior. Um recurso importante do Elasticsearch é a escalabilidade horizontal inerente incorporada ao software. Aumentar o tamanho de um cluster é simplesmente uma questão de adicionar mais nós. Você não precisa realizar nenhuma operação manual para redistribuir os índices ou os fragmentos, já que essas tarefas são tratadas automaticamente, embora existam inúmeras opções de configuração disponíveis que podem ser usadas para influenciar esse processo.

Adicionar mais nós ajuda a melhorar o desempenho, distribuindo a carga por mais máquinas. Conforme você adiciona mais nós, também será necessário considerar a reindexação de dados para aumentar o número de fragmentos disponíveis. Você pode antecipar esse processo até certo ponto com a criação de índices com mais fragmentos do que os nós disponíveis inicialmente. Quando mais nós forem adicionados, os fragmentos poderão ser distribuídos.

Além de aproveitar a escalabilidade horizontal do Elasticsearch, há outras razões para implementar índices com mais fragmentos do que nós. Cada fragmento é implementado como uma estrutura de dados separada (um índice [Lucene](https://lucene.apache.org/)) e tem seus próprios mecanismos internos para manter a consistência e tratar a simultaneidade. A criação de vários fragmentos ajuda a aumentar o paralelismo em um nó e pode melhorar o desempenho.

No entanto, manter o desempenho durante a expansão é um ato de equilíbrio. Quanto mais nós e fragmentos um cluster contiver, mais esforço será necessário para sincronizar o trabalho realizado pelo cluster, o que pode reduzir a taxa de transferência. Para qualquer carga de trabalho determinada, haverá um ponto ideal que maximizará o desempenho de ingestão, minimizando a sobrecarga de manutenção. Esse ponto ideal dependerá muito da natureza da carga de trabalho e do cluster; especificamente, o volume, o tamanho e o conteúdo dos documentos, a taxa de ingestão e o hardware no qual o sistema é executado.

Esta seção resume os resultados das investigações de dimensionamento de clusters destinados a dar suporte à carga de trabalho usada por testes de desempenho descritos anteriormente. O mesmo teste foi executado em clusters com VMs baseadas no tamanho de VM grande (Standard D4 com oito núcleos de CPU, 16 discos de dados e 28 GB de RAM) executando o Ubuntu Linux 14.0.4, mas configuradas com diferentes números de nós e de fragmentos. Os resultados não pretendem ser definitivos, pois se aplicam somente a um cenário específico, mas podem atuar como um bom ponto de partida para ajudar você a analisar a escalabilidade horizontal dos clusters e a gerar números para que você obtenha a proporção ideal de fragmentos para nós que atenda às suas próprias necessidades.

### Resultados de linha de base – três nós

Para obter um número de linha de base, o teste de desempenho de ingestão de dados foi executado em um cluster de três nós com cinco fragmentos e uma réplica. Essa é a configuração padrão para um índice do Elasticsearch. Nessa configuração, o Elasticsearch distribui dois fragmentos principais por dois dos nós e o fragmento principal restante é armazenado no terceiro nó. A tabela a seguir resume a taxa de transferência em termos de operações de ingestão em massa por segundo e do número de documentos que foram armazenados com êxito pelo teste.

> [AZURE.NOTE] Nas tabelas a seguir nesta seção, a distribuição dos fragmentos principais é apresentada como um número para cada nó separado por traços. Por exemplo, o layout de cinco fragmentos e de três nós é descrito como 2-2-1. O layout dos fragmentos de réplica não está incluído; eles seguirão um esquema semelhante ao dos fragmentos principais.

| Configuração | Nº de documentos | Taxa de transferência (operações/s) | Layout do fragmento |
|---------------|--------------|-----------------------------|--------------|
| Cinco fragmentos | 200560412 | 27,86 | 2-2-1 |

### Resultados de seis nós

O teste foi repetido em um cluster de seis nós. A finalidade desses testes era tentar determinar com mais precisão os efeitos do armazenamento de mais de um fragmento em um nó.

| Configuração | Nº de documentos | Taxa de transferência (operações/s) | Layout do fragmento |
|---------------|--------------|-----------------------------|--------------|
| Quatro fragmentos | 227360412 | 31,58 | 1-1-0-1-1-0 |
| Sete fragmentos | 268013252 | 37,22 | 2-1-1-1-1-1 |
| 10 fragmentos | 258065854 | 35,84 | 1-2-2-2-1-2 |
| 11 fragmentos | 279788157 | 38,86 | 2-2-2-1-2-2 |
| 12 fragmentos | 257628504 | 35,78 | 2-2-2-2-2-2 |
| 13 fragmentos | 300126822 | 41,68 | 2-2-2-2-2-3 |

Esses resultados são exibidos para indicar as seguintes tendências:

* Mais fragmentos por nó melhora a taxa de transferência. Com o pequeno número de fragmentos por nó criado para esses testes, esse fenômeno era esperado, pelos motivos descritos anteriormente.

* Um número ímpar de fragmentos oferece um desempenho melhor do que um número par. Os motivos para isso não são tão claros, mas *pode* ser que o algoritmo de roteamento usado pelo Elasticsearch seja mais capaz de distribuir os dados entre fragmentos nesse caso, levando a uma carga mais uniforme por nó.

Para testar essas hipóteses, vários testes adicionais foram executados com um número maior de fragmentos. No aviso verbal do Elasticsearch, foi decidido que seria usado um número primo de fragmentos para cada teste, já que eles oferecem uma distribuição sensata de números ímpares para o intervalo em questão.

| Configuração | Nº de documentos | Taxa de transferência (operações/s) | Layout do fragmento |
|---------------|--------------|-----------------------------|-------------------|
| 23 fragmentos | 312844185 | 43,45 | 4-4-4-3-4-4 |
| 31 fragmentos | 309930777 | 43,05 | 5-5-5-5-6-5 |
| 43 fragmentos | 316357076 | 43,94 | 8-7-7-7-7-7 |
| 61 fragmentos | 305072556 | 42,37 | 10-11-10-10-10-10 |
| 91 fragmentos | 291073519 | 40,43 | 15-15-16-15-15-15 |
| 119 fragmentos | 273596325 | 38,00 | 20-20-20-20-20-19 |

Esses resultados sugeriram que um ponto de desequilíbrio foi atingido em aproximadamente 23 fragmentos. Depois disso, o aumento do número de fragmentos causou uma pequena queda no desempenho (a taxa de transferência para 43 fragmentos possivelmente é uma anomalia).

### Resultados de nove nós

Os testes foram repetidos usando um cluster de nove nós, novamente usando um número primo de fragmentos.

| Configuração | Nº de documentos | Taxa de transferência (operações/s) | Layout do fragmento |
|---------------|--------------|-----------------------------|----------------------------|
| 17 fragmentos | 325165364 | 45,16 | 2-2-2-2-2-2-2-2-1 |
| 19 fragmentos | 331272619 | 46,01 | 2-2-2-2-2-2-2-2-3 |
| 29 fragmentos | 349682551 | 48,57 | 3-3-3-4-3-3-3-4-3 |
| 37 fragmentos | 352764546 | 49,00 | 4-4-4-4-4-4-4-4-5 |
| 47 fragmentos | 343684074 | 47,73 | 5-5-5-6-5-5-5-6-5 |
| 89 fragmentos | 336248667 | 46,70 | 10-10-10-10-10-10-10-10-9 |
| 181 fragmentos | 297919131 | 41,38 | 20-20-20-20-20-20-20-20-21 |

Esses resultados mostraram um padrão semelhante, com um ponto de desequilíbrio em aproximadamente 37 fragmentos.

### Escalando horizontalmente: conclusões

Usando uma extrapolação grosseira, os resultados dos testes de seis nós e de nove nós indicam que, para esse cenário específico, o número ideal de fragmentos para maximizar o desempenho foi de 4n+/-1, onde n é o número de nós. Essa *pode* ser uma função do número de threads de inserção em massa disponíveis que, por sua vez, depende do número de número de núcleos de CPU; o raciocínio é o seguinte (veja [Padrões de vários documentos](https://www.elastic.co/guide/en/elasticsearch/guide/current/distrib-multi-doc.html#distrib-multi-doc) para obter os detalhes):

- Cada solicitação de inserção em massa enviada pelo aplicativo cliente é recebida por um nó de dados único.

- O nó de dados cria uma nova solicitação de inserção em massa para cada fragmento principal afetado pela solicitação original e a encaminha para os outros nós, em paralelo.

- À medida que cada fragmento principal é gravado, outra solicitação é enviada para cada réplica desse fragmento. O fragmento principal aguarda a conclusão da solicitação enviada para a réplica antes de concluir.

Por padrão, o Elasticsearch cria um thread de inserção em massa para cada núcleo de CPU disponível em uma VM. No caso das VMs D4 usadas neste teste, cada CPU continha oito núcleos e, portanto, foram criados oito threads de inserção em massa. O índice usado estendeu quatro (em um caso, cinco) fragmentos principais em cada nó, mas também havia quatro (cinco) réplicas em cada nó. A inserção de dados nesses fragmentos e réplicas poderia consumir até oito threads em cada nó por solicitação, correspondendo ao número disponível. O aumento ou a redução do número de fragmentos pode causar ineficiências nos threads, já que os threads possivelmente ficam ociosos ou as solicitações são enfileiradas. No entanto, sem experimentos adicionais, essa é apenas uma teoria e não pode ser definitiva.

Os testes também ilustraram outro ponto importante. Neste cenário, o aumento do número de nós pode melhorar a taxa de transferência da ingestão de dados, mas os resultados não são necessariamente dimensionados de forma linear. A realização de testes adicionais com clusters de 12 e 15 nós pode mostrar o ponto em que a expansão oferece pouco benefício adicional. Se esse número de nós fornecer espaço insuficiente, pode ser necessário voltar para a estratégia de escala vertical e começar a usar discos maiores ou mais discos baseados no armazenamento premium.

> [AZURE.IMPORTANT] Não considere a proporção 4n+/-1 como uma fórmula mágica que sempre funcionará para todos os clusters. Se você tiver menos ou mais núcleos de CPU disponíveis, a configuração de fragmentos ideal poderá ser diferente. Os resultados se basearam em uma carga de trabalho específica que só realizou a ingestão de dados. Para cargas de trabalho que também incluam uma mistura de consultas e de agregações, os resultados podem ser muito diferentes.

> Além disso, a carga de trabalho de ingestão de dados utilizou um único índice. Em muitas situações, os dados provavelmente serão distribuídos por vários índices, levando a padrões ou usos de recursos diferentes.

> O ponto importante deste exercício é entender o método usado em vez dos resultados obtidos. Você deve estar preparado para realizar sua própria avaliação de escalabilidade com base em suas próprias cargas de trabalho para obter informações mais aplicáveis ao seu cenário.

## Ajuste da ingestão de dados em grande escala

O Elasticsearch é altamente configurável, com muitas opções e configurações que você pode usar para otimizar o desempenho para cenários e casos de uso específicos. Esta seção descreve alguns exemplos comuns. Lembre-se de que a flexibilidade que o Elasticsearch fornece nesse sentido vem com um aviso; é muito fácil desajustar o Elasticsearch e piorar o desempenho. Ao ajustar, faça somente uma alteração por vez e sempre meça os efeitos de todas as alterações para garantir que elas não sejam prejudiciais ao sistema.

### Otimizando recursos para operações de indexação

A lista a seguir descreve alguns pontos que você deve considerar ao ajustar um cluster Elasticsearch para dar suporte à ingestão de dados em larga escala. Os dois primeiros itens têm mais probabilidade de ter um efeito imediatamente perceptível no desempenho, enquanto os outros são mais marginais, dependendo da carga de trabalho:

*  Os novos documentos adicionados a um índice só se tornarão visíveis para pesquisas quando o índice for atualizado. A atualização de um índice é uma operação cara e, portanto, só é realizada periodicamente em vez de a cada documento criado. O intervalo de atualização padrão é de um segundo. Se você está executando operações em massa, considere desabilitar temporariamente as atualizações de índice; defina o *refresh\_interval* do índice como -1.

	```http
	PUT /my_busy_index
	{
		"settings" : {
			"refresh_interval": -1
		}
	}
	```

	Dispare uma atualização manualmente usando a API [*\_refresh*](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-refresh.html) no final da operação para tornar os dados visíveis. Veja [Uso da indexação em massa](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-update-settings.html#bulk) para saber mais. Mais detalhes sobre o [impacto da alteração do intervalo de atualização na inclusão de dados](#the-impact-of-changing-the-index-refresh-interval-on-data-ingestion-performance) serão descritos posteriormente.

* Se um índice for replicado, cada operação de indexação (criar, atualizar ou excluir um documento) será repetida nos fragmentos de réplica à medida que forem ocorrendo no fragmento principal. Considere a desabilitação da replicação durante as operações de importação em massa e a reabilite quando a importação for concluída:

    ```http
	PUT /my_busy_index
	{
		"settings" : {
			"number_of_replicas": 0
		}
	}
	```

	Quando você reabilita a replicação, o Elasticsearch executa uma transferência de dados pela rede byte por byte do índice para cada réplica. Isso é mais eficiente do que repetir o processo de indexação documento por documentos em cada nó. O risco é que os dados podem ser perdidos se o nó principal falhar ao executar a importação em massa, mas a recuperação pode ser simplesmente uma questão de iniciar a importação novamente. O [impacto da replicação no desempenho de ingestão de dados](#the-impact-of-replicas-on-data-ingestion-performance) será descrito em mais detalhes posteriormente.

* O Elasticsearch tenta equilibrar os recursos disponíveis entre aqueles necessários para a consulta e aqueles necessários para a ingestão de dados. Como resultado, ele pode limitar o desempenho da ingestão de dados (os eventos de limitação são registros no log do Elasticsearch). Essa restrição se destina a impedir que muitos segmentos de índice que exijam mesclagem e salvamento em disco sejam criados ao mesmo tempo; um processo que pode monopolizar os recursos. Se o sistema não estiver executando consultas, você poderá desabilitar a limitação de ingestão de dados. Isso deve permitir que a indexação maximize o desempenho. Você pode desabilitar a limitação de um cluster inteiro da seguinte maneira:

	```http
	PUT /_cluster/settings
	{
		"transient" : {
			"indices.store.throttle.type": "none"
		}
	}
	```

    Defina o tipo de limitação do cluster novamente como *"mesclar"* quando a ingestão for concluída. Observe também que desabilitar a limitação pode levar à instabilidade no cluster, portanto garanta que você tenha procedimentos que possam recuperar o cluster, se necessário.

* O Elasticsearch reserva uma proporção da memória heap para as operações de indexação; o restante é usado principalmente por consultas e pesquisas. A finalidade desses buffers é reduzir o número de operações de E/S de disco, com o objetivo de executar menos gravações maiores em vez de mais gravações menores. A proporção padrão de memória heap alocada é de 10%. Se você estiver indexando um grande volume de dados, esse valor poderá ser insuficiente. Para os sistemas que dão suporte à ingestão de grandes volumes de dados, você deve permitir até 512 MB de memória para cada fragmento ativo no nó. Por exemplo, se você estiver executando o Elasticsearch nas VMs D4 (28 GB de RAM) e se tiver alocado 50% da memória disponível para a JVM (14 GB), 1,4 GB estará disponível para uso por operações de indexação. Se um nó contiver três fragmentos ativos, provavelmente essa configuração será suficiente. No entanto, se um nó contiver mais fragmentos do que isso, considere aumentar o valor do parâmetro *indices.memory.index\_buffer\_size* no arquivo de configuração elasticsearch.yml. Para saber mais, confira [Considerações sobre desempenho para a indexação do Elasticsearch](https://www.elastic.co/blog/performance-considerations-elasticsearch-indexing).

    A alocação de mais de 512 MB por fragmento ativo provavelmente não melhorará o desempenho da indexação; na verdade, poderá ser prejudicial, pois haverá menos memória disponível para executar outras tarefas. Esteja ciente também que alocar mais espaço de heap para buffers de índice removerá a memória de outras operações, como pesquisar e agregar dados, e poderá reduzir o desempenho de operações de consulta.

* O Elasticsearch restringe o número de threads (o valor padrão é oito) que podem executar simultaneamente as operações de indexação em um fragmento. Se um nó contém apenas um pequeno número de fragmentos, considere aumentar o *index\_concurrency* configuração para um índice que está sujeito a um grande volume de operações de indexação ou é o destino de uma inserção em massa, da seguinte maneira:

	```http
	PUT /my_busy_index
	{
		"settings" : {
			"index_concurrency": 20
		}
	}
	```

* Se você estiver executando um grande número de operações em massa e de indexação por um curto período, poderá aumentar o número de threads de *índice* e em *massa* disponíveis no pool de threads e estender o tamanho da fila de *inserção em massa* para cada nó de dados. Isso permitirá que mais solicitações sejam enfileiradas em vez de descartadas. Para saber mais, confira [Pool de threads](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-threadpool.html). Se você estiver executando altos níveis sustentados na ingestão de dados, o aumento do número de threads em massa não será recomendável; em vez disso, crie nós adicionais e use a fragmentação para distribuir a carga da indexação por esses nós. Como alternativa, considere o envio em série de lotes de inserção em massa em vez de em paralelo, já que isso agirá como um mecanismo natural de limitação que poderá reduzir as chances de erros devido a um estouro da fila de inserção em massa.

### O impacto da alteração do intervalo de atualização do índice no desempenho da ingestão de dados

O intervalo de atualização controla a taxa na qual os dados ingeridos se tornam visíveis para consultas e agregações, mas as atualizações frequentes podem afetar o desempenho das operações de ingestão de dados. O intervalo de atualização padrão é de um segundo. Você pode desabilitar completamente a atualização, mas isso pode não ser apropriado para sua carga de trabalho. Você pode experimentar diferentes intervalos e estabelecer o ponto ideal que equilibra o desempenho da ingestão com relação à necessidade de apresentar informações atualizadas.

Como um exemplo do impacto, o teste de desempenho de ingestão de dados foi repetido em um cluster Elasticsearch com sete fragmentos distribuídos por três nós de dados. O índice tinha uma única réplica. Cada nó de dados baseou-se em uma VM D4 (28 GB de RAM, oito núcleos de processador) usando armazenamento efêmero com suporte de SSD para manter os dados. Cada teste foi executado por uma hora.

Neste teste, a taxa de atualização foi definida com o valor padrão de um segundo. A tabela a seguir mostra os taxa de transferência e os tempos de resposta para este teste em comparação a uma execução separada em que a taxa de atualização foi reduzida para uma vez a cada 30 segundos.

| Taxa de atualização | Nº de amostras | Tempo médio de resposta – operações bem-sucedidas (ms) | Taxa de transferência – operações bem-sucedidas (operações/s) |
|--------------|------------|----------------------------------------------------|---------------------------------------------------|
| 1 segundo | 93755 | 460 | 26,0 |
| 30 segundos | 117758 | 365 | 32,7 |

Neste teste, a redução da taxa de atualização resultou em uma melhora de 18% na taxa de transferência e na redução do tempo de resposta médio em 21%. Os gráficos a seguir gerados pelo Marvel ilustram o principal motivo para essa diferença. Os valores abaixo mostram a atividade de mesclagem de índice que ocorreu com o intervalo de atualização definido como 1 segundo e 30 segundos.

As mesclagens de índice são realizadas para evitar que o número de segmentos de índice na memória se tornem muito numerosos. Um intervalo de atualização de um segundo gera um grande número de segmentos pequenos que precisam ser mesclados com frequência, enquanto que o intervalo de atualização de 30 segundos gera menos segmentos grandes que podem ser mesclados de forma mais ideal.

![](media/guidance-elasticsearch/data-ingestion-image15.png)

***Atividade de mesclagem de índice para uma taxa de atualização de índice de um segundo***

![](media/guidance-elasticsearch/data-ingestion-image16.png)

***Atividade de mesclagem de índice para uma taxa de atualização de índice de 30 segundos***

### O impacto das réplicas no desempenho da ingestão de dados

As réplicas são um recurso essencial de qualquer cluster resiliente e, se não forem usadas, existe o risco de perda de informações caso um nó falhe. No entanto, as réplicas aumentam a quantidade de execuções de E/S de disco e de rede e podem ser prejudiciais à taxa na qual os dados são ingeridos. Pelos motivos descritos anteriormente, pode ser benéfico desabilitar temporariamente as réplicas durante as operações de carregamento de dados em larga escala.

Os testes de desempenho da ingestão de dados foram repetidos usando três configurações:

* Usando um cluster sem réplicas,

* Usando um cluster com uma réplica

* Usando um cluster com duas réplicas.

Em todos os casos, o cluster composto por sete fragmentos espalhados pelos três nós e executados em VMs configuradas como descrito nos conjuntos de testes anteriores. O índice de teste usou um intervalo de atualização de 30 segundos.

A tabela a seguir resume os tempos de resposta e a taxa de transferência de cada teste para fins de comparação:

| Configuração | Nº de amostras | Tempo médio de resposta – operações bem-sucedidas (ms) | Taxa de transferência – operações bem-sucedidas (operações/s) | Nº de erros de ingestão de dados |
|---------------|------------|----------------------------------------------------|---------------------------------------------------|--------------------------|
| Nenhuma réplica | 215451 | 200 | 59,8 | 0 |
| 1 réplica | 117758 | 365 | 32,7 | 0 |
| 2 réplicas | 94218 | 453 | 26,1 | 194262 |


A queda no desempenho à medida que o número de réplicas diminui é clara, mas você também deve observar o grande volume de erros de ingestão de dados no terceiro teste. As mensagens geradas por esses erros indicaram que eles se deviam ao estouro de fila de inserção em massa fazendo com que solicitações fossem rejeitadas. Essas rejeições ocorreram muito rapidamente, por isso o número é grande.

> [AZURE.NOTE] Os resultados do terceiro teste destacam a importância do uso de uma estratégia de repetição inteligente quando ocorrem erros transitórios, como esses — recue por um curto período para permitir que a fila de inserção em massa seja reduzida antes de tentar repetir a operação de inserção em massa.

Os próximos conjuntos de gráficos comparam os tempos de resposta durante os testes. Em cada caso, o primeiro gráfico mostra os tempos de resposta gerais, enquanto o segundo gráfico detalha os tempos de resposta para as operações mais rápidas (observe que a escala do primeiro gráfico é dez vezes a do segundo). Você pode ver como o perfil dos tempos de resposta varia nos três testes.

Sem réplicas, a maioria das operações levou entre 75ms e 750ms, com os tempos de resposta mais rápidos em torno de 25 ms:

![](media/guidance-elasticsearch/data-ingestion-image17.png)

Com uma réplica, o tempo de resposta operacional mais encontrado ficou no intervalo entre 125 ms e 1250 ms. As respostas mais rápidas levaram aproximadamente 75 ms, embora houvesse menos respostas rápidas do que no caso sem réplicas. Houve também muito mais respostas significativamente mais demoradas do que na maioria dos casos mais comuns, além dos 1250 ms:

![](media/guidance-elasticsearch/data-ingestion-image18.png)

Com duas réplicas, o intervalo de tempo de resposta mais encontrado foi de 200 ms a 1500 ms, mas houve muito menos resultados abaixo do intervalo mínimo do que no teste de uma réplica. No entanto, o padrão de resultados acima do limite superior foi muito semelhante ao do teste de uma réplica. Isso provavelmente se deve aos efeitos do estouro da fila de inserção em massa (excedendo um comprimento de fila de 50 solicitações). O trabalho adicional necessário para manter duas réplicas faz com que a fila estoure com mais frequência, impedindo que as operações de ingestão tenham tempos de resposta excessivos; as operações são rejeitadas de forma rápida em vez de demorarem muito, possivelmente causando exceções de tempo limite ou afetando a capacidade de resposta dos aplicativos cliente (essa é a finalidade do mecanismo de fila de inserção em massa):

![](media/guidance-elasticsearch/data-ingestion-image19.png)

<span id="_The_Impact_of_1" class="anchor"><span id="_Impact_of_Increasing" class="anchor"></span></span>Com o Marvel, você pode ver o efeito do número de réplicas na fila de índice em massa. A Figura abaixo mostra os dados do Marvel que demonstram como a fila de inserção em masa foi preenchida durante o teste. O comprimento médio da fila ficou em torno de 40 solicitações, mas picos periódicos causaram o estouro e, como resultado, solicitações foram rejeitadas:

![](media/guidance-elasticsearch/data-ingestion-image20.png)

***O tamanho da fila de índice em massa e o número de solicitações rejeitadas com duas réplicas.***

Você deve comparar isso à figura abaixo, que mostra os resultados de uma única réplica. O mecanismo do Elasticsearch foi capaz de processar solicitações rápido o suficiente para manter o comprimento médio da fila em cerca de 25 e em nenhum momento o comprimento da fila excedeu 50 solicitações; portanto, nenhum trabalho foi rejeitado.

![](media/guidance-elasticsearch/data-ingestion-image21.png)

***O tamanho da fila de índice em massa e o número de solicitações rejeitadas com uma réplica.***

## Práticas recomendadas para os clientes que enviam dados para o Elasticsearch

Muitos aspectos de desempenho estão relacionados não apenas à parte interna do sistema, mas também com a forma como o sistema é usado pelos aplicativos cliente. O Elasticsearch oferece muitos recursos que podem ser utilizados pelo processo de ingestão de dados; a geração de identificadores exclusivos para documentos, a realização de análises de documentos e até mesmo o uso de scripts para transformar os dados conforme eles são armazenados são alguns exemplos. No entanto, todas essas funções são somadas à carga no mecanismo de Elasticsearch e, em muitos casos, podem ser executadas com mais eficiência por aplicativos cliente antes da transmissão.

> [AZURE.NOTE] Esta lista de práticas recomendadas está preocupada principalmente com a ingestão de novos dados, e não com a modificação de dados existentes já armazenados em um índice. As cargas de trabalho de ingestão são executadas como operações de acréscimo pelo Elasticsearch, enquanto que as modificações de dados são executadas como operações de exclusão/acréscimo. Isso ocorre porque os documentos em um índice são imutáveis e, portanto, modificar um documento envolve a substituição do documento inteiro por uma nova versão. Você pode executar uma solicitação HTTP PUT para substituir um documento existente ou pode usar a API de *atualização* do Elasticsearch, que abstrai uma consulta para buscar um documento existente, mescla as alterações e executa um PUT para armazenar o novo documento.

Além disso, considere implementar as seguintes práticas recomendadas onde for apropriado:

* Desabilite a análise de texto para os campos de índice que não precisem ser analisados. A análise envolve a tokenização do texto para permitir consultas que possam pesquisar termos específicos. No entanto, pode ser uma tarefa de uso intensivo de CPU, portanto seja seletivo. Se você estiver usando o Elasticsearch para armazenar dados de log, pode ser útil indexar as mensagens de log detalhadas para permitir pesquisas complexas. Outros campos, como os que contêm códigos de erro ou identificadores, provavelmente não devem ser indexados (com que frequência é provável que você solicite os detalhes de todas as mensagens cujo código de erro contenha um "3", por exemplo?) O código a seguir desabilita a análise dos campos *name* e *hostip* no tipo *logs* tipo do índice *systembase*:

	```http
	PUT /systembase
	{
		"settings" : {
			...
		},
		"logs" : {
			...
			"name": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"hostip": {
				"type": "string",
				"index" : "not_analyzed"
			},
			...
		}
	}
	```

* Desabilite o campo *\_all* de um índice se ele não for necessário. O campo *\_all* concatena os valores dos outros campos no documento para análise e indexação. Ele é útil para a execução de consultas que possam fazer a correspondência de qualquer campo em um documento. Se você espera que os clientes façam a correspondência em campos nomeados, a habilitação de *\_all* simplesmente significa uma sobrecarga de CPU e de armazenamento. O exemplo a seguir mostra como desabilitar o campo *\_all* para o tipo *logs* no índice *systembase*.

	```http
	PUT /systembase
	{
		"settings" : {
			...
		},
		"logs" : {
			"_all": {
				"enabled" : false
			},
			...,
		...
		}
	}
	```

    Observe que você pode criar uma versão seletiva de *\_all* que contenha somente as informações de campos específicos. Para saber mais, veja [Desabilitando o campo \_all](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-all-field.html#disabling-all-field).

* Evite mapeamentos dinâmicos em índices. O mapeamento dinâmico é um recurso poderoso, mas a adição de novos campos a um índice existente exige a coordenação de alterações na estrutura do índice em nós, o que pode fazer com que o índice fique temporariamente bloqueado. O mapeamento dinâmico também pode levar a uma explosão do número de campos e do volume consequente de metadados de índice, caso não seja usado com cuidado. Por sua vez, isso resulta no aumento de requisitos de armazenamento e de E/S, para ingestão de dados e na execução de consultas. Esses dois problemas afetarão o desempenho. Considere desabilitar o mapeamento dinâmico e definir suas estruturas de índice explicitamente. Para saber mais, confira [Mapeamento dinâmico de campos](https://www.elastic.co/guide/en/elasticsearch/reference/current/dynamic-field-mapping.html#dynamic-field-mapping).

* Entenda como balancear a carga de trabalho para atender a requisitos conflitantes. Você sempre deve considerar que a ingestão de dados pode afetar significativamente o desempenho de outras operações simultâneas, como usuários executando consultas. A ingestão de dados pode estar sujeita a picos repentinos e se o sistema tentar consumir todos os dados que chegam, imediatamente o influxo poderá causar uma lentidão das taxas de consulta. O Elasticsearch tenta evitar essa situação ao regulamentar a taxa na qual ele processará solicitações de ingestão na fila de inserção em massa (veja a seção [Determinando os fatores de limitação – utilização de CPU](#determining-limiting-factors-cpu-utilization) para saber mais), mas esse mecanismo será tratado como um último recurso; se o código do aplicativo não estiver preparado para lidar com as solicitações rejeitadas, há o risco de perda de dados. Em vez disso, considere o uso de um padrão como [Nivelando a carga baseada em fila](https://msdn.microsoft.com/library/dn589783.aspx) para controlar a taxa na qual os dados são passados para o Elasticsearch.

* Verifique se o cluster tem recursos suficientes para lidar com a carga de trabalho, especialmente se os índices estiverem configurados com várias réplicas.

* Use a API de inserção em massa para carregar grandes lotes de documentos. Dimensione as solicitações em massa adequadamente. Às vezes, os lotes maiores não são melhores para o desempenho e podem causar uma sobrecarga nos threads e em outros recursos do Elasticsearch, atrasando outras operações simultâneas. Os documentos em um lote de inserção em massa são mantidos na memória no nó coordenador enquanto a operação é executada; o tamanho físico de cada lote é mais importante do que a contagem de documentos. Não há nenhuma regra definitiva e cabal sobre o que constitui o tamanho de lote ideal, embora a documentação do Elasticsearch recomende o uso entre 5 MB e 15 MB como um ponto de partida para suas próprias investigações. Realize testes de desempenho para estabelecer o tamanho de lote ideal para seus próprios cenários e sua combinação de cargas de trabalho.

* Verifique se as solicitações de inserção em massa foram distribuídas entre os nós em vez de direcionadas para um único nó. O direcionamento de todas as solicitações para um único nó pode causar esgotamento de memória, já que cada solicitação de inserção em massa processada é armazenada na memória no nó. Ele também pode aumentar a latência de rede à medida que as solicitações são redirecionadas para outros nós.

* O Elasticsearch usa um quórum com uma maioria de nós principais e de réplica ao gravar dados. Uma operação de gravação não será concluída até que o quórum relata o êxito. Essa abordagem ajuda a garantir que os dados não sejam gravados se a maioria de nós não estiver disponível devido a um evento de partição de rede (falha). O uso de um quórum pode reduzir o desempenho de operações de gravação. Você pode desabilitar a gravação baseada em quórum ao definir o parâmetro *consistency* como *um* ao gravar dados. O exemplo a seguir adiciona um novo documento, mas é concluído assim que a gravação no fragmento principal é concluída.

	```http
	PUT /my_index/my_data/104?consistency=one
	{
		"name": "Bert",
		"age": 23
	}
	```

	Observe que, assim como acontece com a replicação assíncrona, a desabilitação da gravação baseada em quórum pode levar a inconsistências entre o fragmento principal e cada uma das réplicas.

* Ao usar os quóruns, o Elasticsearch aguardará até que nós suficientes estejam disponíveis antes de determinar que uma operação de gravação deverá ser anulada porque um quórum não foi alcançado. Esse período de espera é determinado pelo parâmetro de consulta de tempo limite (o padrão é um minuto). Você pode modificar essa configuração usando o parâmetro de consulta de tempo limite. O exemplo a seguir cria um novo documento e aguarda até cinco segundos pela resposta do quórum antes de anular:

	```http
	PUT /my_index/my_data/104?timeout=5s
	{
		"name": "Sid",
		"age": 27
	}
	```

	O Elasticsearch também permite que você use seus próprios números de versão [gerados externamente](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html#_version_types).

* Considere desabilitar o campo *\_source* de um índice. Esse campo contém uma cópia do documento JSON original usado quando um documento foi armazenado. O salvamento desse campo incorre em custos adicionais de armazenamento e de E/S de disco. No entanto, esses custos poderão ser marginais, dependendo da estrutura do documento, e você também deve estar ciente de que desabilitar o campo *\_source* impede que um cliente seja capaz de executar as seguintes operações:

	* Uso da API de atualização para modificar um documento.
	* Execução em tempo real do realce ao executar consultas.
	* Reindexação de dados.
	* Depuração de consultas e de agregações ao exibir o documento original.

	O exemplo a seguir desabilita o campo *\_source* para o tipo *logs* no índice *systembase*.

  ```http
  PUT /systembase
  {
		"settings" : {
			...
		},
		"logs" : {
			"_source": {
				"enabled": false
			},
			...,
		...
		}
  }
  ```

## Diretrizes gerais para a realização de testes de desempenho de ingestão de dados com o Elasticsearch

Os pontos a seguir destacam alguns dos itens que você deve considerar ao executar testes de desempenho com o Elasticsearch e ao analisar os resultados.

* O teste de desempenho é necessariamente demorado e dispendioso. No mínimo, colete estatísticas que meçam as taxas de transferência em disco e em rede, a utilização da CPU, os tempos de espera de CPU e a latência de disco (se possível). Isso pode oferecer uma resposta rápida para seu esforço de teste com um bom retorno do investimento.

* Aproveite quaisquer recursos de script fornecidos por sua ferramenta de teste de carga para coletar métricas que não estejam disponíveis de outra forma. Por exemplo, o Linux tem uma grande quantidade de estatísticas de desempenho boas e confiáveis que você pode coletar usando utilitários como o *vmstat* e o *iostat*. Você pode usar scripts com o JMeter para capturar dados como parte de um plano de teste.

* A engenharia de desempenho está relacionada principalmente à análise de estatísticas baseada em dados confiáveis e reproduzíveis. Não pare em métricas de alto nível, pois elas não fornecerão as informações necessárias. Estude os dados e faça com que a engenharia de desempenho transforme as operações de desenvolvimento em um processo com um loop de resposta rápida. Sempre examine as estatísticas comparando as tendências e os resultados/configurações anteriores. Ao fazer isso regularmente, você gerará dados compreensíveis, reproduzíveis em suas cargas de trabalho e com os quais você poderá avaliar os efeitos das alterações na configuração e na implantação.

* Use uma ferramenta como o Marvel para monitorar o desempenho do cluster e do nó durante o teste para obter informações adicionais. O JMeter pode ser eficaz para capturar dados brutos para análise posterior, mas o Marvel pode oferecer uma ideia em tempo real sobre o desempenho e as possíveis causas de falhas e de atrasos. Além disso, muitas ferramentas de teste de carga não fornecem visibilidade das métricas internas do Elasticsearch. Use e compare as taxas de transferência de indexação, as contagens de segmentos de mesclagem, as estatísticas de GC e os tempos de limitação disponíveis nas estatísticas de índice. Repita essa análise regularmente.

* Compare as estatísticas da ferramenta de teste de carga com as estatísticas de nó do Marvel (tráfego de disco e de rede, utilização de CPU, uso de memória e de pool de threads) para entender o padrão de correlação entre os números relatados pela infraestrutura e as estatísticas específicas do Elasticsearch.

* Como regra geral, considere *um nó, um fragmento* como a linha de base para testes de desempenho e avalie os custos de aplicativo ao adicionar nós. No entanto, não dependa completamente da extrapolação do desempenho com base em um pequeno número de nós e de fragmentos. Os custos de comunicação e de sincronização no cluster poderão ter efeitos imprevisíveis quanto maior for o número de nós e de fragmentos.

* Examine a alocação de fragmentos nos nós para comparar as estatísticas. Alguns nós terão menos réplicas e fragmentos, o que criará um desequilíbrio na utilização de recursos.

* Se você estiver executando o teste de carga, aumente o número de threads que sua ferramenta de teste usa para enviar o trabalho para o cluster até que os erros ocorram. Para um teste de taxa de transferência sustentável, considere manter seu nível de teste abaixo de um *teto de vidro* acordado. Se a taxa de erros exceder o teto de vidro, os erros incorrerão em custos nos recursos de back-end devido à capacidade de recuperação. Nessas situações, a taxa de transferência diminuirá inevitavelmente.

* Para simular como o sistema reage a grandes picos de atividade inesperados, considere executar testes que gerem um teto de vidro com taxa de erro. Isso lhe dará números de taxa de transferência não apenas em termos de capacidade, mas também do custo da capacidade de recuperação.

* Use uma contagem de documentos para avaliar o seu perfil de desempenho e recicle os documentos seguindo seus padrões de carga de trabalho. Considere a possibilidade de que quanto mais documentos forem adicionados, mais o perfil de desempenho poderá mudar.

* Lembre-se dos SLAs para IOPS e dos limites de taxa de transferência para o armazenamento que você está usando. Os diferentes tipos de armazenamento (SSD, mídia de rotação) têm taxas de transferência diferentes.

* Lembre-se de que o desempenho da CPU pode cair não apenas devido à atividade de disco e de rede, mas porque os aplicativos de back-end podem usar mecanismos de bloqueio e de comunicação com o processamento distribuído que podem causar a subutilização do processador.

* Execute testes de desempenho por pelo menos duas horas (não apenas alguns minutos). A indexação pode afetar o desempenho de maneiras que podem não ser visíveis imediatamente. Por exemplo, as estatísticas de coleta de lixo e a indexação de mesclagens da JVM podem alterar o perfil de desempenho ao longo do tempo.

* Considere como as atualizações de índice podem ter um grande impacto na taxa de transferência e na limitação da ingestão de dados com um cluster.

## Resumo

É importante entender como dimensionar a solução à medida que os volumes de dados e o número de solicitações aumentarem. O Elasticsearch em execução no Azure habilita a escala vertical e horizontal; você pode executar em VMs maiores com mais recursos e pode distribuir um cluster Elasticsearch em uma rede de VMs. O intervalo de opções pode ser surpreendente; é mais econômico implementar um cluster em um grande número de VMs pequenas, em um cluster com um pequeno número de VMs grandes ou em algum lugar entre eles? Além disso, quantos fragmentos cada índice deve conter e quais são as compensações em relação à ingestão de dados versus o desempenho da consulta? A maneira na qual os fragmentos são distribuídos entre os nós pode ter um impacto significativo na taxa de transferência de ingestão de dados. O uso de mais fragmentos pode reduzir a contenção interna que ocorre dentro de um fragmento, mas você deve equilibrar esse benefício com a sobrecarga que o uso de vários fragmentos pode impor a um cluster. Para responder a essas perguntas com eficiência, você deve estar preparado para testar seu sistema para determinar a estratégia mais adequada.

Para as cargas de trabalho de ingestão de dados, o desempenho do subsistema de E/S de disco é um fator crítico. O uso de SSDs pode aumentar a taxa de transferência, reduzindo a latência de operações de gravação do disco. Se você não precisar de grandes quantidades de espaço em disco em um nó, considere o uso de VMs padrão com o armazenamento efêmero em vez de VMs mais caras que ofereçam suporte ao armazenamento premium.

## Apêndice: O teste de desempenho de ingestão de dados de carga em massa

Este apêndice descreve o teste de desempenho executado no cluster Elasticsearch. Os testes foram realizados usando o JMeter executado em um conjunto separado de VMs. Os detalhes da configuração do ambiente de teste foram descritos em [Como criar um ambiente de teste de desempenho para o Elasticsearch no Azure][]. Para executar seus próprios testes, você pode criar seu próprio plano de teste do JMeter de forma manual ou pode usar os scripts de teste automatizados disponíveis separadamente. Confira [Como executar os testes de desempenho automatizados do Elasticsearch][] para saber mais.

A carga de trabalho de ingestão de dados executou um upload de documentos usando a API de inserção em massa. A finalidade desse índice era simular um repositório recebendo dados de log que representam os eventos do sistema para análise e pesquisa subsequentes. Cada documento foi armazenado em um único índice chamado *systembase*, com o tipo *logs*. Todos os documentos tinham o mesmo esquema fixo descrito pela tabela a seguir:

| Campo | Tipo de dados | Exemplo |
|---------------|---------------------|-----------------------------------|
| @timestamp | datetime | 2013-12-11T08:01:45.000Z |
| name | cadeia de caracteres | checkout.payment |
| message | cadeia de caracteres | Mensagem de solicitação de entrada |
| severityCode | inteiro | 1 |
| severity | cadeia de caracteres | informações |
| hostname | cadeia de caracteres | sixshot |
| hostip | string (endereço ip) | 10\.0.0.4 |
| pid | int | 123 |
| tid | int | 4325 |
| appId | string (uuid) | {00000000-0000-0000-000000000000} |
| appName | cadeia de caracteres | mytestapp |
| appVersion | cadeia de caracteres | 0\.1.0.1234 |
| type | int | 5 |
| subtype | int | 1 |
| correlationId | guid | {00000000-0000-0000-000000000000} |
| os | cadeia de caracteres | Linux |
| osVersion | cadeia de caracteres | 4\.1.1 |
| parameters | [ ] | {key:value,key:value} |

Você pode usar a solicitação a seguir para criar o índice. As configurações *number\_of\_replicas*, *refresh\_interval* e *number\_of\_shards* variaram dos valores mostrados abaixo em muitos dos testes.

> [AZURE.IMPORTANT] O índice foi removido e recriado antes de cada execução de teste.

```http
PUT /systembase
{
	"settings" : {
		"number_of_replicas": 1,
		"refresh_interval": "30s",
		"number_of_shards": "5"
	},
	"logs" : {
		"properties" : {
			"@timestamp": {
			"type": "date",
			"index" : "not_analyzed"
			},
			"name": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"message": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"severityCode": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"severity": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"hostname": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"hostip": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"pid": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"tid": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"appId": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"appName": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"appVersion": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"type": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"subtype": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"correlationId": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"os": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"osVersion": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"parameters": {
				"type": "string",     
				"index" : "not_analyzed"
			}
		}
	}
}
```

Cada lote de inserção em massa continha 1000 documentos. Cada documento foi gerado com base em uma combinação de valores aleatórios para os campos *severityCode*, *hostname*, *hostip*, *pid*, *tid*, *appName*, *appVersion*, *type*, *subtype* e *correlationId* e uma seleção aleatória de texto de um conjunto de termos fixos para os campos *name*, *message*, *severity*, *os*, *osVersion*, *parameters*, *data1* e *data2*. O número de instâncias de aplicativos cliente usadas para carregar dados foi cuidadosamente selecionado para maximizar o volume de entrada bem-sucedida. Os testes foram executados por duas horas para permitir ao cluster se estabelecer e reduzir a influência de qualquer falha temporária sobre os resultados gerais. Nesse momento, alguns testes carregaram cerca de 1,5 bilhão de documentos.

Os dados foram gerados dinamicamente usando uma amostra de solicitação JUnit personalizada adicionada a um grupo de threads em um plano de teste JMeter. O código JUnit foi criado usando o modelo Caso de Teste JUnit no IDE Eclipse.

> [AZURE.NOTE] Para saber mais sobre como criar um teste JUnit para o JMeter, confira [Como criar e implantar uma amostra do JMeter JUnit para testes de desempenho do Elasticsearch][].

O trecho a seguir mostra o código Java para testar o Elasticsearch 1.7.3. Observe que a classe de teste JUnit neste exemplo é denominada *ElasticsearchLoadTest2*:

```java
/* Java */
package elasticsearchtest2;

	import static org.junit.Assert.*;

	import org.junit.*;

	import java.util.*;

	import java.io.*;

	import org.elasticsearch.action.bulk.*;
	import org.elasticsearch.common.transport.*;
	import org.elasticsearch.client.transport.*;
	import org.elasticsearch.common.settings.*;
	import org.elasticsearch.common.xcontent.*;

	public class ElasticsearchLoadTest2 {

		private String [] names={"checkout","order","search","payment"};
		private String [] messages={"Incoming request from code","incoming operation succeeded with code","Operation completed time","transaction performed"};
		private String [] severity={"info","warning","transaction","verbose"};
		private String [] apps={"4D24BD62-20BF-4D74-B6DC-31313ABADB82","5D24BD62-20BF-4D74-B6DC-31313ABADB82","6D24BD62-20BF-4D74-B6DC-31313ABADB82","7D24BD62-20BF-4D74-B6DC-31313ABADB82"};

		private String hostname = "";
		private String indexstr = "";
		private String typestr = "";
		private int port = 0;
		private int itemsPerInsert = 0;
		private String clustername = "";
		private static Random rand=new Random();

		@Before
		public void setUp() throws Exception {
		}

		public ElasticsearchLoadTest2(String paras) {
		* Paras is a string containing a set of comma separated values for:
			hostname
			indexstr
			typestr
			port
			clustername
			node
			itemsPerInsert
		*/

			// Note: No checking/validation is performed

			String delims = "[ ]*,[ ]*"; // comma surrounded by zero or more spaces
			String[] items = paras.split(delims);

			hostname = items[0];
			indexstr = items[1];
			typestr = items[2];
			port = Integer.parseInt(items[3]);
			clustername = items[4];
			itemsPerInsert = Integer.parseInt(items[5]);

			if (itemsPerInsert == 0)
				itemsPerInsert = 1000;
			}

		@After
		public void tearDown() throws Exception {
		}

		@Test
		public void BulkBigInsertTest() throws IOException {

			Settings settings = ImmutableSettings.settingsBuilder().put("cluster.name", clustername).build();

			TransportClient client;
			client = new TransportClient(settings);

			try {
				client.addTransportAddress(new InetSocketTransportAddress(hostname, port));
				BulkRequestBuilder bulkRequest = client.prepareBulk();
				Random random = new Random();
				char[] exmarks = new char[12000];
				Arrays.fill(exmarks, 'x');
				String dataString = new String(exmarks);

				for(int i=1; i &lt; itemsPerInsert; i++){
					random.nextInt(10);
					int host=random.nextInt(20);

					bulkRequest.add(client.prepareIndex(indexstr, typestr).setSource(XContentFactory.jsonBuilder().startObject()
						.field("@timestamp", new Date())
						.field("name", names[random.nextInt(names.length)])
						.field("message", messages[random.nextInt(messages.length)])
						.field("severityCode", random.nextInt(10))
						.field("severity", severity[random.nextInt(severity.length)])
						.field("hostname", "Hostname"+host)
						.field("hostip", "10.1.0."+host)
						.field("pid",random.nextInt(10))
						.field("tid",random.nextInt(10))
						.field("appId", apps[random.nextInt(apps.length)])
						.field("appName", "application" + host)
						.field("appVersion", random.nextInt(5))
						.field("type", random.nextInt(6))
						.field("subtype", random.nextInt(6))
						.field("correlationId", UUID.randomUUID().toString())
						.field("os", "linux")
						.field("osVersion", "14.1.5")
						.field("parameters", "{key:value,key:value}")
						.field("data1",dataString)
						.field("data2",dataString)
					.endObject()));
				}

				BulkResponse bulkResponse = bulkRequest.execute().actionGet();
				assertFalse(bulkResponse.hasFailures());
			}
			finally {
				client.close();
			}
		}

		@Test
		public void BulkDataInsertTest() throws IOException {
			Settings settings = ImmutableSettings.settingsBuilder().put("cluster.name", clustername).build();

			TransportClient client;
			client = new TransportClient(settings);

			try {
				client.addTransportAddress(new InetSocketTransportAddress(hostname, port));
				BulkRequestBuilder bulkRequest = client.prepareBulk();

				for(int i=1; i&lt; itemsPerInsert; i++){
					rand.nextInt(10);
					int host=rand.nextInt(20);

					bulkRequest.add(client.prepareIndex(indexstr, typestr).setSource(XContentFactory.jsonBuilder().startObject()
						.field("@timestamp", new Date())
						.field("name", names[rand.nextInt(names.length)])
						.field("message", messages[rand.nextInt(messages.length)])
						.field("severityCode", rand.nextInt(10))
						.field("severity", severity[rand.nextInt(severity.length)])
						.field("hostname", "Hostname" + host)
						.field("hostip", "10.1.0."+host)
						.field("pid",rand.nextInt(10))
						.field("tid",rand.nextInt(10))
						.field("appId", apps[rand.nextInt(apps.length)])
						.field("appName", "application"+host)
						.field("appVersion", rand.nextInt(5))
						.field("type", rand.nextInt(6))
						.field("subtype", rand.nextInt(6))
						.field("correlationId", UUID.randomUUID().toString())
						.field("os", "linux")
						.field("osVersion", "14.1.5")
						.field("parameters", "{key:value,key:value}")
					.endObject()));
				}

				BulkResponse bulkResponse = bulkRequest.execute().actionGet();
				assertFalse(bulkResponse.hasFailures());
			}
			finally {
				client.close();
			}
		}
	}
```

As matrizes *names*, *messages*, *severity* e *apps* da *String* privada contêm um pequeno conjunto de valores dos quais os itens são selecionados aleatoriamente. Os itens de dados restantes para cada documento são gerados em tempo de execução.

O construtor que obtém o parâmetro *String* é invocado do JMeter, e os valores passados na cadeia de caracteres são especificados como parte da configuração da amostra da Solicitação JUnit. Para este teste do JUnit, o parâmetro *String* deve conter as seguintes informações:

* **Hostname**. É o nome ou o endereço IP do balanceador de carga do Azure. O balanceador de carga tenta distribuir a solicitação entre os nós de dados do cluster. Se você não estiver usando um balanceador de carga, poderá especificar o endereço de um nó do cluster, mas todas as solicitações serão direcionadas a esse nó, fazendo com que ele se torne um afunilamento.

* **Indexstr**. É o nome do índice ao qual os dados gerados pelo teste JUnit são adicionados; se você tiver criado o índice como descrito acima, esse valor deverá ser *systembase*.

* **Typestr**. É o tipo do índice no qual os dados são armazenados. Se você tiver criado o índice como descrito acima, esse valor deverá ser *logs*.

* **Port**. É a porta do host para a conexão. Na maioria dos casos, deve ser definida como 9300 (a porta usada pelo Elasticsearch para escutar solicitações de API de cliente; a porta 9200 é usada somente para solicitações HTTP).

* **Clustername**. É o nome do cluster Elasticsearch que contém o índice.

* **ItemsPerInsert**. É um parâmetro numérico que indica o número de documentos a serem adicionados a cada lote de inserção em massa. O tamanho de lote padrão é 1000.

Você pode especificar os dados para a cadeia de caracteres do construtor na página da Solicitação JUnit usada para configurar a amostra do JUnit no JMeter. A imagem a seguir mostra um exemplo:

![](media/guidance-elasticsearch/data-ingestion-image22.png)

Os métodos *BulkInsertTest* e *BigBulkInsertTest* executam o trabalho real de geração e de upload dos dados. Ambos os métodos são muito semelhantes; eles se conectam ao cluster Elasticsearch e criam um lote de documentos (como determinado pelo parâmetro de cadeia de caracteres do construtor *ItemsPerInsert*). Os documentos são adicionados ao índice usando a API em massa do Elasticsearch. A diferença entre os dois métodos é que os campos de cadeia de caracteres *data1* e *data2* em cada documento são omitidos do upload no método *BulkInsertTest*, mas são preenchidos com cadeias de caracteres de 12.000 caracteres no método *BigBulkInsertTest*. Observe que você selecionará qual desses métodos será executado usando a caixa *Método de Teste* na página Solicitação do JUnit no JMeter (realçada na figura anterior).

> [AZURE.NOTE] O código de exemplo apresentado aqui usa a biblioteca Cliente de Transporte do Elasticsearch 1.7.3. Se você estiver usando o Elasticsearch 2.0.0 ou posterior, deverá usar a biblioteca apropriada para a versão selecionada. Para saber mais sobre a biblioteca Cliente de Transporte do Elasticsearch 2.0.0, confira a página [Cliente de Transporte](https://www.elastic.co/guide/en/elasticsearch/client/java-api/2.0/transport-client.html) no site do Elasticsearch.

[Configurando a resiliência e a recuperação do Elasticsearch no Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Configurando resiliência e a recuperação do Elasticsearch no Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Como criar um ambiente de teste de desempenho para o Elasticsearch no Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Como executar os testes de desempenho automatizados do Elasticsearch]: guidance-elasticsearch-running-automated-performance-tests.md
[Como criar e implantar uma amostra do JMeter JUnit para testes de desempenho do Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->