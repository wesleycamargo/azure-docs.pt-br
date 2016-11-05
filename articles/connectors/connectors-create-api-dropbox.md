---
title: Dropbox | Microsoft Docs
description: Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. Conecte-se ao Dropbox para gerenciar seus arquivos. Você pode executar várias ações, como carregar, atualizar, obter e excluir arquivos no Dropbox.
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: app-service-logic
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: deonhe

---
# Introdução ao conector do Dropbox
Conecte-se ao Dropbox para gerenciar seus arquivos. Você pode executar várias ações, como carregar, atualizar, obter e excluir arquivos no Dropbox.

Para usar [qualquer conector](apis-list.md), primeiro é preciso criar um aplicativo lógico. Você pode começar [criando um aplicativo lógico agora mesmo](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conectar-se ao Dropbox
Para que o aplicativo lógico possa acessar qualquer serviço, crie primeiro uma *conexão* com o serviço. Uma conexão fornece uma conectividade entre um aplicativo lógico e outro serviço. Por exemplo, para se conectar ao Dropbox, é preciso ter uma *conexão* do Dropbox. Para criar uma conexão, é preciso fornecer as credenciais normalmente usadas para acessar o serviço ao qual você deseja se conectar. Desse modo, no exemplo do Dropbox, são necessárias as credenciais da sua conta no Dropbox para criar a conexão com o Dropbox. [Saiba mais sobre conexões]()

### Criar uma conexão com o Dropbox
> [!INCLUDE [Etapas para criar uma conexão com o Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## Usar um gatilho do Dropbox
Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Neste exemplo, usaremos o gatilho **Quando um arquivo é criado**. Quando esse gatilho ocorrer, chamaremos a ação do Dropbox **Obter conteúdo do arquivo usando o caminho**.

1. Insira *dropbox* na caixa de pesquisa no designer de Aplicativos Lógicos e escolha o gatilho **Dropbox – Quando um arquivo é criado**.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)
2. Escolha a pasta na qual você deseja controlar a criação do arquivo. Escolha ... (identificado na caixa vermelha) e navegue até a pasta que deseja selecionar para entrada do gatilho.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)

## Usar uma ação do Dropbox
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Agora que o gatilho foi adicionado, siga estas etapas para adicionar uma ação que obterá o novo conteúdo do arquivo.

1. Escolha **+ Nova Etapa** para adicionar a ação que deseja executar quando um novo arquivo é criado.  
   
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. Escolha **Adicionar uma ação**. Isso abre a caixa de pesquisa, onde é possível procurar qualquer ação que você deseja realizar.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. Insira *dropbox* para pesquisar as ações relacionadas ao Dropbox.
4. Escolha **Dropbox – Obter conteúdo do arquivo usando o caminho** como a ação a ser tomada quando um novo arquivo for criado na pasta Dropbox selecionada. O bloco de controle de ação é aberto. Será solicitado que você autorize o aplicativo lógico a acessar sua conta no Dropbox, caso não tenha feito isso anteriormente.  
   
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. Escolha ... (localizado no lado direito do controle **Caminho do Arquivo**) e navegue até o caminho do arquivo que deseja usar. Ou use o token do **caminho do arquivo** para agilizar a criação do aplicativo lógico.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. Salve seu trabalho e crie um novo arquivo no Dropbox para ativar o fluxo de trabalho.

## Detalhes técnicos
Veja abaixo os detalhes sobre os gatilhos, as ações e as respostas que essa conexão permite:

## Gatilhos do Dropbox
O conector do Dropbox tem os seguintes gatilhos:

| Gatilho | Descrição |
| --- | --- |
| [Quando um arquivo é criado](connectors-create-api-dropbox.md#when-a-file-is-created) |Esta operação dispara um fluxo quando um novo arquivo é criado em uma pasta. |
| [Quando um arquivo é modificado](connectors-create-api-dropbox.md#when-a-file-is-modified) |Esta operação dispara um fluxo quando um arquivo é modificado em uma pasta. |

## Ações do Dropbox
O conector do Dropbox tem as seguintes ações:

| Ação | Descrição |
| --- | --- |
| [Obter metadados do arquivo](connectors-create-api-dropbox.md#get-file-metadata) |Esta operação obtém os metadados de um arquivo. |
| [Atualizar arquivo](connectors-create-api-dropbox.md#update-file) |Esta operação atualiza um arquivo. |
| [Excluir arquivo](connectors-create-api-dropbox.md#delete-file) |Esta operação exclui um arquivo. |
| [Obter metadados do arquivo usando o caminho](connectors-create-api-dropbox.md#get-file-metadata-using-path) |Esta operação obtém os metadados de um arquivo usando o caminho. |
| [Obter o conteúdo do arquivo usando o caminho](connectors-create-api-dropbox.md#get-file-content-using-path) |Esta operação obtém o conteúdo de um arquivo usando o caminho. |
| [Obter conteúdo do arquivo](connectors-create-api-dropbox.md#get-file-content) |Esta operação obtém o conteúdo de um arquivo. |
| [Criar arquivo](connectors-create-api-dropbox.md#create-file) |Esta operação cria um arquivo. |
| [Copiar arquivo](connectors-create-api-dropbox.md#copy-file) |Esta operação copia um arquivo para o Dropbox. |
| [Lista de arquivos na pasta](connectors-create-api-dropbox.md#list-files-in-folder) |Esta operação obtém a lista de arquivos e subpastas em uma pasta. |
| [Lista de arquivos na pasta-raiz](connectors-create-api-dropbox.md#list-files-in-root-folder) |Esta operação obtém a lista de arquivos e subpastas na pasta-raiz. |
| [Extrair o arquivo morto para a pasta](connectors-create-api-dropbox.md#extract-archive-to-folder) |Essa operação extrai um arquivo para uma pasta (exemplo: .zip). |

### Detalhes da ação
Veja abaixo os detalhes das ações e dos gatilhos para esse conector, com suas respostas:

### Obter metadados do arquivo
Esta operação obtém os metadados de um arquivo.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| id* |Arquivo |Selecionar um arquivo |

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
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
Esta operação atualiza um arquivo.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| id* |Arquivo |Selecionar um arquivo |
| body* |Conteúdo do arquivo |O conteúdo do arquivo |

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
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
| id* |Arquivo |Selecionar um arquivo |

Um * indica que uma propriedade é obrigatória

### Obter metadados do arquivo usando o caminho
Esta operação obtém os metadados de um arquivo usando o caminho.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| path* |Caminho do arquivo |Selecionar um arquivo |

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
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
Esta operação obtém o conteúdo de um arquivo usando o caminho.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| path* |Caminho do arquivo |Selecionar um arquivo |

Um * indica que uma propriedade é obrigatória

### Obter conteúdo do arquivo
Esta operação obtém o conteúdo de um arquivo.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| id* |Arquivo |Selecionar um arquivo |

Um * indica que uma propriedade é obrigatória

### Criar arquivo
Esta operação cria um arquivo.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| folderPath* |Caminho da pasta |Selecionar uma pasta |
| name* |Nome do arquivo |Nome do arquivo |
| body* |Conteúdo do arquivo |O conteúdo do arquivo |

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
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

### Copiar arquivo
Esta operação copia um arquivo para o Dropbox.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| source* |Url da origem |URL para o arquivo de origem |
| destination* |Caminho do arquivo de destino |Caminho do arquivo de destino, incluindo o nome do arquivo de destino |
| substituir |Substituir? |Substitui o arquivo de destino se estiver definido como "true" |

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
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

### Quando um arquivo é criado
Esta operação dispara um fluxo quando um novo arquivo é criado em uma pasta.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| folderId* |Pasta |Selecionar uma pasta |

Um * indica que uma propriedade é obrigatória

### Quando um arquivo é modificado
Esta operação dispara um fluxo quando um arquivo é modificado em uma pasta.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| folderId* |Pasta |Selecionar uma pasta |

Um * indica que uma propriedade é obrigatória

### Lista de arquivos na pasta
Esta operação obtém a lista de arquivos e subpastas em uma pasta.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| id* |Pasta |Selecionar uma pasta |

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
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
Esta operação obtém a lista de arquivos e subpastas na pasta-raiz.

Não há parâmetros para essa chamada

#### Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
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

### Extrair o arquivo morto para a pasta
Essa operação extrai um arquivo para uma pasta (exemplo: .zip).

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| source* |Caminho do arquivo de origem |Caminho para o arquivo morto |
| destination* |Caminho da pasta de destino |Caminho para extrair o conteúdo do arquivo |
| substituir |Substituir? |Substitui os arquivos de destino se estiver definido como "true" |

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
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

<!---HONumber=AcomDC_0803_2016-->
