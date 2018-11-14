---
title: 'Azure Cosmos DB: API Java Assíncrona SQL, SDK e recursos| Microsoft Docs'
description: Saiba tudo sobre o SDK e a API Java Assíncrona SQL, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK Java Assíncrono SQL do Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/02/2018
ms.author: moderakh
ms.openlocfilehash: 80beeda607c83bc2a583c140779766952efcc568
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51252863"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>SDK Java Assíncrono SQL do Azure Cosmos DB para a API SQL: notas de versão e recursos
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed de alterações do .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provedor de recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

O SDK Java Assíncrono de API SQL difere do SDK Java de API SQL fornecendo operações assíncronas com o suporte da [biblioteca Netty](http://netty.io/). O [SDK Java de API SQL](sql-api-sdk-java.md) preexistente não oferece suporte a operações assíncronas. 

<table>

<tr><td>**Baixe o SDK**</td><td>[Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**Documentação da API**</td><td>[Documentação de referência de API Java](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client?view=azure-java-stable)</td></tr>

<tr><td>**Contribuir para o SDK**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Introdução**</td><td>[Introdução ao SDK Java Assíncrono](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**Exemplo de código**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Dicas de desempenho**</td><td>[Leiame do GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Tempo de execução mínimo com suporte**</td><td>[JDK 8](https://aka.ms/azure-jdks)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de versão

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Corrigido um bug NPE no caminho de nova tentativa de gravação.
* Corrigido um bug NPE no gerenciamento de ponto de extremidade.
* Dependências vulneráveis atualizadas ([github #68](https://github.com/Azure/azure-cosmosdb-java/issues/68)).
* Adicionado suporte para o log de rede Netty para solução de problemas.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Adicionado suporte para gravação em várias regiões.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Adicionado suporte para Proxy.
* Adicionado suporte para autorização de token de recurso.
* Corrigido um bug no tratamento de chaves de partição grande ([github #63](https://github.com/Azure/azure-cosmosdb-java/issues/63)).
* Documentação aprimorada.
* SDK reestruturado em módulos mais granulares.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Corrigido um bug para localidades que não são de língua inglesa ([github #51](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* Adicionado métodos auxiliares no Recurso de Conflito.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Substituição da dependência org.json por jackson por motivos de desempenho e licenciamento ([github nº 29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
* Remoção da classe OfferV2 preterida.
* Adição de método de acessador à classe Offer para taxa de transferência de conteúdo.
* Qualquer método no Documentos/Recurso que retorne tipos org.json foram alterados para retornar um tipo de objeto jackson.
* Método getObject(.) de classes que estendem JsonSerializable alterado para retornar um tipo ObjectNode jackson.
* Método getCollection(.) alterado para retornar a coleção de ObjectNode.
* Remoção de construtores das subclasses JsonSerializable com argumento org.json.JSONObject.
* Agora, JsonSerializable.toJson (SerializationFormattingPolicy.Indented) usa dois espaços para recuo.
  
### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* Suporte adicionado para Política de Índice Exclusivo.
* Suporte adicionado para limitação do tamanho do token de continuação do resposta em opções de feed.
* Suporte adicionado para a Divisão de Partição na consulta de Partição Cruzada.
* Correção de bug na Serialização de carimbo de data/hora Json ([github #32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Correção de bug na serialização enum Json.
* Correção de bug no gerenciamento de documentos de tamanho de 2MB ([github 33 #](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Dependência com.fasterxml.jackson.core:jackson-databind atualizado para 2.9.5 devido a um bug ([jackson databind: github 1599 #](https://github.com/FasterXML/jackson-databind/issues/1599))
* Dependência rxjava-extras atualizado para 0.8.0.17 devido a um bug ([rxjava extras: github 30 #](https://github.com/davidmoten/rxjava-extras/issues/30)).
* A descrição de metadados no arquivo pom atualizada para estar embutido com o restante da documentação.
* Aperfeiçoamento de sintaxe ([github #41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([github 40 #](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Suporte de pressão contrária adicionado na consulta.
* Suporte adicionado para ID de intervalo de chaves de partição na consulta.
* Correção para permitir token de continuação maior no cabeçalho de solicitação (bugfix github #24).
* Dependência netty atualizada para 4.1.22.Final para garantir que a JVM será desligada após a conclusão do thread principal.
* Correção para evitar transmitir o token de sessão ao ler recursos mestres.
* Mais exemplos adicionados.
* Mais cenários de parâmetros de comparação adicionados.
* Arquivos de cabeçalho de Java fixos para a geração de java doc apropriada.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK de GA com suporte de ponta a ponta para E/S sem bloqueio usando a [biblioteca Netty](http://netty.io/) no modo de gateway. 

## <a name="release-and-retirement-dates"></a>Datas de lançamento e desativação
A Microsoft fornecerá uma notificação pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Novos recursos e funcionalidade e otimizações são adicionados apenas ao SDK atual. Portanto, é recomendável que você sempre atualize para a versão mais recente do SDK o quanto antes.

Qualquer solicitação feita ao Cosmos DB com o uso de um SDK desativado será rejeitada pelo serviço.

<br/>

| Versão | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [2.2.1](#2.1.0) |2 de Nov, 2018|--- |
| [2.2.0](#2.2.1) |22 de Setembro, 2018|--- |
| [2.1.0](#2.1.0) |5 de setembro de 2018|--- |
| [2.0.1](#2.0.1) |16 de agosto de 2018|--- |
| [2.0.0](#2.0.0) |20 de junho de 2018|--- |
| [1.0.2](#1.0.2) |18 de maio de 2018|--- |
| [1.0.1](#1.0.1) |20 de abril de 2018|--- |
| [1.0.0](#1.0.0) |27 de fevereiro de 2018|--- |

## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

