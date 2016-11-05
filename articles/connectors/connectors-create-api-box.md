---
title: Adicionar o conector do Box aos seus Aplicativos Lógicos | Microsoft Docs
description: Visão geral do Conector do Box com os parâmetros da API REST
services: ''
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
tags: connectors

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: mandia

---
# Introdução ao conector do Box
Conecte-se ao Box, crie arquivos, exclua arquivos e muito mais.

> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.
> 
> 

Com o Box, você pode:

* Compile seu fluxo de negócios baseado nos dados que obtém do Box.
* Usar gatilhos para quando um arquivo for criado ou atualizado.
* Usar ações para copiar um arquivo, excluir um arquivo e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando um arquivo é alterado no Box, você pode obtê-lo e enviá-lo por email usando o Office 365.

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações
O Box inclui os seguintes gatilhos e ações.

| Gatilhos | Ações |
| --- | --- |
| <ul><li>Quando um arquivo é criado</li><li>Quando um arquivo é modificado</li></ul> |<ul><li>Criar arquivo</li><li>Quando um arquivo é criado</li><li>Copiar arquivo</li><li>Excluir arquivo</li><li>Extrair o arquivo para a pasta</li><li>Obter conteúdo do arquivo usando a ID</li><li>Obter arquivo usando o caminho</li><li>Obter metadados do arquivo usando a ID</li><li>Obter metadados de arquivo usando o caminho</li><li>Atualizar arquivo</li><li>Quando um arquivo é modificado</li></ul> |

Todos os conectores dão suporte a dados nos formatos JSON e XML.

## Criar uma conexão com o Box
Quando você adiciona esse conector aos seus aplicativos lógicos, precisa autorizar que os aplicativos lógicos se conectem ao Box.

> [!INCLUDE [Etapas para criar uma conexão com o Box](../../includes/connectors-create-api-box.md)]
> 
> 

Depois de criar a conexão, insira as propriedades do Box. A **Referência da API REST** neste tópico descreve essas propriedades.

> [!TIP]
> Você pode usar essa mesma conexão do Box em outros aplicativos lógicos.
> 
> 

## Referência da API REST do Swagger
Aplica-se à versão: 1.0.

### Criar arquivo
Carrega um arquivo no Box. ```POST: /datasets/default/files```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |Sim |query |Nenhum |Caminho da pasta para carregar o arquivo no Box |
| name |string |Sim |query |Nenhum |Nome do arquivo a ser criado no Box |
| corpo |cadeia de caracteres(binário) |Sim |corpo |Nenhum |Conteúdo do arquivo para carregar para o Box |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### Quando um arquivo é criado
Dispara um fluxo quando um novo arquivo é criado em uma pasta do Box.```GET: /datasets/default/triggers/onnewfile```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| folderId |string |Sim |query |Nenhum |Identificador exclusivo da pasta no Box |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### Copiar arquivo
Copia um arquivo no Box. ```POST: /datasets/default/copyFile```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| fonte |string |Sim |query |Nenhum |URL para o arquivo de código-fonte |
| destino |string |Sim |query |Nenhum |Caminho do arquivo de destino no Box, incluindo o nome do arquivo de destino |
| substituir |Booliano |Não |query |Nenhum |Substitui o arquivo de destino se estiver definido como “true” |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### Excluir arquivo
Exclui um arquivo do Box. ```DELETE: /datasets/default/files/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |string |Sim |path |Nenhum |Identificador exclusivo do arquivo a ser excluído do Box |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### Extrair o arquivo morto para a pasta
Extrai um arquivo morto para uma pasta no Box (exemplo: .zip). ```POST: /datasets/default/extractFolderV2```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| fonte |string |Sim |query | |Caminho para o arquivo morto |
| destino |string |Sim |query | |Caminho no Box para extrair os conteúdos do arquivo morto |
| substituir |Booliano |Não |query | |Substitui os arquivos de destino se estiver definido como “true” |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### Obter o conteúdo do arquivo usando a ID
Recupera o conteúdo do arquivo do Box usando a ID. ```GET: /datasets/default/files/{id}/content```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |string |Sim |path |Nenhum |Identificador exclusivo do arquivo no Box |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### Obter o conteúdo do arquivo usando o caminho
Recupera o conteúdo do arquivo do Box usando o caminho. ```GET: /datasets/default/GetFileContentByPath```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| path |string |Sim |query |Nenhum |Caminho exclusivo para o arquivo no Box |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### Obter metadados de arquivo usando a ID
Recupera os metadados do arquivo do Box usando a ID do arquivo. ```GET: /datasets/default/files/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |string |Sim |path |Nenhum |Identificador exclusivo do arquivo no Box |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### Obter metadados de arquivo usando o caminho
Recupera os metadados do arquivo do Box usando o caminho. ```GET: /datasets/default/GetFileByPath```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| path |string |Sim |query |Nenhum |Caminho exclusivo para o arquivo no Box |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### Atualizar arquivo
Atualiza um arquivo no Box. ```PUT: /datasets/default/files/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| ID |string |Sim |path |Nenhum |Identificador exclusivo do arquivo a ser atualizado no Box |
| corpo |cadeia de caracteres(binário) |Sim |corpo |Nenhum |Conteúdo do arquivo para atualizar no Box |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### Quando um arquivo é modificado
Dispara um fluxo quando um arquivo é modificado em uma pasta do Box. ```GET: /datasets/default/triggers/onupdatedfile```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| folderId |string |Sim |query |Nenhum |Identificador exclusivo da pasta no Box |

#### Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## Definições de objeto
#### DataSetsMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| tabular |não definido |não |
| blob |não definido |não |

#### TabularDataSetsMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| fonte |string |não |
| displayName |string |não |
| urlEncoding |string |não |
| tableDisplayName |string |não |
| tablePluralName |string |não |

#### BlobDataSetsMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| fonte |string |não |
| displayName |string |não |
| urlEncoding |string |não |

#### BlobMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |não |
| Nome |string |não |
| DisplayName |string |não |
| Caminho |string |não |
| LastModified |string |não |
| Tamanho |inteiro |não |
| MediaType |string |não |
| IsFolder |Booliano |não |
| ETag |string |não |
| FileLocator |string |não |

## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0824_2016-->