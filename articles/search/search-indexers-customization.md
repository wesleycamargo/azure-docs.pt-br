<properties 
	pageTitle="Personalização do indexador da Pesquisa do Azure" 
	description="Saiba como personalizar as configurações e as políticas de indexadores da Pesquisa do Azure." 
	services="search" 
	documentationCenter="" 
	authors="chaosrealm" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="09/29/2015" 
	ms.author="eugenesh"/>

#Personalização do indexador da Pesquisa do Azure

A configuração de um indexador na Pesquisa do Azure permite renomear campos entre uma fonte de dados e um índice de destino, transformar cadeias de caracteres de uma tabela de banco de dados em coleções de cadeia de caracteres, alterna a política de detecção de alteração em uma fonte de dados, codificar em URL chaves de documento que contêm caracteres não seguros para URL e tolerar falhas para indexar alguns documentos.

Se você não estiver familiarizado com os indexadores da Pesquisa do Azure, convém examinar estes artigos primeiro:

- [Conectando o Banco de Dados do SQL do Azure à Pesquisa do Azure usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [Conectando o Banco de Dados de Documentos à Pesquisa do Azure usando indexadores](../documentdb/documentdb-search-indexer.md)
- [SDK do .NET com suporte para indexadores](https://msdn.microsoft.com/library/dn951165.aspx) ou 
- [Referência da API REST dos indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx)

##Renomear campos entre uma fonte de dados e um índice de destino##

**Mapeamentos de campo** são propriedades que reconciliam as diferenças entre as definições de campo. Os exemplos mais comuns são encontrados nos nomes de campo que violam as regras de nomenclatura da Pesquisa de Azure. Considere uma tabela de origem quando um ou mais nomes de campo começarem com um sublinhado à esquerda (como `_id`). A Pesquisa do Azure não permite um nome de campo iniciado com um sublinhado e, portanto, o campo deve ser renomeado.

O exemplo a seguir ilustra a atualização de um indexador para incluir um mapeamento de campo que "renomeia" o campo `_id` da fonte de dados no campo `id` do índice de destino:

	PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
    } 

**OBSERVAÇÃO:** você precisa usar uma versão da API 2015-02-28-Preview para usar mapeamentos de campo.

Você pode especificar vários mapeamentos de campo:

	"fieldMappings" : [ 
		{ "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
	 ]

Nomes de campos de origem e destino diferenciam maiúsculas de minúsculas.

##Transformar cadeias de caracteres de uma tabela de banco de dados em coleções de cadeias de caracteres##

Mapeamentos de campo também podem ser usados para transformar os valores de campo de origem usando *funções de mapeamento*.

Tal função, `jsonArrayToStringCollection`, analisa um campo que contém uma cadeia de caracteres formatada como uma matriz JSON em um campo Collection(Edm.String) no índice de destino. Ele se destina ao uso com o indexador do Azure SQL em particular, pois o SQL não tem um tipo de dados nativo de coleção. Ele pode ser usado da seguinte maneira:

	"fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

Por exemplo, se o campo de origem contiver a cadeia de caracteres `["red", "white", "blue"]`, o campo de destino do tipo `Collection(Edm.String)` será preenchido com os três valores `"red"`, `"white"` e `"blue"`.

Observe que a propriedade `targetFieldName` é opcional; se omitida, o valor `sourceFieldName` será usado.

##Alternância a política de detecção de alteração em uma fonte de dados##
  
Na Pesquisa do Azure, a decisão de para qual política de detecção de alteração deve-se ir é determinada principalmente pelo que tiver suporte da sua fonte de dados e do esquema de dados. Ao longo do tempo, especialmente se você atualizar ou migrar bancos de dados, talvez queira alternar uma política de detecção de alteração para outro tipo. Por exemplo, talvez você tenha acabado de atualizar seu Banco de Dados SQL do Azure para uma versão mais recente que dê suporte ao Controle de Alterações Integrado e, portanto, deseja alternar da política de marca d'água alta para a política de controle de alterações integrada. Ou talvez você decida usar uma coluna diferente como sua marca d’água alta.

Se você simplesmente chamar a API REST da fonte de dados PUT para atualizar sua fonte de dados, talvez obtenha uma resposta 400 com uma mensagem de erro semelhante à seguinte:


	"Change detection policy cannot be changed for data source '…' because indexer '…' references this data source and has a non-empty change tracking state. You can use Reset API to reset the indexer's change tracking state, and retry this call."

 Provavelmente você quer saber o que isso significa e como contornar esse problema. Esse erro ocorre porque a Pesquisa do Azure mantém o estado interno associado à sua política de detecção de alteração. Quando a política for alterada, o estado existente será invalidado, pois não se aplica à nova política. Isso significa que o indexador deve iniciar a indexação da fonte de dados a partir do zero usando a nova política, o que tem implicações potenciais para você (por exemplo, carga adicional no banco de dados ou encargos de largura de banda de rede adicionais). É por isso que a Pesquisa do Azure solicita que você chame a [API Redefinir Indexador](https://msdn.microsoft.com/library/azure/dn946897.aspx) para redefinir o estado associado à política de detecção de alteração atual. Depois disso, a política poderá ser alterada com uma chamada PUT de fonte de dados comum. É claro que a Pesquisa do Azure poderia fazer a redefinição para você automaticamente, mas achamos que era mais importante para você reconhecer explicitamente sua compreensão das implicações chamando a API de Redefinição.

##Chaves de documento de codificação de URL que contêm caracteres não seguros URL##

A Pesquisa do Azure restringe os caracteres dentro de um campo chave de documento para caracteres seguros URL, porque os usuários devem poder procurar documentos por suas chaves. Dessa forma, o que acontece quando os documentos necessários à indexação contêm esses caracteres no campo de chave? Se você estiver indexando documentos por conta própria usando um SDK cliente ou uma API REST, poderá codificar as chaves por URL. Com os indexadores, você pode dizer à Pesquisa do Azure para codificar suas chaves pela URL ao configurar o parâmetro **base64EncodeKeys** como `true` ao criar ou atualizar o indexador:

    PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "parameters" : { "base64EncodeKeys": true }
    }

Para obter detalhes de codificação, consulte este [artigo do MSDN](http://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx).

OBSERVAÇÃO: se você precisar pesquisar ou filtrar em valores de chave, terá de codificar de forma semelhante as chaves usadas em suas solicitações, para que sua solicitação corresponda ao valor codificado armazenado no índice da pesquisa.


##Tolerar falhas para indexar alguns documentos##

Por padrão, um indexador da Pesquisa do Azure para de indexar assim que um único documento falha ao ser indexado. Dependendo do cenário, você pode optar por tolerar algumas falhas (por exemplo, se você reindexar repetidamente sua fonte de dados inteira). A Pesquisa do Azure fornece dois parâmetros de indexador para ajustar esse comportamento:

- **maxFailedItems**: o número de itens que podem não ser indexados antes que a execução de um indexador seja considerada como falha. O padrão é 0.
- **maxFailedItemsPerBatch**: o número de itens que podem não ser indexados em um único lote antes que a execução de um indexador seja considerada como falha. O padrão é 0.

Você pode alterar esses valores a qualquer momento, especificando um ou ambos os parâmetros ao criar ou atualizar o indexador:

	PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
	Content-Type: application/json
	api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }
    }

Mesmo se você optar por tolerar algumas falhas, as informações sobre quais documentos não puderam são retornadas pela [API Obter Status do Indexador](https://msdn.microsoft.com/library/azure/dn946884.aspx).

E isso é tudo por enquanto. Caso você tenha pensamentos ou sugestões para futuras ideias de conteúdo, envie um tweet para nós usando a hashtag #AzureSearch ou envie suas ideias pela nossa [página UserVoice](http://feedback.azure.com/forums/263029-azure-search).
 

<!---HONumber=Oct15_HO3-->