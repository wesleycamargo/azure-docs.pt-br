---
title: Diagnosticar e solucionar problemas do SDK do Java Assíncrono do Azure Cosmos DB| Microsoft Docs
description: Use recursos como registro em log do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e solucionar problemas do Azure Cosmos DB.
services: cosmos-db
author: moderakh
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 10/28/2018
ms.author: moderakh
ms.devlang: java
ms.component: cosmosdb-sql
ms.openlocfilehash: 74813634aad95f163b06717521bb2c746ac3df6b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238822"
---
# <a name="troubleshooting-issues-when-using-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Solução de problemas ao usar o SDK do Java Assíncrono com contas de API do SQL do Azure Cosmos DB
Este artigo aborda problemas comuns, soluções alternativas, etapas de diagnóstico e ferramentas ao usar o [ADK do Async Java](sql-api-sdk-async-java.md) com contas da API do SQL do Azure Cosmos DB.
O SDK do Java Assíncrono fornece uma representação lógica do lado do cliente para acessar a API do SQL do Azure Cosmos DB. Este artigo descreve as ferramentas e as abordagens para ajudá-lo se você tiver algum problema.

Comece com esta lista:
    * Dê uma olhada na seção [Problemas comuns e soluções alternativas] neste artigo.
    * Nosso SDK é de [software livre no github](https://github.com/Azure/azure-cosmosdb-java) e temos uma [seção de problemas](https://github.com/Azure/azure-cosmosdb-java/issues) que monitoramos ativamente. Verifique se você encontra algum problema semelhante com uma solução alternativa já arquivada.
    * Examine [dicas de desempenho](performance-tips-async-java.md) e siga as práticas sugeridas.
    * Acompanhe o restante deste artigo. Se você não encontrar uma solução, registre um [Problema do GitHub](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-workarounds"></a>Problemas comuns e soluções alternativas

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problemas de rede, falha de tempo limite do Netty, baixa taxa de transferência, alta latência de leitura

#### <a name="general-suggestions"></a>Sugestões gerais
* Verifique se que o aplicativo está em execução na mesma região que sua conta do Cosmos DB. 
* Verifique o uso da CPU no host em que o aplicativo está sendo executado. Se o uso da CPU for de 90% ou mais, considere a execução de seu aplicativo em um host com a configuração mais alta ou distribuir a carga em mais computadores.

#### <a name="connection-throttling"></a>Limitação de conexão
A limitação de conexão pode ocorrer devido a um [Limite de conexão no computador host] ou a [Esgotamento da porta SNAT (PAT) do Azure]:

##### <a name="connection-limit-on-host"></a>Limite de conexão no computador host
Alguns sistemas Linux (como "Red Hat") têm um limite superior para o número total de arquivos abertos. Soquetes no Linux são implementados como arquivos, portanto, esse número limita o número total de conexões também.
Execute o comando a seguir:

```bash
ulimit -a
```
O número de arquivos abertos ("nofile") precisa ser grande o suficiente (pelo menos o dobro do tamanho do pool de conexão). Leia mais detalhes em [dicas de desempenho](performance-tips-async-java.md).

##### <a name="snat"></a>Esgotamento da porta SNAT (PAT) do Azure

Se o seu aplicativo for implantado na VM do Azure sem um endereço IP público, por padrão, as [portas SNAT do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) serão usadas para estabelecer conexões com qualquer ponto de extremidade fora de sua VM. O número de conexões permitidas da VM para o ponto de extremidade do Cosmos DB é limitado pela [configuração do Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

As portas SNAT do Azure são usadas somente quando sua VM do Azure tem um endereço IP privado e um processo da VM tenta estabelecer uma conexão com um endereço IP público. Portanto, há duas soluções alternativas para evitar a limitação de SNAT do Azure:
    * Adicione o ponto de extremidade de serviço do Azure Cosmos DB à sub-rede da sua VNET de VM do Azure conforme explicado em [Como habilitar o Ponto de Extremidade de Serviço da VNET](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Quando o ponto de extremidade de serviço é habilitado, as solicitações não são mais enviadas de um IP público para o Cosmos DB e, em vez disso, a identidade da VNET e da sub-rede é enviada. Essa alteração poderá resultar em quedas de firewall se apenas IPs públicos forem permitidos. Se você estiver usando o firewall, ao habilitar o ponto de extremidade de serviço, adicione a sub-rede ao firewall usando [ACLs da VNET](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
    * Atribua um IP público à sua VM do Azure.

#### <a name="http-proxy"></a>Proxy HTTP

Se você usar um HttpProxy, verifique se o HttpProxy pode dar suporte ao número de conexões configurado no SDK do `ConnectionPolicy`.
Caso contrário, você enfrentará problemas de conexão.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Padrão de codificação inválido: bloqueio do thread de E/S do Netty

O SDK usa a biblioteca de E/S [Netty](https://netty.io/) para se comunicar com o serviço do Azure Cosmos DB. Nós temos a API Assíncrona e podemos usar as APIs de E/S do netty sem bloqueio. O trabalho de E/S do SDK é executado em threads de netty de E/S. O número de threads de E/S netty está configurado para ser igual ao número de núcleos da CPU no computador do aplicativo. Os threads de E/S netty destinam-se somente a serem usados para o trabalho de E/S netty sem bloqueio. O SDK retorna o resultado da invocação de API em um dos threads de E/S netty para o código de aplicativos. Se o aplicativo, depois de receber os resultados no thread netty, executar uma operação de longa duração no thread netty, isso poderá resultar em o SDK não ter um número suficiente de threads de E/S para realizar seu trabalho de E/S interno. Essa codificação de aplicativo pode resultar em baixa taxa de transferência, latência alta e falhas de `io.netty.handler.timeout.ReadTimeoutException`. A solução alternativa é mudar o thread quando você sabe que a operação levará tempo.

   Por exemplo, o snippet de código a seguir mostra que, se você executar o trabalho de longa duração, que leva mais de alguns milissegundos, no thread netty, você acabará entrando em um estado em que nenhum thread netty de E/S está presente para processar o trabalho de E/S e, como resultado, obterá a ReadTimeoutException:
```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // time consuming work. For example:
                        // writing to a file, computationally heavy work, or just sleep
                        // basically anything which takes more than a few milliseconds
                        // doing such operation on the IO netty thread
                        // without a proper scheduler, will cause problems.
                        // The subscriber will get ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //will be io.netty.handler.timeout.ReadTimeoutException
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
   A solução alternativa é alterar o thread em que você executa o trabalho demorado. Defina uma instância singleton do Agendador para seu aplicativo:
   ```java
// have a singleton instance of executor and scheduler
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   Sempre que você precisar realizar trabalho demorado (por exemplo, trabalho computacionalmente pesado, bloquear E/S), mude o thread para um trabalhador fornecido pelo seu `customScheduler` usando a API `.observeOn(customScheduler)`.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // switches the thread.
        .subscribe(
            // ...
        );
```
Usando `observeOn(customScheduler)`, você libera o thread de E/S netty e muda para seu próprio thread personalizado fornecido pelo customScheduler. Essa modificação resolverá o problema e você não obterá mais a falha `io.netty.handler.timeout.ReadTimeoutException`.

### <a name="connection-pool-exhausted-issue"></a>Problema de pool de conexão esgotado

`PoolExhaustedException` é uma falha do lado do cliente. Se você recebe essa falha com frequência, isso é uma indicação de que sua carga de trabalho do aplicativo é maior do que o pool de conexão do SDK pode atender. Pode ajudar aumentar o tamanho do pool de conexão ou distribuir a carga em vários aplicativos.

### <a name="request-rate-too-large"></a>Taxa de solicitação grande demais
Essa falha é lado do servidor e indica que você consumiu sua taxa de transferência provisionada e deve tentar novamente mais tarde. Se você recebe essa falha com frequência, considere aumentar a taxa de transferência da coleção.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Falha ao conectar-se ao emulador do Azure Cosmos DB

O certificado HTTPS do emulador do Cosmos DB é autoassinado. Para o SDK funcionar com o emulador, você deve importar o certificado do emulador para Java TrustStore. Conforme explicado [aqui](local-emulator-export-ssl-certificates.md).


## <a name="enable-client-sice-logging"></a>Habilitar o log do SDK do cliente

O SDK Java assíncrono usa SLF4j como a fachada de registro em log que dá suporte ao registro em log em estruturas de log populares, como log4j, logback.

Por exemplo, se você quiser usar log4j como a estrutura de registros, adicione estas bibliotecas ao seu classpath de Java:

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Adicione também uma configuração de log4j:
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Examine o [manual de registro em log sfl4j](https://www.slf4j.org/manual.html) para obter mais informações.

## <a name="netstats"></a>Estatísticas de rede do sistema operacional
Execute o comando netstat para ter uma noção de quantas conexões estão no estado `Established`, no estado `CLOSE_WAIT`, etc.

No Linux, você pode executar o comando a seguir:
```bash
netstat -nap
```
Filtre o resultado somente para conexões de ponto de extremidade do Cosmos DB.

Aparentemente, o número de conexões com o ponto de extremidade do Cosmos DB no estado `Established` não deve ser maior que o tamanho do pool de conexão configurado.

Se há muitas conexões para o ponto de extremidade do Cosmos DB no estado `CLOSE_WAIT`, por exemplo, mais de 1.000 conexões, isso é uma indicação de conexões estabelecidas e interrompidas rapidamente, o que pode causar problemas. Examine a seção [Problemas comuns e soluções alternativas] para obter mais detalhes.

 <!--Anchors-->
[Problemas comuns e soluções alternativas]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limite de conexão no computador host]: #connection-limit-on-host
[Esgotamento da porta SNAT (PAT) do Azure]: #snat


