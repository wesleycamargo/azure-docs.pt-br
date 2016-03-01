<properties
   pageTitle="Execução do Elasticsearch no Azure | Microsoft Azure"
   description="Como instalar, configurar e executar o Elasticsearch no Azure."
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
   ms.date="02/05/2016"
   ms.author="mabsimms"/>

# Execução do Elasticsearch no Azure

Este artigo faz [parte de uma série](guidance-elasticsearch-introduction.md).

## Visão geral

O Elasticsearch é um banco de dados e mecanismo de pesquisa de software livre altamente escalonável. Ele é adequado para situações que exigem análise rápida e descoberta de informações mantidas em grandes conjuntos de dados. Cenários comuns incluem:

- Pesquisa de texto livre em larga escala, em que os documentos que apresentam combinações de termos de pesquisa podem ser localizados e recuperados rapidamente.
- Registro de eventos em log, onde as informações podem chegar de várias fontes. Talvez os dados precisem ser analisados para ter certeza de como uma cadeia de eventos levou a uma conclusão específica.
- Armazenamento de dados capturados de dispositivos remotos e de outras fontes. Os dados podem conter informações variadas, mas é um requisito comum apresentar essas informações em uma série de painéis para permitir que um operador entenda o estado do sistema geral. Os aplicativos também podem usar as informações para tomar decisões rápidas sobre o fluxo de dados e as operações de negócios que precisam ser executadas como resultado.
- Controle de estoque, onde as alterações no inventário são registradas à medida que os bens são vendidos. Os sistemas de negócios podem usar essas informações para enviar relatórios dos níveis de estoque aos usuários e reordenar o estoque se o nível de um produto ficar baixo. Os analistas podem examinar os dados em busca de tendências para ajudar a determinar quais produtos vendem bem sob certas circunstâncias.
- Análise financeira, na qual as informações sobre o mercado chegam quase que em tempo real. Os painéis podem ser gerados com o objetivo de indicar o desempenho minuto a minuto de vários instrumentos financeiros que podem ser usados para auxiliar na tomada de decisões de compra e venda.

Este documento fornece uma breve introdução à estrutura geral do Elasticsearch e descreve como implementar um cluster Elasticsearch usando o Azure. O documento tem como foco as práticas recomendadas para implantação de um cluster Elasticsearch, concentrando-se nos vários requisitos funcionais de desempenho e gerenciamento do sistema, e considerando como seus requisitos devem orientar a configuração e a topologia que você selecionar.

> [AZURE.NOTE] Este guia pressupõe que você tenha uma familiaridade básica com o [Elasticsearch][].

## A Estrutura do Elasticsearch

O Elasticsearch é um banco de dados de documentos altamente otimizado para atuar como um mecanismo de pesquisa. Os documentos são serializados no formato JSON. Os dados são mantidos em índices, implementados usando o [Apache Lucene][], no entanto, os detalhes são omitidos e não é necessário compreender totalmente o Lucene para usar o Elasticsearch.

### Clusters, Nós, Índices e Fragmentos

O Elasticsearch implementa uma arquitetura em cluster que usa a fragmentação para distribuir dados entre vários nós e a replicação para fornecer alta disponibilidade.

Os documentos são armazenados em índices. O usuário pode especificar quais campos em um documento são usados para identificá-lo de forma exclusiva dentro de um índice ou o sistema pode gerar automaticamente valores e um campo de chave. O índice é usado para organizar fisicamente os documentos e como o principal meio de localização de documentos. Além disso, o Elasticsearch cria automaticamente um conjunto de estruturas adicionais que atuam como índices invertidos nos campos restantes para permitir pesquisas e análises rápidas dentro de uma coleção.

Um índice é formado por um conjunto de fragmentos. Os documentos são distribuídos uniformemente pelos fragmentos usando um mecanismo de hash baseado em valores de chave do índice e no número de fragmentos no índice. Quando um documento é alocado em um fragmento, ele não será movido desse fragmento a menos que sua chave do índice seja alterada. O Elasticsearch distribui os fragmentos em todos os nós de dados disponíveis em um cluster. Um único nó pode conter inicialmente um ou mais fragmentos que pertencem ao mesmo índice, mas à medida que novos nós são adicionados ao cluster, o Elasticsearch realoca os fragmentos para garantir uma carga equilibrada em todo o sistema. O mesmo reequilíbrio é aplicado quando os nós são removidos.

Os índices podem ser replicados. Nesse caso, cada fragmento no índice é copiado. O Elasticsearch garante que cada fragmento original de um índice (chamado de "fragmento principal") e sua réplica sempre residam em nós diferentes.

> [AZURE.NOTE] O número de fragmentos em um índice não pode ser alterado com facilidade após a criação do índice, mas é possível adicionar réplicas.

Quando um documento é adicionado ou modificado, todas as operações de gravação são realizadas primeiro no fragmento principal e, depois, em cada réplica. Por padrão, esse processo é executado de forma síncrona para ajudar a garantir a consistência. O Elasticsearch usa a simultaneidade otimista com controle de versão ao gravar dados. As operações de leitura podem ser realizadas usando o fragmento principal ou qualquer uma de suas réplicas.

A Figura 1 mostra os aspectos essenciais de um cluster Elasticsearch formado por três nós. Foi criado um índice composto por dois fragmentos principais com duas réplicas para cada fragmento (seis fragmentos no total).

![](media/guidance-elasticsearch-general-cluster1.png)

**Figura 1.**
Um cluster Elasticsearch simples formado por dois nós principais e dois conjuntos de réplicas

Neste cluster, o fragmento principal 1 e o fragmento principal 2 estão localizados em nós separados para ajudar a equilibrar a carga entre eles. As réplicas são distribuídas de maneira semelhante. Se apenas um nó falhar, os restantes terão informações suficientes para permitir que o sistema continue funcionando. Se necessário, o Elasticsearch promoverá um fragmento de réplica a fragmento principal, caso o fragmento principal correspondente fique indisponível.
Quando um nó começa a ser executado, ele pode iniciar um novo cluster (se for o primeiro nó no cluster) ou ingressar em um cluster existente. O cluster ao qual um nó pertence é determinado pela configuração *cluster.name* no arquivo elasticsearch.yml.

### Funções do Nó

Os nós em um cluster Elasticsearch podem executar as seguintes funções:

- Um nó de dados que pode conter um ou mais fragmentos contendo dados de índice.
- Um nó de cliente que não contém dados de índice, mas que lida com solicitações de entrada feitas ao nó de dados apropriado por aplicativos cliente.
- Um nó mestre que não contém dados de índice, mas que executa operações de gerenciamento de cluster, como manutenção e distribuição de informações de roteamento pelo cluster (a lista de nós e dos fragmentos que eles contêm), indicação dos nós disponíveis, realocação dos fragmentos à medida que os nós aparecem e desaparecem e coordenação da recuperação após a falha do nó. Vários nós podem ser configurados como mestres, mas apenas um será escolhido para realmente realizar as funções de mestre. Se esse nó falhar, outra eleição ocorrerá e um dos outros nós mestres qualificados será escolhido e assumirá o controle.

Por padrão, os nós do Elasticsearch executam todas as três funções (para permitir que você crie um cluster funcional completo em uma única máquina para fins de desenvolvimento e prova de conceito), mas você pode alterar suas operações por meio das configurações *node.data* e *node.master* no arquivo *elasticsearch.yml*, da seguinte maneira:

```yaml
node.data: true
node.master: false
```

**Configuração para um nó de dados**

```yaml
node.data: false
node.master: false
```

**Configuração para um nó de cliente**

```yaml
node.data: false
node.master: true
```

**Configuração para um nó mestre**

> [AZURE.NOTE] O nó mestre escolhido é essencial para o bem-estar do cluster. Os outros nós efetuam ping nesse nó regularmente para assegurar que ele ainda está disponível. Se o nó mestre escolhido também estiver atuando como um nó de dados, talvez o nó fique muito ocupado e deixe de responder a esses pings. Nessa situação, o mestre é considerado com falha e um dos outros nós mestres é escolhido em seu lugar. Se o mestre original ainda estiver disponível, poderá resultar em um cluster com dois mestres escolhidos, causando um problema de "dupla personalidade" que pode levar à corrupção de dados e outros problemas. O documento Configuração, Teste e Análise da Resiliência e Recuperação do Elasticsearch descreve como você deve configurar o cluster para reduzir as chances dessa situação ocorrer. No entanto, e em última análise, em um cluster de tamanho moderado a grande, é uma boa estratégia usar nós mestres dedicados que não assumem a responsabilidade pelo gerenciamento dos dados.

Os nós em um cluster compartilham informações sobre os outros nós no cluster (com o protocolo [gossiping][]) e quais fragmentos eles contêm. Aplicativos cliente que armazenam e buscam dados podem se conectar a qualquer nó em um cluster e as solicitações serão roteadas de forma transparente para o nó correto. Quando um aplicativo cliente solicita dados do cluster, o nó que recebe primeiro a solicitação é responsável por dirigir a operação, comunicar-se com cada nó relevante para buscar os dados e, em seguida, agregar o resultado antes de retorná-lo ao aplicativo cliente. O uso de nós de cliente para lidar com as solicitações libera os nós de dados da execução desse trabalho de dispersão/coleta e permite que eles gastem seu tempo fornecendo dados. Você pode impedir que os aplicativos cliente se comuniquem acidentalmente com os nós de dados (fazendo com que eles atuem como nós de cliente) desabilitando o transporte HTTP para os nós de dados:

```yaml
http.enabled: false
```

Os nós de dados ainda podem se comunicar com outros nós de dados, com nós de clientes e nós mestres dedicados na mesma rede usando o módulo de transporte do Elasticsearch (que usa soquetes TCP para se conectar diretamente entre os nós), mas os aplicativos cliente só podem se conectar a nós de cliente por HTTP. A Figura 2 mostra uma topologia composta por uma mistura de nós mestres dedicados, nós de cliente e nós de dados em um cluster Elasticsearch.

![](media/guidance-elasticsearch-general-cluster2.png)

**Figura 2.**
Um cluster Elasticsearch mostrando tipos diferentes de nós

### Custos e benefícios de usar nós de clientes

Quando um aplicativo envia uma consulta a um cluster Elasticsearch, o nó ao qual o aplicativo se conecta é responsável por direcionar o processo de consulta. O nó encaminha a solicitação para cada nó de dados e coleta os resultados, retornando as informações acumuladas para o aplicativo. Se uma consulta envolve agregações e outros cálculos, o nó ao qual o aplicativo se conecta executa as operações necessárias depois de recuperar os dados de cada um dos outros nós. Esse processo de dispersão/coleta pode consumir recursos consideráveis de memória e processamento.

O uso de nós de cliente dedicados para execução dessas tarefas permite que os nós de dados se concentrem no gerenciamento e armazenamento de dados. O resultado é que muitos cenários que envolvem agregações e consultas complexas podem se beneficiar do uso de nós de cliente dedicados. No entanto, o impacto do uso de nós de cliente dedicados provavelmente sofrerá variações dependendo do cenário, da carga de trabalho e do tamanho do cluster. Por exemplo, cargas de trabalho de ingestão de dados podem ser menos eficientes usando nós de cliente devido ao "salto" de rede adicional que é necessário ao armazenar dados. Em um cluster de 3 nós com 6 fragmentos, se o sistema não estiver configurado com nós de cliente dedicados, com todos os fatores ambientais e cargas de nó iguais, haverá uma probabilidade de 1/3 de que um aplicativo que está armazenando ou modificando dados se conecte diretamente ao fragmento mais apropriado, eliminando a necessidade de executar um salto de rede adicional em 1/3 dos casos. Por outro lado, as cargas de trabalho que executam agregações complexas poderiam se beneficiar do uso de clientes dedicados, pois um único nó assumirá a responsabilidade por cada conjunto de operações de dispersão/coleta executadas por essas operações. Em um ambiente misto, você deve estar preparado para executar testes de desempenho para avaliar o impacto do uso de nós de cliente em suas cargas de trabalho específicas.

> [AZURE.NOTE] O documento Maximização da Agregação de Dados e do Desempenho de Consulta com o Elasticsearch no Azure resume um conjunto de marcos que foram realizados pela equipe de desenvolvimento de padrões e práticas da Microsoft, parcialmente para essa finalidade.

### Conexão com um cluster

O Elasticsearch expõe uma série de APIs REST para criar aplicativos cliente e enviar solicitações a um cluster. Se você estiver desenvolvendo aplicativos usando o .NET Framework, poderá contar com duas APIs de nível superior: [Elasticsearch.Net & NEST][].

Se você estiver compilando aplicativos cliente usando Java, use a [API de Cliente do Nó][] para criar nós de cliente dinamicamente e adicioná-los ao cluster. A criação dos nós de cliente de forma dinâmica será conveniente se seu sistema usar uma quantidade relativamente pequena de conexões de vida longa. Os nós de cliente criados usando a API de Nó são fornecidos com o mapa de roteamento de cluster (os detalhes de quais nós contêm quais fragmentos) pelo nó mestre. Essas informações permitem que o aplicativo Java se conecte diretamente aos nós apropriados ao indexar ou consultar dados, reduzindo o número de saltos que podem ser necessários ao usar outras APIs. O custo dessa abordagem é a sobrecarga de registrar o nó de cliente no cluster. Se uma grande quantidade de nós de cliente aparecer e desaparecer rapidamente, o impacto da manutenção e distribuição do mapa de roteamento do cluster pode ser considerável.

**Balanceamento de carga de conexão**

O Elasticsearch permite vários mecanismos de implementação de balanceamento de carga de conexão. A lista a seguir resume algumas abordagens comuns:

**Balanceamento de carga baseado no cliente**: se estiver compilando aplicativos cliente usando as APIs Elasticsearch.Net ou NEST, você poderá usar um pool de conexões para aplicar o round-robin em solicitações de conexão entre nós, ajudando a balancear a carga de solicitações sem a necessidade de um balanceador de carga externo. O trecho de código a seguir mostra como criar um objeto *ElasticsearchClient* configurado com os endereços de três nós. As solicitações do aplicativo cliente serão distribuídas entre esses nós:

```csharp
// C#
var node1 = new Uri("http://node1.example.com:9200");
var node2 = new Uri("http://node2.example.com:9200");
var node3 = new Uri("http://node3.example.com:9200");

var connectionPool = new SniffingConnectionPool(new[] {node1, node2, node3});
var config = new ConnectionConfiguration(connectionPool);
var client = new ElasticsearchClient(config);
```

> [AZURE.NOTE] Uma funcionalidade semelhante está disponível para aplicativos Java por meio da [API de Cliente de Transporte][].

**Balanceamento de carga baseado no servidor**: você pode usar um balanceador de carga separado para distribuir solicitações para os nós. Essa abordagem oferece a vantagem de transparência do endereço. Os aplicativos cliente não precisam ser configurados com os detalhes de cada nó, facilitando a adição, remoção ou realocação dos nós sem modificar qualquer código de cliente. A Figura 3 mostra uma configuração que usa um balanceador de carga para rotear solicitações para um conjunto de nós de cliente, embora a mesma estratégia possa ser usada para se conectar diretamente a nós de dados se os nós de cliente não forem usados.

> [AZURE.NOTE] Você pode usar o [Balanceador de Carga do Azure][] para expor o cluster à Internet pública ou usar um [balanceador de carga interno][] se os aplicativos cliente e o cluster estiverem totalmente na mesma VNET (rede virtual privada).

![](media/guidance-elasticsearch-general-clientappinstances.png)

**Figura 3.**
Instâncias do aplicativo cliente se conectando a um cluster Elasticsearch por meio do Balanceador de Carga do Azure

**Balanceamento de carga personalizado**: você pode usar [nginx][] como um servidor proxy reverso em vez de usar o Balanceador de Carga do Azure. O Nginx fornece vários métodos de balanceamento de carga, incluindo round robin, menos conectado (uma solicitação é roteada para o destino que possui menos conexões no momento) e hash com base no endereço IP do cliente.

> [AZURE.NOTE] Você pode implantar um servidor nginx como uma VM do Azure. Para manter a disponibilidade, você deve criar pelo menos dois servidores nginx no mesmo conjunto de disponibilidade do Azure.

Considere os seguintes pontos ao determinar se vai usar ou não o balanceamento de carga e qual implementação usar:

- Conectar-se ao mesmo nó para lidar com todas as solicitações de todas as instâncias de um aplicativo pode fazer com que esse nó se torne um afunilamento. À medida que o número de threads disponíveis no nó acaba, as solicitações são enfileiradas e podem ser rejeitadas se a fila ficar muito grande (não codifique os detalhes da conexão de um único nó no código do aplicativo que possa ser implantado para muitos usuários).
- O mecanismo de round robin das APIs Elasticsearch.Net, NEST e de Cliente de Transporte lida com as solicitações de conexão com falha repetindo a tentativa de conexão no próximo nó disponível no pool de conexões. Uma conexão com um nó sem resposta no pool pode ser temporariamente marcada como *inativa*. Ela pode voltar a ser ativada mais tarde e o pool pode efetuar o ping no nó para determinar se ele tornou-se ativo novamente.
- O balanceador de carga do Azure pode redirecionar transparentemente as solicitações para nós com base em vários fatores (endereço IP do cliente, porta do cliente, endereço IP de destino, porta de destino, tipo de protocolo). Seguindo essa estratégia, uma instância de um aplicativo cliente em execução em um determinado computador, provavelmente será direcionada para o mesmo nó Elasticsearch. Dependendo da configuração de investigação do balanceador de carga, se o serviço Elasticsearch falhar nesse nó, mas a própria VM permanecer em execução, todas as conexões com esse nó atingirão o tempo limite enquanto as conexões de outras instâncias do cliente com outros nós poderão continuar a ter êxito.
- O balanceador de carga do Azure pode ser configurado para retirar um nó do rodízio se ele não conseguir responder adequadamente às solicitações de investigação de integridade executadas pelo balanceador de carga.

### Descoberta de nó

O Elasticsearch baseia-se em comunicações ponto a ponto. Portanto, descobrir outros nós em um cluster é uma parte importante do ciclo de vida de um nó. A descoberta do nó permite que novos nós de dados sejam adicionados dinamicamente a um cluster e isso permite que o cluster escale horizontalmente de forma transparente. Além disso, se um nó de dados deixar de responder às solicitações de comunicações de outros nós, um nó mestre poderá decidir que o nó de dados falhou e tomar as medidas necessárias para realocar os fragmentos que ele estava mantendo para outros nós de dados operacionais.

A descoberta do nó do Elasticsearch é tratada usando um módulo de descoberta. O módulo de descoberta é um plug-in que pode ser alternado para usar um mecanismo de descoberta diferente. O módulo de descoberta padrão ([Zen][]) faz com que um nó emita solicitações de ping para localizar outros nós na mesma rede. Se outros nós responderem, eles usarão o protocolo gossip para trocar informações. Um nó mestre pode distribuir os fragmentos para o novo nó (se for um nó de dados) e reequilibrar o cluster.

O módulo de descoberta Zen também manipula o processo de eleição do mestre e o protocolo para a detecção de falha do nó.

Antes da versão 2.0 do Elasticsearch, o módulo de descoberta Zen usava comunicações multicast para permitir o contato entre os nós. Isso facilita muito a introdução de um novo nó em um cluster, mas também pode causar problemas de segurança se outra instalação do Elasticsearch na mesma rede usar o mesmo nome de cluster. A nova instalação é considerada parte do mesmo cluster e os fragmentos podem ser direcionados para nós nessa instalação. Além disso, se você estiver executando nós Elasticsearch como VMs (máquinas virtuais) do Azure, não haverá suporte para as mensagens de multicast. Por esses motivos, você deve configurar a descoberta Zen para usar mensagens unicast e fornecer uma lista de nós de contato válidos no arquivo de configuração elasticsearch.yml.

> [AZURE.NOTE] No Elasticsearch 2.0 e posteriores, o multicast já não é o mecanismo de descoberta padrão.

Se você estiver hospedando um cluster Elasticsearch dentro de uma rede virtual do Azure, você poderá especificar que o endereço IP particular atribuído pelo DHCP fornecido a cada VM no cluster deverá permanecer alocado (estático). Você pode configurar a mensagem unicast de descoberta do Zen usando esses endereços IP estáticos. Se você estiver usando VMs com endereços IP dinâmicos, saiba que se uma VM for interrompida e reiniciada, ela poderá receber um novo endereço IP, dificultando a descoberta. Para lidar com esse cenário, você pode trocar o módulo de descoberta Zen para o [Plug-in de Nuvem do Azure][]. Esse plug-in usa a API do Azure para implementar o mecanismo de descoberta que tem base nas informações de assinatura do Azure.

> [AZURE.NOTE] A versão atual do Plug-in de Nuvem do Azure exige a instalação do certificado de gerenciamento para sua assinatura do Azure no repositório de chaves Java no nó Elasticsearch e forneça o local e as credenciais de acesso do repositório de chaves no arquivo elasticsearch.yml. Esse arquivo é mantido em texto não criptografado, portanto, é extremamente importante que você tenha certeza de que esse arquivo só possa ser acessado pela conta que executa o serviço Elasticsearch. Além disso, talvez essa abordagem não seja compatível com implantações do ARM (Gerenciador de Recursos do Azure). Por esses motivos, recomendamos que você use endereços IP estáticos para nós mestres e use esses nós para implementar as mensagens unicast de descoberta do Zen em todo o cluster. Na configuração a seguir (retirada do arquivo elasticsearch.yml para um exemplo de nó de dados), os endereços IP do host fazem referência aos nós mestres do cluster:

>`discovery.zen.ping.multicast.enabled: false`  
`discovery.zen.ping.unicast.hosts: ["10.0.0.10","10.0.0.11","10.0.0.12"]`

## Diretrizes Gerais do Sistema

O Elasticsearch pode ser executado em vários computadores, desde um único laptop até um cluster de servidores high-end. No entanto, quanto mais recursos estiverem disponíveis em termos de memória, poder de computação e discos rápidos, melhor será o desempenho. As seções a seguir resumem os requisitos básicos de hardware e software para execução do Elasticsearch.

### Requisitos de Memória
O Elasticsearch tenta armazenar dados na memória para manter a velocidade. Um servidor de produção hospedando um nó para uma implantação corporativa comum ou comercial de tamanho moderado no Azure deve ter entre 14 e 28 GB de RAM (VMs D3 ou D4). Distribua a carga entre mais nós em vez de criar nós com mais memória (experimentos mostraram que o uso de nós maiores com mais memória pode causar tempos de recuperação maiores em caso de falha). No entanto, embora a criação de clusters com uma quantidade muito grande de nós pequenos possa aumentar a disponibilidade e a taxa de transferência, isso também aumenta o esforço envolvido no gerenciamento e manutenção desse sistema.

Aloque 50% da memória disponível em um servidor ao heap do Elasticsearch. Se você estiver usando o Linux, defina a variável de ambiente ES\_HEAP\_SIZE antes de executar o Elasticsearch. Como alternativa, se você estiver usando o Windows ou Linux, poderá especificar o tamanho da memória nos parâmetros *Xmx* e *Xms* ao iniciar o Elasticseach. Defina esses dois parâmetros com o mesmo valor para evitar que a JVM (máquina virtual Java) redimensione o heap em tempo de execução. No entanto, não aloque mais de 30 GB. Use a memória restante para o cache de arquivos do sistema operacional.

> [AZURE.NOTE] O Elasticsearch utiliza a biblioteca do Lucene para criar e gerenciar índices. As estruturas do Lucene usam um formato baseado em disco e o armazenamento dessas estruturas no cache do sistema de arquivos melhorará muito o desempenho.

> Além disso, o Elasticsearch é gravado em Java. O tamanho máximo ideal de heap para Java em uma máquina de 64 bits é um pouco acima de 30 GB. Acima desse tamanho, o Java alterna para o uso de um mecanismo estendido para fazer referência a objetos no heap, o que aumenta os requisitos de memória para cada objeto e reduz o desempenho. O coletor de lixo padrão Java (marcação e limpeza simultânea) também pode ser executado abaixo do ideal se o tamanho do heap estiver acima de 30 GB. No momento, não recomendamos a alternância para um coletor de lixo diferente, pois o Elasticsearch e o Lucene foram testados apenas com o padrão.

Não sobrecarregue a memória, pois a troca da memória principal pelo disco afetará gravemente o desempenho. Se possível, desabilite completamente a troca (os detalhes dependem do sistema operacional). Se isso não for possível, habilite a configuração *mlockall* no arquivo de configuração do Elasticsearch (elasticsearch.yml) da seguinte maneira:

```yaml
bootstrap.mlockall: true
```

Essa configuração faz com que a JVM bloqueie sua memória e impeça que ela seja trocada pelo sistema operacional.

### Requisitos de Disco e de Sistema de Arquivos

Use SSD (Unidades de Estado Sólido) para armazenar fragmentos. Os discos devem ser dimensionados para manter a quantidade máxima de dados prevista em seus fragmentos, embora seja possível adicionar mais discos posteriormente. Você pode estender um fragmento em vários discos em um nó.

> [AZURE.NOTE] O Elasticsearch compacta os dados para campos armazenados usando o algoritmo LZ4. Além disso, no Elasticsearch 2.0 em diante, você pode alterar o tipo de compactação. Você pode alternar o algoritmo de compactação para DEFLATE, que é usado pelos utilitários *zip* e *gzip*. Essa técnica de compactação pode usar mais recursos, mas considere usá-la para índices frios, por exemplo, dados de log arquivados. Essa abordagem pode ajudar a reduzir o tamanho do índice.

Não é essencial que todos os nós em um cluster tenham o mesmo layout de disco e capacidade. No entanto, um nó com uma capacidade de disco muito grande em comparação com os outros nós de um cluster atrairá mais dados e exigirá uma capacidade de processamento maior para manipular esses dados. Consequentemente, o nó pode se tornar "hot", ou muito acessado, se comparado a outros nós e isso pode, por sua vez, afetar o desempenho.

> [AZURE.NOTE] O Azure fornece várias opções de armazenamento de disco, incluindo o armazenamento padrão, o armazenamento premium e o armazenamento efêmero. O armazenamento padrão baseia-se em uma mídia de rotação, enquanto o armazenamento premium usa SSDs. Dependendo do SKU usado para implementar uma VM, o armazenamento efêmero pode ser implementado como uma mídia de rotação (VMs da série A) ou SSDs (VMs da série D e superior). Há compensações técnicas e financeiras que você precisa equilibrar ao considerar as opções de armazenamento e elas estão descritas mais detalhadamente no documento Maximização do Desempenho de Ingestão de Dados com o Elasticsearch no Azure.

Se possível, use o RAID 0 (distribuição). Outras formas de RAID que implementam espelhamento e paridade são desnecessárias, pois o Elasticsearch fornece sua própria solução de alta disponibilidade na forma de réplicas e os discos do Azure salvam três cópias dos dados do disco mesmo assim.

> [AZURE.NOTE] Antes do Elasticsearch 2.0.0, também era possível implementar a distribuição no nível do software, especificando vários diretórios na configuração *path.data*. No Elasticsearch 2.0.0, essa forma de distribuição já não tem suporte. Em vez disso, diferentes fragmentos podem ser alocados para caminhos diferentes, mas todos os arquivos em um único fragmento serão gravados no mesmo caminho. Se você precisar de distribuição, distribua os dados no nível de hardware ou do sistema operacional.

Você também deve estar ciente de que a taxa de transferência total de E/S para discos do Azure anexados a uma VM é limitada pelo grupo de armazenamento ao qual eles pertencem. Uma única conta de armazenamento padrão pode lidar com uma taxa de solicitação de até 20 mil IOPS (essa limitação não se aplica ao armazenamento premium). Se esse valor for menor do que o tráfego antecipado de E/S do disco, distribua os discos das VMs no cluster em várias contas de armazenamento, lembrando que uma única assinatura pode criar até 100 contas de armazenamento.

A biblioteca do Lucene pode usar uma grande quantidade de arquivos para armazenar dados de índice e o Elasticsearch pode abrir uma quantidade considerável de soquetes para comunicação entre nós e com clientes. Certifique-se de que o sistema operacional esteja configurado para dar suporte a uma quantidade adequada de descritores de arquivo aberto (até 64 mil se houver memória suficiente disponível). Observe que a configuração padrão para muitas distribuições do Linux limita o número de descritores de arquivo aberto para 1024, o que é muito pouco.

O Elasticsearch usa uma combinação de E/S mmap (mapeada em memória) e (NIO) (Nova E/S) do Java para otimizar o acesso simultâneo a arquivos de dados e índices. Se você estiver usando o Linux, configure o sistema operacional para garantir que haja memória virtual suficiente disponível com espaço para 256k de áreas mapeadas em memória.

> [AZURE.NOTE] Muitas distribuições Linux assumem o padrão de uso do agendador CFQ (Enfileiramento Completamente Justo) ao organizar a gravação de dados no disco. Este agendador não é otimizado para SSDs. Considere a possibilidade de reconfigurar o sistema operacional para usar o Agendador NOOP ou o Agendador de prazo, ambos mais eficazes para SSDs.

### Requisitos de CPU

Use CPUs com vários núcleos. Prefira mais núcleos do que CPUs mais rápidas com menos núcleos. Isso é porque o tamanho padrão do threadpool do Elasticsearch está configurado com base no número de núcleos de CPU disponíveis. Os algoritmos usados pelo Elasticsearch são otimizados com base nesses cálculos e recomendamos que você não altere as configurações padrão do threadpool do Elasticsearch.

> [AZURE.NOTE] As VMs do Azure estão disponíveis em várias configurações de CPU, com suporte para 1 a 32 núcleos. Para um nó de dados, um bom ponto de partida é considerar uma VM da série D padrão e selecionar os SKUs da D3 (4 núcleos) ou D4 (8 núcleos). O D3 também fornece 14 GB de RAM, enquanto o D4 inclui 28 GB. Se você precisar de armazenamento em disco premium, use uma VM da série DS (considere um computador DS3 ou DS4), embora a série Dv2 forneça processadores Intel Xeon de 2.4 GHz se você suspeitar que o desempenho da CPU é um fator fundamental na limitação da taxa de transferência. A série G (para armazenamento padrão) e a série GS (para armazenamento premium) usam processadores Xeon E5 V3 que podem ser úteis para cargas de trabalho muito pesadas, como agregações em larga escala. Para obter as informações mais recentes, visite [Tamanhos de Máquinas Virtuais][].

### Requisitos de Rede

O Elasticsearch exige uma largura de banda entre 1 e 10 Gbps, dependendo do tamanho e da volatilidade dos clusters que ele implementa. O Elasticsearch migra os fragmentos entre os nós à medida que mais nós são adicionados a um cluster. O Elasticsearch pressupõe que o tempo de comunicação entre todos os nós é praticamente equivalente e não considera a localização relativa dos fragmentos mantidos nesses nós. Além disso, a replicação pode incorrer em uma E/S de rede considerável entre os fragmentos. Por esses motivos, evite a criação de clusters em nós que estão dispersos geograficamente.

Observe que a largura de banda de rede em VMs do Azure é limitada não apenas pelo SKU, mas também pelo uso total da rede do hardware físico no qual as VMs são executadas. As VMs que compartilham hardware terão menos largura de banda do que aquelas que são executadas em máquinas dedicadas, mas são mais baratas para implantar. Isso significa que, além de selecionar a série e o SKU de suas VMs, você tem pouco controle sobre a largura de banda de rede disponível. Por exemplo, embora você possa criar VMs com vários NICs virtuais, isso não aumentará a largura de banda total disponível para suas VMs. Isso significa apenas que mais NICs virtuais competem pelos mesmos recursos de rede física.

### Requisitos de Software

Você pode executar o Elasticsearch no Windows ou no Linux. O serviço Elasticsearch é implantado como uma biblioteca jar do Java e possui dependências em outras bibliotecas Java incluídas no pacote do Elasticsearch. Você deve instalar a JVM do Java 7 (atualização 55 ou mais recente) ou Java 8 (atualização 20 ou mais recente) para executar o Elasticsearch.

> [AZURE.NOTE] Além dos parâmetros de memória *Xmx* e *Xms* (especificados como opções de linha de comando para o mecanismo Elasticsearch, confira [Requisitos de Memória][]) não modifique as configurações de JVM padrão. O Elasticsearch foi criado usando os valores padrão. Alterá-los pode causar o desajuste e um desempenho ruim do Elasticsearch.

### Implantação do Elasticsearch no Azure

Embora não seja difícil implantar uma única instância do Elasticsearch, a criação de vários nós e a instalação e configuração do Elasticsearch em cada um deles pode ser um processo demorado e propenso a erros. Se você estiver considerando a execução do Elasticsearch nas VMs do Azure, terá duas opções que podem ajudar a reduzir as chances de erros.
- Usar o [modelo ARM (Gerenciador de Recursos do Azure)](http://azure.microsoft.com/documentation/templates/elasticsearch/) para compilar o cluster. Esse modelo é totalmente parametrizado para permitir que você especifique os níveis de tamanho e desempenho das VMs que implementam os nós, o número de discos a serem usados e outros fatores comuns. O modelo pode criar um cluster baseado no Windows Server 2012 ou no Ubuntu Linux 14.0.4.
- Usar scripts que podem ser automatizadas ou executados no modo autônomo. Os scripts que podem criar e implantar um cluster Elasticsearch estão disponíveis no site [Modelos de Início Rápido do Azure][].

## Considerações sobre Dimensionamento e Escalabilidade do Cluster e dos Nós

O Elasticsearch permite várias topologias de implantação, projetadas para oferecer suporte a diferentes requisitos e níveis de escala. Esta seção discute algumas topologias comuns e descreve as considerações de implementação de clusters com base nessas topologias.

### Topologias do Elasticsearch

A Figura 4 ilustra um ponto de partida para a criação de uma topologia do Elasticsearch para a nuvem com base em VMs do Azure.

![](media/guidance-elasticsearch-general-startingpoint.png)

**Figura 4.**
Ponto de partida sugerido para a criação de um cluster Elasticsearch com o Azure

Essa topologia é composta por seis nós de dados junto com três nós de cliente e três nós mestres (apenas um nó mestre é escolhido, os outros dois estão disponíveis para eleição caso o mestre escolhido falhe.) Cada nó é implementado como uma VM separada. Os aplicativos Web do Azure são direcionados aos nós de cliente por meio de um balanceador de carga. Neste exemplo, todos os nós e os aplicativos Web residem na mesma rede virtual do Azure, o que os isola efetivamente do mundo exterior. Se houver a necessidade de disponibilizar o cluster externamente (possivelmente como parte de uma solução híbrida incorporando clientes locais), você poderá usar o Balanceador de Carga do Azure para fornecer um endereço IP público, mas será necessário tomar precauções de segurança adicionais para impedir o acesso não autorizado ao cluster. O "Jump Box" opcional é uma VM disponível somente para administradores. Essa VM tem uma conexão de rede com a rede virtual do Azure, mas também uma conexão de rede voltada para fora para permitir o logon de administrador de uma rede externa (esse logon deve ser protegido com uma senha forte ou um certificado). Um administrador pode fazer logon na Jump Box e conectar-se de lá diretamente a qualquer um dos nós no cluster. Entre as abordagens alternativas estão o uso de uma VPN site a site entre uma organização e a rede virtual ou circuitos de [Rota Expressa][] para se conectar à rede virtual. Esses mecanismos permitem o acesso administrativo ao cluster sem expor o cluster à Internet pública.

Para manter a disponibilidade da VM, os nós de dados são agrupados no mesmo conjunto de disponibilidade do Azure. Da mesma forma, os nós de cliente são mantidos em outro conjunto de disponibilidade e os nós mestres são armazenados em um terceiro conjunto de disponibilidade.

Essa topologia é relativamente fácil de expandir. Basta adicionar mais nós do tipo apropriado e certificar-se de que eles estejam configurados com o mesmo nome de cluster no arquivo elasticsearch.yml. Os nós de cliente também precisam ser adicionados ao pool de back-end do balanceador de carga do Azure.

**Localização Geográfica de Clusters**

Não espalhe nós em um cluster entre regiões, pois isso pode afetar o desempenho da comunicação entre os nós (confira [Requisitos de Rede][]). A localização geográfica dos dados próximos aos usuários em regiões diferentes exigem a criação de vários clusters. Nessa situação, você precisa considerar como (ou até mesmo se deve) sincronizar os clusters. Entre as soluções possíveis estão:

**Conectar-se ao cluster usando [nós de tribo][].** Um nó de tribo é semelhante a um nó de cliente, exceto pelo fato de que ele pode participar de vários clusters Elasticsearch e pela possibilidade de visualizá-los como um grande cluster. Os dados ainda são gerenciados localmente por cada cluster (as atualizações não são propagadas pelos clusters), mas todos os dados são visíveis. Um nó de tribo pode consultar, criar e gerenciar documentos em qualquer cluster. As principais restrições de um nó de tribo são: ele não pode ser usado para criar um novo índice e os nomes de índice devem ser exclusivos em todos os clusters. Portanto, é importante considerar como os índices serão chamados quando você criar os clusters que serão acessados por nós de tribo.

Com esse mecanismo, cada cluster pode conter os dados que provavelmente serão acessados por aplicativos cliente locais, mas esses clientes ainda podem acessar e modificar dados remotos, embora com uma possível extensão de latência. A Figura 5 mostra um exemplo dessa topologia. O nó de tribo no Cluster 1 está realçado. Os outros clusters também podem ter nós de tribo, embora eles não apareçam no diagrama:

![](media/guidance-elasticsearch-general-tribenode.png)

**Figura 5.**
Um aplicativo cliente acessando vários clusters por meio de um nó de tribo

Neste exemplo, o aplicativo cliente conecta-se ao nó de tribo no Cluster 1 (colocalizado na mesma região), mas esse nó está configurado para poder acessar os Clusters 2 e 3, que podem estar localizados em regiões diferentes. O aplicativo cliente pode enviar solicitações para recuperar ou modificar dados em qualquer um dos clusters.

> [AZURE.NOTE] Os nós de tribo exigem a descoberta multicast para se conectarem aos clusters, o que pode representar uma preocupação com relação à segurança. Consulte a seção [Descoberta de Nós][] para saber mais.

*	Implementação da replicação geográfica entre clusters. Nessa abordagem, as alterações feitas em cada cluster são propagadas quase que em tempo real para os clusters localizados em outros data centers. Há plug-ins de terceiros disponíveis para o Elasticsearch que oferecem suporte a essa funcionalidade, como o [Plug-in de Alterações PubNub][].
*	Usando o [módulo de Instantâneo e de Restauração do Elasticsearch][]. Se os dados apresentarem uma movimentação muito lenta e forem modificados apenas por um cluster, você poderá considerar o uso de instantâneos para fazer uma cópia periódica dos dados e restaurar esses instantâneos em outros clusters (os instantâneos podem ser armazenados no Armazenamento de Blobs do Azure, se você tiver instalado o [Plug-in de Nuvem do Azure][]). No entanto, essa solução não funciona bem para dados que sofrem alterações rapidamente ou se os dados puderem ser alterados em mais de um cluster.

**Topologias de Pequena Escala**

As topologias de larga escala, formada por clusters de nós mestres dedicados, nós de cliente e nós de dados, talvez não sejam apropriadas para todos os cenários. Se você estiver compilando um sistema de desenvolvimento ou de produção em pequena escala, considere o cluster de 3 nós mostrado na Figura 6. Os aplicativos cliente se conectam diretamente a qualquer nó de dados disponível no cluster. O cluster contém três fragmentos rotulados P1-P3 (para permitir o crescimento), além de réplicas rotuladas como R1-R3. O uso de três nós permite ao Elasticsearch distribuir os fragmentos e as réplicas de forma que se qualquer nó falhar não haverá perda de dados.

![](media/guidance-elasticsearch-general-threenodecluster.png)

**Figura 6.**
Um cluster de três nós com três fragmentos e réplicas

Se você estiver executando uma instalação de desenvolvimento em uma máquina autônoma, poderá configurar um cluster com um único nó que atua como mestre, cliente e armazenamento de dados. Como alternativa, você pode iniciar vários nós em execução como um cluster no mesmo computador por meio da inicialização de mais de uma instância do Elasticsearch. A Figura 7 mostra um exemplo.

![](media/guidance-elasticsearch-general-developmentconfiguration.png)

**Figura 7.**
Uma configuração de desenvolvimento executando vários nós do Elasticsearch na mesma máquina

Observe que nenhuma dessas configurações autônomas são recomendadas para um ambiente de produção, pois podem causar contenção, a menos que sua máquina de desenvolvimento tenha uma quantidade significativa de memória e vários discos rápidos. Além disso, elas não fornecem qualquer garantia de alta disponibilidade. Se a máquina falhar, todos os nós serão perdidos.

### Considerações sobre o Dimensionamento de um Cluster e Nós de Dados

O Elasticsearch pode ser dimensionado em duas dimensões: verticalmente (usando computadores maiores e mais poderosos) e horizontalmente (distribuindo a carga entre computadores).

**Dimensionamento Vertical dos Nós de Dados do Elasticsearch**

Se você estiver hospedando um cluster Elasticsearch usando VMs do Azure, cada nó poderá corresponder a uma VM. O limite de escalabilidade vertical de um nó é amplamente controlado pelo SKU da VM e pelas restrições gerais aplicadas às contas de armazenamento individuais e assinaturas do Azure. A página [Limites, Cotas e Restrições da Assinatura e do Serviço do Azure](azure-subscription-service-limits/) descreve detalhadamente esses limites, mas quando o assunto é a compilação de um cluster Elasticsearch, os itens na lista a seguir são mais pertinentes. Além disso, provavelmente não é uma boa ideia usar VMs com mais de 64 GB de memória sem um bom motivo para isso. Conforme descrito na seção [Requisitos de Memória][], você não deve alocar mais de 30 GB de RAM a cada VM para a JVM e permitir que o sistema operacional utilize a memória restante para o buffer de E/S: - cada conta de armazenamento é restrita a
- 20.000 IOPS. O uso da mesma conta de armazenamento para manter uma quantidade de VHDs pode limitar o desempenho dessas VHDs.
- O número de nós de dados em uma rede virtual. Se você não estiver usando o ARM (Gerenciador de Recursos do Azure), haverá um limite de 2048 instâncias de VM por rede virtual. Embora isso seja suficiente em muitos casos, se você tiver uma configuração muito grande com milhares de nós, isso pode ser uma limitação.
- Número de contas de armazenamento por assinatura por região. Você pode criar até 100 contas de armazenamento por assinatura do Azure em cada região. As contas de armazenamento são usadas para armazenar discos virtuais e cada conta de armazenamento tem um limite de 500 TB de espaço.
- Número de núcleos por assinatura. O limite padrão é de 20 núcleos por assinatura, mas isso pode ser aumentado pela Microsoft para até 10 mil núcleos. Lembre-se de que alguns tamanhos de VM (A9, A11, D14 e DS14) podem conter 16 núcleos, enquanto uma VM G5 tem 32 núcleos.
- A quantidade de memória por tamanho da VM. VMs com um tamanho menor têm quantidades limitadas de memória disponível (máquinas D1 têm 3,5 GB, e máquinas D2 têm 7 GB). Talvez essas máquinas não sejam adequadas para cenários que exigem do Elasticsearch o armazenamento em cache de uma quantidade considerável de dados para atingir um bom desempenho (agregação de dados ou análise de uma grande quantidade de documentos durante a ingestão de dados, por exemplo).
- O número máximo de discos por tamanho de VM. Essa restrição pode limitar o tamanho e o desempenho de um cluster. Menos discos significa que menos dados podem ser armazenados e o desempenho pode ser reduzido com menos discos disponíveis para distribuição.
- O número de domínios de atualização/domínios com falha por conjunto de disponibilidade. Se você criar VMs usando o ARM, cada conjunto de disponibilidade poderá receber até três domínios de falha e até 20 domínios de atualização. Essa limitação pode afetar a resiliência de um cluster de grande porte sujeito a atualizações cumulativas frequentes.

Com essas restrições em mente, você deve sempre distribuir os discos virtuais nas VMs de um cluster entre contas de armazenamento para reduzir as chances de limitação de E/S. Em um cluster muito grande, talvez seja necessário reprojetar sua infraestrutura lógica e dividi-la em partições separadas ou funcionais. Por exemplo, talvez seja necessário dividir o cluster entre assinaturas, embora esse processo possa gerar mais complicações devido à necessidade de conectar redes virtuais.

>	[AZURE.NOTE] Esteja ciente de que, com o Azure, contas de armazenamento são fixadas em um carimbo de armazenamento específico. Esse é um mecanismo interno usado para manter consistência e disponibilidade. O artigo [A Highly Available Cloud Storage Service with Strong Consistency][] fornece mais detalhes sobre como isso funciona. Se você tiver uma interrupção de armazenamento localizada em um carimbo específico, terá erros em todas as unidades criadas usando essa conta. Quando isso ocorre, quaisquer VMs que usem essas unidades podem falhar. Usar várias contas de armazenamento para hospedar as diferentes unidades de uma VM pode, portanto, aumentar o risco de falha nessa VM. Por esse motivo, recomenda-se usar uma única conta de armazenamento por nó e armazenar a unidade do sistema e todas as unidades de dados nessa conta.

**Dimensionamento Horizontal de um Cluster Elasticsearch**

Internamente, no Elasticsearch, o limite de escalabilidade horizontal é determinado pelo número de fragmentos definido para cada índice. Inicialmente, vários fragmentos podem ser alocados para o mesmo nó em um cluster, mas à medida que o volume de dados cresce, é possível adicionar outros nós e os fragmentos podem ser distribuídos entre esses nós. Teoricamente, somente quando o número de nós atingir o número de fragmentos, o sistema deixará de dimensionar horizontalmente.

Assim como ocorre com o dimensionamento vertical, há alguns problemas que você deve considerar ao avaliar a implementação do dimensionamento horizontal, incluindo:

- O número máximo de VMs que você pode conectar em uma rede virtual do Azure. Isso pode limitar a escalabilidade horizontal de um cluster muito grande. Você pode criar um cluster de nós que abrange mais de uma rede virtual para evitar esse limite, mas essa abordagem pode causar a redução do desempenho devido à falta de localidade de cada nó em relação aos seus colegas.
- O número de discos por tamanho da VM. Séries e SKUs diferentes oferecem suporte a números diferentes de discos anexados. Além disso, você também pode usar o armazenamento efêmero incluído com a VM para fornecer um limite para o armazenamento mais rápido de dados, apesar de haver implicações de resiliência e recuperação que você deve considerar (confira o documento Configuração, Teste e Análise de Resiliência e Recuperação do Elasticsearch para saber mais). As séries D, DS, Dv2 e GS de VMs usam SSDs para o armazenamento efêmero.
- O IOPS máximo por disco virtual. Discos comuns anexados criados por meio do armazenamento padrão são limitados a 500 IOPS por disco. Os SSDs anexados podem oferecer suporte a até 5 mil IOPS por disco. Para usar SSDs para discos anexados (que não seja armazenamento efêmero), você deve criar uma VM que dê suporte ao armazenamento premium (máquinas da série DS ou da série GS).

Você pode considerar o uso de Conjuntos de Escala do Azure para iniciar e parar VMs, conforme exigido pela demanda (confira [Dimensionamento automático de computadores em um Conjunto de Escala de Máquinas Virtuais][] para obter detalhes). No entanto, talvez essa abordagem não seja adequada para um cluster Elasticsearch pelos seguintes motivos:

- Essa abordagem é mais adequada para VMs sem estado. Sempre que você adiciona ou remove um nó de um cluster Elasticsearch, os fragmentos são realocados para balancear a carga e esse processo pode gerar volumes consideráveis de tráfego de rede e de E/S de disco e pode afetar gravemente as taxas de ingestão de dados. Você deve avaliar se essa sobrecarga vale o benefício do processamento adicional e dos recursos de memória que ficam disponíveis por meio da inicialização dinâmica de mais VMs.
- A inicialização da VM não acontece instantaneamente e pode demorar vários minutos antes de VMs adicionais ficarem disponíveis ou serem encerradas. O dimensionamento dessa maneira deve ser usado apenas para lidar com mudanças constantes na demanda.
- Após a expansão, você precisa considerar a redução? A remoção de uma VM de um cluster Elasticsearch pode ser um processo com uso intenso de recursos, exigindo que o Elasticsearch recupere os fragmentos e as réplicas localizados nessa VM e os recrie em um ou mais dos nós restantes. A remoção de várias VMs ao mesmo tempo pode comprometer a integridade do cluster, dificultando a recuperação. Além disso, muitas implementações do Elasticsearch crescem com o tempo, mas a natureza desses dados é não reduzir em volume. É possível excluir os documentos manualmente e configurá-los com uma TTL (vida útil). Após essa TTL, eles expiram e são removidos, mas na maioria dos casos, provavelmente o espaço previamente alocado será reutilizado rapidamente por documentos novos ou modificados. A fragmentação dentro de um índice pode ocorrer quando os documentos são removidos ou alterados e, nesse caso, você pode usar a API de [Otimização][] de HTTP do Elasticsearch (Elasticsearch 2.0.0 e anteriores) ou a API de [Mesclagem Forçada][] (Elasticsearch 2.1.0 e posteriores) para executar a desfragmentação.

> [AZURE.NOTE] A otimização é uma operação muito dispendiosa e você não deve executá-la em um índice altamente ativo. No entanto, convém otimizar um índice inativo, pois isso ajudará a reduzir os recursos necessários para a execução da pesquisa.

### Determinação do Número de Fragmentos de um Índice

O número de nós em um cluster pode variar ao longo do tempo, mas o número de fragmentos em um índice é fixo após a criação do índice. Adicionar ou remover fragmentos exige a reindexação dos dados, um processo de criação de um novo índice com o número necessário de fragmentos e, em seguida, a cópia dos dados do índice antigo para o novo (você pode usar aliases para não alarmar os usuários sobre a reindexação dos dados. Confira o documento Maximização da Agregação de Dados e do Desempenho de Consulta com o Elasticsearch no Azure para obter mais detalhes). Portanto, é importante determinar o número de fragmentos que você provavelmente vai precisar antes de criar o primeiro índice no cluster. Você pode executar as seguintes etapas para estabelecer esse número:

1. Criar um cluster de nó único usando a mesma configuração de hardware que você pretende implantar na produção.
2. Criar um índice que corresponda à estrutura que você pretende usar na produção. Dê a esse índice um único fragmento e nenhuma réplica.
3. Adicionar uma quantidade específica de dados de produção realistas ao índice.
4. Executar consultas típicas, agregações e outras cargas de trabalho no índice e medir o tempo de resposta e a taxa de transferência.
5. Se o tempo de resposta e a taxa de transferência estiverem dentro dos limites aceitáveis, repita o processo da etapa 3 (adicionar mais dados).
6. Quando parecer que você atingiu a capacidade do fragmento (os tempos de resposta e a taxa de transferência começarem a se tornar inaceitáveis), anote o volume de documentos.
7. Extrapole a capacidade de um único fragmento com o número previsto de documentos em produção para calcular o número necessário de fragmentos (você deve incluir alguma margem de erro nesses cálculos, pois a extrapolação não é uma ciência exata).

> [AZURE.NOTE] Lembre-se de que cada fragmento é implementado como um índice Lucene que consome memória, CPU e identificadores de arquivo. Quanto mais fragmentos você tiver, mais recursos desse tipo serão necessários.

Além disso, criar mais fragmentos pode aumentar a escalabilidade (dependendo de seu cenário e das cargas de trabalho) e pode aumentar a taxa de ingestão de dados, mas pode reduzir a eficiência de muitas consultas. Por padrão, uma consulta interrogará cada fragmento usado por um índice (você pode usar o [roteamento personalizado][] para modificar esse comportamento, se você souber em quais fragmentos os dados necessários estão localizados).

Esse processo só pode gerar uma estimativa do número de fragmentos e o volume de documentos esperado na produção de documentos talvez não seja conhecido. Nesse caso, você deve determinar o volume inicial (conforme indicado acima) e a taxa de crescimento prevista. Crie uma quantidade apropriada de fragmentos que possa lidar com o crescimento dos dados durante o período até você estar disposto a reindexar o banco de dados. Outras estratégias usadas para cenários como o gerenciamento de eventos e registro em log incluem o uso de índices pregressivos. Crie um novo índice para os dados ingeridos a cada dia e acesse esse índice por meio de um alias, que é trocado diariamente para apontar para o índice mais recente. Essa abordagem permite a fácil exclusão dos dados mais antigos (você pode excluir os índices com informações que já não são necessárias) e mantém o volume de dados em um nível gerenciável.

Tenha em mente que o número de nós não precisa corresponder ao número de fragmentos. Por exemplo, se você criar 50 fragmentos, poderá distribuí-los inicialmente em 10 nós e, em seguida, adicionar mais nós para expandir o sistema à medida que o volume de trabalho aumentar. No entanto, evite criar uma quantidade excepcionalmente grande de fragmentos em uma quantidade pequena de nós (1000 fragmentos distribuídos em dois nós, por exemplo). Embora o sistema possa, teoricamente, ser expandido para 1000 nós com essa configuração, a execução de 500 fragmentos em um único nó enfraquece o desempenho do nó.

> [AZURE.NOTE] Para sistemas com muita ingestão de dados, considere o uso de um número primo de fragmentos. O algoritmo padrão usado pelo Elasticsearch para o roteamento de documentos nos fragmentos produz uma distribuição mais uniforme nesse caso.

### Considerações de segurança

Por padrão, o Elasticsearch implementa uma segurança mínima e não fornece qualquer meio de autenticação e autorização. Esses aspectos exigem a configuração do sistema operacional e da rede subjacentes, além de usar plug-ins e utilitários de terceiros. Entre os exemplos estão o [Shield][] e o [Search Guard][].

> [AZURE.NOTE] O Shield é um plug-in fornecido pela Elastic para autenticação de usuário, criptografia de dados, controle de acesso baseado em função, filtragem de IP e auditoria. Talvez seja necessário configurar o sistema operacional subjacente para implementar outras medidas de segurança, como criptografia de disco.

Em um sistema de produção, considere como:

- Impedir o acesso não autorizado ao cluster.
- Identificar e autenticar usuários.
- Autorizar as operações que os usuários autenticados podem executar.
- Proteger o cluster contra invasores ou operações prejudiciais.
- Proteger os dados contra o acesso não autorizado.
- Atender aos requisitos normativos de segurança de dados comerciais (se for apropriado).

### Como Proteger o Acesso ao Cluster

O Elasticsearch é um serviço de rede. Os nós em um cluster Elasticsearch escutam as solicitações de cliente recebidas usando HTTP e se comunicam entre si usando um canal TCP. Você deve executar ações para impedir que clientes ou serviços não autorizados possam enviar solicitações pelos caminhos HTTP e TCP. Considere os itens a seguir:

- Defina grupos de segurança de rede para limitar o tráfego de rede de entrada e saída para uma rede virtual ou VM apenas para portas específicas.
- Altere as portas padrão usadas para acesso à Web do cliente (9200) e acesso à rede através de programação (9300). Use um firewall para proteger cada nó contra o tráfego mal-intencionado na Internet.
- Dependendo do local e da conectividade dos clientes, coloque o cluster em uma sub-rede privada sem acesso direto à Internet. Se o cluster precisar ficar exposto fora da sub-rede, encaminhe todas as solicitações por um servidor bastião ou um proxy suficientemente blindado para proteger o cluster.
- Se você precisar fornecer acesso direto aos nós, use o plug-in Jetty do Elasticsearch para fornecer conectividade SSL, autenticação e log de conexão. Como alternativa, configure um servidor proxy nginx e configure a autenticação HTTPS.

> [AZURE.NOTE] Com um servidor proxy, por exemplo, o nginx, também é possível restringir o acesso à funcionalidade. Por exemplo, você pode configurar o nginx para permitir solicitações para o ponto de extremidade \_search somente se você precisar impedir que os clientes executem outras operações.

- Se você precisar de uma segurança de acesso de rede mais abrangente, use os plug-ins Shield ou Search Guard.

### Identificação e Autenticação de Usuários

Todas as solicitações feitas pelos clientes para o cluster devem ser autenticadas. Além disso, você deve impedir que nós não autorizados ingressem no cluster, pois eles podem fornecer uma porta dos fundos para o sistema que ignora a autenticação.

Há plug-ins disponíveis do Elasticsearch que podem executar tipos de autenticação diferentes, incluindo:
- autenticação HTTP básica. Os nomes de usuário e senhas estão incluídos em cada um deles. Todas as solicitações devem ser criptografadas usando SSL/TLS ou um nível equivalente de proteção.
- Integração LDAP e do Active Directory. Essa abordagem exige que os clientes recebam funções em grupos de LDAP ou AD.
- Autenticação nativa usando identidades definidas dentro do próprio cluster Elasticsearch.
- Autenticação TLS dentro de um cluster para autenticar todos os nós.
- Filtragem de IP, para impedir que clientes de sub-redes não autorizadas se conectem e também para impedir que os nós dessas sub-redes ingressem no cluster.

### Autorização de Solicitações de Clientes

A autorização depende do plug-in do Elasticsearch usado para fornecer esse serviço. Por exemplo, um plug-in que fornece Autenticação Básica normalmente fornece recursos que definem o nível de autenticação, enquanto um plug-in que usa LDAP ou AD normalmente associará clientes a funções e, então, atribuirá direitos de acesso a essas funções. Ao usar qualquer plug-in, você deve considerar o seguinte:
- você precisa restringir as operações que um cliente pode executar? Por exemplo, um cliente deve ser capaz de monitorar o status do cluster ou criar e excluir índices?
- O cliente deve ficar restrito a índices específicos? Isso é útil em uma situação com vários locatários, na qual os locatários podem receber seu próprio conjunto específico de índices e esses índices não devem estar acessíveis aos demais locatários.
- O cliente deve conseguir ler e gravar dados em um índice? Um cliente pode ser capaz de realizar pesquisas que recuperam dados usando um índice, mas devem ser impedidos de adicionar ou excluir dados desse índice, por exemplo.

Atualmente, a maioria dos plug-ins de segurança definem o escopo de suas operações para o nível do índice ou do cluster e não para subconjuntos de documentos dentro dos índices. Isso ocorre por motivos de eficiência. Portanto, não é fácil limitar as solicitações para documentos específicos dentro de um único índice. Se você precisar desse nível de granularidade, salve os documentos em índices separados e use aliases que agrupam os índices. Por exemplo, em um sistema de funcionários, se o usuário A precisar acessar todos os documentos que contenham informações sobre os funcionários do departamento X, o usuário B precisar acessar todos os documentos que contenham informações sobre os funcionários do departamento Y e o usuário C precisar acessar todos os documentos que contenham informações sobre os funcionários nos dois departamentos, crie dois índices (para o departamento X e Y) e um alias que consulte ambos os índices. Conceda acesso de leitura ao usuário A para o primeiro índice, conceda acesso de leitura ao usuário B para o segundo índice e conceda acesso de leitura ao usuário C para os dois índices por meio do alias. Para saber mais, confira [Faking Index per User with Aliases][].

### Proteção do Cluster

O cluster pode ficar vulnerável ao uso indevido se não estiver cuidadosamente protegido, conforme descrito pelos seguintes pontos:

- Desabilite scripts de consulta dinâmica nas consultas do Elasticsearch, pois eles podem gerar vulnerabilidades de segurança. Use scripts nativos em vez de scripts de consulta. Um script nativo é um plug-in do Elasticsearch gravado em Java e compilado em um arquivo JAR.

> [AZURE.NOTE] O script de consulta dinâmica está desativado por padrão. Não habilite-o, a menos que você tenha um motivo muito bom para fazer isso.

- Evite a exposição de pesquisas de cadeia de caracteres de consulta aos usuários. A pesquisa de cadeia de caracteres de consulta permite que os usuários executem consultas que consomem muitos recursos sem qualquer restrição. Essas pesquisas podem afetar gravemente o desempenho do cluster e abrir o sistema para ataques de DOS. Além disso, a pesquisa de cadeia de caracteres de consulta pode expor informações possivelmente particulares.
- Impeça que as operações consumam muita memória, pois isso pode causar exceções de falta de memória, resultando na falha do Elasticsearch em um nó. Operações longas e com uso excessivo de recursos também podem ser usadas para implementar ataques de DOS. Os exemplos incluem:
- Solicitações de pesquisa que tentam carregar campos muito grandes na memória (se uma consulta classificar, gerar scripts ou facetas nesses campos).
  
> [AZURE.NOTE] O uso de [Doc Values][] pode reduzir os requisitos de memória dos índices salvando dados de campo no disco em vez de carregá-los na memória. Isso pode ajudar a reduzir as chances de esgotamento da memória em um nó, mas com uma redução na velocidade.

- Pesquisas que consultam vários índices ao mesmo tempo.
- Pesquisas que recuperam uma grande quantidade de campos. Essas pesquisas podem esgotar a memória, causando o armazenamento em cache de uma grande quantidade de dados de campo. Por padrão, o cache de dados do campo tem um tamanho ilimitado, mas você pode definir as propriedades [indices.fielddata.cache.*](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-fielddata.html) no arquivo de configuração elasticsearch.yml para limitar os recursos disponíveis. Você também pode configurar o [disjuntor de dados de campo][] para ajudar a impedir que os dados armazenados em cache de um único campo esgotem a memória e que o [disjuntor de solicitação][] impeça que consultas individuais monopolizem a memória. O custo da configuração desses parâmetros é o aumento da probabilidade de algumas consultas falharem ou atingirem o tempo limite.
  
> [AZURE.NOTE] O Elasticsearch sempre pressupõe que há memória suficiente para executar sua carga de trabalho atual. Se esse não for o caso, o serviço do Elasticsearch poderá falhar. O Elasticsearch fornece pontos de extremidade que retornam informações sobre o uso dos recursos (as [APIs cat][] de HTTP) e você deve monitorar essas informações cuidadosamente.

- Aguardar muito tempo para liberar um segmento da memória em andamento. Isso pode esgotar o espaço de buffer na memória. Se for necessário, [configure o translog][] para reduzir os limites de liberação de dados no disco.

- Criar índices com grandes quantidades de metadados. Um índice que contém documentos com uma grande variação nos nomes de campo pode consumir muita memória. Para saber mais, confira [Mapping Explosion][].
  
> [AZURE.NOTE] A definição de uma operação de longa duração ou com uso intenso de consultas depende muito do cenário. A carga de trabalho normalmente esperada por um cluster pode ter um perfil completamente diferente da carga de trabalho em outro cluster. Determinar quais operações são inaceitáveis exige pesquisa e testes consideráveis de seus aplicativos.

Seja proativo e detecte e interrompa as atividades mal-intencionadas antes que elas causem danos consideráveis ou perda de dados. Considere o uso de um sistema de monitoramento de segurança e notificação que possa detectar rapidamente padrões incomuns de acesso aos dados e emita alertas quando, por exemplo, ocorrer falha nas solicitações de logon do usuário, nós inesperados ingressarem ou saírem do cluster ou quando as operações demorarem mais do que o esperado. O Elasticearch [Watcher][] está entre as ferramentas que podem realizar essas tarefas.

### Proteção dos Dados

Você pode proteger os dados dinamicamente usando SSL/TLS, mas o Elasticsearch não fornece qualquer forma interna de criptografia de dados para as informações armazenadas no disco. Lembre-se de que essas informações são mantidas em arquivos de disco comuns e qualquer usuário com acesso a esses arquivos pode comprometer os dados que eles contêm, por exemplo, copiando-os para seu próprio cluster. Considere os seguintes pontos:
- proteger os arquivos usados pelo Elasticsearch para armazenar os dados. Não permita acesso aleatório de leitura ou gravação a identidades que não sejam do serviço do Elasticsearch.

- Criptografe os dados armazenados nesses arquivos usando um sistema de arquivos com criptografia.

> [AZURE.NOTE] Agora, o Azure oferece suporte à criptografia de disco para VMs com Linux e Windows. Para saber mais, confira [Azure Disk Encryption para VMs IaaS com Windows e Linux, Preview][]

### Como Atender aos Requisitos Normativos

Os requisitos normativos preocupam-se principalmente com operações de auditoria com o objetivo de manter um histórico de eventos e assegurar a privacidade dessas operações para impedir que elas sejam monitoradas (e reproduzidas) por uma agência externa. Particularmente, você deve considerar como:

- Rastrear todas as solicitações (bem-sucedidas ou não) e todas as tentativas de acessar o sistema.
- Criptografar as comunicações feitas pelos clientes para o cluster, bem como as comunicações entre os nós executadas pelo cluster. Você deve implementar SSL/TLS para todas as comunicações do cluster. O Elasticsearch também oferece suporte às codificações conectáveis, caso sua organização tenha requisitos diferentes dos que estão disponíveis por meio de SSL/TLS.
- Armazene todos os dados de auditoria com segurança. O volume de informações de auditoria pode crescer rapidamente e deve ser protegido de forma sólida para impedir a adulteração das informações de auditoria.
- Arquive os dados de auditoria com segurança.

### Considerações sobre o Monitoramento

O monitoramento é importante tanto no nível do sistema operacional quanto no nível do Elasticsearch.

Você pode executar o monitoramento no nível de sistema operacional usando ferramentas específicas ao sistema operacional. No Windows, isso inclui itens como o Monitor de Desempenho com os contadores de desempenho adequados, enquanto no Linux você pode usar ferramentas como *vmstat*, *iostat* e *top*. Os principais itens a serem monitorados no nível do sistema operacional incluem a utilização da CPU, volumes de E/S de disco, tempos de espera de E/S do disco, tráfego de rede. Em um cluster Elasticsearch bem ajustado, a utilização da CPU pelo processo do Elasticsearch deve ser alta e os tempos de espera de E/S do disco devem ser mínimos.

No nível do software, você deve monitorar a taxa de transferência e os tempos de resposta das solicitações, junto com os detalhes das solicitações que falharam. O Elasticsearch fornece uma série de APIs que podem ser usadas para examinar o desempenho de diferentes aspectos de um cluster. As duas APIs mais importantes são *\_cluster/health* e *\_nodes/stats*. A API *\_cluster/health* pode ser usada para fornecer um instantâneo da integridade geral do cluster, além de fornecer informações detalhadas para cada índice, conforme mostrado no exemplo a seguir:

`GET _cluster/health?level=indices`

O exemplo de saída abaixo foi gerado usando essa API:

```json
{
    "cluster_name": "elasticsearch",
    "status": "green",
    "timed_out": false,
    "number_of_nodes": 6,
    "number_of_data_nodes": 3,
    "active_primary_shards": 10,
    "active_shards": 20,
    "relocating_shards": 0,
    "initializing_shards": 0,
    "unassigned_shards": 0,
    "delayed_unassigned_shards": 0,
    "number_of_pending_tasks": 0,
    "number_of_in_flight_fetch": 0,
    "indices": {
        "systwo": {
            "status": "green",
            "number_of_shards": 5,
            "number_of_replicas": 1,
            "active_primary_shards": 5,
            "active_shards": 10,
            "relocating_shards": 0,
            "initializing_shards": 0,
            "unassigned_shards": 0
        },
        "sysfour": {
            "status": "green",
            "number_of_shards": 5,
            "number_of_replicas": 1,
            "active_primary_shards": 5,
            "active_shards": 10,
            "relocating_shards": 0,
            "initializing_shards": 0,
            "unassigned_shards": 0
        }
    }
}
```

Esse cluster contém dois índices nomeados *systwo* e *sysfour*. As principais estatísticas a serem monitoradas para cada índice são status, active\_shards e unassigned\_shards. O status deve estar verde, o número de active\_shards deve refletir o number\_of\_shards e o number\_of\_replicas e unassigned\_shards deve ser zero. Se o status for "vermelho", parte do índice estará ausente ou corrompido. Você pode verificar isso se a configuração de *active\_shards* for menor do que *number\_of\_shards* - (*number\_of\_replicas* + 1) e unassigned\_shards for diferente de zero. Observe que um status amarelo indica que um índice está em estado de transição, como resultado da adição de mais réplicas ou realocação de fragmentos. O status deve mudar para verde quando a transição for concluída. Se ele permanecer amarelo por um período muito longo ou mudar para vermelho, você deve verificar se algum evento significativo de E/S (como uma falha do disco ou da rede) ocorreu no nível do sistema operacional.
A API \_nodes/stats emite informações abrangentes sobre cada nó do cluster:

`GET _nodes/stats`

A saída gerada inclui detalhes sobre como os índices são armazenados em cada nó (incluindo os tamanhos e a quantidade de documentos), o tempo gasto ao executar a indexação, consultas, pesquisas e mesclagem, colocação em cache, sistema operacional e informações do processo, estatísticas sobre a JVM (incluindo o desempenho da coleta de lixo) e pools de threads. Para saber mais, confira [Monitoring Individual Nodes][].

Se uma parte considerável das solicitações do Elasticsearch estiver falhando com mensagens de erro *EsRejectedExecutionException*, significa que o Elasticsearch não está conseguindo acompanhar o trabalho que está sendo enviado. Nessa situação, você precisa identificar o afunilamento que está causando o atraso do Elasticsearch. Considere os itens a seguir:

- Se o afunilamento ocorrer devido a uma limitação de recursos, como uma quantidade insuficiente de memória alocada à JVM, causando um número excessivo de coletas de lixo, considere a alocação de recursos adicionais (nesse caso, configure a JVM para usar mais memória, até 50% do armazenamento disponível no nó. Confira [Requisitos de Memória][]).
- Se o cluster mostrar tempos de espera de E/S muito longos e as estatísticas de mesclagem coletadas para um índice usando a API de \_node/stats contiver valores altos, o índice terá uma atividade de gravação intensa. Examine os pontos levantados na seção [Otimização de Recursos para Operações de Indexação] (Otimização de Recursos para Operações de Indexação) para ajustar o desempenho da indexação.
- Limite os aplicativos cliente que estão executando operações de ingestão de dados e determinar o efeito que isso tem sobre o desempenho. Se essa abordagem mostrar um aprimoramento considerável, considere a manutenção da limitação ou expanda distribuindo a carga dos índices com atividade de gravação intensa entre outros nós. Para saber mais, confira o documento Maximizar o desempenho da ingestão de dados com o Elasticsearch no Azure.
- Se as estatísticas de pesquisa de um índice indicarem que as consultas estão demorando muito tempo, considere o modo como as consultas são otimizadas. Para saber mais, confira a seção [Ajuste de consulta][]. Observe que você pode usar os valores *query\_time\_in\_millis* e *query\_total* relatados pelas estatísticas da pesquisa para calcular um valor aproximado da eficiência de consulta. A equação *query\_time\_in\_millis* / *query\_total* proporcionará a você um tempo médio para cada consulta.

### Ferramentas para Monitoramento do Elasticsearch

Há várias ferramentas disponíveis para realizar o monitoramento diário do Elasticsearch em produção. Essas ferramentas geralmente usam as APIs subjacentes do Elasticsearch para coletar informações e apresentar os detalhes de uma maneira que seja mais fácil de observar do que os dados brutos. Entre os exemplos comuns estão [Elasticsearch-Head][], [Bigdesk][], [Kopf][] e [Marvel][].

Elasticsearch-Head, Bigdesk e Kopf são executados como plug-ins do software Elasticsearch. As versões mais recentes do Marvel podem ser executadas de forma independente, mas exigem o [Kibana][] para fornecer um ambiente de hospedagem e captura de dados. A vantagem de usar o Marvel com o Kibana é que você pode implementar o monitoramento em um ambiente separado do cluster Elasticsearch, permitindo a exploração de problemas com o Elasticsearch de uma forma que talvez não fosse possível se as ferramentas de monitoramento fossem executadas como parte do software Elasticsearch. Por exemplo, se o Elasticsearch falhar repetidamente ou estiver muito lento, as ferramentas executadas como plug-ins do Elasticsearch também serão afetadas, dificultando ainda mais o monitoramento e o diagnóstico.

No nível do sistema operacional, você pode usar ferramentas como o recurso de Análise de Log do [Pacote de Gerenciamento de Operações do Azure][] ou os [Diagnósticos do Azure com o Portal do Azure][] para capturar dados de desempenho de VMs que hospedam nós Elasticsearch. Outra abordagem é usar o [Logstash][] para capturar dados de desempenho e de log, armazenar essas informações em um cluster Elasticsearch separado (não use o mesmo cluster que você está usando para seu aplicativo) e, em seguida, usar o Kibana para visualizar os dados. Para saber mais, confira [Diagnóstico do Microsoft Azure com ELK][].

### Ferramentas para Teste de Desempenho do Elasticsearch

Outras ferramentas estão disponíveis se você estiver realizando benchmarking do Elasticsearch ou submetendo um cluster a testes de desempenho. Essas ferramentas devem ser usadas em um ambiente de desenvolvimento ou de teste e não em produção. Um exemplo frequentemente usado é do [Apache JMeter][].

O JMeter foi usado para realizar o benchmarking e outros testes de carga descritos em documentos relacionados a este guia. O documento Execução de Testes de Desempenho no Elasticsearch Usando JMeter descreve detalhadamente como o JMeter foi configurado e usado.

## Próximas etapas
- [Elasticsearch: o Guia Definitivo](https://www.elastic.co/guide/en/elasticsearch/guide/master/index.html)

[Apache JMeter]: http://jmeter.apache.org/
[Apache Lucene]: https://lucene.apache.org/
[Dimensionamento automático de computadores em um Conjunto de Escala de Máquinas Virtuais]: virtual-machines-vmss-walkthrough/
[Azure Disk Encryption para VMs IaaS com Windows e Linux, Preview]: azure-security-disk-encryption/
[Balanceador de Carga do Azure]: load-balancer-overview/
[Rota Expressa]: expressroute-introduction/
[balanceador de carga interno]: load-balancer-internal-overview/
[Tamanhos de Máquinas Virtuais]: virtual-machines-size-specs/

[Requisitos de Memória]: #memory-requirements
[Requisitos de Rede]: #network-requirements
[Descoberta de Nós]: #node-discovery
[Ajuste de consulta]: #query-tuning

[A Highly Available Cloud Storage Service with Strong Consistency]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx
[Plug-in de Nuvem do Azure]: https://www.elastic.co/blog/azure-cloud-plugin-for-elasticsearch
[Diagnósticos do Azure com o Portal do Azure]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[Pacote de Gerenciamento de Operações do Azure]: https://www.microsoft.com/server-cloud/operations-management-suite/overview.aspx
[Modelos de Início Rápido do Azure]: https://azure.microsoft.com/documentation/templates/
[Bigdesk]: http://bigdesk.org/
[APIs cat]: https://www.elastic.co/guide/en/elasticsearch/reference/1.7/cat.html
[configure o translog]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-translog.html
[roteamento personalizado]: https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-routing-field.html
[Doc Values]: https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html
[Elasticsearch]: https://www.elastic.co/products/elasticsearch
[Elasticsearch-Head]: https://mobz.github.io/elasticsearch-head/
[Elasticsearch.Net & NEST]: http://nest.azurewebsites.net/
[módulo de Instantâneo e de Restauração do Elasticsearch]: https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html
[Faking Index per User with Aliases]: https://www.elastic.co/guide/en/elasticsearch/guide/current/faking-it.html
[disjuntor de dados de campo]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-fielddata.html#fielddata-circuit-breaker
[Mesclagem Forçada]: https://www.elastic.co/guide/en/elasticsearch/reference/2.1/indices-forcemerge.html
[gossiping]: https://en.wikipedia.org/wiki/Gossip_protocol
[Kibana]: https://www.elastic.co/downloads/kibana
[Kopf]: https://github.com/lmenezes/elasticsearch-kopf
[Logstash]: https://www.elastic.co/products/logstash
[Mapping Explosion]: https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion
[Marvel]: https://www.elastic.co/products/marvel
[Diagnóstico do Microsoft Azure com ELK]: https://github.com/mspnp/semantic-logging/tree/elk
[Monitoring Individual Nodes]: https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_monitoring_individual_nodes
[nginx]: http://nginx.org/en/
[API de Cliente do Nó]: https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/node-client.html
[Otimização]: https://www.elastic.co/guide/en/elasticsearch/reference/1.7/indices-optimize.html
[Plug-in de Alterações PubNub]: http://www.pubnub.com/blog/quick-start-realtime-geo-replication-for-elasticsearch/
[disjuntor de solicitação]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-fielddata.html#request-circuit-breaker
[Search Guard]: https://github.com/floragunncom/search-guard
[Shield]: https://www.elastic.co/products/shield
[API de Cliente de Transporte]: https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/transport-client.html
[nós de tribo]: https://www.elastic.co/blog/tribe-node
[Watcher]: https://www.elastic.co/products/watcher
[Zen]: https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html

<!---HONumber=AcomDC_0211_2016-->
