---
title: Saiba como usar o conector de SFTP nos seus aplicativos lógicos | Microsoft Docs
description: Crie aplicativos lógicos com o serviço de Aplicativo do Azure. Conecte-se à API do SFTP para enviar e receber arquivos. Você pode executar várias operações, como criar, atualizar, obter ou excluir arquivos.
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
ms.date: 07/20/2016
ms.author: deonhe

---
# Introdução ao conector de SFTP
Use o conector de SFTP de modo a acessar uma conta SFTP para enviar e receber arquivos. Você pode executar várias operações, como criar, atualizar, obter ou excluir arquivos.

Para usar [qualquer conector](apis-list.md), primeiro é preciso criar um aplicativo lógico. Você pode começar [criando um aplicativo lógico agora mesmo](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conectar-se ao SFTP
Para que o aplicativo lógico possa acessar qualquer serviço, crie primeiro uma *conexão* com o serviço. Uma [conexão](connectors-overview.md) fornece conectividade entre um aplicativo lógico e outro serviço.

### Criar uma conexão com o SFTP
> [!INCLUDE [Etapas para criar uma conexão com o SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## Usar um gatilho de SFTP
Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Neste exemplo, mostrarei como usar o gatilho **SFTP – quando um arquivo é adicionado ou modificado** para iniciar um fluxo de trabalho do aplicativo lógico quando um arquivo é adicionado, ou modificado, em um servidor SFTP. No exemplo, você também aprenderá a adicionar uma condição que verifica o conteúdo do arquivo novo ou modificado e a tomar uma decisão para extrair o arquivo se seu conteúdo indicar que ele deve ser extraído antes do uso. Por fim, você aprenderá a adicionar uma ação para extrair o conteúdo de um arquivo e colocar o conteúdo extraído em uma pasta no servidor SFTP.

Em um exemplo corporativo, você pode usar esse gatilho para monitorar uma pasta SFTP em busca de novos arquivos que representam pedidos de clientes. Você pode usar uma ação de conector de SFTP, como **Obter conteúdo do arquivo** para obter o conteúdo do pedido para processamento posterior e armazenamento em seu banco de dados de pedidos.

> [!INCLUDE [Etapas para criar um gatilho de SFTP](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## Adicione uma condição
> [!INCLUDE [Etapas para adicionar uma condição](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## Usar uma ação de SFTP
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

> [!INCLUDE [Etapas para criar uma ação de SFTP](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## Detalhes técnicos
Veja abaixo os detalhes sobre os gatilhos, as ações e as respostas que essa conexão permite:

## Gatilhos de SFTP
O SFTP tem os seguintes gatilhos:

| Gatilho | Descrição |
| --- | --- |
| [Quando um arquivo é adicionado ou modificado](connectors-create-api-sftp.md#when-a-file-is-added-or-modified) |Esta operação dispara um fluxo quando um arquivo é adicionado ou modificado em uma pasta. |

## Ações de SFTP
O SFTP tem as seguintes ações:

| Ação | Descrição |
| --- | --- |
| [Obter metadados do arquivo](connectors-create-api-sftp.md#get-file-metadata) |Esta operação obtém os metadados do arquivo usando a ID do arquivo. |
| [Atualizar arquivo](connectors-create-api-sftp.md#update-file) |Esta operação atualiza o conteúdo do arquivo. |
| [Excluir arquivo](connectors-create-api-sftp.md#delete-file) |Esta operação exclui um arquivo. |
| [Obter metadados do arquivo usando o caminho](connectors-create-api-sftp.md#get-file-metadata-using-path) |Esta operação obtém os metadados do arquivo usando o caminho do arquivo. |
| [Obter o conteúdo do arquivo usando o caminho](connectors-create-api-sftp.md#get-file-content-using-path) |Esta operação obtém o conteúdo do arquivo usando o caminho do arquivo. |
| [Obter conteúdo do arquivo](connectors-create-api-sftp.md#get-file-content) |Esta operação obtém o conteúdo do arquivo usando a ID do arquivo. |
| [Criar arquivo](connectors-create-api-sftp.md#create-file) |Esta operação carrega um arquivo em um servidor SFTP. |
| [Copiar arquivo](connectors-create-api-sftp.md#copy-file) |Esta operação copia um arquivo em um servidor SFTP. |
| [Lista de arquivos na pasta](connectors-create-api-sftp.md#list-files-in-folder) |Esta operação obtém os arquivos contidos em uma pasta. |
| [Lista de arquivos na pasta-raiz](connectors-create-api-sftp.md#list-files-in-root-folder) |Esta operação obtém os arquivos na pasta raiz. |
| [Extrair pasta](connectors-create-api-sftp.md#extract-folder) |Essa operação extrai um arquivo para uma pasta (exemplo: .zip). |

### Detalhes da ação
Veja abaixo os detalhes das ações e dos gatilhos para esse conector, com suas respostas:

### Obter metadados do arquivo
Esta operação obtém os metadados do arquivo usando a ID do arquivo.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| id* |Arquivo |Especificar o arquivo |

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Caminho |string |
| LastModified |string |
| Tamanho |inteiro |
| MediaType |string |
| IsFolder |booleano |
| ETag |string |
| FileLocator |string |

### Atualizar arquivo
Esta operação atualiza o conteúdo do arquivo.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| id* |Arquivo |Especificar o arquivo |
| body* |Conteúdo do arquivo |Conteúdo do arquivo para atualizar |

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Caminho |string |
| LastModified |string |
| Tamanho |inteiro |
| MediaType |string |
| IsFolder |booleano |
| ETag |string |
| FileLocator |string |

### Excluir arquivo
Esta operação exclui um arquivo.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| id* |Arquivo |Especificar o arquivo |

Um * indica que uma propriedade é obrigatória

### Obter metadados do arquivo usando o caminho
Esta operação obtém os metadados do arquivo usando o caminho do arquivo.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| path* |Caminho do arquivo |Caminho exclusivo do arquivo |

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Caminho |string |
| LastModified |string |
| Tamanho |inteiro |
| MediaType |string |
| IsFolder |booleano |
| ETag |string |
| FileLocator |string |

### Obter o conteúdo do arquivo usando o caminho
Esta operação obtém o conteúdo do arquivo usando o caminho do arquivo.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| path* |Caminho do arquivo |Caminho exclusivo do arquivo |

Um * indica que uma propriedade é obrigatória

### Obter conteúdo do arquivo
Esta operação obtém o conteúdo do arquivo usando a ID do arquivo.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| id* |Arquivo |Especificar o arquivo |

Um * indica que uma propriedade é obrigatória

### Criar arquivo
Esta operação carrega um arquivo em um servidor SFTP.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| folderPath* |Caminho da pasta |Caminho exclusivo da pasta |
| name* |Nome do arquivo |Nome do arquivo |
| body* |Conteúdo do arquivo |Conteúdo do arquivo a ser criado |

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída
BlobMetadata

|  | Nome da Propriedade | Tipo de Dados |
| --- | --- | --- |
| ID |string | |
| Nome |string | |
| DisplayName |string | |
| Caminho |string | |
| LastModified |string | |
| Tamanho |inteiro | |
| MediaType |string | |
| IsFolder |booleano | |
| ETag |string | |
| FileLocator |string | |

### Copiar arquivo
Esta operação copia um arquivo em um servidor SFTP.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| source* |Caminho do arquivo de origem |Caminho para o arquivo de origem |
| destination* |Caminho do arquivo de destino |Caminho para o arquivo de destino, incluindo o nome do arquivo |
| substituir |Substituir? |Substitui o arquivo de destino se estiver definido como "true" |

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Caminho |string |
| LastModified |string |
| Tamanho |inteiro |
| MediaType |string |
| IsFolder |booleano |
| ETag |string |
| FileLocator |string |

### Quando um arquivo é adicionado ou modificado
Esta operação dispara um fluxo quando um arquivo é adicionado ou modificado em uma pasta.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| folderId* |Pasta |Especificar uma pasta |

Um * indica que uma propriedade é obrigatória

### Lista de arquivos na pasta
Esta operação obtém os arquivos contidos em uma pasta.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| id* |Pasta |Especificar a pasta |

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Caminho |string |
| LastModified |string |
| Tamanho |inteiro |
| MediaType |string |
| IsFolder |booleano |
| ETag |string |
| FileLocator |cadeia de caracteres |

### Lista de arquivos na pasta-raiz
Esta operação obtém os arquivos na pasta raiz.

Não há parâmetros para essa chamada

#### Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Caminho |string |
| LastModified |string |
| Tamanho |inteiro |
| MediaType |string |
| IsFolder |booleano |
| ETag |string |
| FileLocator |string |

### Extrair pasta
Essa operação extrai um arquivo para uma pasta (exemplo: .zip).

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| source* |Caminho do arquivo de origem |Caminho para o arquivo morto |
| destination* |Caminho da pasta de destino |Caminho para a pasta de destino |
| substituir |Substituir? |Substitui os arquivos de destino se estiver definido como "true" |

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Caminho |string |
| LastModified |string |
| Tamanho |inteiro |
| MediaType |string |
| IsFolder |booleano |
| ETag |string |
| FileLocator |cadeia de caracteres |

## Respostas HTTP
As ações e os gatilhos acima podem retornar um ou mais dos seguintes códigos de status HTTP:

| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 202 |Aceita |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido. |
| padrão |Falha na Operação. |

## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!----HONumber=AcomDC_0803_2016-->
