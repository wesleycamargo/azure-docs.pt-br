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
	ms.date="07/24/2015" 
	ms.author="sdanie"/>

# Perguntas frequentes sobre Cache Redis do Azure

Conheça as respostas a perguntas, padrões e práticas recomendadas comuns do Cache Redis do Azure.

<a name="cache-size"></a>
## Qual oferta e tamanho de Cache Redis devo usar?

Cada oferta de Cache Redis do Azure fornece diferentes níveis de opções **tamanho**, **largura de banda**, **alta disponibilidade** e **SLA**.

-	SKU básico - nó único, sem replicação nem SLA, tamanhos de cache de 250 MB até 53 GB.
-	SKU padrão - nós primário/secundário com replicação automática, SLA de 99,9% e tamanhos de cache de 250 MB a 53 GB.

Se desejar alta disponibilidade, escolha a oferta de cache padrão que tem um SLA de 99,9%. Para criação de protótipos e desenvolvimento, ou então para cenários em que um SLA não é necessário, a oferta básica pode ser apropriada.

O mapeamento dos tamanhos de cache e largura de banda é feito de acordo com, aproximadamente, os tamanhos e largura de banda das máquinas virtuais que hospedam o cache. Os tamanhos de 250 MB para as ofertas básica e padrão são hospedados no tamanho da máquina virtual Extra pequeno (A0), que é hospedado usando núcleos compartilhados, enquanto os outros tamanhos são hospedados usando núcleos dedicados. Os tamanhos de cache de 1 GB são hospedados no tamanho da máquina virtual Pequeno (A1), que tem 1 núcleo virtual dedicado que é usado para atender a ambos o sistema operacional e o cache redis. Tamanhos de cache maiores são hospedados em instâncias maiores de VM, com vários núcleos virtuais dedicados.

Se o cache tem uma alta taxa de transferência, escolha o tamanho de 1 GB ou maior para que o cache esteja em execução usando núcleos dedicados. O tamanho de cache de 1 GB é hospedado em uma máquina virtual de 1 núcleo. Esse núcleo é usado para atender a ambos o sistema operacional e o cache. Caches maiores que 1 GB são executados em máquinas virtuais com vários núcleos, enquanto o cache Redis usa um núcleo dedicado que não é compartilhado com o sistema operacional.

**O Redis usa thread único**, portanto, ter mais de dois núcleos não oferece benefício adicional em relação a ter apenas dois, mas **tamanhos maiores de VM normalmente têm mais largura de banda que tamanhos menores**. Se o cliente ou servidor de cache atingir os limites de largura de banda, você receberá tempos limite no lado do cliente.

A tabela a seguir mostra os valores de largura de banda máxima observados durante o teste de vários tamanhos de Cache Redis do Azure usando `redis-benchmark.exe` em uma VM Iaas no ponto de extremidade do Cache Redis do Azure. Observe que esses valores não são garantidos e não há nenhum SLA para esses números, mas eles devem ser típicos. Você deve realizar teste de carga em seu próprio aplicativo para determinar o tamanho de cache certo para ele.

Nome do cache|Tamanho do cache|Get/s (chamadas GET simples com valores de 1 KB)|Largura de banda (MBits/s)
---|---|---|---
C0|250 MB|610|5
C1|1 GB|12\.200|100
C2|2,5 GB|24\.300|200
C3|6 GB|48\.875|400
C4|13 GB|61\.350|500
C5|26 GB|112\.275|1000
C6|53 GB|153\.219|1\.000 +

Para obter instruções sobre como baixar as ferramentas do Redis como `redis-benchmark.exe`, consulte a seção [Como posso executar comandos do Redis?](#cache-commands)

<a name="cache-region"></a>
## Em que região posso localizar meu cache?

Para melhor desempenho e menor latência, localize o Cache Redis do Azure na mesma região que o aplicativo cliente de cache.

<a name="cache-billing"></a>
## Como eu sou cobrado pelo Cache Redis do Azure?

O preço do Cache Redis do Azure está [aqui](http://azure.microsoft.com/pricing/details/cache/). A página de preços lista os preços com uma taxa horária. Os caches são cobrados em uma base por minuto do momento em que o cache é criado até o momento em que um cache é excluído. Não há nenhuma opção para parar ou pausar a cobrança de um cache.

<a name="cache-timeouts"></a>
## Por que vejo tempos limite?

Tempos limite ocorrem no cliente que você usa para se comunicar com o Redis. Na maior parte dos casos, o servidor Redis não atinge o tempo limite. Quando um comando é enviado ao servidor Redis, o comando é colocado na fila e o servidor Redis, eventualmente, seleciona o comando e o executa. No entanto, o cliente pode atingir o tempo limite durante este processo e, se ele faz isso, uma exceção é gerada no lado que realiza a chamada. Para obter mais informações sobre como solucionar problemas de tempo limite, consulte [investigando exceções de tempo limite em StackExchange.Redis para o Azure Redis Cache](http://azure.microsoft.com/blog/2015/02/10/investigating-timeout-exceptions-in-stackexchange-redis-for-azure-redis-cache/).

<a name="cache-monitor"></a>
## Como monitorar a integridade e o desempenho do meu cache?

As instâncias do Cache Redis do Microsoft Azure podem ser monitoradas no [Portal de visualização do Azure](https://portal.azure.com). Você pode exibir métricas, fixar gráficos de métricas ao quadro inicial, personalizar o intervalo de data e hora de gráficos de monitoramento, adicionar/remover as métricas de gráficos e definir alertas quando determinadas condições forem atendidas. Essas ferramentas permitem monitorar a integridade de suas instâncias de Cache Redis do Azure e ajudá-lo a gerenciar seus aplicativos de cache. Para obter mais informações sobre como monitorar seus caches, consulte [Monitorar o Cache Redis do Azure](https://msdn.microsoft.com/library/azure/dn763945.aspx).

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

-	[Habilite o diagnóstico de cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para que você possa [monitorar](https://msdn.microsoft.com/library/azure/dn763945.aspx) a integridade do cache. Você pode exibir as métricas no portal e também pode [baixá-las e analisá-las](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando as ferramentas de sua escolha.
-	Você pode usar o redis-benchmark.exe para testar a carga em seu servidor do Redis.
	-	Certifique-se que o cliente e o cache Redis de teste de carga estejam na mesma região.
-	Use o redis cli.exe e monitore o cache usando o comando INFO.
	-	Se sua carga está causando um nível elevado de fragmentação de memória, você deve escalar verticalmente para um tamanho de cache maior.
-	Para obter instruções sobre como baixar as ferramentas do Redis, consulte a seção [Como posso executar comandos do Redis?](#cache-commands)

<a name="cache-commands"></a>
## Como posso executar comandos do Redis?

Você pode usar qualquer um dos comandos listados em [Comandos Redis](http://redis.io/commands#), exceto os comandos listados em [Comandos Redis sem suporte no Cache Redis do Azure](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Para executar comandos Redis, há várias opções.

-	Se você tiver um cache Standard, poderá executar comandos Redis usando o [Console Redis](cache-configure.md#redis-console). Isso fornece uma maneira segura para executar comandos do Redis no portal do Azure.
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

<!---HONumber=August15_HO6-->