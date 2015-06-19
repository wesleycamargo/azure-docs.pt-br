<properties 
	pageTitle="Políticas de Indexação do Banco de Dados de Documentos | Azure" 
	description="Compreender como a indexação funciona no Banco de Dados de Documentos e saber como configurar a política de indexação." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="03/13/2015" 
	ms.author="mimig"/>


Políticas de indexação do Banco de Dados de Documentos
============================

O Banco de Dados de Documentos é um verdadeiro banco de dados livre de esquema. Ele não assume nem requer qualquer esquema para os documentos JSON que indexa. Isso permite que você defina rapidamente e itere em modelos de dados de aplicativo. Ao incluir documentos em uma coleção, o Banco de Dados de Documentos indexa todas as propriedades de documento automaticamente e elas ficam disponíveis para consulta. A indexação automática permite armazenar tipos heterogêneos de documentos.

A indexação automática de documentos é habilitada por técnicas de manutenção de índice de gravação otimizada, livre de bloqueio e log estruturadas. O Banco de Dados de Documentos oferece suporte a um volume permanente de gravações rápidas ao mesmo tempo em que oferece consultas consistentes.

O subsistema de indexação do Banco de Dados de Documentos foi projetado para oferecer suporte a:

·         Pesquisas hierárquicas e relacionais ricas e eficientes sem quaisquer definições de esquema ou índice.

·         Resultados de consulta consistentes ao manipular um volume prolongado de gravações. Para possibilitar cargas de trabalho com alta produtividade de gravação com consultas consistentes, o índice é atualizado gradativamente, eficientemente e online, ao manipular um volume prolongado de gravações.

·         Eficiência do armazenamento. Para manter um bom custo-benefício, a sobrecarga do armazenamento em disco do índice é vinculada e previsível.

·         Multilocação. As atualizações do índice são realizadas dentro do orçamento dos recursos do sistema alocados por coleção de documento.

Para a maioria dos aplicativos, você pode usar a diretiva de indexação automática padrão já que ela permite mais flexibilidade e compensações perfeitas entre desempenho e eficiência de armazenamento. Por outro lado, especificar uma política de indexação personalizada permite que você faça compensações granulares entre desempenho de pesquisa, desempenho de gravação e sobrecarga de armazenamento do índice.

Por exemplo, excluindo certos documentos ou caminhos nos documentos da indexação, você pode reduzir o espaço armazenamento usado para indexação, bem como o custo de tempo de inserção para manutenção de índice. Você pode alterar o tipo do índice mais adequado para consultas de intervalo ou aumentar a precisão de índice em bytes para melhorar o desempenho da consulta. Este artigo descreve diferentes configurações de indexação disponíveis no Banco de Dados de Documentos e como personalizar a política de indexação para suas cargas de trabalho.

<a id="HowWorks"></a>Como funciona a indexação do Banco de Dados de Documentos
-----------------------------

A indexação do Banco de Dados de Documentos aproveita-se que a gramática JSON permite que os documentos sejam **representados como árvores**. Para um documento JSON ser representado como uma árvore, precisa ser criado um nó raiz fictício que seja pai do restante de nós reais no documento abaixo. Cada rótulo, inclusive os índices da matriz em um documento JSON torna-se um nó da árvore. A figura a seguir ilustra um documento JSON de exemplo e sua representação de árvore correspondente.

![Indexing Policies](media/documentdb-indexing-policies/image001.png)


Por exemplo, a propriedade JSON {"headquarters": A propriedade "Belgium"} no exemplo acima corresponde ao caminho/"headquarters"/"Belgium". A matriz JSON {"exports": [{"city": "Moscow"}, {"city": Athens"}]} correspondem aos caminhos /"exports"/0/"city"/"Moscow" e /"exports"/1/"city"/"Athens".

**Observação** A representação de caminho confunde os limites entre a estrutura/esquema e os valores de instância em documentos, permitindo que o Bando de Dados de Documentos seja realmente livre de esquema.

No Banco de Dados de Documentos, os documentos são organizados em coleções que podem ser consultadas usando o SQL ou processadas dentro do escopo de uma única transação. Cada coleção pode ser configurada com sua própria política de indexação expressada em termos de caminhos. Na seção a seguir, veremos como configurar o comportamento de indexação de uma coleção do Banco de Dados de Documentos.

<a id="ConfigPolicy"></a>Configurando a política de indexação de uma coleção
-------------------------------------------

O exemplo a seguir mostra como definir uma política de indexação personalizada durante a criação de uma coleção, usando a API REST do Banco de Dados de Documentos. O exemplo mostra a política de indexação expressada em termos de caminhos, tipos de índice e precisões.


	POST https://<REST URI>/colls HTTP/1.1                                                  
 	...                                                             
 	Accept: application/json 
                                                                                                                         
 	{                                                                     
	 "id":"customIndexCollection",                                     
	 "indexingPolicy":{                                                 
     "automatic":true,                                            
	 "indexingMode":"Consistent",                                     
	 "IncludedPaths":[                                       
	           {                                                             
	              "IndexType":"Hash",                                        
	              "Path":"/"                                                 
	           }                                                  
	        ],                                                               
	        "ExcludedPaths":[                                                
	           "/"nonIndexedContent"/*"                                 
	        ]                                                               
	     }                                                                 
	 }                                                                                                                                                
 	...                                                                      
                  
                                                        
	 HTTP/1.1 201 Created                                                     


**Observação:** a política de indexação de uma coleção deve ser especificada no momento da sua criação. Não é permitido modificar a política de indexação após a criação do conjunto, porém, isso terá suporte em uma versão futura do Banco de Dados de Documentos.

**Observação:** por padrão, O Banco de Dados de Documentos indexa todos os caminhos nos documentos consistentemente com um índice de hash. O caminho interno do carimbo de data/hora (_ts) é armazenado com um índice de intervalo.

### Indexação automática

Você pode escolher se deseja ou não que a coleção indexe automaticamente todos os documentos. Por padrão, todos os documentos são indexados automaticamente, mas você pode optar por desativá-lo. Quando a indexação estiver desativada, documentos podem ser acessados somente por meio de seus self links ou através de consultas usando um ID.

Com a indexação automática desativada, você ainda pode adicionar seletivamente somente documentos específicos para o índice. Por outro lado, você pode deixar a indexação automática e seletivamente optar por excluir apenas documentos específicos. As configurações de Ativar/desativar a indexação são úteis quando você tem apenas um subconjunto dos documentos que precisa ser consultado.

Você pode configurar a política padrão especificando o valor da propriedade automática para ser verdadeiro ou falso. Para substituir um único documento, você pode definir a solicitação de cabeçalho x-ms-indexingdirective ao inserir ou substituir um documento.

Por exemplo, o exemplo a seguir mostra como incluir um documento explicitamente usando o [SDK do .NET do Banco de Dados de Documentos](https://github.com/Azure/azure-documentdb-java) e a propriedade [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx).

	// If you want to override the default collection behavior to either     
	// exclude (or include) a Document from indexing,                                                                                           
	// use the RequestOptions.IndexingDirective property.                                  
	                                                                         
	client.CreateDocumentAsync(defaultCollection.SelfLink,  
	    new { Id = "AndersenFamily", isRegistered = true },                            
		new RequestOptions                               
		    {                                                                    
			    IndexingDirective = IndexingDirective.Include                                                                                      
			}
		);                                                                  


### Modos de Indexação

Escolha entre atualizações de índice síncronas (**Consistentes**) e assíncronas (**lentas**). Por padrão, o índice é atualizado sincronamente em cada ação de inserção, substituição ou exclusão realizada em um documento na coleção. Isso permite que as consultas obedeçam ao mesmo nível de consistência das leituras de documentos sem demora para o índice atualizado.

Embora o Banco de Dados de Documentos seja otimizado para gravação e dê suporte a volumes constantes de gravações de documentos junto com a manutenção síncrona de índice e atendimento a consultas consistentes, você pode configurar determinadas coleções para atualizar seu índice, sem pressa. A indexação lenta é excelente para situações em que dados são gravados em picos e deseja amortizar o trabalho necessário para indexar o conteúdo em um período mais longo de tempo. Isso permite que você use a taxa de transferência provisionada com eficiência e atenda solicitações de gravação em horários de pico com latência mínima. Com a indexação lenta ativada, os resultados de consultas serão eventualmente consistentes, independentemente do nível de consistência configurada para a conta de banco de dados.

O exemplo a seguir mostra como criar uma coleção do Banco de Dados de Documentos usando o SDK do .NET com indexação automática consistente em todas as inserções de documentos.


	 // Default collection creates a hash index for all string and numeric    
	 // fields. Hash indexes are compact and offer efficient                                                                                           
	 // performance for equality queries.                                     
	                                                                          
	 var defaultCollection = new DocumentCollection { Id ="defaultCollection" };                                                   
	                                                                          
	 // Optional. Override Automatic to false for opt-in indexing of documents.                                                                
	                                                                          
	 defaultCollection.IndexingPolicy.Automatic = true;                       
	                                                                          
	 // Optional. Set IndexingMode to Lazy for bulk import/read heavy        
	 // collections. Queries might return stale results with Lazy indexing.       
	                                                                          
	 defaultCollection.IndexingPolicy.IndexingMode = IndexingMode.Consistent; 
	                                                                          
	 defaultCollection = await client.CreateDocumentCollectionAsync(database.SelfLink,defaultCollection);                                                      


### Tipos de índice e precisão

O tipo ou esquema usado para as entradas de índice tem um impacto direto no armazenamento de índice e no desempenho. Para um esquema usando mais precisão, as consultas são geralmente mais rápidas. No entanto, há também um armazenamento com maior sobrecarga para o índice. Escolher uma precisão menor significa que mais documentos podem ser processados durante a execução da consulta, e a sobrecarga de armazenamento será menor.

A precisão de índice para valores em qualquer caminho pode ser entre 3 e 7 bytes.
Haja vista que o mesmo caminho pode ter valores numéricos e de sequência de caracteres em documentos diferentes, eles podem ser controlados separadamente. No SDK do .NET, eles correspondem às propriedades [NumericPrecision](http://msdn.microsoft.com/library/microsoft.azure.documents.indexingpath.numericprecision.aspx) e [StringPrecision](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingpath.stringprecision.aspx).

Há dois tipos com suporte de índice: Hash e intervalo. Escolher um tipo de índice **Hash** permite consultas de mesma eficiência. Na maioria dos casos de uso, os índices de hash não precisam de uma precisão maior que o valor padrão de 3 bytes.

Escolhendo um tipo de índice **intervalo** permite consultas de intervalo (usando >, <, >=, <=, !=). Para caminhos que possuem grandes intervalos de valores, é recomendável usar uma precisão igual ou superior a 6 bytes. Um caso de uso comum que requer um índice de intervalo de maior precisão são os carimbos de hora armazenados como tempo de época.

Se o seu caso de uso não requer consultas de intervalo eficientes, o padrão de índices de hash oferece uma melhor relação de armazenamento e desempenho. Observe que, para oferecer suporte a consultas de intervalo, você deve especificar uma política de índice personalizado.

> [AZURE.NOTE] Índices de intervalo têm suporte somente para valores numéricos.
  
O exemplo a seguir mostra como aumentar a precisão de índices de intervalo em uma coleção usando o SDK do .NET. Observe que isso usa um caminho especial "/" - que é explicado na próxima seção.


	 // If your collection has numeric fields that need to be queried    
	 // against ranges (>,>=,<=,<), then you can configure the collection to 
	 // use range queries for all numeric values.                                                                                                      
 
	var rangeDefault = new DocumentCollection { Id = "rangeCollection" };                                                              
	rangeDefault.IndexingPolicy.IncludedPaths.Add(                                                             
												 new IndexingPath {   
													IndexType = IndexType.Range, Path = "/", 
													NumericPrecision = 7 }
												 ); 
	 rangeDefault = await client.CreateDocumentCollectionAsync(database.SelfLink, rangeDefault);   


### Caminhos de índice

Nos documentos, você pode escolher quais caminhos devem ser incluídos ou excluídos da indexação. Isso pode oferecer um melhor desempenho de gravação e menor armazenamento de índice para situações onde os padrões de consulta são previamente conhecidos.

Caminhos de índice começam com a raiz (/) e geralmente terminam com o operador de curinga ?, indicando que há vários valores possíveis para o prefixo. Por exemplo, para servir de SELECT * FROM Families F WHERE F.familyName = "Andersen", você deve incluir um caminho de índice para a política de índice da coleção /"familyName"/?in.

Caminhos de índice também podem usar o * operador curinga para especificar o comportamento de caminhos recursivamente sob o prefixo. Por exemplo, /"payload"/* pode ser usado para excluir tudo sob a propriedade de carga da indexação.

Estes são os padrões comuns para especificar caminhos de índice:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>Caminho</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Descrição/caso de uso</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /
                </p>
            </td>
            <td valign="top">
                <p>
                    Caminho padrão para coleta. Recursiva e aplica-se em toda árvore do documento inteiro.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Caminho de índice necessário para fazer consultas como as seguintes (com tipos hash e de intervalo respectivamente):
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop &gt; 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/*
                </p>
            </td>
            <td valign="top">
                <p>
                    Caminho de índice para todos os caminhos sob o rótulo especificado. Especificado somente para exclusão de indexação.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/"subprop"/
                </p>
            </td>
            <td valign="top">
                <p>
                    Caminho de índice usado durante a execução da consulta para remover documentos que não possuem o caminho especificado.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/"subprop"/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Caminho de índice necessário para fazer consultas (com tipos hash e de intervalo respectivamente):
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop &gt; 5
                </p>
            </td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] Ao definir caminhos de índice personalizados, é necessário especificar a regra de indexação padrão para a árvore de todo o documento indicada pelo caminho especial "/".

O exemplo a seguir configura um caminho específico com a indexação de intervalo e um valor personalizado de precisão de 7 bytes:


 	// If you only want to specify range queries against a specific field,   
 	// then use a range index against that field. Doing so reduces the   
	// amount of storage required for indexes for the collection. In this    
 	// case, the query creates an index against the JSON path                   
 	// /"CreatedTimestamp"/?    
 	// allowing queries of the form WHERE CreatedTimestamp [>] X            
	
	var pathRange = new DocumentCollection { Id = "rangeSinglePathCollection" };    
	
	pathRange.IndexingPolicy.IncludedPaths.Add(
								new IndexingPath { 
										IndexType = IndexType.Range, 
										Path = "/"CreatedTimestamp"/?",   
										NumericPrecision = 7   
							 			}
									);   
	
	pathRange.IndexingPolicy.IncludedPaths.Add(   
											 new IndexingPath {  
											  	 Path = "/"  
											 });                                                                      
                                                   
	 pathRange = await client.CreateDocumentCollectionAsync(database.SelfLink, pathRange);      


O Banco de Dados de Documentos retornará um erro quando uma consulta usa um operador de intervalo, mas não tem um índice de intervalo contra o caminho consultado e não tem todos os outros filtros que podem ser feitos a partir do índice. Mas essas consultas ainda podem ser executadas sem um índice de intervalo usando o cabeçalho x-ms-documentdb-allow-scans na API REST ou usando a opção AllowScanInQueryrequest do SDK do .NET.

O exemplo a seguir exclui uma subárvore de caminhos de indexação usando o
"*" curinga.

	var excluded = new DocumentCollection { Id = "excludedPathCollection" };                                                                       
  	excluded.IndexingPolicy.IncludedPaths.Add(
	newIndexingPath {  Path = "/" });  

	excluded.IndexingPolicy.ExcludedPaths.Add("/" nonIndexedContent"/*");    
	excluded = await client.CreateDocumentCollectionAsync(database.SelfLink,excluded);                                                               


Ajuste de desempenho
------------------

Durante a avaliação de diferentes configurações de política de indexação, você deve medir as implicações de armazenamento e de taxa de transferência da política por meio das APIs do Banco de Dados de Documentos.

Para verificar se a cota de armazenamento e o uso de uma coleção, execute uma solicitação HEAD ou GET em relação ao recurso de coleção e inspecione os cabeçalhos x-ms-request-quota e x-ms-request-usage. No SDK do .NET, as propriedades [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) e [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) em [ResourceResponse <T>](http://msdn.microsoft.com/library/dn799209.aspx) contêm esses valores correspondentes.


 	// Measure the document size usage (which includes the index size) against   
 	// different policies.        
	 ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(collectionSelfLink);  
	 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentSizeQuota, collectionInfo.DocumentSizeUsage);                                       


Para medir a sobrecarga de indexação em cada operação de gravação (criar, atualizar ou excluir), inspecione o cabeçalho x-ms-request-charge (ou a propriedade equivalente [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) em [ResourceResponse <T>](http://msdn.microsoft.com/library/dn799209.aspx) no SDK do .NET) para medir o número de unidades de solicitação consumidas por essas operações.


 	// Measure the performance (request units) of writes.     
 	ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);              
                                                                    
 	Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);                                                 
                                                                          
 	// Measure the performance (request units) of queries.    
	 IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();                                          
                                                                          
 	while (queryable.HasMoreResults)                                                                            
 	{                                                                         
 	   FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
	   Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
	}                                                                        




<!--HONumber=49--> 