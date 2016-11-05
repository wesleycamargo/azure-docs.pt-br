---
title: SDK e API Java para o Banco de Dados de Documentos | Microsoft Docs
description: Saiba tudo sobre o SDK e a API Java, incluindo as datas de lançamento, as datas de desativação e as alterações feitas entre cada versão do SDK do Java para o Banco de Dados de Documentos.
services: documentdb
documentationcenter: java
author: rnagpal
manager: jhubbard
editor: cgronlun

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/09/2016
ms.author: rnagpal

---
# SDKs e APIs de Banco de Dados de Documentos
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://go.microsoft.com/fwlink/?LinkId=402413)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

## SDK e API Java para o Banco de Dados de Documentos
<table>

<tr><td>***Baixe o SDK***</td><td>[Maven](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)</td></tr>

<tr><td>**Documentação da API**</td><td>[Documentação de referência de API Java](http://azure.github.io/azure-documentdb-java/)</td></tr>

<tr><td>**Contribuir para o SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>

<tr><td>**Introdução**</td><td>[Introdução ao SDK do Java](documentos-java-application.md)</td></tr>

<tr><td>**Tempo de execução atual com suporte**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## Notas de versão
### <a name="1.8.1"/>[1\.8.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.1)
* Um bug foi corrigido em PartitionKeyDefinitionMap para armazenar as coleções de partição única em cache e para não fazer solicitações extras de chave de partição de busca.
* Um bug foi corrigido para não tentar novamente quando um valor de chave de partição incorreto for fornecido.

### <a name="1.8.0"/>[1\.8.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.0)
* Suporte adicionado para contas de banco de dados de várias regiões.
* Suporte adicionado para repetição automática em solicitações limitadas, com opções para personalizar o número máximo de repetições e o tempo de espera máximo de repetição. Consulte RetryOptions e ConnectionPolicy.getRetryOptions().
* IPartitionResolver preterido com base no código de particionamento personalizado. Use coleções particionadas para uma taxa de transferência e armazenamento superiores.

### <a name="1.7.1"/>[1\.7.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.1)
* Adicionado suporte à política de repetição para limitação.

### <a name="1.7.0"/>[1\.7.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.0)
* Adicionado suporte a TTL (vida útil) para documentos.

### <a name="1.6.0"/>[1\.6.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.6.0)
* Implementação de [coleções particionadas](documentdb-partition-data.md) e [níveis de desempenho definidos pelo usuário](documentdb-performance-levels.md).

### <a name="1.5.1"/>[1\.5.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.1)
* Foi corrigido um bug no HashPartitionResolver para gerar valores de hash em little-endian para ser consistente com outros SDKs.

### <a name="1.5.0"/>[1\.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
* Adicionar resolvedores de hash e intervalo para ajudar com a fragmentação de arquivos em várias partições.

### <a name="1.4.0"/>[1\.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
* Implementar o Upsert. Novos métodos upsertXXX adicionados para dar suporte ao recurso Upsert.
* Implementar o roteamento com base em ID. Nenhuma alteração pública de API, todas as alterações são internas.

### <a name="1.3.0"/>1.3.0
* Versão ignorada para alinhar os números de versão com outros SDKs

### <a name="1.2.0"/>[1\.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
* Oferece suporte ao Índice Geoespacial.
* Valida a propriedade de ID de todos os recursos. As IDs de recursos não podem conter caracteres ?, /, #, \\ ou terminar com um espaço.
* Adiciona o novo cabeçalho "andamento de transformação do índice" ao ResourceResponse.

### <a name="1.1.0"/>[1\.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
* Implementa a política de indexação V2

### <a name="1.0.0"/>[1\.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
* SDK DO GA

## Datas de lançamento e desativação
A Microsoft fornecerá uma notificação pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente.

As solicitações feitas ao Banco de Dados de Documentos usando um SDK obsoleto serão rejeitadas pelo serviço.

> [!WARNING]
> Todas as versões do SDK do Banco de Dados de Documentos do Azure para Java anteriores à versão **1.0.0** serão desativadas em **29 de fevereiro de 2016**.
> 
> 

<br/>

| Versão | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [1.8.1](#1.8.1) |30 de junho de 2016 |--- |
| [1.8.0](#1.8.0) |14 de junho de 2016 |--- |
| [1.7.1](#1.7.1) |30 de abril de 2016 |--- |
| [1.7.0](#1.7.0) |27 de abril de 2016 |--- |
| [1.6.0](#1.6.0) |29 de março de 2016 |--- |
| [1.5.1](#1.5.1) |10 de dezembro de 2015 |--- |
| [1\.5.0](#1.5.0) |04 de dezembro de 2015 |--- |
| [1\.4.0](#1.4.0) |05 de outubro de 2015 |--- |
| [1\.3.0](#1.3.0) |05 de outubro de 2015 |--- |
| [1\.2.0](#1.2.0) |05 de agosto de 2015 |--- |
| [1\.1.0](#1.1.0) |09 de julho de 2015 |--- |
| [1\.0.1](#1.0.1) |12 de maio de 2015 |--- |
| [1\.0.0](#1.0.0) |07 de abril de 2015 |--- |
| 0.9.5-prelease |09 de março de 2015 |29 de fevereiro de 2016 |
| 0.9.4-prelease |17 de fevereiro de 2015 |29 de fevereiro de 2016 |
| 0.9.3-prelease |13 de janeiro de 2015 |29 de fevereiro de 2016 |
| 0.9.2-prelease |19 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.1-prelease |19 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.0-prelease |10 de dezembro de 2014 |29 de fevereiro de 2016 |

## Perguntas frequentes
[!INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Consulte também
Para saber mais sobre o Banco de Dados de Documentos, confira a página de serviço do [Banco de Dados de Documentos do Microsoft Azure](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0810_2016-->
