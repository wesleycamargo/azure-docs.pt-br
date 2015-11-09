<properties 
	pageTitle="Perguntas frequentes sobre Cache Redis do Azure" 
	description="Conheça as respostas a perguntas, padrões e práticas recomendadas comuns do Cache Redis do Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2015" 
	ms.author="sdanie"/>

# Perguntas frequentes sobre Cache Redis do Azure

Conheça as respostas a perguntas, padrões e práticas recomendadas comuns do Cache Redis do Azure.

<a name="cache-size"></a>
## Qual oferta e tamanho de Cache Redis devo usar?
Cada oferta de Cache Redis do Azure fornece diferentes níveis de opções **tamanho**, **largura de banda**, **alta disponibilidade** e **SLA**.

Veja abaixo as considerações para a escolha de uma oferta de Cache.

-	**Memória**: as camadas Básica e Standard oferecem 250 MB – 53 GB. A camada Premium oferece até 530 GB com mais espaço disponível [mediante solicitação](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Para obter mais informações, veja [Preço do Cache Redis do Azure](https://azure.microsoft.com/pricing/details/cache/).
-	**Desempenho de rede**: se você tiver uma carga de trabalho que requer uma alta taxa de transferência, a camada Premium oferece mais largura de banda quando comparada às camadas Standard ou Básica. Também dentro de cada camada, caches de tamanhos maiores têm mais largura de banda, devido à VM subjacente que hospeda o cache. Veja a tabela a seguir para obter mais informações.
-	**Taxa de transferência**: a camada Premium oferece a taxa de transferência máxima disponível. Se o cliente ou servidor de cache atingir os limites de largura de banda, você receberá tempos limite no lado do cliente. Veja a tabela a seguir para obter mais informações.
-	**Alta disponibilidade/SLA**: O Cache Redis do Azure garante que um cache Standard/Premium (só há SLA para o cache Premium após o período de visualização) estará disponível pelo menos 99,9% do tempo. Para saber mais sobre nosso SLA, veja [Preço do Redis Cache do Azure](https://azure.microsoft.com/pricing/details/cache/). O SLA abrange apenas a conectividade com os pontos de extremidade do Cache. O SLA não abrange a proteção contra perda de dados. Recomendamos usar o recurso de persistência de dados do Redis na camada Premium para aumentar a resiliência contra a perda de dados.
-	**Persistência de dados do Redis**: a camada Premium permite persistir os dados de cache em uma conta do Armazenamento do Azure. Em um cache Básico/Standard, todos os dados são armazenados apenas na memória. Em caso de problemas de infraestrutura subjacente, pode haver uma possível perda de dados. Recomendamos usar o recurso de persistência de dados do Redis na camada Premium para aumentar a resiliência contra a perda de dados. O Cache Redis do Azure oferece opções de RDB e AOF (em breve) na persistência do Redis. Para obter mais informações, veja [Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md).
-	**Cluster Redis**: se desejar criar caches maiores que 53 GB ou fragmentar dados entre vários nós do Redis, você pode usar o clustering do Redis que está disponível na camada Premium. Cada nó consiste em um par de cache primário/de réplica para alta disponibilidade. Para obter mais informações, veja [Como configurar o clustering para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md).
-	**Isolamento de rede e segurança avançados**: a implantação de VNET (Rede Virtual) do Azure fornece isolamento e segurança avançados para o Cache Redis do Azure, bem como sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso. Para obter mais informações, veja [Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium](cache-how-to-premium-vnet.md).
-	**Configurar o Redis**: nas camadas Standard e Premium, é possível configurar o Redis para notificações de Keyspace.
-	**Número máximo de conexões de cliente**: a camada Premium oferece o número máximo de clientes que podem se conectar ao Redis, com um número maior de conexões para caches de tamanhos maiores. [Confira a página de preço para obter detalhes](https://azure.microsoft.com/pricing/details/cache/).
-	**Núcleo dedicado para o servidor do Redis**: na camada Premium, todos os tamanhos de cache têm um núcleo dedicado para o Redis. Nas camadas Básica/Standard, o tamanho C1 e acima têm um núcleo dedicado para o servidor do Redis.
-	**Como o Redis usa thread único**, ter mais de dois núcleos não oferece um benefício adicional em relação a ter apenas dois, mas tamanhos maiores de VM normalmente têm mais largura de banda que tamanhos menores. Se o cliente ou servidor de cache atingir os limites de largura de banda, você receberá tempos limite no lado do cliente.
-	**Melhorias de desempenho**: Os caches na camada Premium são implantados no hardware que tem processadores mais rápidos e que oferece um melhor desempenho quando comparado às camadas Básica ou Standard. Os Caches da camada Premium têm a taxa de transferência mais alta e as latências mais baixas.

A tabela a seguir mostra os valores de largura de banda máxima observados durante o teste de vários tamanhos de caches Standard e Premium usando `redis-benchmark.exe` de uma VM de Iaas no ponto de extremidade do Cache Redis do Azure. É importante observar que esses valores não são garantidos e que não há nenhum SLA para esses números, mas eles devem ser típicos. Você deve realizar teste de carga em seu próprio aplicativo para determinar o tamanho de cache certo para ele.

Podemos tirar as seguintes conclusões desta tabela.

-	A taxa de transferência para o Cache do mesmo tamanho é maior na camada Premium quando comparada à camada Standard. Por exemplo, para um Cache de 6 GB, a taxa de transferência de P1 será 140 mil RPS, em comparação com 49 mil para C3.
-	Com o cluster Redis, a taxa de transferência aumenta linearmente à medida que o número de fragmentos (nós) no cluster aumenta. Por exemplo, se você criar um cluster P4 de 10 fragmentos, a taxa de transferência disponível será de 250 mil *10 = 2,5 milhões de RPS.
-	A taxa de transferência tamanhos de chave maiores é mais alta na camada Premium quando comparada à camada Standard.

| Camada de preços | Tamanho | Largura de banda disponível (Mbps) | Tamanho de chave de 1 KB |
|----------------------|--------|----------------------------|--------------------------------|
| **Tamanhos de cache padrão** | &nbsp; | &nbsp; | **RPS (solicitações por segundo)** |
| C0 | 250 MB | 5 | 600 |
| C1 | 1 GB | 100 | 12\.200 |
| C2 | 2,5 GB | 200 | 24\.000 |
| C3 | 6 GB | 400 | 49\.000 |
| C4 | 13 GB | 500 | 61\.000 |
| C5 | 26 GB | 1000 | 115\.000 |
| C6 | 53 GB | 2000 | 150\.000 |
| **Tamanhos de cache Premium** | &nbsp; | &nbsp; | **RPS (solicitações por segundo) por fragmento** |
| P1 | 6 GB | 1000 | 140\.000 |
| P2 | 13 GB | 2000 | 220\.000 |
| P3 | 26 GB | 2000 | 220\.000 |
| P4 | 53 GB | 4000 | 250\.000 |


Para obter instruções sobre como baixar as ferramentas do Redis como `redis-benchmark.exe`, consulte a seção [Como posso executar comandos do Redis?](#cache-commands)

<a name="cache-region"></a>
## Em que região posso localizar meu cache?

Para melhor desempenho e menor latência, localize o Cache Redis do Azure na mesma região que o aplicativo cliente de cache.

<a name="cache-billing"></a>
## Como eu sou cobrado pelo Cache Redis do Azure?

O preço do Cache Redis do Azure pode ser encontrado [aqui](http://azure.microsoft.com/pricing/details/cache/). A página de preços lista os preços com uma taxa horária. Os caches são cobrados em uma base por minuto do momento em que o cache é criado até o momento em que um cache é excluído. Não há nenhuma opção para parar ou pausar a cobrança de um cache.

<a name="cache-timeouts"></a>
## Por que vejo tempos limite?

Tempos limite ocorrem no cliente que você usa para se comunicar com o Redis. Na maior parte dos casos, o servidor Redis não atinge o tempo limite. Quando um comando é enviado ao servidor Redis, o comando é colocado na fila e o servidor Redis, eventualmente, seleciona o comando e o executa. No entanto, o cliente pode atingir o tempo limite durante este processo e, se ele faz isso, uma exceção é gerada no lado que realiza a chamada. Para obter mais informações sobre como solucionar problemas de tempo limite, consulte [investigando exceções de tempo limite em StackExchange.Redis para o Azure Redis Cache](http://azure.microsoft.com/blog/2015/02/10/investigating-timeout-exceptions-in-stackexchange-redis-for-azure-redis-cache/).

<a name="cache-monitor"></a>
## Como monitorar a integridade e o desempenho do meu cache?

As instâncias do Cache Redis do Microsoft Azure podem ser monitoradas no [portal de visualização do Azure](https://portal.azure.com). Você pode exibir métricas, fixar gráficos de métricas ao quadro inicial, personalizar o intervalo de data e hora de gráficos de monitoramento, adicionar/remover as métricas de gráficos e definir alertas quando determinadas condições forem atendidas. Essas ferramentas permitem monitorar a integridade de suas instâncias de Cache Redis do Azure e ajudá-lo a gerenciar seus aplicativos de cache. Para obter mais informações sobre como monitorar seus caches, consulte [Monitorar o Cache Redis do Azure](https://msdn.microsoft.com/library/azure/dn763945.aspx).

<a name="cache-disconnect"></a>
## Por que meu cliente foi desconectado do cache?

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

<a name="cache-configuration"></a>
## O que as opções de configuração do StackExchange.Redis fazem?

O StackExchange.Redis tem muitas opções. Esta seção fala sobre algumas das configurações comuns. Para obter mais informações sobre opções do StackExchange.Redis, consulte [configuração do StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md).

ConfigurationOptions|Descrição|Recomendações
---|---|---
AbortOnConnectFail|Quando definido como true, a conexão não reconectará após uma falha de rede.|Defina como false e deixe o StackExchange.Redis reconectar-se automaticamente.
ConnectRetry|O número de vezes para repetir tentativas de conexão durante a conexão inicial.||
ConnectTimeout|Tempo limite em ms para operações de conexão.|

Na maioria dos casos, os valores padrão do cliente são suficientes. Você pode realizar o ajuste fino das opções com base na sua carga de trabalho.

-	Novas tentativas
	-	Para ConnectRetry e ConnectTimeout, a orientação geral é falhar no modo rápido e tentar novamente. Isso se baseia na sua carga de trabalho e em quanto tempo em média leva para o cliente emitir um comando Redis e receber uma resposta.
	-	Deixe o StackExchange.Redis reconectar-se automaticamente, em vez de você verificar o status de conexão e realizar a reconexão. **Evitar usar a propriedade ConnectionMultiplexer.IsConnected**.
	-	Bola de neve - às vezes, você poderá ter um problema em que você esteja realizando novas tentativas e isso se torne uma bola de neve, sem nunca se recuperar. Nesse caso você deve considerar usar um algoritmo de nova tentativa de retirada exponencial, conforme descrito em [Orientação geral sobre novas tentativas](https://github.com/mspnp/azure-guidance/blob/master/Retry-General.md) publicado pelo grupo de Padrões e práticas da Microsoft.
-	Valores de tempo limite
	-	Considere sua carga de trabalho e defina os valores adequadamente. Se você estiver armazenando valores grandes, defina o tempo limite como um valor mais alto.
		-	Defina ABortOnConnectFail como false e permita que o StackExchange.Redis faça a reconexão para você.
-	Use uma única instância de ConnectionMultiplexer para o aplicativo. Você pode usar uma LazyConnection para criar uma única instância que é retornada por uma propriedade Connection, conforme mostrado em [Conectar-se ao cache usando a classe ConnectionMultiplexer](https://msdn.microsoft.com/library/azure/dn690521.aspx#Connect).
-	Defina a propriedade `ConnectionMultiplexer.ClientName` como um nome exclusivo de instância de aplicativo para fins de diagnóstico.
-	Usar várias instâncias de `ConnectionMultiplexer` para cargas de trabalho personalizadas.
	-	Você pode seguir este modelo se tem variação de carga em seu aplicativo. Por exemplo:
		-	Você pode ter um multiplexador para lidar com chaves grandes. 
		-	Você pode ter um multiplexador para lidar com chaves pequenas. 
		-	Você pode definir valores diferentes para o tempo limite da conexão e a lógica de repetição para cada ConnectionMultiplexer que você usa.
		-	Definir a propriedade `ClientName` em cada multiplexador para ajudar no diagnóstico. 
		-	Isso levará a uma latência mais aperfeiçoada por `ConnectionMultiplexer`.

<a name="cache-redis-commands"></a>
## Quais são algumas das considerações ao usar os comandos comuns do Redis?

-	Você não deve executar determinados comandos do Redis que levam muito tempo para ser concluídos sem compreender o impacto desses comandos.
	-	Por exemplo, não execute o comando [CHAVES](http://redis.io/commands/keys) durante a produção, já que ele pode levar muito tempo para retornar dependendo do número de chaves. O Redis é um servidor de thread único e processa um comando por vez. Se você emitir outros comandos após CHAVES, eles não serão processados até que o Redis processe o comando CHAVES.
-	Tamanhos de chave - devo usar valores pequenos ou grandes de chave? Em geral, depende do cenário. Se seu cenário exigir chaves maiores, você poderá ajustar os valores de ConnectionTimeout e de novas tentativas e ajustar sua lógica de repetição de tentativas. Da perspectiva do servidor Redis, valores menores têm um desempenho melhor.
	-	Isso não significa que você não pode armazenar valores maiores em Redis, mas você deve estar ciente das considerações a seguir. As latências serão maiores. Se você tiver um conjunto de dados maior e um menor, você pode usar várias instâncias do ConnectionMultiplexer, cada uma configurada com um conjunto diferente de valores de tempo limite e de repetição de tentativa, conforme descrito anteriormente na seção [O que as opções de configuração de StackExchange.Redis fazem](#cache-configuration).


<a name="cache-ssl"></a>
## Quando devo habilitar a porta não SSL para conexão ao Redis?

O servidor Redis não dá suporte ao SSL pronto para uso, enquanto o Cache Redis do Azure dá. Se você estiver conectando ao Cache Redis do Azure e o seu cliente dá suporte a SSL, como StackExchange.Redis, você deve usar SSL.

Observe que a porta que não é do tipo SSL é desabilitada por padrão para novas instâncias de Cache Redis do Azure. Se o cliente não dá suporte a SSL, você deve habilitar a porta não SSL seguindo as instruções na seção [Portas de acesso](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) do artigo [Configurar um cache no Cache Redis do Azure](https://msdn.microsoft.com/library/azure/dn793612.aspx).

Ferramentas do Redis como `redis-cli` não funcionam com a porta SSL, mas você pode usar um utilitário como `stunnel` para conectar as ferramentas com segurança à porta SSL seguindo as instruções na postagem de blog [Anunciando o provedor de estado de sessão ASP.NET para versão de visualização do Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Para obter instruções sobre como baixar as ferramentas do Redis, consulte a seção [Como posso executar comandos do Redis?](#cache-commands)

<a name="cache-benchmarking"></a>
## Como medir e testar o desempenho do meu cache?

-	[Habilite o diagnóstico de cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para que você possa [monitorar](https://msdn.microsoft.com/library/azure/dn763945.aspx) a integridade do cache. É possível exibir as métricas no portal de visualização e também [baixá-las e examiná-las](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando as ferramentas de sua escolha.
-	Você pode usar o redis-benchmark.exe para testar a carga em seu servidor do Redis.
	-	Certifique-se que o cliente e o cache Redis de teste de carga estejam na mesma região.
-	Use o redis cli.exe e monitore o cache usando o comando INFO.
	-	Se sua carga está causando um nível elevado de fragmentação de memória, você deve escalar verticalmente para um tamanho de cache maior.
-	Para obter instruções sobre como baixar as ferramentas do Redis, consulte a seção [Como posso executar comandos do Redis?](#cache-commands)

<a name="cache-commands"></a>
## Como posso executar comandos do Redis?

Você pode usar qualquer um dos comandos listados em [Comandos do Redis](http://redis.io/commands#), exceto pelos comandos listados em [Comandos do Redis sem suporte no Cache Redis do Azure](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Para executar comandos Redis, há várias opções.

-	Se você tiver um cache Standard ou Premium, é possível executar comandos do Redis usando o [Console do Redis](cache-configure.md#redis-console). Isso fornece uma maneira segura para executar comandos do Redis no portal de visualização do Azure.
-	Também é possível usar as ferramentas de linha de comando do Redis. Para usá-las, você executará as seguintes etapas:
	-	Baixe as [Ferramentas de linha de comando do Redis](https://github.com/MSOpenTech/redis/releases/download/win-2.8.19.1/redis-2.8.19.zip).
	-	Conecte-se ao cache usando `redis-cli.exe`. Passe no ponto de extremidade de cache usando que o comutador -h e a chave usando - a, como mostrado no exemplo a seguir.
		-	`redis-cli -h <your cache name>.redis.cache.windows.net -a <key>`
	-	Observe que as ferramentas de linha de comando do Redis não funcionam com a porta SSL, mas você pode usar um utilitário como `stunnel` para conectar as ferramentas com segurança à porta SSL seguindo as instruções na postagem de blog [Anunciando o provedor de estado de sessão ASP.NET para versão de visualização do Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

<a name="cache-common-patterns"></a>
## Quais são alguns padrões comuns de cache e considerações a respeito?

-	O documento Padrões e práticas da Microsoft oferece as instruções a seguir.
	-	[Orientação sobre caching](https://github.com/mspnp/azure-guidance/blob/master/Caching.md).
	-	[Orientação sobre criação e implementação de aplicativos de nuvem do Azure](https://github.com/mspnp/azure-guidance)
-	[Padrões de cache comuns com o Cache Redis do Azure](cache-howto-common-cache-patterns.md)

<a name="cache-reference"></a>
## Por que o Cache Redis do Azure não tem uma referência da biblioteca de classes MSDN, assim como alguns dos outros serviços do Azure?

O Cache Redis do Microsoft Azure baseia-se no popular software livre Cache Redis, oferecendo acesso a um cache Redis seguro e dedicado, gerenciado pela Microsoft. Uma variedade de [clientes Redis](http://redis.io/clients) estão disponíveis para várias linguagens de programação. Cada cliente tem sua própria API, que faz chamadas para a instância de cache Redis usando [comandos do Redis](http://redis.io/commands).

Como cada cliente é diferente, não há não uma referência de classe centralizada no MSDN; em vez disso, cada cliente mantém sua própria documentação de referência. Além de documentação de referência, há vários tutoriais no Azure.com mostrando como começar a usar o Cache Redis do Azure usando diferentes linguagens e clientes de cache na página [Documentação do Cache Redis](http://azure.microsoft.com/documentatgion/services/redis-cache/).


## Qual oferta de Cache do Azure é a correta para mim?

>[AZURE.IMPORTANT]A Microsoft recomenda que todos os novos desenvolvimentos usem o Cache Redis do Azure.

Atualmente, o cache do Azure tem três ofertas:

-	Cache Redis do Azure
-	Serviço de Cache gerenciado do Azure
-	Cache na função do Azure


### Cache Redis do Azure
O Cache Redis do Azure está disponível em tamanhos de 53 GB e tem SLA de disponibilidade de 99,9%. A nova [camada premium](cache-premium-tier.md) está na visualização e oferece tamanhos até 530 GB e suporte para cluster, VNET e persistência.

O Cache Redis do Azure dá aos clientes a capacidade de usar um Cache Redis seguro e dedicado, gerenciado pela Microsoft. Com essa oferta, você pode aproveitar o rico conjunto de recursos e ecossistema fornecido pelo Redis, além de hospedagem e monitoramento confiáveis da Microsoft.

Diferentemente dos caches tradicionais que lidam exclusivamente com pares chave-valor, o Redis é popular por seus tipos de dados altamente funcionais. O Redis também permite executar operações atômicas nesses tipos, como acréscimos a uma cadeia de caracteres; incrementação de valor em hash; envios para uma lista; intersecção, união e diferença em conjuntos de computação; ou colocar o membro com classificação mais alta em um conjunto ordenado. Outros recursos incluem suporte para transações, publicação/assinatura, scripts LUA, chaves com vida útil limitada e definições de configuração para fazer com que o Redis se comporte mais como um cache tradicional.

Outro aspecto fundamental do sucesso do Redis é o ecossistema de software livre íntegro e vibrante criado em torno dele. Isso é refletido no conjunto variado de clientes Redis disponíveis em vários idiomas. Isso permite que ele seja usado em quase qualquer carga de trabalho criada dentro do Azure.

Para obter mais informações sobre como iniciar o Cache Redis do Azure, consulte [Como usar o Cache Redis do Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e a [documentação do Cache Redis do Azure](https://azure.microsoft.com/documentation/services/redis-cache/).

### Serviço de Cache gerenciado
Se você já for cliente do serviço de Cache gerenciado do Azure, pode continuar usando o serviço existente ou optar por migrar o Cache Redis do Azure para aproveitar o avançado conjunto de recursos. O serviço de Cache gerenciado do Azure também tem disponibilidade geral e oferece um SLA de disponibilidade de 99,9%.

### Cache em Função
Se você estiver hospedando um cache internamente usando Cache na função, pode continuar a fazer o mesmo. Como o Cache na função é um componente de software auto-hospedado e não um serviço hospedado da Microsoft, ele não oferece nenhum SLA. Usuários de Cache na função podem optar por migrar para o Cache Redis do Azure para aproveitar o avançado conjunto de recursos e obter um SLA.

<!---HONumber=Nov15_HO1-->