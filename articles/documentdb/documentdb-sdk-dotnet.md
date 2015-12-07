<properties 
	pageTitle="SDK do .NET para o Banco de Dados de Documentos | Microsoft Azure" 
	description="Saiba tudo sobre o SDK do .NET, incluindo as datas de lançamento, as datas de desativação e as alterações feitas entre cada versão do SDK do .NET para o Banco de Dados de Documentos." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="ryancraw"/>

# SDK do Banco de Dados de Documentos

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##SDK do .NET para o Banco de Dados de Documentos

<table> <tr><td>**Download**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr> <tr><td>**Documentação**</td><td>[Documentação de referência do SDK do .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr> <tr><td>**Exemplos**</td><td>[Exemplos de código do .NET](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)</td></tr> <tr><td>**Introdução**</td><td>[Introdução ao SDK do .NET para o Banco de Dados de Documentos](documentdb-get-started.md)</td></tr> <tr><td>**Framework com suporte atualmente**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr> </table></br>

## Notas de versão

### <a name="1.5.1"/>[1\.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[Corrigido]** Se Select não fosse a última expressão, o provedor LINQ assumiria que não havia projeção e produziria SELECT * incorretamente. [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="1.5.0"/>[1\.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - Implementação de Upsert, adição de métodos UpsertXXXAsync
 - Melhorias de desempenho de todas as solicitações
 - Suporte ao provedor de LINQ para os métodos conditional, coalesce e CompareTo em cadeias de caracteres
 - **[Corrigido]** Provedor LINQ --> Implementa o método Contains em List para gerar o mesmo SQL de IEnumerable e Array
 - **[Corrigido]** BackoffRetryUtility usa o mesmo HttpRequestMessage novamente em vez de criar um novo em nova tentativa
 - **[Obsoleto]** UriFactory.CreateCollection--> agora deve usar UriFactory.CreateDocumentCollection
 
### <a name="1.4.1"/>[1\.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[Corrigido]** Problemas de localização ao usar informações de cultura sem ser em inglês, como nl-NL, etc. 
 
### <a name="1.4.0"/>[1\.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - Roteamento baseado em ID
    - Novo auxiliar UriFactory para ajudá-lo a construir links para recursos baseados em identificação
    - Novas sobrecargas em DocumentClient para receber URI
  - Adicionado IsValid() e IsValidDetailed () em LINQ para geoespaciais
  - Suporte ao provedor de LINQ aprimorado
    - **Math** - Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate
    - **String** - Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper
    - **Array** - Concat, Contains, Count
    - Operador **IN**

### <a name="1.3.0"/>[1\.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - Suporte adicionado para modificar políticas de indexação
    - Novo método ReplaceDocumentCollectionAsync no DocumentClient
    - Nova propriedade IndexTransformationProgress em ResourceResponse<T> para controlar o progresso em porcentagem de alterações na política de índice
    - DocumentCollection.IndexingPolicy agora é mutável
  - Suporte adicionado para consulta e indexação espacial
    - Novo namespace Microsoft.Azure.Documents.Spatial para serializar/desserializar tipos espaciais, como Ponto e Polígono
    - Nova classe SpatialIndex para indexação de dados GeoJSON armazenados no Banco de Dados de Documentos
  - **[Corrigido]** : consulta SQL incorreta gerada usando expressão linq [#38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="1.2.0"/>[1\.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- Dependência de Newtonsoft.Json v5.0.7 
- Alterações para dar suporte a Order By
  - Suporte ao provedor de LINQ para OrderBy() ou OrderByDescending()
  - IndexingPolicy para dar suporte a Order By 
  
		**NB: Possible breaking change** 
  
    	If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="1.1.0"/>[1\.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- Suporte para dados de particionamento usando as novas classes HashPartitionResolver e RangePartitionResolver e o IPartitionResolver
- Serialização DataContract
- Suporte a GUID no provedor de LINQ
- Suporte a UDF no LINQ

### <a name="1.0.0"/>[1\.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- SDK DO GA

> [AZURE.NOTE]Houve uma alteração de nome do pacote NuGet entre a visualização e a GA. Mudamos de **Microsoft.Azure.Documents.Client** para **Microsoft.Azure.DocumentDB** <br/>


### <a name="0.9.x-preview"/>[0\.9.x-preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- SDKs de visualização [Obsoleto]

## Datas de lançamento e desativação
A Microsoft fornecerá uma notificação pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente.

Qualquer solicitação feita ao Banco de Dados de Documentos usando um SDK obsoleto será rejeitada pelo serviço.

> [AZURE.WARNING]Todas as versões do SDK do Banco de Dados de Documentos do Azure para .NET anteriores à versão **1.0.0** serão desativadas em **29 de fevereiro de 2016**.
 
<br/>
 
| Versão | Data do lançamento | Data de desativação 
| ---	  | ---	         | ---
| [1\.5.1](#1.5.1) | 23 de novembro de 2015 |--| [1\.5.0](#1.5.0) | 5 de outubro de 2015 |--| [1\.4.1](#1.4.1) | 25 de agosto de 2015 |--| [1\.4.0](#1.4.0) | 13 de agosto de 2015 |--| [1\.3.0](#1.3.0) | 5 de agosto de 2015 |--| [1\.2.0](#1.2.0) | 6 de julho de 2015 |--| [1\.1.0](#1.1.0) | 30 de abril de 2015 |--| [1\.0.0](#1.0.0) | 8 de abril de 2015 |--| [0\.9.3-pré-lançamento](#0.9.x-preview) | 12 de março de 2015 | 29 de fevereiro de 2016 | [0\.9.2-pré-lançamento](#0.9.x-preview) | Janeiro de 2015 | 29 de fevereiro de 2016 | [.9.1-pré-lançamento](#0.9.x-preview) | 13 de outubro de 2014 | 29 de fevereiro de 2016 | [0\.9.0-pré-lançamento](#0.9.x-preview) | 21 de agosto de 2014 | 29 de fevereiro de 2016

## Perguntas frequentes
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Consulte também

Para saber mais sobre o Banco de Dados de Documentos, confira a página de serviço do [Banco de Dados de Documentos do Microsoft Azure](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_1125_2015-->