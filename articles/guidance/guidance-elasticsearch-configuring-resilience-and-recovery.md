<properties
   pageTitle="Configurando a resiliência e a recuperação no Elasticsearch no Azure"
   description="Considerações relacionadas a resiliência e recuperação no Elasticsearch."
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
   
# Configurando a resiliência e a recuperação no Elasticsearch no Azure

Este artigo faz [parte de uma série](guidance-elasticsearch.md).

Um recurso fundamental do Elasticsearch é o suporte que ele dá à resiliência no caso de falhas de nó e/ou de eventos de partição de rede. A replicação é a maneira mais óbvia para melhorar a resiliência de qualquer cluster, permitindo que o Elasticsearch disponibilize mais de uma cópia de todos os itens de dados em nós diferentes para o caso de algum nó ficar inacessível. Se um nó ficar temporariamente indisponível, outros nós que contenham réplicas dos dados do nó ausente poderão servir os dados ausentes até que o problema seja resolvido. No caso de um problema de longo prazo, o nó que está faltando pode ser substituído por um novo e o Elasticsearch pode restaurar os dados no novo nó usando as réplicas.

Vamos resumir as opções de recuperação e resiliência disponíveis com o Elasticsearch quando hospedado no Azure e descrever alguns aspectos importantes de um cluster Elasticsearch que você deve considerar para minimizar a possibilidade de perda de dados e de aumento dos tempos de recuperação de dados.

Este artigo também ilustra alguns testes de exemplo que foram executados para mostrar os efeitos de diferentes tipos de falhas em um cluster Elasticsearch e como o sistema responde durante a recuperação.

Um cluster Elasticsearch usa réplicas para manter a disponibilidade e melhorar o desempenho de leitura. As réplicas devem ser armazenadas em diferentes VMs usando os fragmentos primários que elas replicam. A finalidade é permitir que o sistema continue a funcionar usando as VMs que têm as réplicas em caso de falha ou indisponibilidade de um nó de dados.

## Usando nós mestre dedicados

Um nó em um cluster Elasticsearch é escolhido como o nó mestre. A finalidade deste nó é realizar operações de gerenciamento de cluster, como:

- Detecção de falha de nós e alternação para réplicas,

- Realocação de fragmentos para balancear a carga de trabalho do nó e

- Recuperação de fragmentos quando um nó volta a ficar online novamente.

Você deve considerar o uso de nós mestre dedicados em clusters essenciais e fazer com que haja três nós dedicados cuja única função seja ser mestre. Essa configuração reduz a quantidade de trabalho com uso intensivo de recursos que esses nós devem executar (eles não armazenam dados ou manipulam consultas) e ajuda a melhorar a estabilidade do cluster. Somente um dos nós será escolhido, mas os outros conterão uma cópia do estado do sistema e poderão assumir em caso de falha do mestre escolhido.

## Controle de alta disponibilidade com o Azure – domínios de atualização e domínios de falha 

As VMs diferentes podem compartilhar o mesmo hardware físico. Em um datacenter do Azure, um único rack pode hospedar uma quantidade de VMs, e todas essas VMs compartilham uma mesma chave de rede e fonte de energia. Portanto, uma única falha no nível do rack pode ter impacto em várias VMs. O Azure usa o conceito de FDs (domínios de falha) para tentar distribuir esse risco. Um FD corresponde aproximadamente a um grupo de VMs que compartilham o mesmo rack. Para garantir que uma falha no nível do rack não falhe um nó e os nós que contenham todas as suas réplicas simultaneamente, você deve distribuir as VMs entre os FDs.

Da mesma forma, as VMs podem ser desligada pelos [Azure Fabric Controller](https://azure.microsoft.com/documentation/videos/fabric-controller-internals-building-and-updating-high-availability-apps/) para executar atualizações de sistema operacional e manutenções planejadas. O Azure aloca as VMs em UDs (domínios de atualização). Quando ocorre um evento de manutenção planejada, somente as VMs de um único UD são afetadas por vez. As VMs de outros UDs permanecem em execução até que as VMs do UD que estão sendo atualizadas voltem a ficar online. Portanto, você também precisa fazer com que as VMs que hospedam nós e suas réplicas pertençam a UDs diferentes sempre que possível.

> [AZURE.NOTE] Para saber mais sobre domínios de falha e de atualização, confira [Gerenciar a disponibilidade das máquinas virtuais][].

Você não pode alocar explicitamente uma VM para determinados UD e FD; essa alocação é controlada pelo Azure quando as VMs são criadas. Confira o documento [Gerenciar a disponibilidade das máquinas virtuais][] para saber mais. No entanto, você pode especificar que as VMs devem ser criadas como parte de um AS (conjunto de disponibilidade). As máquinas virtuais no mesmo AS serão espalhadas pelos UDs e FDs. Se você cria VMs manualmente, o Azure associa cada AS com dois FDs e cinco UDs, e as máquinas serão alocados para esses FDs e UDs, circulando conforme novas VMs vão sendo provisionadas, da seguinte maneira:

- A primeira VM provisionada no AS será colocada no primeiro FD (FD 0) e no primeiro UD (UD 0).
- A segunda VM provisionada no AS será colocada em FD 1 e UD 1.
- A terceira VM provisionada no AS será colocada em FD 0 e UD 2. -A quarta VM provisionada no AS será colocado em FD 1 e UD 3.
- A quinta VM provisionada no AS será colocada em FD 0 e UD 4.
- A sexta VM provisionada no AS será colocada em FD 1 e UD 0.
- A sétima VM provisionada no AS será colocada em FD 0 e UD 1.

> [AZURE.IMPORTANT] Se você criar VMs usando o ARM (Azure Resource Manager), cada conjunto de disponibilidade pode ser alocado em até 3 FDs e 20 UDs. Esse é um motivo convincente para usar o ARM.

Em geral, coloque todas as VMs que têm a mesma finalidade no mesmo conjunto de disponibilidade, mas crie diferentes conjuntos de disponibilidade para VMs que executam funções diferentes. Com o Elasticsearch, isso significa que você deve pensar em criar conjuntos de disponibilidade separados pelo menos para:

- As VMs que hospedam nós de dados.
- As VMs que hospedam nós de cliente (se você estiver usando-os).
- As VMs que hospedam nós mestre.

Além disso, você deve fazer com que cada nó em um cluster reconheça o domínio de atualização e o domínio de falha aos quais ele pertence. Essas informações podem ajudar a fazer com que o Elasticsearch não crie fragmentos e suas réplicas nos mesmos domínios de falha e de atualização, minimizando a possibilidade de desligarem o escopo de um fragmento e suas réplicas ao mesmo tempo. Você pode configurar um nó do Elasticsearch para espelhar a distribuição de hardware do cluster configurando o [Reconhecimento de Alocação de Fragmento](https://www.elastic.co/guide/en/elasticsearch/reference/current/allocation-awareness.html#allocation-awareness). Por exemplo, você poderia definir um par de atributos de nó personalizado chamados *faultDomain* e *updateDomain* no arquivo elasticsearch.yml, da seguinte maneira:

```yaml
node.faultDomain: \${FAULTDOMAIN}
node.updateDomain: \${UPDATEDOMAIN}
```

Nesse caso, os atributos são definidos usando os valores mantidos nas variáveis de ambiente *\\${FAULTDOMAIN}* e *\\${UPDATEDOMAIN}* quando o Elasticsearch é iniciado. Você também precisará adicionar as seguintes entradas no arquivo Elasticsearch.yml para indicar que *faultDomain* e *updateDomain* são atributos de reconhecimento de alocação e especificar os conjuntos de valores aceitáveis para eles:

```yaml
cluster.routing.allocation.awareness.force.updateDomain.values: 0,1,2,3,4
cluster.routing.allocation.awareness.force.faultDomain.values: 0,1
cluster.routing.allocation.awareness.attributes: updateDomain, faultDomain
```

Você pode usar o reconhecimento de alocação de fragmentos em conjunto com a [filtragem de alocação de fragmentos](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/shard-allocation-filtering.html#shard-allocation-filtering) para especificar explicitamente os nós que podem hospedar fragmentos para determinado índice.

Se você precisar dimensionar além do número de FDs e UDs em um AS, poderá criar VMs em ASs adicionais. No entanto, você precisa entender que nós em ASs diferentes podem ser desligados para manutenção simultaneamente. Tente fazer com que cada fragmento e pelo menos uma de suas réplicas fiquem no mesmo AS.

> [AZURE.NOTE] Atualmente, há um limite de 100 VMs por AS. Para saber mais, confira [Assinatura e limites de serviço, cotas e restrições do Azure](../azure-subscription-service-limits.md).

### Backup e restauração

O uso de réplicas não oferece proteção completa contra uma falha grave (como a exclusão acidental de todo o cluster). Faça o backup dos dados em um cluster regularmente e tenha uma estratégia já testada para restaurar o sistema usando esses backups.

Use as [APIs de restauração e instantâneo](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html) para fazer backup e restaurar índices. Os instantâneos podem ser salvos em um sistema de arquivos compartilhado. Como alternativa, há plug-ins disponíveis que podem gravar instantâneos para o HDFS (o [plug-in do HDFS](https://github.com/elasticsearch/elasticsearch-hadoop/tree/master/repository-hdfs)) ou no armazenamento do Azure (o [plug-in da Nuvem do Azure](https://github.com/elasticsearch/elasticsearch-cloud-azure#azure-repository)).

Você deve considerar os seguintes pontos ao selecionar o mecanismo de armazenamento de instantâneo:

- Você pode usar o [Armazenamento de Arquivos do Azure](https://azure.microsoft.com/services/storage/files/) para implementar um sistema de arquivos compartilhado que seja acessível de todos os nós.

- Somente use o plug-in HDFS se você estiver executando o Elasticsearch em conjunto com o Hadoop.

- O plug-in HDFS exige que você desabilite o Gerenciador de Segurança do Java em execução dentro da instância de Elasticsearch do JVM.

- O plug-in HDFS dá suporte a qualquer sistema de arquivos compatível, desde que a configuração correta do Hadoop seja usada com o Elasticsearch.

  
## Tratamento de conectividade intermitente entre nós

Falhas intermitentes de rede, reinicialização de VMs após a manutenção de rotina no datacenter e outros eventos semelhantes podem causar a inacessibilidade temporária dos nós. Nessas situações, em que o evento provavelmente terá curta duração, a sobrecarga de rebalanceamento dos fragmentos ocorre duas vezes em rápida sucessão (uma vez quando a falha é detectada e novamente quando o nó se torna visíveis para o mestre) e pode se tornar uma sobrecarga significativa que afeta o desempenho. Você pode impedir que a inacessibilidade temporária do nó faça com que o mestre faça o rebalanceamento do cluster definindo a propriedade *delayed\_timeout* de um ou todos os índices. O exemplo a seguir define o atraso como cinco minutos:

```http
PUT /_all/settings
{
	"settings": {
    "index.unassigned.node_left.delayed_timeout": "5m"
	}
}
```

Para saber mais, confira [Atraso na alocação com a saída de um nó](https://www.elastic.co/guide/en/elasticsearch/reference/current/delayed-allocation.html).

Em uma rede que está sujeita a interrupções, você também pode modificar os parâmetros que configuram um mestre para detectar quando outro nó não está acessível. Esses parâmetros são parte do módulo [Zen Discovery](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html#modules-discovery-zen) fornecido com o Elasticsearch, e você pode defini-los no arquivo Elasticsearch.yml. Por exemplo, o parâmetro *discovery.zen.fd.ping.retries* especifica quantas vezes um nó mestre tentará executar o ping para outro nó no cluster antes de decidir que falhou. O padrão desse parâmetro é três, mas você pode modificá-lo da seguinte maneira:

```yaml
discovery.zen.fd.ping_retries: 6
```

## Controle da recuperação

Quando a conectividade para um nó é restaurada após uma falha, os fragmentos nesse nó precisam ser recuperados para serem atualizados. Por padrão, o Elasticsearch recupera fragmentos na seguinte ordem:

- Pela data de criação do índice inverso. Os índices mais recentes são recuperados antes dos índices mais antigos.

- Pelo nome do índice inverso. Os índices com nomes em ordem alfanumérica maiores que outros serão restaurados primeiro.

Se alguns índices forem mais críticos que outros, mas não corresponderem a esses critérios, você poderá substituir a precedência de índices definindo a propriedade *index.priority*. Os índices com valor mais alto para essa propriedade serão recuperados antes de índices com valor mais baixo:

```http
PUT low_priority_index
{
	"settings": {
		"index.priority": 1
	}
}

PUT high_priority_index
{
	"settings": {
		"index.priority": 10
	}
}
```

Para saber mais, confira [Priorização da recuperação de índice](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/recovery-prioritization.html#recovery-prioritization).

Você pode monitorar o processo de recuperação para um ou mais índices usando a API *\_recovery*:

```http
GET /high_priority_index/_recovery?pretty=true
```

Para saber mais, confira [Recuperação de índices](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-recovery.html#indices-recovery).

> [AZURE.NOTE] Um cluster com fragmentos que exijam recuperação terá um status *amarelo* para indicar que nem todos os fragmentos estão disponíveis no momento. Quando todos os fragmentos estão disponíveis, o status do cluster deve ser revertido para *verde*. Um cluster com um status *vermelho* indica que um ou mais fragmentos estão fisicamente ausentes. Pode ser necessário restaurar os dados de um backup.

## Como evitar a dupla personalidade 

Se as conexões entre nós falharem, pode ocorrer um episódio de dupla personalidade. Se um nó mestre se torna inacessível para parte do cluster, o segmento de rede que pode ser conectado escolherá outro nó como mestre. Em um cluster mal configurado, é possível que cada parte do cluster tenha diferentes mestres, o que resulta em inconsistência ou danos aos dados. Esse fenômeno é conhecido como *dupla personalidade*.

Você pode reduzir as chances de uma dupla personalidade configurando a propriedade *minimum\_master\_nodes* do módulo de descoberta, no arquivo elasticsearch.yml. Esta propriedade especifica quantos nós devem estar disponíveis para habilitar a escolha de um mestre. O exemplo a seguir define o valor dessa propriedade como 2:

```yaml
discovery.zen.minimum_master_nodes: 2
```

Esse valor deve ser definido como a menor quantidade dos nós que poderiam assumir a função de mestre. Por exemplo, se o cluster tiver três nós mestre, *minimum\_master\_nodes* deve ser definido como dois; se você tiver cinco nós mestre, *minimum\_master\_nodes* deve ser definido como três. Idealmente, você deve ter um número ímpar de nós mestre.

**Observação:** é possível que ocorra dupla personalidade se vários nós mestre no mesmo cluster forem iniciados simultaneamente. Embora essa ocorrência seja rara, você pode evitá-la iniciando os nós em série com um pequeno atraso (cinco segundos) entre eles.

## Tratamento de atualizações sem interrupção

Se você estiver executando uma atualização de software nos nós por conta própria (como a migração para uma versão mais recente ou execução de um patch), precisará trabalhar nos nós individualmente, o que exige que você desligue-os enquanto o resto do cluster permanece disponível. Nessa situação, considere a implementação do seguinte processo:

Verifique se a realocação do fragmento é atrasada o suficiente para evitar que o mestre escolhido faça o rebalanceamento de fragmentos de um nó que esteja faltando entre o restante do cluster. Por padrão, a realocação do fragmento é atrasada em um minuto, mas você pode aumentar a duração se houver a probabilidade de o nó ficar indisponível por mais tempo. O exemplo a seguir aumenta o intervalo para cinco minutos:

```http
PUT /_all/_settings
{
	"settings": {
		"index.unassigned.node_left.delayed_timeout": "5m"
	}
}
```

> [AZURE.IMPORTANT] Você também pode desabilitar a realocação do fragmento completamente definindo o *cluster.routing.allocation.enable* do cluster para *nenhum*. No entanto, você deverá evitar usar essa abordagem se houver a probabilidade de criação de novos índices enquanto o nó estiver offline, pois isso pode fazer com que a alocação de índice falhe, resultando em um cluster com status vermelho.

Pare o Elasticsearch no nó a ser mantido. Se o Elasticsearch for executado como um serviço, você poderá interromper o processo de maneira controlada usando um comando do sistema operacional. O exemplo abaixo mostra como parar o serviço Elasticsearch em um único nó em execução no Ubuntu:

```bash
service elasticsearch stop
```

Como alternativa, você pode usar a API de desligamento diretamente no nó:

```http
POST /_cluster/nodes/_local/_shutdown
```

Realize a manutenção necessária do nó, reinicie-o e aguarde o seu ingresso no cluster.

Habilite novamente a alocação de fragmento:

```http
PUT /_cluster/settings
{
	"transient": {
		"cluster.routing.allocation.enable": "all"
	}
}
```

> [AZURE.NOTE] Se você precisar manter mais de um nó, repita as etapas 2, 3 e 4 em cada nó antes de habilitar novamente a alocação de fragmento.

Se possível, interrompa a indexação novos dados durante esse processo. Isso ajudará a minimizar o tempo de recuperação quando os nós voltarem a ficar online e reingressarem no cluster.

Cuidado com atualizações automáticas de itens como o JVM (o ideal é desabilitar as atualizações automáticas para esses itens), especialmente durante a execução do Elasticsearch no Windows. O agente de atualização do Java pode baixar a versão mais recente do Java automaticamente, mas pode exigir que o Elasticsearch seja reiniciado para que a atualização entre em vigor. Isso pode resultar em perda temporária não coordenada de nós, dependendo da configuração do agente de atualização de Java. Isso também pode resultar em instâncias diferentes do Elasticsearch no mesmo cluster executando versões diferentes do JVM, o que pode causar problemas de compatibilidade.

## Teste e análise de recuperação e resiliência do Elasticsearch

Esta seção descreve uma série de testes que foram executados para avaliar a resiliência e a recuperação de um cluster Elasticsearch composto por três nós de dados e três nós mestre.

Quatro cenários foram testados:

1.  Falha de nó e reinicialização sem perda de dados. Um nó de dados é interrompido e reiniciado após cinco minutos. O Elasticsearch foi configurado para não realocar fragmentos ausentes nesse intervalo; portanto, nenhuma E/S adicional é exigida na movimentação de fragmentos. Quando o nó é reiniciado, o processo de recuperação retorna os fragmentos nesse nó atualizados.

2.  Falha de nó com perda de dados catastrófica. Um nó de dados é interrompido e os dados que ele contém são apagados para simular uma falha de disco catastrófica. O nó é reiniciado (após cinco minutos), atuando efetivamente como uma substituição do nó original. O processo de recuperação requer a recriação dos dados ausentes para esse nó e pode envolver a realocação de fragmentos mantidos em outros nós.

3.  Falha de nó e reinicialização sem perda de dados, mas com realocação do fragmento. Um nó de dados é interrompido e os fragmentos que ele contém são realocados para outros nós. O nó é reiniciado e outras realocações são feitas para reequilibrar o cluster.

4.  Atualizações sem interrupção. Cada nó do cluster é interrompido e reiniciado após um breve intervalo para simular a reinicialização dos computadores depois de uma atualização de software. Somente um nó é interrompido por vez. Os fragmentos não serão realocados enquanto um nó estiver inativo.

Cada cenário estava sujeito à mesma carga de trabalho incluindo uma combinação de tarefas de ingestão de dados, agregações e consultas de filtro enquanto os nós eram desligados e recuperados. Cada operação de inserção em massa na carga de trabalho armazenou 1000 documentos e foi executada em relação a um índice, enquanto as agregações e consultas de filtro usavam um índice separado que continha vários milhões de documentos. A finalidade era permitir a avaliação do desempenho das consultas separadamente das inserções em massa. Cada índice era composto por cinco fragmentos e uma réplica.

As seções a seguir resumem os resultados desses testes, observando possíveis degradações no desempenho enquanto um nó está offline ou sendo recuperado, e possíveis erros que foram relatados. Os resultados são apresentados graficamente, destacando os pontos em que um ou mais nós estão ausentes e estimando o tempo necessário para que o sistema seja totalmente recuperado e atinja um nível semelhante de desempenho ao que era antes de os nós serem desligados.

> [AZURE.NOTE] Os equipamentos de teste usados para executar esses testes estão disponíveis online. Você pode adaptar e usar esses equipamentos para verificar a resiliência e a capacidade de recuperação de suas próprias configurações de cluster. Para saber mais, confira [Executando os testes automatizados de resiliência do Elasticsearch][].

## Falha de nó e reinicialização sem perda de dados: resultados

<!-- TODO; reformat this pdf for display inline -->

Os resultados do teste são mostrados no arquivo [ElasticsearchRecoveryScenario1.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario1.pdf). Os gráficos mostram o perfil de desempenho da carga de trabalho e os recursos físicos para cada nó no cluster. A parte inicial dos gráficos mostra o sistema em execução normalmente por aproximadamente 20 minutos, quando então o nó 0 é desligado por 5 minutos antes de ser reiniciado. As estatísticas para mais 20 minutos são mostradas. O sistema leva aproximadamente 10 minutos para recuperar e estabilizar. Isso é ilustrado pela taxas de transação e tempos de resposta para diferentes cargas de trabalho.

Observe os seguintes pontos:

- Nenhum erro foi relatado durante o teste. Nenhum dado foi perdido e todas as operações foram concluídas com êxito.

- As taxas de transação para todos os três tipos de operação (inserção em massa, consulta de agregação e consulta de filtro) diminuíram e os tempos de resposta médios aumentaram quando o nó 0 estava offline.

- Durante o período de recuperação, as taxas de transação e os tempos de resposta para as operações de consulta de filtro e consulta de agregação foram restauradas gradualmente. O desempenho da inserção em massa foi recuperado brevemente antes de diminuir. No entanto, isso provavelmente ocorre pelo crescimento do índice usado pela inserção em massa, e as taxas de transação dessa operação podem diminuir mesmo antes do desligamento do nó 0.

- O gráfico de utilização da CPU para o nó 0 mostra a redução da atividade durante a fase de recuperação. Isso ocorre devido ao aumento das atividades de rede e de disco causado pelo mecanismo de recuperação. O nó tem que acompanhar os dados que possa ter perdido enquanto estava desligado e atualizar os fragmentos que ele contém.

- Os fragmentos para os índices não são distribuídos de maneira exatamente igual entre todos os nós. Há dois índices que incluem cinco fragmentos e uma réplica cada, criando um total de 20 fragmentos. Dois nós, portanto, conterão seis fragmentos, enquanto os outros dois têm sete cada um. Isso fica evidente nos gráficos de utilização da CPU durante o período inicial de 20 minutos. O nó 0 fica menos ocupado do que os outros dois. Após a conclusão da recuperação, algumas trocas parecem ocorrer enquanto o nó 2 começa a ter cargas mais leves.

    
## Falha de nó com perda de dados catastrófica: resultados

<!-- TODO; reformat this pdf for display inline -->

Os resultados do teste são descritos no arquivo [ElasticsearchRecoveryScenario2.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario2.pdf). Assim como no primeiro teste, a parte inicial dos gráficos mostra o sistema em execução normalmente por aproximadamente 20 minutos, quando então o nó 0 é desligado por 5 minutos. Durante esse intervalo, os dados do Elasticsearch neste nó são removidos, simulando a perda de dados catastrófica, antes de ser reiniciado. A recuperação completa parece levar de 12 a 15 minutos antes que os níveis de desempenho vistos antes do teste sejam restaurados.

Observe os seguintes pontos:

- Nenhum erro foi relatado durante o teste. Nenhum dado foi perdido e todas as operações foram concluídas com êxito.

- As taxas de transação para todos os três tipos de operação (inserção em massa, consulta de agregação e consulta de filtro) diminuíram e os tempos de resposta médios aumentaram quando o nó 0 estava offline. Neste momento, o perfil de desempenho do teste é semelhante ao primeiro cenário. Isso não é surpreendente, já que, neste momento, os cenários são os mesmos.

- Durante o período de recuperação, as taxas de transação e tempos de resposta foram restaurados, embora tenha havido muito mais volatilidade nos números durante esse tempo. Isso ocorre mais provavelmente devido a um trabalho adicional que os nós do cluster estão executando, fornecendo os dados para restaurar os fragmentos ausentes. Esse trabalho adicional fica evidente na utilização da CPU, na atividade de disco e nos gráficos de atividade de rede.

- O gráfico de utilização da CPU para os nós 0 e 1 mostra a redução da atividade durante a fase de recuperação, devido ao aumento das atividades de rede e de disco causado pelo processo de recuperação. No primeiro cenário, somente o nó em recuperação exibiu esse comportamento, mas neste cenário é possível que a maior parte dos dados ausentes do nó 0 esteja sendo restaurada no nó 1.

- A atividade de E/S para o nó 0 realmente é reduzida em comparação com o primeiro cenário. Isso se deve as eficiências de E/S de simplesmente copiar os dados para um fragmento inteiro em vez de uma série de solicitações de E/S menores necessárias para atualizar um fragmento existente.

- A atividade de rede para todos os três nós indica picos de atividade conforme os dados são transmitidos e recebidos entre os nós. No cenário 1, somente o nó 0 apresentou essa quantidade de atividade de rede, mas ela parecia ser mantida por um período maior. Novamente, essa diferença pode ser devido à eficiência de transmitir todos os dados para um fragmento como solicitação única em vez de uma série de solicitações menores recebidas durante a recuperação de um fragmento.

## Falha de nó e reinicialização com realocação do fragmento: resultados

<!-- TODO; reformat this pdf for display inline -->

O arquivo [ElasticsearchRecoveryScenario3.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario3.pdf) ilustra os resultados do teste. Como ocorreu no primeiro teste, a parte inicial dos gráficos mostra o sistema em execução normalmente por aproximadamente 20 minutos, quando então o nó 0 é desligado por 5 minutos. Neste ponto, o cluster Elasticsearch tenta recriar os fragmentos ausentes e reequilibrar os fragmentos em todos os nós restantes. Após cinco minutos, o nó 0 volta a ficar online e, mais uma vez, o cluster tem de rebalancear os fragmentos. O desempenho é restaurado entre 12 e 15 minutos.

Observe os seguintes pontos:

- Nenhum erro foi relatado durante o teste. Nenhum dado foi perdido e todas as operações foram concluídas com êxito.

- As taxas de transação para todos os três tipos de operação (inserção em massa, consulta de agregação e consulta de filtro) diminuíram e os tempos de resposta médios aumentaram significativamente quando o nó 0 estava offline, comparado com os dois testes anteriores. Isso ocorre devido ao aumento da atividade do cluster na recriação dos fragmentos ausentes e no rebalanceamento do cluster, conforme evidenciado pelos valores gerados para as atividades de disco e de rede dos nós 1 e 2 no período.

- Durante o período após o religamento do nó 0, as taxas de transação e tempos de resposta permanecem voláteis.

- Os gráficos de utilização da CPU e atividade de disco do nó 0 mostra uma ação inicial bastante reduzida durante a fase de recuperação. Isso ocorre porque, nesse ponto, o nó 0 não está servindo dados. Após um período de aproximadamente cinco minutos, o nó entra em ação, mostrado pelo aumento repentino nas atividades de CPU, disco e rede. Isso provavelmente é causado pelo cluster ao redistribuir fragmentos entre os nós. O nó 0 mostra a atividade normal.
  
## Atualizações sem interrupção: resultados

<!-- TODO; reformat this pdf for display inline -->

Os resultados desse teste, no arquivo [ElasticsearchRecoveryScenario4.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario4.pdf), mostram como cada nó é desligado e religado sucessivamente. Cada nó é desligado por cinco minutos antes de ser reiniciado, quando então o próximo nó na sequência é interrompido.

Observe os seguintes pontos:

- Enquanto cada nó é desligado e religado, o desempenho em termos de taxa de transferência e tempos de resposta permanece razoavelmente o mesmo.

- A atividade de disco aumenta para cada nó por um curto período conforme ele vai sendo religado. Isso provavelmente ocorre porque o processo de recuperação efetua roll forward das alterações ocorridas enquanto o nó estava desligado.

- Quando um nó é desligado, ocorrem picos de atividade de rede nos nós restantes. Os picos também ocorrem quando um nó é reiniciado.

- Depois que o nó final for revezado, o sistema entra em um período de volatilidade significativa. Isso provavelmente é causado pelo fato de o processo de recuperação precisar sincronizar alterações em cada nó e garantir que todas as réplicas e seus fragmentos correspondentes estejam consistentes. Em determinado momento, esse esforço faz com que as operações sucessivas de inserção em massa atinjam o tempo limite e falhem. Os erros relatados em cada caso foram:

```
Failure -- BulkDataInsertTest17(org.apache.jmeter.protocol.java.sampler.JUnitSampler$AnnotatedTestCase): java.lang.AssertionError: failure in bulk execution:
[1]: index [systwo], type [logs], id [AVEg0JwjRKxX_sVoNrte], message [UnavailableShardsException[[systwo][2] Primary shard is not active or isn't assigned to a known node. Timeout: [1m], request: org.elasticsearch.action.bulk.BulkShardRequest@787cc3cd]]

```

A experimentação subsequente mostrou que introduzir um atraso de alguns minutos entre o revezamento de cada nó eliminava esse erro; portanto, ele provavelmente era causado pela contenção entre a tentativa do processo de recuperação de restaurar vários nós simultaneamente e a tentativa das operações de inserção em massa de armazenar milhares de documentos novos.


## Resumo

Os testes executados indicaram que:

- O Elasticsearch foi altamente resiliente aos modos mais comuns de falha que podem ocorrer em um cluster.

- O Elasticsearch pode recuperar rapidamente se um cluster bem projetado ficar sujeito a perda de dados catastrófica em um nó. Isso poderá ocorrer se você configurar o Elasticsearch para salvar dados no armazenamento efêmero e o nó for novamente provisionado após a reinicialização. Esses resultados mostram que mesmo nesse caso, os riscos do uso do armazenamento efêmero tem maior probabilidade de serem superados pelos benefícios de desempenho fornecidos por essa classe de armazenamento.

- Nos três primeiros cenários, nenhum erro ocorreu nas cargas de trabalho simultâneas de inserção em massa, agregação e consulta de filtro enquanto um nó era desligado e recuperado.

- Somente o cenário 4 indicou uma potencial perda de dados, e essa perda afetava somente novos dados que estavam sendo adicionados. Em aplicativos que realizam ingestão de dados, para reduzir essa probabilidade, é uma boa prática tentar novamente as operações de inserção que falharam, já que o tipo de erro relatado possivelmente é transitório.

- Os resultados de teste 4 também mostram que se você estiver fazendo a manutenção planejada de nós em um cluster, o desempenho melhorará se você der vários minutos entre o revezamento de dois nós. Em uma situação não planejada (como o revezamento de nós pelo datacenter depois de executar uma atualização do sistema operacional), você tem menos controle sobre como e quando os nós são desligados e reiniciados. A contenção que ocorre quando o Elasticsearch tenta recuperar o estado do cluster após interrupções de nós sequenciais pode resultar em tempos limite e erros.

[Gerenciar a disponibilidade das máquinas virtuais]: ../articles/virtual-machines/virtual-machines-manage-availability.md
[Executando os testes automatizados de resiliência do Elasticsearch]: guidance-elasticsearch-running-automated-resilience-tests.md

<!---HONumber=AcomDC_0224_2016-->