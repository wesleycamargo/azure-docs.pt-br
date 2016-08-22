<properties
pageTitle="Indexação do Armazenamento de Blobs do Azure com a Pesquisa do Azure"
description="Saiba como indexar o Armazenamento de Blobs do Azure e extrair texto de documentos com a Pesquisa do Azure"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="08/08/2016"
ms.author="eugenesh" />

# Indexação de documentos no Armazenamento de Blobs do Azure com a Pesquisa do Azure

Este artigo mostra como usar a Pesquisa do Azure para indexar documentos (como PDFs, documentos do Microsoft Office e vários outros formatos comuns) armazenados no armazenamento de blobs do Azure. O novo indexador de blob da Pesquisa do Azure torna esse processo rápido e direto.

> [AZURE.IMPORTANT] Atualmente, essa funcionalidade está no modo de visualização. Ela está disponível somente na API REST que usa a versão **2015-02-28-Preview**. Lembre-se de que as APIs de visualização servem para teste e avaliação, e não devem ser usadas em ambientes de produção.

## Formatos de documento com suporte

O indexador de blob pode extrair o texto dos seguintes formatos de documento:

- PDF
- Formatos do Microsoft Office: DOCX/DOC, XLSX/XLS, PPT/PPTX, MSG (emails do Outlook)
- HTML
- XML
- ZIP
- EML
- Arquivos de texto sem formatação
- JSON (consulte [Indexando os blobs JSON](search-howto-index-json-blobs.md) para obter detalhes)
- CSV (consulte [Indexando os blobs CSV](search-howto-index-csv-blobs.md) para obter detalhes)

## Configuração da indexação de blob

Para instalar e configurar um indexador do Armazenamento de Blobs do Azure, você pode usar a API REST da Pesquisa do Azure para criar e gerenciar **indexadores** e **fontes de dados**, conforme descrito [neste artigo](https://msdn.microsoft.com/library/azure/dn946891.aspx). No futuro, o suporte para a indexação de blob será adicionado ao SDK .NET da Pesquisa do Azure e ao Portal do Azure.

Para configurar um indexador, siga estas três etapas: crie uma fonte de dados, crie um índice, configure o indexador.

### Etapa 1: Criar uma fonte de dados

Uma fonte de dados especifica quais dados indexar, as credenciais necessárias para acessar os dados e as políticas que permitem à Pesquisa do Azure identificar com eficiência as alterações nos dados (linhas novas, modificadas ou excluídas). Uma fonte de dados pode ser usada por vários indexadores na mesma assinatura.

Para a indexação de blobs, a fonte de dados deve ter as seguintes propriedades requeridas:

- **name** é o nome exclusivo da fonte de dados dentro de seu serviço de pesquisa.

- **type** deve ser `azureblob`.

- **credentials** fornece a cadeia de conexão da conta de armazenamento como o parâmetro `credentials.connectionString`. Você pode obter a cadeia de conexão no Portal do Azure navegando até a folha da conta de armazenamento desejada > **Configurações** > **Chaves** e usar o valor "Cadeia de Conexão Primária" ou "Cadeia de Conexão Secundária". Como a cadeia de conexão está ligada a uma conta de armazenamento, especificar a cadeia de conexão implicitamente identifica a conta de armazenamento fornecendo os dados.

- **container** especifica um contêiner em sua conta de armazenamento. Por padrão, todos os blobs no contêiner são recuperáveis. Se você quiser apenas blobs de índice em um diretório virtual específico, poderá especificar o diretório usando o parâmetro opcional **query**.

O exemplo a seguir ilustra uma definição da fonte de dados:

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
	}   

Para obter mais informações sobre Criar a API da Fonte de Dados, consulte [Criar Fonte de Dados](search-api-indexers-2015-02-28-preview.md#create-data-source).

### Etapa 2: Criar um índice 

O índice especifica os campos em um documento, atributos e outras construções que modelam a experiência de pesquisa.

Para a indexação de blob, verifique se o índice tem um campo `content` de pesquisa para armazenar o blob.

	POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
	Content-Type: application/json
	api-key: [admin key]

	{
  		"name" : "my-target-index",
  		"fields": [
    		{ "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    		{ "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
  		]
	}

Para obter mais informações sobre Criar a API de Índice, consulte [Criar Índice](https://msdn.microsoft.com/library/dn798941.aspx)

### Etapa 3: Criar um indexador 

Um indexador conecta as fontes de dados com os índices de pesquisa de destino e fornece informações de agendamento para que você possa automatizar a atualização dos dados. Depois do índice e da fonte de dados ser criados, será relativamente simples criar um indexador que referencia a fonte de dados e um índice de destino. Por exemplo:

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "blob-indexer",
	  "dataSourceName" : "blob-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" }
	}

Esse indexador será executado a cada duas horas (o intervalo de agendamento é definido como "PT2H"). Para executar um indexador a cada 30 minutos, defina o intervalo para "PT30M". O intervalo mais curto permitido é de 5 minutos. O agendamento é opcional - se ele for omitido, um indexador será executado apenas uma vez quando for criado. No entanto, você pode executar um indexador sob demanda a qualquer momento.

Para obter mais detalhes sobre Criar a API do Indexador, consulte [Criar Indexador](search-api-indexers-2015-02-28-preview.md#create-indexer).


## Processo de extração de documento

A Pesquisa do Azure indexa cada documento (blob) da seguinte maneira:

- Todo o conteúdo de texto do documento é extraído para um campo de cadeia de caracteres chamado `content`. Observe que no momento não fornecemos suporte para extração de vários documentos de um único blob:
	- Por exemplo, um arquivo CSV é indexado como um único documento. Se você precisar tratar cada linha em um CSV como um documento separado, vote [nesta sugestão do UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/13865325-please-treat-each-line-in-a-csv-file-as-a-separate).
	- Um documento composto ou incorporado (como um arquivo ZIP ou um documento do Word com email do Outlook incorporado com um anexo em PDF) também é indexado como um único documento.

- As propriedades de metadados especificadas pelo usuário e presentes no blob, se houver alguma, são extraídas literalmente. Elas também podem ser usadas para controlar determinados aspectos do processo de extração de documentos, confira [Uso de metadados personalizados para controlar a extração de documento](#CustomMetadataControl) para obter mais detalhes.

- As propriedades de metadados de blob padrão são extraídas para os seguintes campos:

	- **metadata\_storage\_name** (Edm.String): o nome do arquivo do blob. Por exemplo, se você tiver um blob /my-container/my-folder/subfolder/resume.pdf, o valor desse campo será `resume.pdf`.

	- **metadata\_storage\_path** (Edm.String): o URI completo do blob, incluindo a conta de armazenamento. Por exemplo, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

	- **metadata\_storage\_content\_type** (Edm.String): o tipo de conteúdo, conforme especificado pelo código usado para carregar o blob. Por exemplo: `application/octet-stream`.

	- **metadata\_storage\_last\_modified** (Edm.DateTimeOffset): último carimbo de data e hora modificado para o blob. A Pesquisa do Azure usa esse carimbo de data e hora para identificar os blobs alterados, a fim de evitar a reindexação total após a indexação inicial.

	- **metadata\_storage\_size** (Edm.Int64): tamanho do blob em bytes.

	- **metadata\_storage\_content\_md5** (Edm.String): hash MD5 do conteúdo do blob, se estiver disponível.

- As propriedades de metadados específicas a cada formato de documento são extraídas nos campos listados [aqui](#ContentSpecificMetadata).

Não é necessário definir os campos para todas as propriedades acima em seu índice de pesquisa, basta capturar as propriedades necessárias para seu aplicativo.

> [AZURE.NOTE] Geralmente, os nomes de campo no índice existente serão diferentes dos nomes de campo gerados durante a extração do documento. Você pode usar os **mapeamentos de campo** para mapear os nomes de propriedade fornecidos pela Pesquisa do Azure para os nomes de campo em seu índice de pesquisa. Você verá um exemplo de uso de mapeamento de campo abaixo.

## Escolha do campo de chave do documento e tratamento de nomes de campo diferentes

Na Pesquisa do Azure, a chave do documento identifica exclusivamente um documento. Cada índice de pesquisa deve ter exatamente um campo de chave do tipo Edm.String. O campo de chave é necessário para cada documento adicionado ao índice (é, na verdade, o único campo obrigatório).
   
Você deve considerar cuidadosamente qual campo extraído deve ser mapeado para o campo de chave de seu índice. Os candidatos são:

- **metadata\_storage\_name**: este pode ser um candidato conveniente, mas observe que 1) talvez os nomes não sejam exclusivos, pois você pode ter blobs com o mesmo nome em pastas diferentes, e 2) o nome pode conter caracteres inválidos em chaves de documento, por exemplo, traços. Você pode lidar com caracteres inválidos habilitando a opção `base64EncodeKeys` nas propriedades do indexador. Se fizer isso, lembre-se de codificar as chaves de documento ao transmiti-las em chamadas de API, como Pesquisa. (Por exemplo, em .NET você pode usar o [método UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) para essa finalidade).

- **metadata\_storage\_path**: o uso do caminho completo garante a exclusividade, mas o caminho contém definitivamente caracteres `/` que são [inválidos em uma chave de documento](https://msdn.microsoft.com/library/azure/dn857353.aspx). Como foi mencionado acima, você tem a opção de codificar as chaves usando a opção `base64EncodeKeys`.

- Se nenhuma das opções acima funcionar para você, ainda há a flexibilidade máxima de adicionar uma propriedade de metadados personalizada aos blobs. No entanto, essa opção exige que seu processo de carregamento de blob adicione essa propriedade de metadados a todos os blobs. Como a chave é uma propriedade obrigatória, todos os blobs que não tiverem essa propriedade apresentarão falha na indexação.

> [AZURE.IMPORTANT] Se não houver um mapeamento explícito para o campo de chave no índice, a Pesquisa do Azure usará automaticamente `metadata_storage_path` (a segunda opção acima) como a chave e habilitará a codificação com base-64 das chaves.

Para este exemplo, vamos escolher o campo `metadata_storage_name` como a chave do documento. Vamos supor também que o índice tenha um campo de chave chamado `key` e um campo `fileSize` para armazenamento do tamanho do documento. Para conectar as coisas conforme o desejado, especifique os seguintes mapeamentos de campo ao criar ou atualizar o indexador:

	"fieldMappings" : [
	  { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key" },
	  { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
	]

Para unir tudo isso, veja como você pode adicionar mapeamentos de campo e habilitar a codificação das chaves em base-64 para um indexador existente:

	PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "dataSourceName" : " blob-datasource ",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" },
	  "fieldMappings" : [
	    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key" },
	    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
	  ],
	  "parameters" : { "base64EncodeKeys": true }
	}

> [AZURE.NOTE] Para saber mais sobre mapeamentos de campo, consulte [este artigo](search-indexer-field-mappings.md).

## Indexação incremental e detecção de exclusão

Quando você configura um indexador de blob para execução em um cronograma, ele reindexa apenas os blobs alterados, conforme determinado pelo carimbo de data/hora `LastModified` do blob.

> [AZURE.NOTE] Não é necessário especificar uma política de detecção de alteração, a indexação incremental é habilitada automaticamente para você.

Para indicar que determinados documentos devem ser removidos do índice, você deve usar uma estratégia de exclusão reversível. Em vez de excluir os blobs correspondentes, adicione uma propriedade de metadados personalizada para indicar que eles foram excluídos e configure uma política de detecção de exclusão reversível na fonte de dados.

> [AZURE.WARNING] Se você apenas excluir os blobs em vez de usar uma política de detecção de exclusão, os documentos correspondentes não serão removidos do índice de pesquisa.

Por exemplo, a política abaixo considerará que um blob foi excluído se tiver uma propriedade de metadados `IsDeleted` com o valor `true`:

	PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<your storage connection string>" },
		"container" : { "name" : "my-container", "query" : "my-folder" },
		"dataDeletionDetectionPolicy" : {
			"@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", 	
			"softDeleteColumnName" : "IsDeleted",
			"softDeleteMarkerValue" : "true"
		}
	}   

<a name="ContentSpecificMetadata"></a>
## Propriedades de metadados específicas ao tipo de conteúdo

A tabela a seguir resume o processo executado para cada formato de documento, e descreve as propriedades dos metadados extraídos pela Pesquisa do Azure.

Formato de documento/tipo de conteúdo | Propriedades de metadados específicas do tipo de conteúdo | Detalhes do processamento
-------------------------------|-------------------------------------------|-------------------
HTML (`text/html`) | `metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` | Remoção da marcação HTML e extração do texto
PDF (`application/pdf`) | `metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title`| Extração do texto, incluindo documentos incorporados (excluindo imagens)
DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | Extração de texto, incluindo documentos incorporados
DOC (application/msword) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | Extração de texto, incluindo documentos incorporados
XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | Extração de texto, incluindo documentos incorporados
XLS (application/vnd.ms-excel) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | Extração de texto, incluindo documentos incorporados
PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | Extração de texto, incluindo documentos incorporados
PPT (application/vnd.ms-powerpoint) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | Extração de texto, incluindo documentos incorporados
MSG (application/vnd.ms-outlook) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` | Extração do texto, incluindo anexos
ZIP (application/zip) | `metadata_content_type` | Extração do texto de todos os documentos no arquivo
XML (application/xml) | `metadata_content_type`</br>`metadata_content_encoding`</br> | Remoção da marcação XML e extração do texto
JSON (application/json) | `metadata_content_type`</br>`metadata_content_encoding` | Extrair texto<br/>OBSERVAÇÃO: se você precisar extrair vários campos de documento de um blob JSON, consulte [Indexando blobs JSON](search-howto-index-json-blobs.md) para obter detalhes
EML (message/rfc822) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` | Extração do texto, incluindo anexos
Texto sem formatação (text/plain) | `metadata_content_type`</br>`metadata_content_encoding`</br> | 

<a name="CustomMetadataControl"></a>
## Uso de metadados personalizados para controlar a extração do documento

Você pode adicionar propriedades de metadados a um blob para controlar determinados aspectos do processo de indexação do blob e de extração de documento. Atualmente, há suporte para as seguintes propriedades:

Nome da propriedade | Valor da propriedade | Explicação
--------------|----------------|------------
AzureSearch\_Skip | "true" | Instrui o indexador do blob a ignorar completamente o blob; não ocorrerá a tentativa de extração dos metadados nem do conteúdo. Isso é útil quando você quer ignorar determinados tipos de conteúdo, ou quando um blob específico falha repetidamente e interrompe o processo de indexação.
AzureSearch\_SkipContent | "true" | Instrui o indexador de blob a indexar somente os metadados e a ignorar a extração de conteúdo do blob. Isso é útil quando o conteúdo do blob não é interessante, mas ainda assim você deseja indexar os metadados anexados ao blob.

<a name="IndexerParametersConfigurationControl"></a>
## Usando parâmetros de indexador para controlar a extração de documentos

Diversos parâmetros de configuração do indexador estão disponíveis para controlar quais blobs e quais partes de um conteúdo e metadados de blob serão indexados.

### Indexe apenas os blobs com extensões de arquivo específicas

Você pode indexar apenas os blobs com as extensões do nome de arquivo especificadas usando o parâmetro de configuração do indexador `indexedFileNameExtensions`. O valor é uma cadeia de caracteres que contém uma lista separada por vírgulas das extensões de arquivo (com um ponto à esquerda). Por exemplo, para indexar apenas blobs de .PDF e .DOCX, faça o seguinte:

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
	}

### Exclua blobs com extensões de arquivo específicas da indexação

Você pode excluir os blobs com extensões do nome de arquivo específicas da indexação usando o parâmetro de configuração `excludedFileNameExtensions`. O valor é uma cadeia de caracteres que contém uma lista separada por vírgulas das extensões de arquivo (com um ponto à esquerda). Por exemplo, para indexar todos os blobs, exceto aqueles com as extensões .PNG e .JPEG, faça o seguinte:

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
	}

Se os parâmetros `indexedFileNameExtensions` e `excludedFileNameExtensions` estiverem presentes, a Pesquisa do Azure primeiro examinará `indexedFileNameExtensions`, em seguida, `excludedFileNameExtensions`. Isso significa que, se a mesma extensão de arquivo estiver presente nas duas listas, ela será excluída da indexação.

### Indexar somente metadados de armazenamento

Você pode indexar apenas os metadados de armazenamento e ignorar por completo o processo de extração de documentos usando a propriedade de configuração `indexStorageMetadataOnly`. Isso é útil quando você não precisa do conteúdo do documento ou quando não precisa das propriedades de metadados específicas do tipo de conteúdo. Para fazer isso, defina a propriedade `indexStorageMetadataOnly` para `true`:

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "indexStorageMetadataOnly" : true } }
	}

### Indexe metadados de tipo de conteúdo e armazenamento, mas ignore a extração de conteúdo

Se você precisar extrair todos os metadados, mas ignorar a extração de conteúdo de todos os blobs, poderá solicitar esse comportamento usando a configuração do indexador, em vez de ter que adicionar os metadados `AzureSearch_SkipContent` a cada blob individualmente. Para fazer isso, defina a propriedade de configuração do indexador `skipContent` para `true`:

	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "skipContent" : true } }
	}

## Ajude-nos a aprimorar a Pesquisa do Azure

Se você tiver solicitações de recursos ou ideias para o aperfeiçoamentos, entre em contato conosco pelo [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0810_2016-->