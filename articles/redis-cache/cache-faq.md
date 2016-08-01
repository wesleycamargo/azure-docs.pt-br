<properties 
	pageTitle="Perguntas Frequentes Sobre o Cache Redis do Azure | Microsoft Azure" 
	description="Conheça as respostas a perguntas, padrões e práticas recomendadas comuns do Cache Redis do Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/20/2016" 
	ms.author="sdanie"/>

# Perguntas frequentes sobre Cache Redis do Azure

Conheça as respostas a perguntas, padrões e práticas recomendadas comuns do Cache Redis do Azure.


## E se dúvida não foi respondida aqui?

Se sua pergunta não estiver listada aqui, fale conosco e nós o ajudaremos a encontrar uma resposta.

-	Você pode postar uma pergunta no [tópico de discussão](#comments) no final do FAQ e entrar em contato com a equipe do Cache do Azure e outros membros da comunidade sobre este artigo.
-	Você pode postar uma pergunta sobre o [Fórum do MSDN do Cache do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) e entrar em contato com a equipe do Cache do Azure e outros membros da comunidade.
-	Você pode enviar um email para nós em [comentários externos sobre o Cache do Azure](mailto:azurecache@microsoft.com).

## Perguntas frequentes sobre planejamento

-	[Qual oferta e tamanho de Cache Redis devo usar?](#what-redis-cache-offering-and-size-should-i-use)
-	[Desempenho do Cache Redis do Azure](#azure-redis-cache-performance)
-	[Em que região posso localizar meu cache?](#in-what-region-should-i-locate-my-cache)
-	[Como eu sou cobrado pelo Cache Redis do Azure?](#how-am-i-billed-for-azure-redis-cache)

<a name="cache-size"></a>
### Qual oferta e tamanho de Cache Redis devo usar?
Cada oferta de Cache Redis do Azure fornece diferentes níveis de opções **tamanho**, **largura de banda**, **alta disponibilidade** e **SLA**.

Veja abaixo as considerações para a escolha de uma oferta de Cache.

-	**Memória**: as camadas Básica e Standard oferecem 250 MB – 53 GB. A camada Premium oferece até 530 GB com mais espaço disponível [mediante solicitação](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Para obter mais informações, veja [Preço do Cache Redis do Azure](https://azure.microsoft.com/pricing/details/cache/).
-	**Desempenho de rede**: se você tiver uma carga de trabalho que requer uma alta taxa de transferência, a camada Premium oferece mais largura de banda quando comparada às camadas Standard ou Básica. Também dentro de cada camada, caches de tamanhos maiores têm mais largura de banda, devido à VM subjacente que hospeda o cache. Veja a [tabela a seguir](#cache-performance) para saber mais.
-	**Taxa de transferência**: a camada Premium oferece a maior taxa de transferência disponível. Se o cliente ou servidor de cache atingir os limites de largura de banda, você receberá tempos limite no lado do cliente. Veja a tabela a seguir para obter mais informações.
-	**Alta Disponibilidade/SLA**: o Cache Redis do Azure garante que um cache Standard/Premium esteja disponível durante, pelo menos, 99,9% do tempo. Para saber mais sobre nosso SLA, confira [Preço do Cache Redis do Azure](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). O SLA abrange apenas a conectividade com os pontos de extremidade do Cache. O SLA não abrange a proteção contra perda de dados. Recomendamos usar o recurso de persistência de dados do Redis na camada Premium para aumentar a resiliência contra a perda de dados.
-	**Persistência de dados do Redis**: a camada Premium permite persistir os dados de cache em uma conta do Armazenamento do Azure. Em um cache Básico/Standard, todos os dados são armazenados apenas na memória. Em caso de problemas de infraestrutura subjacente, pode haver uma possível perda de dados. Recomendamos usar o recurso de persistência de dados do Redis na camada Premium para aumentar a resiliência contra a perda de dados. O Cache Redis do Azure oferece opções de RDB e AOF (em breve) na persistência do Redis. Para saber mais, confira [Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md).
-	**Cluster Redis**: se desejar criar caches maiores que 53 GB ou fragmentar dados entre vários nós do Redis, você poderá usar o clustering do Redis que está disponível na camada Premium. Cada nó consiste em um par de cache primário/de réplica para alta disponibilidade. Para saber mais, confira [Como configurar o clustering para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md).
-	**Isolamento de rede e segurança avançados**: a implantação de VNET (Rede Virtual) do Azure fornece isolamento e segurança avançados para o Cache Redis do Azure, bem como sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso. Para saber mais,confira [Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium](cache-how-to-premium-vnet.md).
-	**Configurar o Redis**: nas camadas Standard e Premium, você pode configurar o Redis para notificações de Keyspace.
-	**Número máximo de conexões de cliente**: a camada Premium oferece o número máximo de clientes que podem se conectar ao Redis, com um número maior de conexões para caches de tamanhos maiores. [Confira a página de preço para obter detalhes](https://azure.microsoft.com/pricing/details/cache/).
-	**Núcleo dedicado para o servidor do Redis**: na camada Premium, todos os tamanhos de cache têm um núcleo dedicado para o Redis. Nas camadas Básica/Standard, o tamanho C1 e acima têm um núcleo dedicado para o servidor do Redis.
-	**Como o Redis usa thread único**, ter mais de dois núcleos não oferece um benefício adicional em relação a ter apenas dois, mas tamanhos maiores de VM normalmente têm mais largura de banda que tamanhos menores. Se o cliente ou servidor de cache atingir os limites de largura de banda, você receberá tempos limite no lado do cliente.
-	**Melhorias de desempenho**: os caches na camada Premium são implantados no hardware que tem processadores mais rápidos e que oferece um melhor desempenho quando comparado às camadas Básica ou Standard. Os Caches da camada Premium têm a taxa de transferência mais alta e as latências mais baixas.

<a name="cache-performance"></a>
### Desempenho do Cache Redis do Azure

A tabela a seguir mostra os valores de largura de banda máxima observados durante o teste de vários tamanhos de caches Standard e Premium usando `redis-benchmark.exe` de uma VM de Iaas no ponto de extremidade do Cache Redis do Azure. É importante observar que esses valores não são garantidos e que não há nenhum SLA para esses números, mas eles devem ser típicos. Você deve realizar teste de carga em seu próprio aplicativo para determinar o tamanho de cache certo para ele.

Podemos tirar as seguintes conclusões desta tabela.

-	A taxa de transferência para o Cache do mesmo tamanho é maior na camada Premium quando comparada à camada Standard. Por exemplo, para um Cache de 6 GB, a taxa de transferência de P1 será 140 mil RPS, em comparação com 49 mil para C3.
-	Com o cluster Redis, a taxa de transferência aumenta linearmente à medida que o número de fragmentos (nós) no cluster aumenta. Por exemplo, se você criar um cluster P4 de 10 fragmentos, a taxa de transferência disponível será de 250 mil *10 = 2,5 milhões de RPS.
-	A taxa de transferência tamanhos de chave maiores é mais alta na camada Premium quando comparada à camada Standard.

| Camada de preços | Tamanho | Núcleos de CPU | Largura de banda disponível | Tamanho de chave de 1 KB |
|--------------------------|--------|-----------|--------------------------------------------------------|------------------------------------------|
| **Tamanhos de cache padrão** | | | **Megabits por segundo (Mb/s) / Megabytes por segundo (MB/s)** | **RPS (solicitações por segundo)** |
| C0 | 250 MB | Compartilhado | 5 / 0,625 | 600 |
| C1 | 1 GB | 1 | 100 / 12,5 | 12\.200 |
| C2 | 2,5 GB | 2 | 200 / 25 | 24\.000 |
| C3 | 6 GB | 4 | 400 / 50 | 49\.000 |
| C4 | 13 GB | 2 | 500 / 62,5 | 61\.000 |
| C5 | 26 GB | 4 | 1000 / 125 | 115\.000 |
| C6 | 53 GB | 8 | 2000 / 250 | 150\.000 |
| **Tamanhos de cache Premium** | | **Núcleos de CPU por fragmento** | | **RPS (solicitações por segundo) por fragmento** |
| P1 | 6 GB | 2 | 1000 / 125 | 140\.000 |
| P2 | 13 GB | 4 | 2000 / 250 | 220\.000 |
| P3 | 26 GB | 4 | 2000 / 250 | 220\.000 |
| P4 | 53 GB | 8 | 4000 / 500 | 250\.000 |


Para obter instruções sobre como baixar as ferramentas do Redis como `redis-benchmark.exe`, consulte a seção [Como posso executar comandos do Redis?](#cache-commands)

<a name="cache-region"></a>
### Em que região posso localizar meu cache?

Para melhor desempenho e menor latência, localize o Cache Redis do Azure na mesma região que o aplicativo cliente de cache.

<a name="cache-billing"></a>
### Como eu sou cobrado pelo Cache Redis do Azure?

O preço do Cache Redis do Azure pode ser encontrado [aqui](https://azure.microsoft.com/pricing/details/cache/). A página de preços lista os preços com uma taxa horária. Os caches são cobrados em uma base por minuto do momento em que o cache é criado até o momento em que um cache é excluído. Não há nenhuma opção para parar ou pausar a cobrança de um cache.

## Perguntas frequentes sobre desenvolvimento

-	[O que as opções de configuração do StackExchange.Redis fazem?](#what-do-the-stackexchangeredis-configuration-options-do)
-	[Quais clientes do cache Redis eu posso usar?](#what-redis-cache-clients-can-i-use)
-	[Há um emulador local para o Cache Redis do Azure?](#is-there-a-local-emulator-for-azure-redis-cache)
-	[Como posso executar comandos do Redis?](#how-can-i-run-redis-commands)
-	[Por que o Cache Redis do Azure não tem uma referência da biblioteca de classes MSDN, assim como alguns dos outros serviços do Azure?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)

<a name="cache-configuration"></a>
### O que as opções de configuração do StackExchange.Redis fazem?

O StackExchange.Redis tem muitas opções. Esta seção fala sobre algumas das configurações comuns. Para obter mais informações sobre opções do StackExchange.Redis, consulte [configuração do StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md).

ConfigurationOptions|Descrição|Recomendações
---|---|---
AbortOnConnectFail|Quando definido como true, a conexão não reconectará após uma falha de rede.|Defina como false e deixe o StackExchange.Redis reconectar-se automaticamente.
ConnectRetry|O número de vezes para repetir tentativas de conexão durante a conexão inicial.| Veja orientações abaixo. |
ConnectTimeout|Tempo limite em ms para operações de conexão.| Veja orientações abaixo. |

Na maioria dos casos, os valores padrão do cliente são suficientes. Você pode realizar o ajuste fino das opções com base na sua carga de trabalho.

-	**Novas tentativas**
	-	Para ConnectRetry e ConnectTimeout, a orientação geral é falhar no modo rápido e tentar novamente. Isso se baseia na sua carga de trabalho e em quanto tempo em média leva para o cliente emitir um comando Redis e receber uma resposta.
	-	Deixe o StackExchange.Redis reconectar-se automaticamente, em vez de você verificar o status de conexão e realizar a reconexão. **Evitar usar a propriedade ConnectionMultiplexer.IsConnected**.
	-	Bola de neve - às vezes, você poderá ter um problema em que você esteja realizando novas tentativas e isso se torne uma bola de neve, sem nunca se recuperar. Nesse caso você deve considerar usar um algoritmo de nova tentativa de retirada exponencial, conforme descrito em [Orientação geral sobre novas tentativas](best-practices-retry-general.md) publicado pelo grupo de Padrões e práticas da Microsoft.
-	**Valores de tempo limite**
	-	Considere sua carga de trabalho e defina os valores adequadamente. Se você estiver armazenando valores grandes, defina o tempo limite como um valor mais alto.
	-	Defina `AbortOnConnectFail` como false e deixe o StackExchange.Redis reconectar-se para você.
	-	Use uma única instância de ConnectionMultiplexer para o aplicativo. Você pode usar uma LazyConnection para criar uma única instância que é retornada por uma propriedade Connection, conforme mostrado em [Conectar-se ao cache usando a classe ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
	-	Defina a propriedade `ConnectionMultiplexer.ClientName` como um nome exclusivo de instância de aplicativo para fins de diagnóstico.
	-	Usar várias instâncias de `ConnectionMultiplexer` para cargas de trabalho personalizadas.
	-	Você pode seguir este modelo se tem variação de carga em seu aplicativo. Por exemplo:
	-	Você pode ter um multiplexador para lidar com chaves grandes.
	-	Você pode ter um multiplexador para lidar com chaves pequenas.
	-	Você pode definir valores diferentes para o tempo limite da conexão e a lógica de repetição para cada ConnectionMultiplexer que você usa.
	-	Definir a propriedade `ClientName` em cada multiplexador para ajudar no diagnóstico.
	-	Isso levará a uma latência mais aperfeiçoada por `ConnectionMultiplexer`.

### Quais clientes do cache Redis eu posso usar?

Uma das coisas legais sobre o Redis é que há muitos clientes que dão suporte a várias linguagens de programação diferentes. Para obter uma lista atual dos clientes, consulte [Clientes Redis](http://redis.io/clients). Para ver tutoriais que abrangem várias linguagens e clientes diferentes, consulte [Como usar o Cache Redis do Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e clique na linguagem desejada do seletor de linguagens na parte superior do artigo.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>
### Há um emulador local para o Cache Redis do Azure?

Não há nenhum emulador local para o Cache Redis do Azure, mas é possível executar a versão MSOpenTech do redis-server.exe nas [ferramentas de linha de comando do Redis](https://github.com/MSOpenTech/redis/releases/) no computador local e conectá-lo para obter uma experiência semelhante ao emulador de cache local, conforme mostrado no exemplo a seguir.


	private static Lazy<ConnectionMultiplexer>
  		lazyConnection = new Lazy<ConnectionMultiplexer>
	    (() =>
	    {
		    // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
		    return ConnectionMultiplexer.Connect("127.0.0.1:6379");
	    });
	
	    public static ConnectionMultiplexer Connection
	    {
		    get
		    {
			    return lazyConnection.Value;
		    }
	    }


Se desejar, será possível configurar um arquivo [redis.conf](http://redis.io/topics/config) para corresponder melhor às [configurações de cache padrão](cache-configure.md#default-redis-server-configuration) do Cache Redis do Azure online.

<a name="cache-commands"></a>
### Como posso executar comandos do Redis?

Você pode usar qualquer um dos comandos listados em [Comandos do Redis](http://redis.io/commands#), exceto pelos comandos listados em [Comandos do Redis sem suporte no Cache Redis do Azure](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Para executar comandos Redis, há várias opções.

-	Se você tiver um cache Standard ou Premium, poderá executar comandos do Redis usando o [Console do Redis](cache-configure.md#redis-console). Isso permite executar comandos do Redis no Portal do Azure com segurança.
-	Também é possível usar as ferramentas de linha de comando do Redis. Para usá-las, você executará as seguintes etapas:
-	Baixe as [Ferramentas de linha de comando do Redis](https://github.com/MSOpenTech/redis/releases/).
-	Conecte-se ao cache usando `redis-cli.exe`. Passe no ponto de extremidade de cache usando que o comutador -h e a chave usando - a, como mostrado no exemplo a seguir.
-	`redis-cli -h <your cache="" name="">
.redis.cache.windows.net -a <key>
  `
  -	Observe que as ferramentas de linha de comando do Redis não funcionam com a porta SSL, mas você pode usar um utilitário como `stunnel` para conectar as ferramentas com segurança à porta SSL seguindo as instruções na postagem de blog [Anunciando o provedor de estado de sessão ASP.NET para versão de visualização do Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

<a name="cache-reference"></a>
### Por que o Cache Redis do Azure não tem uma referência da biblioteca de classes MSDN, assim como alguns dos outros serviços do Azure?

O Cache Redis do Microsoft Azure baseia-se no popular software livre Cache Redis e pode ser acessado por uma ampla variedade de [clientes Redis](http://redis.io/clients) que estão disponíveis para várias linguagens de programação. Cada cliente tem sua própria API, que faz chamadas para a instância de cache Redis usando [comandos do Redis](http://redis.io/commands).

Como cada cliente é diferente, não há não uma referência de classe centralizada no MSDN; em vez disso, cada cliente mantém sua própria documentação de referência. Além de documentação de referência, há vários tutoriais mostrando como começar a usar o Cache Redis do Azure usando diferentes linguagens e clientes de cache. Para acessar esses tutoriais, consulte [Como usar o Cache Redis do Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e clique no linguagem desejada do seletor de linguagens na parte superior do artigo.


## Perguntas frequentes sobre segurança

-	[Quando devo habilitar a porta não SSL para conexão ao Redis?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

<a name="cache-ssl"></a>
### Quando devo habilitar a porta não SSL para conexão ao Redis?

O servidor Redis não dá suporte ao SSL pronto para uso, enquanto o Cache Redis do Azure dá. Se você estiver conectando ao Cache Redis do Azure e o seu cliente dá suporte a SSL, como StackExchange.Redis, você deve usar SSL.

Observe que a porta que não é do tipo SSL é desabilitada por padrão para novas instâncias de Cache Redis do Azure. Se o cliente não dá suporte a SSL, você deve habilitar a porta não SSL seguindo as instruções na seção [Portas de acesso](cache-configure.md#access-ports) do artigo [Configurar um cache no Cache Redis do Azure](cache-configure.md).

Ferramentas do Redis como `redis-cli` não funcionam com a porta SSL, mas você pode usar um utilitário como `stunnel` para conectar as ferramentas com segurança à porta SSL seguindo as instruções na postagem de blog [Anunciando o provedor de estado de sessão ASP.NET para versão de visualização do Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Para obter instruções sobre como baixar as ferramentas do Redis, consulte a seção [Como posso executar comandos do Redis?](#cache-commands)

## Perguntas frequentes sobre produção

-	[Quais são algumas das considerações ao usar os comandos comuns do Redis?](#what-are-some-of-the-considerations-whpt-BRing-common-redis-commands)
-	[Como medir e testar o desempenho do meu cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-	[Detalhes importantes sobre o crescimento de ThreadPool](#important-details-about-threadpool-growth)
-	[Habilitar a GC (coleta de lixo) do servidor para obter mais produtividade no cliente ao usar o StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-whpt-BRing-stackexchangeredis)


<a name="cache-redis-commands"></a>
### Quais são algumas das considerações ao usar os comandos comuns do Redis?

-	Você não deve executar determinados comandos do Redis que levam muito tempo para ser concluídos sem compreender o impacto desses comandos.
-	Por exemplo, não execute o comando [CHAVES](http://redis.io/commands/keys) durante a produção, já que ele pode levar muito tempo para retornar dependendo do número de chaves. O Redis é um servidor de thread único e processa um comando por vez. Se você emitir outros comandos após CHAVES, eles não serão processados até que o Redis processe o comando CHAVES.
-	Tamanhos de chave - devo usar valores pequenos ou grandes de chave? Em geral, depende do cenário. Se seu cenário exigir chaves maiores, você poderá ajustar os valores de ConnectionTimeout e de novas tentativas e ajustar sua lógica de repetição de tentativas. Da perspectiva do servidor Redis, valores menores têm um desempenho melhor.
-	Isso não significa que você não pode armazenar valores maiores em Redis, mas você deve estar ciente das considerações a seguir. As latências serão maiores. Se você tiver um conjunto de dados maior e um menor, você pode usar várias instâncias do ConnectionMultiplexer, cada uma configurada com um conjunto diferente de valores de tempo limite e de repetição de tentativa, conforme descrito anteriormente na seção [O que as opções de configuração de StackExchange.Redis fazem](#cache-configuration).



<a name="cache-benchmarking"></a>
### Como medir e testar o desempenho do meu cache?

-	[Habilite o diagnóstico de cache](cache-how-to-monitor.md#enable-cache-diagnostics) para que você possa [monitorar](cache-how-to-monitor.md) a integridade do cache. Você pode exibir as métricas no Portal do Azure e também pode [baixá-las e analisá-las](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando as ferramentas de sua escolha.
-	Você pode usar o redis-benchmark.exe para testar a carga em seu servidor do Redis.
-	Certifique-se que o cliente e o cache Redis de teste de carga estejam na mesma região.
-	Use o redis cli.exe e monitore o cache usando o comando INFO.
-	Se sua carga está causando um nível elevado de fragmentação de memória, você deve escalar verticalmente para um tamanho de cache maior.
-	Para obter instruções sobre como baixar as ferramentas do Redis, consulte a seção [Como posso executar comandos do Redis?](#cache-commands)


<a name="threadpool"></a>
### Detalhes importantes sobre o crescimento de ThreadPool

O ThreadPool do CLR tem dois tipos de threads: "Trabalho" e "Porta de conclusão E/S" (também conhecida como IOCP).

-	Os threads de trabalho são usados em situações como o processamento dos métodos `Task.Run(…)` ou `ThreadPool.QueueUserWorkItem(…)`. Esses threads também são usados por vários componentes no CLR quando o trabalho precisa ser executado em um thread em segundo plano.
-	Os threads IOCP são usados quando há E/S assíncrona (por exemplo, lendo da rede).

O pool de threads fornece novos threads de trabalho ou de conclusão de E/S sob demanda (sem qualquer limitação) até atingir a configuração de "Mínimo" para cada tipo de thread. Por padrão, o número mínimo de threads é definido como o número de processadores em um sistema.

Depois que o número de threads existentes (ocupados) atinge o número "mínimo" de threads, o ThreadPool limitará a taxa à qual injeta novos threads em um thread por 500 milissegundos. Isso significa que se seu sistema obtém um pico de trabalho que precisa de um thread IOCP, ele processará esse trabalho muito rapidamente. No entanto, se o pico de trabalho for maior que a configuração "Mínimo", haverá algum atraso no processamento do trabalho, já que o ThreadPool aguarda um dentre dois acontecimentos.

1. Um thread existente ficar livre para processar o trabalho.
1. Nenhum thread existente ficar livre para 500 ms, o que leva à criação de outro thread.

Basicamente, isso significa que quando o número de threads ocupados é maior que o mínimo de threads, você provavelmente está pagando um atraso de 500 ms antes que o tráfego de rede seja processado pelo aplicativo. Além disso, é importante observar que, quando um thread existente permanece ocioso por mais de 15 segundos (se não me falha a memória), ele é limpo e esse ciclo de crescimento e redução pode ser repetido.

Se examinarmos um exemplo de mensagem de erro do StackExchange.Redis (versão 1.0.450 ou posterior), você verá que agora ela imprime estatísticas de ThreadPool (confira os detalhes de TRABALHO e IOCP abaixo).

	System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive, 
	queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0, 
	IOCP: (Busy=6,Free=994,Min=4,Max=1000), 
	WORKER: (Busy=3,Free=997,Min=4,Max=1000)

No exemplo acima, você pode ver que, para o thread IOCP, há seis threads ocupados e o sistema está configurado para permitir o mínimo de quatro threads. Nesse caso, o cliente provavelmente veria dois atrasos de 500 ms porque 6 > 4.

Observe que o StackExchange.Redis poderá atingir tempos limite se o crescimento de threads de TRABALHO ou IOCP for limitado.

### Recomendações

Dadas essas informações, recomendamos fortemente que os clientes definam o valor de configuração mínima para threads de TRABALHO e IOCP com um valor maior que o valor padrão. Não podemos dar uma orientação que sirva para todos sobre esse valor porque o valor correto para um aplicativo será muito alto/baixo para outro aplicativo. Essa configuração também pode afetar o desempenho de outras partes do aplicativos complicados; portanto, cada cliente precisa ajustar essa configuração para as suas necessidades específicas. Um bom ponto de partida é 200 ou 300, para testar e ajustar conforme necessário.

Como definir essa configuração:

-	No ASP.NET, use a [configuração "minIoThreads"][] no elemento de configuração `<processModel>` no web.config. Se você estiver executando nos Sites do Azure, essa configuração não será exposta pelas opções de configuração. No entanto, você deve ainda ser capaz de definir isso por meio de programação (veja abaixo) do seu método Application\_Start em global.asax.cs.

> **Observação importante:** o valor especificado nesse elemento de configuração é uma configuração *por núcleo*. Por exemplo, se você tiver um computador com quatro núcleos e desejar que sua configuração minIOThreads seja de 200 no tempo de execução, use `<processModel minIoThreads="50"/>`.

-	Fora do ASP.NET, use a API [ThreadPool.SetMinThreads(...)](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx).

<a name="server-gc"></a>
### Habilitar a GC (coleta de lixo) do servidor para obter mais produtividade no cliente ao usar o StackExchange.Redis

Habilitar a GC do servidor pode otimizar o cliente e proporcionar melhor desempenho e produtividade ao usar o Stackexchange.Redis. Para saber mais sobre a GC do servidor e como habilitá-la, confira os artigos a seguir.

-	[Para habilitar a GC (coleta de lixo) do servidor](https://msdn.microsoft.com/library/ms229357.aspx)
-	[Noções básicas sobre a coleta de lixo](https://msdn.microsoft.com/library/ee787088.aspx)
-	[Coleta de lixo e desempenho](https://msdn.microsoft.com/library/ee851764.aspx)





## Perguntas frequentes sobre monitoramento e solução de problemas

As Perguntas frequentes nesta seção tratam de dúvidas comuns sobre monitoramento e solução de problemas. Para saber mais sobre como monitorar e solucionar problemas de suas instâncias do Cache Redis do Azure, consulte [Como monitorar o Cache Redis do Azure](cache-how-to-monitor.md) e [Como solucionar problemas do Cache Redis do Azure](cache-how-to-troubleshoot.md).

-	[Como monitorar a integridade e o desempenho do meu cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
-	[As configurações da minha conta de armazenamento de diagnóstico de cache foram alteradas, o que aconteceu?](#my-cache-diagnostics-storage-account-settings-changed-what-happened)
-	[Por que o diagnóstico está habilitado para alguns novos caches, mas não para outros?](#why-is-diagnostics-enabled-for-some-new-caches-but-not-others)
-	[Por que vejo tempos limite?](#why-am-i-seeing-timeouts)
-	[Por que meu cliente foi desconectado do cache?](#why-was-my-client-disconnected-from-the-cache)

<a name="cache-monitor"></a>
### Como monitorar a integridade e o desempenho do meu cache?

As instâncias do Cache Redis do Microsoft Azure podem ser monitoradas no [Portal do Azure](https://portal.azure.com). Você pode exibir métricas, fixar gráficos de métricas ao quadro inicial, personalizar o intervalo de data e hora de gráficos de monitoramento, adicionar/remover as métricas de gráficos e definir alertas quando determinadas condições forem atendidas. Para saber mais, veja [Monitorar o Cache Redis do Azure](cache-how-to-monitor.md).

A seção **Suporte + solução de problemas** da folha **Configurações** do Cache Redis também contém várias ferramentas para monitorar e solucionar problemas de seus caches.

-	**Solucionar problemas** fornece informações sobre problemas comuns e estratégias para resolvê-los.
-	**Logs de auditoria** fornece informações sobre as ações executadas em seu cache. Você também pode usar a filtragem para expandir essa exibição a fim de incluir outros recursos.
-	O **Resource Health** observa seu recurso e informa se ele está sendo executado conforme o esperado. Para saber mais sobre o serviço Azure Resource Health, confira [Visão geral do Azure Resource Health](../resource-health/resource-health-overview.md).
-	**Nova solicitação de suporte** fornece opções para abrir uma solicitação de suporte para seu cache.

Essas ferramentas permitem monitorar a integridade de suas instâncias de Cache Redis do Azure e ajudá-lo a gerenciar seus aplicativos de cache. Para saber mais, veja a seção [Suporte e solução de problemas de configuração](cache-configure.md#support-amp-troubleshooting-settings).

### As configurações da minha conta de armazenamento de diagnóstico de cache foram alteradas, o que aconteceu?

Os caches na mesma região e assinatura compartilham as mesmas configurações de armazenamento de diagnóstico e, se as configurações forem alteradas (diagnóstico habilitado/desabilitado ou alteração da conta de armazenamento), elas serão aplicadas a todos os caches da assinatura que estão na região. Se as configurações de diagnóstico do seu cache tiverem sido alteradas, verifique se as configurações de diagnóstico de outro cache na mesma assinatura e região foram alteradas. Uma forma de verificar é exibir os logs de auditoria do seu cache para um evento `Write DiagnosticSettings`. Para saber mais sobre como trabalhar com logs de auditoria, confira [Exibir eventos e logs de auditoria](../azure-portal/insights-debugging-with-events.md) e [Operações de auditoria com o Resource Manager](../resource-group-audit.md). Para saber mais sobre como monitorar eventos do Cache Redis do Azure, confira [Operações e alertas](cache-how-to-monitor.md#operations-and-alerts).

### Por que o diagnóstico está habilitado para alguns novos caches, mas não para outros?

Caches na mesma região e assinatura compartilham as mesmas configurações de armazenamento de diagnóstico. Se você criar um novo cache na mesma região e assinatura como outro cache que tem o diagnóstico habilitado, o diagnóstico será habilitado no novo cache usando as mesmas configurações.


<a name="cache-timeouts"></a>
### Por que vejo tempos limite?

Tempos limite ocorrem no cliente que você usa para se comunicar com o Redis. Na maior parte dos casos, o servidor Redis não atinge o tempo limite. Quando um comando é enviado ao servidor Redis, o comando é colocado na fila e o servidor Redis, eventualmente, seleciona o comando e o executa. No entanto, o cliente pode atingir o tempo limite durante este processo e, se ele faz isso, uma exceção é gerada no lado que realiza a chamada. Para saber mais sobre como solucionar problemas de tempo limite, consulte [Solução de problemas do lado do cliente](cache-how-to-troubleshoot.md#client-side-troubleshooting) e [Exceções de tempo limite do StackExchange.Redis] (Solução de problemas do lado do cliente](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).

'<-- Loc Comment: Broken link: [StackExchange.Redis timeout exceptions](Client side troubleshooting](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions). "(Client side troubleshooting]" should be removed. -->'

<a name="cache-disconnect"></a>
### Por que meu cliente foi desconectado do cache?

A seguir estão alguns motivos comuns para uma desconexão de cache.

-	Motivos no lado do cliente
	-	O aplicativo cliente foi reimplantado.
	-	O aplicativo cliente executou uma operação de dimensionamento.
		-	No caso de serviços de nuvem ou aplicativos Web, isso pode ser devido ao dimensionamento automático.
	-	A camada de rede no lado do cliente foi alterada.
	-	Erros transitórios ocorreram no cliente ou em nós de rede entre o cliente e o servidor.
	-	Os limites de largura de banda foram atingidos.
	-	Operações vinculadas à CPU demoraram muito para ser concluídas.
-	Motivos no lado do servidor
	-	Com a oferta de cache padrão, o serviço de Cache Redis do Azure iniciou um failover do nó primário para o nó secundário.
	-	O Azure estava aplicando um patch na instância onde o cache foi implantado
		-	Isso pode ser para atualizações de servidor do Redis ou manutenção geral de VM.





## Perguntas frequentes sobre ofertas anteriores de cache

-	[Qual oferta de Cache do Azure é a correta para mim?](#which-azure-cache-offering-is-right-for-me)

### Qual oferta de Cache do Azure é a correta para mim?

>[AZURE.IMPORTANT]De acordo com o [comunicado](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) do ano passado, o Serviço de Cache Gerenciado do Azure e o serviço Cache na Função do Azure serão desativados em 30 de novembro de 2016. A nossa recomendação é usar o [Cache Redis do Azure](https://azure.microsoft.com/services/cache/). Para saber mais sobre a migração, confira [Migrar do Serviço de Cache gerenciado para o Cache Redis do Azure](cache-migrate-to-redis.md).

### Cache Redis do Azure
O Cache Redis do Azure está disponível em tamanhos de 53 GB e tem SLA de disponibilidade de 99,9%. A nova [camada premium](cache-premium-tier-intro.md) oferece tamanhos de até 530 GB e suporte para clustering, VNET e persistência, com um SLA de 99,9%.

O Cache Redis do Azure dá aos clientes a capacidade de usar um Cache Redis seguro e dedicado, gerenciado pela Microsoft. Com essa oferta, você pode aproveitar o rico conjunto de recursos e ecossistema fornecido pelo Redis, além de hospedagem e monitoramento confiáveis da Microsoft.

Diferentemente dos caches tradicionais que lidam exclusivamente com pares chave-valor, o Redis é popular por seus tipos de dados altamente funcionais. O Redis também permite executar operações atômicas nesses tipos, como acréscimos a uma cadeia de caracteres; incrementação de valor em hash; envios para uma lista; intersecção, união e diferença em conjuntos de computação; ou colocar o membro com classificação mais alta em um conjunto ordenado. Outros recursos incluem suporte para transações, publicação/assinatura, scripts LUA, chaves com vida útil limitada e definições de configuração para fazer com que o Redis se comporte mais como um cache tradicional.

Outro aspecto fundamental do sucesso do Redis é o ecossistema de software livre íntegro e vibrante criado em torno dele. Isso é refletido no conjunto variado de clientes Redis disponíveis em vários idiomas. Isso permite que ele seja usado em quase qualquer carga de trabalho criada dentro do Azure.

Para saber mais sobre como começar a usar o Cache Redis do Azure, confira [Como usar o Cache Redis do Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e a [documentação do Cache Redis do Azure](https://azure.microsoft.com/documentation/services/redis-cache/).

### Serviço de Cache gerenciado
[O serviço de Cache Gerenciado está programado para desativação em 30 de novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

### Cache em Função
[O Cache na Função está programado para desativação em 30 de novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)





[configuração "minIoThreads"]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx

<!---HONumber=AcomDC_0720_2016-->