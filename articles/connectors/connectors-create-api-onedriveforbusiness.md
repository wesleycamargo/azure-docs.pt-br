---
title: OneDrive for Business | Microsoft Docs
description: Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. Conecte-se ao OneDrive for Business para gerenciar seus arquivos. É possível executar várias ações, como carregar, atualizar, obter e excluir arquivos.
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe

---
# Introdução ao conector do OneDrive for Business
Conecte-se ao OneDrive for Business para gerenciar seus arquivos. É possível executar várias ações, como carregar, atualizar, obter e excluir arquivos.

> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.
> 
> 

É possível começar criando um Aplicativo Lógico agora; veja [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações
O conector do OneDrive for Business pode ser usado como uma ação: ele tem um ou mais gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML.

 O conector do OneDrive for Business contém as seguintes ações e/ou gatilhos disponíveis:

### Ações do OneDrive for Business
Você pode executar as seguintes ações:

| Ação | Descrição |
| --- | --- |
| [GetFileMetadata](connectors-create-api-onedriveforbusiness.md#getfilemetadata) |Recupera os metadados de um arquivo no OneDrive for Business usando a ID |
| [UpdateFile](connectors-create-api-onedriveforbusiness.md#updatefile) |Atualiza um arquivo no OneDrive for Business |
| [DeleteFile](connectors-create-api-onedriveforbusiness.md#deletefile) |Exclui um arquivo do OneDrive for Business |
| [GetFileMetadataByPath](connectors-create-api-onedriveforbusiness.md#getfilemetadatabypath) |Recupera os metadados de um arquivo no OneDrive for Business usando o caminho |
| [GetFileContentByPath](connectors-create-api-onedriveforbusiness.md#getfilecontentbypath) |Recupera o conteúdo de um arquivo no OneDrive for Business usando o caminho |
| [GetFileContent](connectors-create-api-onedriveforbusiness.md#getfilecontent) |Recupera o conteúdo de um arquivo no OneDrive for Business usando a ID |
| [CreateFile](connectors-create-api-onedriveforbusiness.md#createfile) |Carrega um arquivo no OneDrive for Business |
| [CopyFile](connectors-create-api-onedriveforbusiness.md#copyfile) |Copia um arquivo no OneDrive for Business |
| [ListFolder](connectors-create-api-onedriveforbusiness.md#listfolder) |Lista os arquivos em uma pasta do OneDrive for Business |
| [ListRootFolder](connectors-create-api-onedriveforbusiness.md#listrootfolder) |Lista os arquivos na pasta raiz do OneDrive for Business |
| [ExtractFolderV2](connectors-create-api-onedriveforbusiness.md#extractfolderv2) |Extrai uma pasta no OneDrive for Business |

### Gatilhos do OneDrive for Business
Você pode escutar estes eventos:

| Gatilho | Descrição |
| --- | --- |
| Quando um arquivo é criado |Dispara um fluxo quando um novo arquivo é criado em uma pasta do OneDrive for Business |
| Quando um arquivo é modificado |Dispara um fluxo quando um arquivo é modificado em uma pasta do OneDrive for Business |

## Criar uma conexão com o OneDrive for Business
Para criar Aplicativos Lógicos com o OneDrive for Business, primeiro, você deve criar uma **conexão** e, em seguida, fornecer os detalhes das seguintes propriedades:

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| Token |Sim |Fornecer as credenciais do OneDrive for Business |

Depois de criar a conexão, é possível usá-la para executar as ações e ouvir os gatilhos descritos neste artigo.

> [!INCLUDE [Etapas para criar uma conexão com o OneDrive for Business](../../includes/connectors-create-api-onedriveforbusiness.md)]
> 
> [!TIP]
> Você pode usar essa conexão em outros aplicativos lógicos.
> 
> 

## Referência do OneDrive for Business
Aplica-se à versão: 1.0

## GetFileMetadata
Obter os metadados de arquivo usando a ID: recupera os metadados de um arquivo no OneDrive for Business usando a ID

```GET: /datasets/default/files/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |string |sim |path |nenhum |Especificar o arquivo |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## UpdateFile
Atualizar arquivo: atualiza um arquivo no OneDrive for Business

```PUT: /datasets/default/files/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |string |sim |path |nenhum |Especificar o arquivo a ser atualizado |
| corpo | |sim |corpo |nenhum |Conteúdo do arquivo a ser atualizado no OneDrive for Business |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## DeleteFile
Excluir arquivo: exclui um arquivo do OneDrive for Business

```DELETE: /datasets/default/files/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |string |sim |path |nenhum |Especifica o arquivo a ser excluído |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## GetFileMetadataByPath
Obter os metadados de arquivo usando o caminho: recupera os metadados de um arquivo no OneDrive for Business usando o caminho

```GET: /datasets/default/GetFileByPath```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| path |string |sim |query |nenhum |Caminho exclusivo para o arquivo no OneDrive for Business |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## GetFileContentByPath
Obter o conteúdo do arquivo usando o caminho: recupera o conteúdo de um arquivo no OneDrive for Business usando o caminho

```GET: /datasets/default/GetFileContentByPath```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| path |string |sim |query |nenhum |Caminho exclusivo para o arquivo no OneDrive for Business |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## GetFileContent
Obter o conteúdo do arquivo usando a ID: recupera o conteúdo de um arquivo no OneDrive for Business usando a ID

```GET: /datasets/default/files/{id}/content```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |string |sim |path |nenhum |Especificar o arquivo |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## CreateFile
Criar arquivo: carrega um arquivo no OneDrive for Business

```POST: /datasets/default/files```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |sim |query |nenhum |Caminho da pasta para carregar o arquivo no OneDrive for Business |
| name |string |sim |query |nenhum |Nome do arquivo a ser criado no OneDrive for Business |
| corpo | |sim |corpo |nenhum |Conteúdo do arquivo para carregar no OneDrive for Business |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## CopyFile
Copiar arquivo: copia um arquivo no OneDrive for Business

```POST: /datasets/default/copyFile```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| fonte |string |sim |query |nenhum |URL para o arquivo de origem |
| destino |string |sim |query |nenhum |Caminho do arquivo de destino no OneDrive for Business, incluindo nome do arquivo de destino |
| substituir |Booliano |não |query |false |Substitui o arquivo de destino se estiver definido como "true" |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## OnNewFile
Quando um arquivo é criado: dispara um fluxo quando um novo arquivo é criado em uma pasta do OneDrive for Business

```GET: /datasets/default/triggers/onnewfile```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| folderId |string |sim |query |nenhum |Especificar uma pasta |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## OnUpdatedFile
Quando um arquivo é modificado: dispara um fluxo quando um arquivo é modificado em uma pasta do OneDrive for Business

```GET: /datasets/default/triggers/onupdatedfile```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| folderId |string |sim |query |nenhum |Especificar uma pasta |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## ListFolder
Listar arquivos na pasta: lista os arquivos em uma pasta do OneDrive for Business

```GET: /datasets/default/folders/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |string |sim |path |nenhum |Especificar a pasta |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## ListRootFolder
Listar pasta raiz: lista arquivos na pasta raiz do OneDrive for Business

```GET: /datasets/default/folders```

Não há parâmetros para essa chamada

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## ExtractFolderV2
Extrair pasta: extrai uma pasta no OneDrive for Business

```POST: /datasets/default/extractFolderV2```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| fonte |string |sim |query |nenhum |Caminho para o arquivo morto |
| destino |string |sim |query |nenhum |Caminho no OneDrive for Business para extrair o conteúdo do arquivo morto |
| substituir |Booliano |não |query |false |Substitui os arquivos de destino se estiver definido como "true" |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## Definições de objeto
### DataSetsMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| tabular |não definido |Não |
| blob |não definido |Não |

### TabularDataSetsMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| fonte |string |Não |
| displayName |string |Não |
| urlEncoding |string |Não |
| tableDisplayName |string |Não |
| tablePluralName |string |Não |

### BlobDataSetsMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| fonte |string |Não |
| displayName |string |Não |
| urlEncoding |string |Não |

### BlobMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |Não |
| Nome |string |Não |
| DisplayName |string |Não |
| Caminho |string |Não |
| LastModified |string |Não |
| Tamanho |inteiro |Não |
| MediaType |string |Não |
| IsFolder |Booliano |Não |
| ETag |string |Não |
| FileLocator |string |Não |

### Objeto
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
|  | | |

## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->