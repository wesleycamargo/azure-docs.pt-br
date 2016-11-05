---
title: Adicionar o conector do armazenamento de blobs do Azure a seus Aplicativos Lógicos | Microsoft Docs
description: Visão geral do conector do armazenamento de blobs do Azure com parâmetros da API REST
services: ''
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/25/2016
ms.author: mandia

---
# Introdução ao conector de armazenamento de blobs do Azure
O armazenamento de Blobs do Azure é um serviço para armazenar grandes quantidades de dados não estruturados. Execute várias ações, como carregar, atualizar, obter e excluir blobs no armazenamento de blobs do Azure.

Com o armazenamento de blobs do Azure, você:

* Compile seu fluxo de trabalho ao carregar novos projetos ou ao obter os arquivos que foram atualizados recentemente.
* Use as ações para obter metadados do arquivo, excluir um arquivo, copiar arquivos e muito mais. Por exemplo, quando uma ferramenta é atualizada em um site do Azure (um gatilho), então, atualize um arquivo no armazenamento de blobs (uma ação).

Este tópico mostra como usar o conector de armazenamento de blobs em um aplicativo lógico e também lista as ações.

> [!NOTE]
> Esta versão do artigo se aplica à disponibilidade de Aplicativos Lógicos em geral (GA).
> 
> 

Para saber mais sobre Aplicativos Lógicos, confira [O que são aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md) e [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conectar o armazenamento de blobs do Azure
Para que o aplicativo lógico possa acessar qualquer serviço, crie primeiro uma *conexão* com o serviço. Uma conexão fornece uma conectividade entre um aplicativo lógico e outro serviço. Por exemplo, para se conectar a uma conta de armazenamento, você primeiro cria uma *conexão* de armazenamento de blobs. Para criar uma conexão, insira as credenciais que você normalmente usa para acessar o serviço ao qual está se conectando. Assim, com o armazenamento do Azure, insira as credenciais de sua conta de armazenamento para criar a conexão.

#### Criar a conexão
> [!INCLUDE [Criar uma conexão com o armazenamento de blobs do Azure](../../includes/connectors-create-api-azureblobstorage.md)]
> 
> 

## Usar um gatilho
Esse conector não tem gatilhos. Use outros gatilhos para iniciar o aplicativo lógico, como um gatilho de Recorrência, um gatilho de Webhook HTTP, gatilhos disponíveis com outros conectores e muito mais. [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md) fornece um exemplo.

## Usar uma ação
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico.

1. Selecione o sinal de mais. Você tem várias opções: **Adicionar uma ação**, **Adicionar uma condição** ou uma das opções **Mais**.
   
    ![](./media/connectors-create-api-azureblobstorage/add-action.png)
2. Escolha **Adicionar uma ação**.
3. Na caixa de texto, digite "blob" para obter uma lista de todas as ações disponíveis.
   
    ![](./media/connectors-create-api-azureblobstorage/actions.png)
4. Em nosso exemplo, escolha **AzureBlob – Obter metadados do arquivo usando o caminho**. Se já existir uma conexão, selecione **...** Botão (Mostrar Seletor) para selecionar um arquivo.
   
    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)
   
    Se as informações de conexão forem solicitadas, insira os detalhes para criar a conexão. [Criar a conexão](connectors-create-api-azureblobstorage.md#create-the-connection) neste tópico descreve estas propriedades.
   
   > [!NOTE]
   > Neste exemplo, obtemos os metadados de um arquivo. Para ver os metadados, adicione outra ação que cria um novo arquivo usando outro conector. Por exemplo, adicione uma ação do OneDrive que cria um novo arquivo de "teste" com base nos metadados.
   > 
   > 
5. **Salve** as alterações (canto superior esquerdo da barra de ferramentas). Seu aplicativo lógico é salvo e pode ser habilitado automaticamente.

> [!TIP]
> [Storage Explorer](http://storageexplorer.com/) é uma excelente ferramenta para gerenciar várias contas de armazenamento.
> 
> 

## Detalhes técnicos
## Ações de blobs de armazenamento
| Ação | Descrição |
| --- | --- |
| [Obter metadados do arquivo](connectors-create-api-azureblobstorage.md#get-file-metadata) |Esta operação obtém os metadados do arquivo usando a id do arquivo. |
| [Atualizar arquivo](connectors-create-api-azureblobstorage.md#update-file) |Esta operação atualiza um arquivo. |
| [Excluir arquivo](connectors-create-api-azureblobstorage.md#delete-file) |Esta operação exclui um arquivo. |
| [Obter metadados do arquivo usando o caminho](connectors-create-api-azureblobstorage.md#get-file-metadata-using-path) |Esta operação obtém os metadados do arquivo usando o caminho. |
| [Obter o conteúdo do arquivo usando o caminho](connectors-create-api-azureblobstorage.md#get-file-content-using-path) |Esta operação obtém o conteúdo do arquivo usando o caminho. |
| [Obter conteúdo do arquivo](connectors-create-api-azureblobstorage.md#get-file-content) |Esta operação obtém o conteúdo do arquivo usando a id. |
| [Criar arquivo](connectors-create-api-azureblobstorage.md#create-file) |Esta operação carrega um arquivo. |
| [Copiar arquivo](connectors-create-api-azureblobstorage.md#copy-file) |Essa operação copia um arquivo para o Armazenamento de Blobs do Azure. |
| [Extrair o arquivo morto para a pasta](connectors-create-api-azureblobstorage.md#extract-archive-to-folder) |Essa operação extrai um arquivo para uma pasta (exemplo: .zip). |

### Detalhes da ação
Nesta seção, consulte os detalhes específicos sobre cada ação, incluindo todas as propriedades de entrada obrigatórias ou opcionais, assim como toda saída correspondente associada ao conector.

#### Obter metadados do arquivo
Esta operação obtém os metadados do arquivo usando a id do arquivo.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| id* |Arquivo |Selecionar um arquivo |

Um asterisco (*) significa que a propriedade é obrigatória.

##### Detalhes da Saída
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

#### Atualizar arquivo
Esta operação atualiza um arquivo.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| id* |Arquivo |Selecionar um arquivo |
| body* |Conteúdo do arquivo |Conteúdo do arquivo para atualizar |

Um asterisco (*) significa que a propriedade é obrigatória.

##### Detalhes da Saída
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

#### Excluir arquivo
Esta operação exclui um arquivo.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| id* |Arquivo |Selecionar um arquivo |

Um asterisco (*) significa que a propriedade é obrigatória.

##### Detalhes da Saída
Nenhum.

#### Obter metadados do arquivo usando o caminho
Esta operação obtém os metadados do arquivo usando o caminho.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| path* |Caminho do arquivo |Selecionar um arquivo |

Um asterisco (*) significa que a propriedade é obrigatória.

##### Detalhes da Saída
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

#### Obter o conteúdo do arquivo usando o caminho
Esta operação obtém o conteúdo do arquivo usando o caminho.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| path* |Caminho do arquivo |Selecionar um arquivo |

Um asterisco (*) significa que a propriedade é obrigatória.

##### Detalhes da Saída
Nenhum.

#### Obter conteúdo do arquivo
Esta operação obtém o conteúdo do arquivo usando a id.

| Nome da Propriedade | Tipo de Dados | Descrição |
| --- | --- | --- |
| id* |string |Selecionar um arquivo |

Um asterisco (*) significa que a propriedade é obrigatória.

##### Detalhes da Saída
Nenhum.

#### Criar arquivo
Esta operação carrega um arquivo.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| folderPath* |Caminho da pasta |Selecionar uma pasta |
| name* |Nome do arquivo |Nome do arquivo para carregar |
| body* |Conteúdo do arquivo |Conteúdo do arquivo para carregar |

Um asterisco (*) significa que a propriedade é obrigatória.

##### Detalhes da Saída
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

#### Copiar arquivo
Essa operação copia um arquivo para o Armazenamento de Blobs do Azure.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| source* |Url da origem |Especificar a Url para o arquivo de origem |
| destination* |Caminho do arquivo de destino |Especificar o caminho do arquivo de destino, incluindo o nome do arquivo de destino |
| substituir |Substituir? |Um arquivo de destino existente deve ser substituído (true/false)? |

Um asterisco (*) significa que a propriedade é obrigatória.

##### Detalhes da Saída
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

#### Extrair o arquivo morto para a pasta
Essa operação extrai um arquivo para uma pasta (exemplo: .zip).

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| source* |Caminho do arquivo de origem |Selecionar um arquivo |
| destination* |Caminho da pasta de destino |Selecionar o conteúdo para extrair |
| substituir |Substituir? |Um arquivo de destino existente deve ser substituído (true/false)? |

Um asterisco (*) significa que a propriedade é obrigatória.

##### Detalhes da Saída
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
Ao fazer chamadas para diferentes ações, você pode obter certas respostas. A tabela a seguir descreve as respostas e suas descrições:

| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 202 |Aceita |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md). Explore os outros conectores disponíveis nos Aplicativos Lógicos em nossa [lista de APIs](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->