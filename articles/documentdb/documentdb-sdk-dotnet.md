<properties 
	pageTitle="SDK e API .NET para o Banco de Dados de Documentos | Microsoft Azure" 
	description="Saiba tudo sobre o SDK e a API .NET, incluindo as datas de lançamento, as datas de desativação e as alterações feitas entre cada versão do SDK do .NET para o Banco de Dados de Documentos." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="rnagpal" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/24/2016" 
	ms.author="rnagpal"/>

# SDKs e APIs de Banco de Dados de Documentos 

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [REST](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## SDK e API .NET para o Banco de Dados de Documentos

<table>
<tr><td>***Baixe o SDK***</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>
<tr><td>**Documentação da API**</td><td>[Documentação de referência da API .NET] (https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>
<tr><td>**Exemplos**</td><td>[Exemplos de código do .NET] (documentdb-dotnet-samples.md)</td></tr>
<tr><td>**Introdução**</td><td>[Introdução ao SDK do .NET do Banco de Dados de Documentos](documentdb-get-started.md)</td></tr>
<tr><td>**Tutorial do aplicativo Web**</td><td>[Desenvolvimento do aplicativo Web com o Banco de Dados de Documentos] (documentdb-dotnet-application.md)</td></tr>
<tr><td>**Framework atual com suporte**</td><td>[Microsoft .NET Framework 4.5] (https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## Notas de versão

> [AZURE.IMPORTANT] Você pode receber System.NotSupportedException ao consultar coleções particionadas. Para evitar esse erro, desmarque a opção "Preferir 32 bits" na janela de propriedades do projeto, na guia Compilar.

### <a name="1.9.4"/>[1\.9.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.4)

  - Foram modificadas as classes ResourceResponse, FeedResponse, StoredProcedureResponse e MediaResponse para implementar a interface pública correspondente para que elas possam ser simuladas para a implantação orientada a testes (TDD).
  - Foi corrigido um problema que causava um cabeçalho de chave de partição malformado ao usar um objeto JsonSerializerSettings personalizado para serialização de dados.

### <a name="1.9.3"/>[1\.9.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.3)

  - Foi corrigido um problema que fazia com que consultas de execução longa falhassem com o erro: o token de autorização não é válido no momento.
  - Foi corrigido um problema que removia o SqlParameterCollection original de consultas superiores/ordenar por entre partições.

### <a name="1.9.2"/>[1\.9.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.2)

  - Adição de suporte a consultas paralelas de coleções particionadas.
  - Adição de suporte a consultas ORDER BY e TOP entre partições para coleções particionadas.
  - Correção das referências ausentes para DocumentDB.Spatial.Sql.dll e Microsoft.Azure.Documents.ServiceInterop.dll, que são obrigatórios ao fazer referência a um projeto do Banco de Dados de Documentos com uma referência para o pacote Nuget do Banco de Dados de Documentos.
  - Correção da capacidade de usar parâmetros de diferentes tipos ao usar funções definidas pelo usuário no LINQ.
  - Correção de um bug para contas globalmente replicadas em que chamadas Upsert estavam sendo direcionadas para locais de leitura em vez de locais de gravação.
  - Adição de métodos que estavam ausentes à interface IDocumentClient:
      - Método UpsertAttachmentAsync que usa mediaStream e opções como parâmetros
      - Método CreateAttachmentAsync que usa opções como um parâmetro
      - Método CreateOfferQuery que usa querySpec como um parâmetro.
  - Retirada do selo de classes públicas que são expostas na interface IDocumentClient.

### <a name="1.8.0"/>[1\.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
  - Suporte adicionado para contas de banco de dados de várias regiões.
  - Suporte adicionado para repetição de solicitações limitadas. O usuário pode personalizar o número de repetições e o tempo máximo de espera configurando a propriedade ConnectionPolicy.RetryOptions.
  - Nova interface IDocumentClient adicionada que define as assinaturas de todos os métodos e propriedades de DocumenClient. Como parte dessa alteração, também foram alterados os métodos de extensão que criam IQueryable e IOrderedQueryable para métodos na própria classe DocumentClient.
  - Opção de configuração adicionada para definir ServicePoint.ConnectionLimit de um determinado URI de ponto de extremidade do Banco de Dados de Documentos. Use ConnectionPolicy.MaxConnectionLimit para alterar o valor padrão, que é definido como 50.
  - IPartitionResolver e sua implementação foram preteridos. O suporte para IPartitionResolver agora é obsoleto. Recomendamos que você use Coleções Particionadas para taxa de transferência e armazenamento superiores.


### <a name="1.7.1"/>[1\.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
  - Adicionada uma sobrecarga ao Uri com base no método ExecuteStoredProcedureAsync que recebe RequestOptions como um parâmetro.
  
### <a name="1.7.0"/>[1\.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
  - Adicionado suporte a TTL (vida útil) para documentos.

### <a name="1.6.3"/>[1\.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
  - Correção de um bug no pacote Nuget do SDK do .NET para empacotamento como parte de uma solução do Serviço de Nuvem do Azure.
  
### <a name="1.6.2"/>[1\.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
  - Implementação de [coleções particionadas](documentdb-partition-data.md) e [níveis de desempenho definidos pelo usuário](documentdb-performance-levels.md).

### <a name="1.5.3"/>[1\.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
  - **[Corrigido]** Consultas no ponto de extremidade do Banco de Dados de Documentos geram: 'System.Net.Http.HttpRequestException: erro ao copiar o conteúdo para um fluxo.

### <a name="1.5.2"/>[1\.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
  - Expansão do suporte do LINQ, incluindo novos operadores para paginação, expressões condicionais e comparação de intervalo.
    - Operador Take para habilitar o comportamento de SELECT TOP no LINQ
    - Operador CompareTo para habilitar comparações de intervalo de cadeia de caracteres
    - Operadores condicionais (?) e de união (??)
  - **[Corrigido]** ArgumentOutOfRangeException ao combinar a projeção do Modelo com o Where-In na consulta linq. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="1.5.1"/>[1\.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[Corrigido]** Se Select não for a última expressão, o Provedor LINQ presumiu que não havia projeção e produziu SELECT * incorretamente. [Nº 58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="1.5.0"/>[1\.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - Implementação de Upsert, adição de métodos UpsertXXXAsync
 - Melhorias de desempenho de todas as solicitações
 - Suporte ao provedor de LINQ para os métodos conditional, coalesce e CompareTo em cadeias de caracteres
 - **[Corrigido]** Provedor LINQ --> Implementa o método Contains em List para gerar o mesmo SQL como em IEnumerable e Array
 - **[Corrigido]** BackoffRetryUtility usa a mesma HttpRequestMessage novamente em vez de criar uma nova em uma nova tentativa
 - **[Obsoleto]** UriFactory.CreateCollection--> agora deve usar UriFactory.CreateDocumentCollection
 
### <a name="1.4.1"/>[1\.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[Corrigido]** Problemas de localização ao usar informações de cultura em outro idioma que não o inglês, como nl-NL, etc.
 
### <a name="1.4.0"/>[1\.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - Roteamento baseado em ID
    - Novo auxiliar UriFactory para ajudá-lo a construir links para recursos baseados em identificação
    - Novas sobrecargas em DocumentClient para receber URI
  - Adicionado IsValid() e IsValidDetailed () em LINQ para geoespaciais
  - Suporte ao provedor de LINQ aprimorado
    - **Matemática** - Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan e Truncate
    - **Cadeia de caracteres** - Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString e ToUpper
    - **Array** - Concat, Contains, Count
    - Operador **IN**

### <a name="1.3.0"/>[1\.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - Suporte adicionado para modificar políticas de indexação
    - Novo método ReplaceDocumentCollectionAsync no DocumentClient
    - Nova propriedade IndexTransformationProgress em ResourceResponse<T> para acompanhar o progresso em percentual de alterações na política de índice
    - DocumentCollection.IndexingPolicy agora é mutável
  - Suporte adicionado para consulta e indexação espacial
    - Novo namespace Microsoft.Azure.Documents.Spatial para serializar/desserializar tipos espaciais, como Ponto e Polígono
    - Nova classe SpatialIndex para indexação de dados GeoJSON armazenados no Banco de Dados de Documentos
  - **[Corrigido]**: consulta SQL incorreta gerada usando a expressão LINQ [Nº 38](https://github.com/Azure/azure-documentdb-net/issues/38)

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

> [AZURE.NOTE]
Houve uma alteração de nome do pacote NuGet entre a visualização e a GA. Mudamos de **Microsoft.Azure.Documents.Client** para **Microsoft.Azure.DocumentDB** <br/>


### <a name="0.9.x-preview"/>[0\.9.x-preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- SDKs de visualização [Obsoleto]

## Datas de lançamento e desativação
A Microsoft fornecerá uma notificação pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente.

Qualquer solicitação feita ao Banco de Dados de Documentos usando um SDK obsoleto será rejeitada pelo serviço.

> [AZURE.WARNING]
Todas as versões do SDK do Banco de Dados de Documentos do Azure para .NET anteriores à versão **1.0.0** serão desativadas em **29 de fevereiro de 2016**.
 
<br/>
 
| Versão | Data do lançamento | Data de desativação 
| ---	  | ---	         | ---
| [1\.9.4](#1.9.4) | 24 de agosto de 2016 |--| [1\.9.3](#1.9.3) | 15 de agosto de 2016 |--| [1\.9.2](#1.9.2) | 23 de julho de 2016 |--| 1.9.1 | Preterido |--| 1.9.0 | Preterido |--| [1\.8.0](#1.8.0) | 14 de junho de 2016 |--| [1\.7.1](#1.7.1) | 06 de maio de 2016 |--| [1\.7.0](#1.7.0) | 26 de abril de 2016 |--| [1\.6.3](#1.6.3) | 08 de abril de 2016 |--| [1\.6.2](#1.6.2) | 29 de março de 2016 |--| [1\.5.3](#1.5.3) | 19 de fevereiro de 2016 |--| [1\.5.2](#1.5.2) | 14 de dezembro de 2015 |--| [1\.5.1](#1.5.1) | 23 de novembro de 2015 |--| [1\.5.0](#1.5.0) | 05 de outubro de 2015 |--| [1\.4.1](#1.4.1) | 25 de agosto de 2015 |--| [1\.4.0](#1.4.0) | 13 de agosto de 2015 |--| [1\.3.0](#1.3.0) | 05 de agosto de 2015 |--| [1\.2.0](#1.2.0) | 06 de julho de 2015 |--| [1\.1.0](#1.1.0) | 30 de abril de 2015 |--| [1\.0.0](#1.0.0) | 08 de abril de 2015 |--| [0\.9.3-prelease](#0.9.x-preview) | 12 de março de 2015 | 29 de fevereiro de 2016 | [0\.9.2-prelease](#0.9.x-preview) | Janeiro de 2015 | 29 de fevereiro de 2016 | [.9.1-prelease](#0.9.x-preview) | 13 de outubro de 2014 | 29 de fevereiro de 2016 | [0\.9.0-prelease](#0.9.x-preview) | 21 de agosto de 2014 | 29 de fevereiro de 2016

## Perguntas frequentes
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Consulte também

Para saber mais sobre o Banco de Dados de Documentos, confira a página de serviço do [Banco de Dados de Documentos do Microsoft Azure](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0824_2016-->