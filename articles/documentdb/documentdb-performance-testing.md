<properties 
	pageTitle="Testes de desempenho e escalabilidade do Banco de Dados de Documentos | Microsoft Azure" 
	description="Aprenda a executar testes de desempenho e escalabilidade com o Banco de Dados de Documentos do Azure"
	keywords="testes de desempenho"
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/20/2016" 
	ms.author="arramac"/>

# Teste de desempenho e escalabilidade com o Banco de Dados de Documentos do Azure
O teste de desempenho e escalabilidade é uma etapa importante no desenvolvimento de aplicativos. Para muitos aplicativos, a camada de banco de dados tem um impacto significativo sobre o desempenho e a escalabilidade geral e, portanto, é um componente essencial do teste de desempenho. O [Banco de Dados de Documentos](https://azure.microsoft.com/services/documentdb/) foi desenvolvido para fins de escala elástica e desempenho previsível e, portanto, é uma excelente opção para aplicativos que precisam de uma camada de banco de dados de alto desempenho.

Este artigo é uma referência para desenvolvedores implementarem conjuntos de testes de desempenho para suas cargas de trabalho de Banco de Dados de Documentos ou avaliarem Banco de Dados de Documentos para cenários de aplicativos de alto desempenho. Ele se concentra principalmente nos testes de desempenho de banco de dados isolados, mas também inclui práticas recomendadas para aplicativos de produção.

Depois de ler este artigo, você poderá responder as seguintes perguntas:

- Onde posso encontrar um aplicativo cliente do .NET de exemplo para testar o desempenho do Banco de Dados de Documentos do Azure?
- Quais são os principais fatores que afetam o desempenho de ponta a ponta das solicitações feitas ao Banco de Dados de Documentos do Azure? 
- Como posso obter altos níveis de taxa de transferência com o Banco de Dados de Documentos do Azure do meu aplicativo cliente?

Para começar com o código, baixe o projeto do [Exemplo de teste de desempenho do Banco de Dados de Documentos](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark).

## Principais opções de configuração
O Banco de Dados de Documentos é um banco de dados distribuído rápido e flexível que pode ser dimensionado perfeitamente com garantia de latência e taxa de transferência. Você não precisa fazer grandes alterações de arquitetura ou escrever códigos complexos para dimensionar a camada de banco de dados com o Banco de Dados de Documentos. Escalar e reduzir verticalmente é tão fácil quanto efetuar uma única chamada à API ou chamada de método do SDK. No entanto, ao testar em grande escala, é importante observar que o Banco de Dados de Documentos é acessado por meio de chamadas de rede. Se você estiver escrevendo um aplicativo cliente autônomo para testar o desempenho do Banco de Dados de Documentos, precisará configurá-lo adequadamente para combater o impacto da latência de rede nas suas medições de desempenho.

Para obter o melhor desempenho de ponta a ponta com o Banco de Dados de Documentos, considere as seguintes opções de configuração de cliente:

- **Aumentar o número de threads/tarefas**: como as chamadas ao Banco de Dados de Documentos são feitas pela rede, talvez seja necessário variar o grau de paralelismo de suas solicitações para que o aplicativo cliente perca muito pouco tempo de espera entre as solicitações. Por exemplo, se você estiver usando a [biblioteca de paralelismo de tarefas](https://msdn.microsoft.com//library/dd460717.aspx) do .NET, crie centenas de tarefas de leitura ou gravação no Banco de Dados de Documentos.
- **Teste na mesma região do Azure**: quando possível, teste de uma máquina virtual ou Serviço de Aplicativo implantado na mesma região do Azure. Para obter uma comparação aproximada, chamadas ao Banco de Dados de Documentos na mesma região são concluídos de 1 a 2 ms, mas a latência entre a Costa Leste e a Oeste dos EUA é > 50 ms.
- **Aumentar conexões máximas do System.Net por host**: as solicitações do Banco de Dados de Documentos são feitas por HTTPS/REST por padrão e estão sujeitas aos limites de conexão padrão por nome do host ou endereço IP. Talvez seja necessário definir isso como um valor mais alto (100-1000) para que a biblioteca de cliente possa utilizar várias conexões simultâneas ao Banco de Dados de Documentos. No .NET, isso é [Defaultconnectionlimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx).
- **Ativar GC no lado do servidor**: a redução da frequência da coleta de lixo pode ajudar em alguns casos. No .NET, defina [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) como true.
- **Usar conectividade direta com protocolo TCP**: use [Conectividade direta](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionmode.aspx) com [protocolo TCP](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.protocol.aspx) para um melhor desempenho. 
- **Retirada de implementação em intervalos de RetryAfter**: durante o teste de desempenho, você deve aumentar a carga até que uma pequena taxa de solicitações seja restringida. Se restringida, o aplicativo cliente deve retirar a limitação no intervalo de nova tentativa do servidor especificado. Isso garante que você perca o mínimo de tempo de espera entre as tentativas. Confira [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
- **Escalar sua carga de trabalho de cliente horizontalmente**: se você estiver testando em altos níveis de taxa de transferência (> 50.000 RU/s), o aplicativo cliente poderá fazer um afunilamento devido à limitação da máquina na utilização da CPU ou de rede. Se você chegar a este ponto, poderá continuar a forçar a conta do Banco de Dados de Documentos escalando seus aplicativos clientes horizontalmente entre vários servidores.

## Introdução
A maneira mais rápida de começar é compilar e executar o exemplo do .NET abaixo, conforme descrito nas etapas abaixo. Você também pode examinar o código-fonte e implementar configurações semelhantes no seus aplicativos clientes.

**Etapa 1:** baixar o projeto do [Exemplo de teste de desempenho do Banco de Dados de Documentos](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark) ou dividir o repositório Github.

**Etapa 2:** modificar as configurações de EndpointUrl, AuthorizationKey, CollectionThroughput e DocumentTemplate (opcional) em App. config.

> [AZURE.NOTE] Antes de provisionar coleções com alta taxa de transferência, confira a [página de preços](https://azure.microsoft.com/pricing/details/documentdb/) para estimar os custos por coleção. O Banco de Dados de Documentos cobra o armazenamento e a taxa de transferência de forma independente e por hora. Portanto, você pode salvar os custos excluindo ou reduzindo a produtividade de suas coleções do Banco de Dados de Documentos depois do teste.

**Etapa 3:** compilar e executar o aplicativo de console na linha de comando. Você verá algo semelhante ao mostrado a seguir:

	Summary:
	---------------------------------------------------------------------
	Endpoint: https://docdb-scale-demo.documents.azure.com:443/
	Collection : db.testdata at 50000 request units per second
	Document Template*: Player.json
	Degree of parallelism*: 500
	---------------------------------------------------------------------

	DocumentDBBenchmark starting...
	Creating database db
	Creating collection testdata
	Creating metric collection metrics
	Retrying after sleeping for 00:03:34.1720000
	Starting Inserts with 500 tasks
	Inserted 661 docs @ 656 writes/s, 6860 RU/s (18B max monthly 1KB reads)
	Inserted 6505 docs @ 2668 writes/s, 27962 RU/s (72B max monthly 1KB reads)
	Inserted 11756 docs @ 3240 writes/s, 33957 RU/s (88B max monthly 1KB reads)
	Inserted 17076 docs @ 3590 writes/s, 37627 RU/s (98B max monthly 1KB reads)
	Inserted 22106 docs @ 3748 writes/s, 39281 RU/s (102B max monthly 1KB reads)
	Inserted 28430 docs @ 3902 writes/s, 40897 RU/s (106B max monthly 1KB reads)
	Inserted 33492 docs @ 3928 writes/s, 41168 RU/s (107B max monthly 1KB reads)
	Inserted 38392 docs @ 3963 writes/s, 41528 RU/s (108B max monthly 1KB reads)
	Inserted 43371 docs @ 4012 writes/s, 42051 RU/s (109B max monthly 1KB reads)
	Inserted 48477 docs @ 4035 writes/s, 42282 RU/s (110B max monthly 1KB reads)
	Inserted 53845 docs @ 4088 writes/s, 42845 RU/s (111B max monthly 1KB reads)
	Inserted 59267 docs @ 4138 writes/s, 43364 RU/s (112B max monthly 1KB reads)
	Inserted 64703 docs @ 4197 writes/s, 43981 RU/s (114B max monthly 1KB reads)
	Inserted 70428 docs @ 4216 writes/s, 44181 RU/s (115B max monthly 1KB reads)
	Inserted 75868 docs @ 4247 writes/s, 44505 RU/s (115B max monthly 1KB reads)
	Inserted 81571 docs @ 4280 writes/s, 44852 RU/s (116B max monthly 1KB reads)
	Inserted 86271 docs @ 4273 writes/s, 44783 RU/s (116B max monthly 1KB reads)
	Inserted 91993 docs @ 4299 writes/s, 45056 RU/s (117B max monthly 1KB reads)
	Inserted 97469 docs @ 4292 writes/s, 44984 RU/s (117B max monthly 1KB reads)
	Inserted 99736 docs @ 4192 writes/s, 43930 RU/s (114B max monthly 1KB reads)
	Inserted 99997 docs @ 4013 writes/s, 42051 RU/s (109B max monthly 1KB reads)
	Inserted 100000 docs @ 3846 writes/s, 40304 RU/s (104B max monthly 1KB reads)

	Summary:
	---------------------------------------------------------------------
	Inserted 100000 docs @ 3834 writes/s, 40180 RU/s (104B max monthly 1KB reads)
	---------------------------------------------------------------------
	DocumentDBBenchmark completed successfully.


**Etapa 4 (se necessário):** a taxa de transferência relatada (RU/s) da ferramenta deve ser igual ou maior do que a taxa de transferência provisionada da coleção. Caso contrário, o aumento do DegreeOfParallelism em pequenos incrementos pode ajudá-lo a atingir o limite. Se a taxa de transferência do seu aplicativo cliente estagnar, iniciar várias instâncias do aplicativo nas mesmas máquinas ou em máquinas diferentes ajudará você atingir o limite configurado em instâncias diferentes. Se você precisar de ajuda com esta etapa, entre em contato conosco em [Pergunte ao Banco de Dados de Documentos](askdocdb@microsoft.com) ou preencha um tíquete de suporte.

Uma vez que o aplicativo estiver em execução, você poderá experimentar [políticas de indexação](documentdb-indexing-policies.md) e [níveis de consistência](documentdb-consistency-levels.md) diferentes para entender seu impacto na taxa de transferência e na latência. Você também pode examinar o código-fonte e implementar configurações semelhantes no seus pacotes de teste ou aplicativos de produção.

## Resumo
Neste artigo, nós examinamos como você pode executar testes de desempenho e escalabilidade com o Banco de Dados de Documentos usando um aplicativo de console do .NET e as principais opções de configuração revisadas para obter o melhor desempenho do Banco de Dados de Documentos do Azure. Consulte os links abaixo para saber mais sobre como trabalhar com o Banco de Dados de Documentos.

* [Exemplo de teste de desempenho do Banco de Dados de Documentos](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Particionamento do lado servidor no Banco de Dados de Documentos](documentdb-partition-data.md)
* [Coleções e níveis de desempenho do Banco de Dados de Documentos](documentdb-performance-levels.md)
* [Documentação do SDK .NET do Banco de Dados de Documentos no MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Amostras do .NET do Banco de Dados de Documentos](https://github.com/Azure/azure-documentdb-net)
* [Blog do Banco de Dados de Documentos sobre dicas de desempenho](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)

<!---HONumber=AcomDC_0525_2016-->