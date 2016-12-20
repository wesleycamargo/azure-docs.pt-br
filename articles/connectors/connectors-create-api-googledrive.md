---
title: "Adicionar o conector do Google Drive nos aplicativos lógicos | Microsoft Docs"
description: "Visão geral do conector do Google Drive com parâmetros da API REST"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2bcebc5-02d2-435b-b0da-ef53bc51c4b6
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 71f9dd111ebdbe885f33d162b2ea320dfaa167bb
ms.openlocfilehash: 5738c78d65ed0394028b90d634e5ebd4b1d5c5bc


---
# <a name="get-started-with-the-google-drive-connector"></a>Introdução ao conector do Google Drive
Conecte-se ao Google Drive para criar arquivos, obter linhas e muito mais. Com o Google Drive, você pode: 

* Crie seu fluxo de negócios baseado nos dados que você obtém da pesquisa. 
* Use ações para pesquisar imagens, notícias e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, você pode pesquisar por um vídeo e usar o Twitter para publicar esse vídeo em um feed do Twitter.

Para adicionar uma operação a aplicativos lógicos, consulte [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Gatilhos e ações
O Google Drive inclui as ações a seguir. Não há gatilhos. 

| Gatilhos | Ações |
| --- | --- |
| Nenhum |<ul><li>Criar arquivo</li><li>Inserir linha</li><li>Copiar arquivo</li><li>Excluir arquivo</li><li>Excluir linha</li><li>Extrair o arquivo morto para a pasta</li><li>Obter o conteúdo do arquivo usando a ID</li><li>Obter o conteúdo do arquivo usando o caminho</li><li>Obter metadados de arquivo usando a id</li><li>Obter metadados do arquivo usando o caminho</li><li>Obter linha</li><li>Atualizar arquivo</li><li>Atualizar linha</li></ul> |

Todos os conectores dão suporte a dados nos formatos JSON e XML.

## <a name="create-the-connection-to-google-drive"></a>Criar a conexão com o Google Drive
Quando você adiciona esse conector aos seus aplicativos lógicos, precisa autorizar que os aplicativos lógicos se conectem ao Google Drive.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]
> 
> 

Depois de criar a conexão, insira as propriedades do Google Drive, como o caminho da pasta ou o nome do arquivo. A **referência da API REST** neste tópico descreve essas propriedades.

> [!TIP]
> Você pode usar essa mesma conexão do Google Drive em outros aplicativos lógicos.
> 
> 

## <a name="swagger-rest-api-reference"></a>Referência da API REST do Swagger
Aplica-se à versão: 1.0.

### <a name="create-file"></a>Criar arquivo
Carrega um arquivo no Google Drive.  
```POST: /datasets/default/files```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |sim |query |Nenhum |Caminho da pasta para carregar o arquivo no Google Drive |
| Nome |string |sim |query |Nenhum |Nome do arquivo a ser criado no Google Drive |
| corpo |string(binary) |Sim |corpo |Nenhum |Conteúdo do arquivo para carregar no Google Drive |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### <a name="insert-row"></a>Inserir linha
Insere uma linha em uma Planilha do Google.  
```POST: /datasets/{dataset}/tables/{table}/items```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |Nenhum |Identificador exclusivo do arquivo de Planilha Google |
| tabela |string |Sim |path |Nenhum |Identificador exclusivo da planilha |
| item |ItemInternalId: cadeia de caracteres |Sim |corpo |Nenhum |Linha para inserir na planilha especificada |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na operação. |

### <a name="copy-file"></a>Copiar arquivo
Copia um arquivo no Google Drive.  
```POST: /datasets/default/copyFile```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| fonte |string |sim |query |nenhum |URL para o arquivo de origem |
| destino |string |sim |query |Nenhum |Caminho do arquivo de destino no Google Drive, incluindo nome do arquivo de destino |
| substituir |booleano |não |query |nenhum |Substitui o arquivo de destino se estiver definido como "true" |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na operação. |

### <a name="delete-file"></a>Excluir arquivo
Exclui um arquivo do Google Drive.  
```DELETE: /datasets/default/files/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |string |Sim |path |Nenhum |Identificador exclusivo do arquivo a ser excluído do Google Drive |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### <a name="delete-row"></a>Excluir linha
Exclui uma linha de uma Planilha do Google.  
```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |Nenhum |Identificador exclusivo do arquivo de Planilha Google |
| tabela |string |Sim |path |Nenhum |Identificador exclusivo da planilha |
| ID |string |Sim |path |Nenhum |Identificador exclusivo da linha a ser excluída |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### <a name="extract-archive-to-folder"></a>Extrair o arquivo morto para a pasta
Extrai um arquivo de armazenamento para uma pasta no Google Drive (exemplo: .zip).  
```POST: /datasets/default/extractFolderV2```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| fonte |string |sim |query |nenhum |Caminho para o arquivo morto |
| destino |string |sim |query |Nenhum |Caminho no Google Drive para extrair o conteúdo do arquivo morto |
| substituir |booleano |não |query |nenhum |Substitui os arquivos de destino se estiver definido como "true" |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na operação. |

### <a name="get-file-content-using-id"></a>Obter o conteúdo do arquivo usando a ID
Recupera o conteúdo do arquivo do Google Drive usando uma id.  
```GET: /datasets/default/files/{id}/content```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |string |Sim |path |Nenhum |Identificador exclusivo do arquivo a ser recuperado no Google Drive |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na operação. |

### <a name="get-file-content-using-path"></a>Obter o conteúdo do arquivo usando o caminho
Recupera o conteúdo do arquivo no Google Drive usando um caminho.  
```GET: /datasets/default/GetFileContentByPath```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| path |string |sim |query |Nenhum |Caminho do arquivo no Google Drive |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### <a name="get-file-metadata-using-id"></a>Obter metadados de arquivo usando a id
Recupera os metadados do arquivo no Google Drive usando uma id.  
```GET: /datasets/default/files/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |string |Sim |path |Nenhum |Identificador exclusivo do arquivo no Google Drive |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na operação. |

### <a name="get-file-metadata-using-path"></a>Obter Metadados do Arquivo usando o caminho
Recupera os metadados do arquivo no Google Drive usando um caminho.  
```GET: /datasets/default/GetFileByPath```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| path |string |sim |query |Nenhum |Caminho do arquivo no Google Drive |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### <a name="get-row"></a>Obter linha
Recupera uma única linha em uma Planilha do Google.  
```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |Nenhum |Identificador exclusivo do arquivo de Planilha Google |
| tabela |string |Sim |path |Nenhum |Identificador exclusivo da planilha |
| ID |string |Sim |path |Nenhum |O identificador exclusivo da linha a ser recuperado |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na operação. |

### <a name="update-file"></a>Atualizar arquivo
Atualiza um arquivo no Google Drive.  
```PUT: /datasets/default/files/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |string |Sim |path |Nenhum |Identificador exclusivo do arquivo a ser atualizado no Google Drive |
| corpo |string(binary) |Sim |corpo |Nenhum |Conteúdo do arquivo para carregar no Google Drive |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### <a name="update-row"></a>Atualizar linha
Atualiza uma linha em uma Planilha do Google.  
```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |Nenhum |Identificador exclusivo do arquivo de Planilha Google |
| tabela |string |Sim |path |Nenhum |Identificador exclusivo da planilha |
| ID |string |Sim |path |Nenhum |Identificador exclusivo da linha a ser atualizada |
| item |ItemInternalId: cadeia de caracteres |Sim |corpo |Nenhum |Linhas com valores atualizados |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na operação. |

## <a name="object-definitions"></a>Definições de objeto
#### <a name="datasetsmetadata"></a>DataSetsMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| tabular |não definido |não |
| blob |não definido |não |

#### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| fonte |string |não |
| displayName |string |não |
| urlEncoding |string |não |
| tableDisplayName |string |não |
| tablePluralName |string |não |

#### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| fonte |string |não |
| displayName |string |não |
| urlEncoding |string |não |

#### <a name="blobmetadata"></a>BlobMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |não |
| Nome |string |não |
| displayName |string |não |
| path |string |não |
| LastModified |string |não |
| Tamanho |inteiro |não |
| MediaType |string |não |
| IsFolder |booleano |não |
| ETag |string |não |
| FileLocator |string |não |

#### <a name="tablemetadata"></a>TableMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Nome |string |não |
| título |string |não |
| x-ms-permission |string |não |
| schema |não definido |não |

#### <a name="tableslist"></a>TablesList
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| value |array |não |

#### <a name="table"></a>tabela
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Nome |string |não |
| displayName |string |não |

#### <a name="item"></a>item
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ItemInternalId |string |não |

#### <a name="itemslist"></a>ItemsList
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| value |array |não |

## <a name="next-steps"></a>Próximas etapas
[Crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

Volte para a [Lista de APIs](apis-list.md).

<!--References-->
[5]: https://console.developers.google.com/
[6]: ./media/connectors-create-api-googledrive/google-developers-console.png
[8]: ./media/connectors-create-api-googledrive/use-google-apis.png
[9]: ./media/connectors-create-api-googledrive/googledrive-api-overview.png
[10]: ./media/connectors-create-api-googledrive/enable-googledrive-api.png
[12]: ./media/connectors-create-api-googledrive/googledrive-api-credentials-add.png
[13]: ./media/connectors-create-api-googledrive/configure-consent-screen.png
[14]: ./media/connectors-create-api-googledrive/create-client-id.png



<!--HONumber=Nov16_HO3-->


