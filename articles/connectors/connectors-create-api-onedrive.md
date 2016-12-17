---
title: "Adicionar o conector do OneDrive em seus Aplicativos Lógicos | Microsoft Docs"
description: "Visão geral do conector do OneDrive com os parâmetros da API REST"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 47a8582a-1b1a-4fc3-beb5-97c60c4306fe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 9c6213f0dcb66ae0c53d716abfe84868b87585f1


---
# <a name="get-started-with-the-onedrive-connector"></a>Introdução ao conector do OneDrive
Conecte-se ao OneDrive para gerenciar seus arquivos, incluindo carregar, obter, excluir arquivos e muito mais. 

Com o OneDrive, você: 

* Compile seu fluxo de trabalho armazenando os arquivos no OneDrive ou atualize os arquivos existentes no OneDrive. 
* Use gatilhos para iniciar o fluxo de trabalho quando um arquivo é criado ou atualizado em seu OneDrive.
* Usar ações para criar um arquivo, excluir um arquivo e muito mais. Por exemplo, quando um novo email do Office 365 for recebido com um anexo (um gatilho), crie um novo arquivo no OneDrive (uma ação).

Este tópico mostra como usar o conector do OneDrive em um aplicativo lógico, além de listar os gatilhos e as ações.

> [!NOTE]
> Esta versão do artigo se aplica à disponibilidade de Aplicativos Lógicos em geral (GA). 
> 
> 

Para saber mais sobre os Aplicativos Lógicos, consulte [O que são aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md) e [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-onedrive"></a>Conectar o OneDrive
Antes do aplicativo lógico poder acessar qualquer serviço, primeiro crie uma *conexão* com o serviço. Uma conexão fornece uma conectividade entre um aplicativo lógico e outro serviço. Por exemplo, para se conectar ao OneDrive, primeiramente é necessária uma *conexão* do OneDrive. Para criar uma conexão, insira as credenciais que você normalmente usa para acessar o serviço ao qual deseja conectar-se. Assim, com o OneDrive, insira as credenciais de sua conta OneDrive para criar a conexão.

### <a name="create-the-connection"></a>Criar a conexão
> [!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]
> 
> 

## <a name="use-a-trigger"></a>Usar um gatilho
Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. Gatilhos "sondam" o serviço no intervalo e na frequência desejados. [Saiba mais sobre gatilhos](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. No aplicativo lógico, digite "onedrive" para obter uma lista de gatilhos:  
   
    ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Escolha **Quando um arquivo é modificado**. Se já existe uma conexão, escolha o botão Mostrar Seletor para selecionar uma pasta.
   
    ![](./media/connectors-create-api-onedrive/sample-folder.png)
   
    Se você for solicitado a entrar, insira os detalhes do logon para criar a conexão. [Criar a conexão](connectors-create-api-onedrive.md#create-the-connection) neste tópico lista as etapas. 
   
   > [!NOTE]
   > Neste exemplo, o aplicativo lógico é executado quando um arquivo na pasta escolhida é atualizado. Para ver os resultados deste gatilho, adicione outra ação que envia um email. Por exemplo, adicione a ação *Enviar um email* do Outlook do Office 365 que envia um email para você quando um arquivo é atualizado. 
   > 
   > 
3. Selecione o botão **Editar** e defina os valores **Frequência** e **Intervalo**. Por exemplo, se você quiser que o gatilho faça uma sondagem a cada 15 minutos, defina a **Frequência** como **Minuto** e **Intervalo** como **15**. 
   
    ![](./media/connectors-create-api-onedrive/trigger-properties.png)
4. **Salve** as alterações (canto superior esquerdo da barra de ferramentas). Seu aplicativo lógico é salvo e pode ser habilitado automaticamente.

## <a name="use-an-action"></a>Usar uma ação
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Selecione o sinal de mais. Você tem várias opções: **adicionar uma ação**, **adicionar uma condição** ou uma das opções **Mais**.
   
    ![](./media/connectors-create-api-onedrive/add-action.png)
2. Escolha **Adicionar uma ação**.
3. Na caixa de texto, digite "onedrive" para obter uma lista de todas as ações disponíveis.
   
    ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 
4. Em nosso exemplo, escolha **OneDrive – criar arquivo**. Se já existir uma conexão, escolha o **Caminho da Pasta** para colocar o arquivo, insira o **Nome do Arquivo** e escolha o **Conteúdo do Arquivo** desejado:  
   
    ![](./media/connectors-create-api-onedrive/sample-action.png)
   
    Se as informações de conexão forem solicitadas, insira os detalhes para criar a conexão. [Criar a conexão](connectors-create-api-onedrive.md#create-the-connection) neste tópico descreve estas propriedades. 
   
   > [!NOTE]
   > Neste exemplo, podemos criar um novo arquivo em uma pasta do OneDrive. Você pode usar a saída de outro gatilho para criar o arquivo do OneDrive. Por exemplo, adicione o gatilho *Quando um novo email chegar* do Outlook do Office 365. Em seguida, adicione a ação *Criar arquivo* do OneDrive, que usa os campos Anexos e Tipo de Conteúdo em um ForEach para criar o novo arquivo no OneDrive. 
   > 
   > ![](./media/connectors-create-api-onedrive/foreach-action.png)
   > 
   > 
5. **Salve** as alterações (canto superior esquerdo da barra de ferramentas). Seu aplicativo lógico é salvo e pode ser habilitado automaticamente.

## <a name="technical-details"></a>Detalhes técnicos
## <a name="triggers"></a>Gatilhos
| Gatilho | Descrição |
| --- | --- |
| [Quando um arquivo é criado](connectors-create-api-onedrive.md#when-a-file-is-created) |Esta operação dispara um fluxo quando um novo arquivo é criado em uma pasta. |
| [Quando um arquivo é modificado](connectors-create-api-onedrive.md#when-a-file-is-modified) |Esta operação dispara um fluxo quando um arquivo é modificado em uma pasta. |

## <a name="actions"></a>Ações
| Ação | Descrição |
| --- | --- |
| [Obter metadados do arquivo](connectors-create-api-onedrive.md#get-file-metadata) |Esta operação obtém os metadados de um arquivo. |
| [Atualizar arquivo](connectors-create-api-onedrive.md#update-file) |Esta operação atualiza um arquivo. |
| [Excluir arquivo](connectors-create-api-onedrive.md#delete-file) |Esta operação exclui um arquivo. |
| [Obter Metadados do Arquivo usando o caminho](connectors-create-api-onedrive.md#get-file-metadata-using-path) |Esta operação obtém os metadados de um arquivo usando o caminho. |
| [Obter o conteúdo do arquivo usando o caminho](connectors-create-api-onedrive.md#get-file-content-using-path) |Esta operação obtém o conteúdo de um arquivo usando o caminho. |
| [Obter conteúdo do arquivo](connectors-create-api-onedrive.md#get-file-content) |Esta operação obtém o conteúdo de um arquivo. |
| [Criar arquivo](connectors-create-api-onedrive.md#create-file) |Esta operação cria um arquivo. |
| [Copiar arquivo](connectors-create-api-onedrive.md#copy-file) |Esta operação copia um arquivo para o OneDrive. |
| [Listar arquivos na pasta](connectors-create-api-onedrive.md#list-files-in-folder) |Esta operação obtém a lista de arquivos e subpastas em uma pasta. |
| [Lista de arquivos na pasta-raiz](connectors-create-api-onedrive.md#list-files-in-root-folder) |Esta operação obtém a lista de arquivos e subpastas na pasta-raiz. |
| [Extrair o arquivo morto para a pasta](connectors-create-api-onedrive.md#extract-archive-to-folder) |Essa operação extrai um arquivo para uma pasta (exemplo: .zip). |

### <a name="action-details"></a>Detalhes da ação
Nesta seção, consulte os detalhes específicos sobre cada ação, incluindo todas as propriedades de entrada obrigatórias ou opcionais, assim como toda saída correspondente associada ao conector.

#### <a name="get-file-metadata"></a>Obter Metadados do Arquivo
Esta operação obtém os metadados de um arquivo. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| id* |Arquivo |Selecionar um arquivo |

Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| ID |string |
| Nome |string |
| displayName |string |
| path |string |
| LastModified |string |
| Tamanho |inteiro |
| MediaType |string |
| IsFolder |booleano |
| ETag |string |
| FileLocator |string |

#### <a name="update-file"></a>Atualizar arquivo
Esta operação atualiza um arquivo. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| id* |Arquivo |Selecionar um arquivo |
| body* |Conteúdo do arquivo |O conteúdo do arquivo |

Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| ID |string |
| Nome |string |
| displayName |string |
| path |string |
| LastModified |string |
| Tamanho |inteiro |
| MediaType |string |
| IsFolder |booleano |
| ETag |string |
| FileLocator |string |

#### <a name="delete-file"></a>Excluir arquivo
Esta operação exclui um arquivo. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| id* |Arquivo |Selecionar um arquivo |

Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
Nenhuma.

#### <a name="get-file-metadata-using-path"></a>Obter Metadados do Arquivo usando o caminho
Esta operação obtém os metadados de um arquivo usando o caminho. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| path* |Caminho do arquivo |Selecionar um arquivo |

Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| ID |string |
| Nome |string |
| displayName |string |
| path |string |
| LastModified |string |
| Tamanho |inteiro |
| MediaType |string |
| IsFolder |booleano |
| ETag |string |
| FileLocator |string |

#### <a name="get-file-content-using-path"></a>Obter o conteúdo do arquivo usando o caminho
Esta operação obtém o conteúdo de um arquivo usando o caminho. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| path* |Caminho do arquivo |Selecionar um arquivo |

Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
Nenhuma.

#### <a name="get-file-content"></a>Obter conteúdo do arquivo
Esta operação obtém o conteúdo de um arquivo. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| id* |Arquivo |Selecionar um arquivo |

Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
Nenhuma.

#### <a name="create-file"></a>Criar arquivo
Esta operação cria um arquivo. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| folderPath* |Caminho da pasta |Selecionar uma pasta |
| name* |Nome do arquivo |Nome do arquivo |
| body* |Conteúdo do arquivo |O conteúdo do arquivo |

Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| ID |string |
| Nome |string |
| displayName |string |
| path |string |
| LastModified |string |
| Tamanho |inteiro |
| MediaType |string |
| IsFolder |booleano |
| ETag |string |
| FileLocator |string |

#### <a name="copy-file"></a>Copiar arquivo
Esta operação copia um arquivo para o OneDrive. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| source* |Url da origem |URL para o arquivo de origem |
| destination* |Caminho do arquivo de destino |Caminho do arquivo de destino, incluindo o nome do arquivo de destino |
| substituir |Substituir? |Substitui o arquivo de destino se estiver definido como "true" |

Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| ID |string |
| Nome |string |
| displayName |string |
| path |string |
| LastModified |string |
| Tamanho |inteiro |
| MediaType |string |
| IsFolder |booleano |
| ETag |string |
| FileLocator |string |

#### <a name="when-a-file-is-created"></a>Quando um arquivo é criado
Esta operação dispara um fluxo quando um novo arquivo é criado em uma pasta. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| folderId* |Pasta |Selecionar uma pasta |

Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
Nenhuma.

#### <a name="when-a-file-is-modified"></a>Quando um arquivo é modificado
Esta operação dispara um fluxo quando um arquivo é modificado em uma pasta. 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| folderId* |Pasta |Selecionar uma pasta |

Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
Nenhuma.

#### <a name="list-files-in-folder"></a>Lista de arquivos na pasta
Esta operação obtém a lista de arquivos e subpastas em uma pasta.

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| id* |Pasta |Selecionar uma pasta |

Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| ID |string |
| Nome |string |
| displayName |string |
| path |string |
| LastModified |string |
| Tamanho |inteiro |
| MediaType |string |
| IsFolder |booleano |
| ETag |string |
| FileLocator |cadeia de caracteres |

#### <a name="list-files-in-root-folder"></a>Lista de arquivos na pasta-raiz
Esta operação obtém a lista de arquivos e subpastas na pasta-raiz. 

Não existem parâmetros para esta chamada.

##### <a name="output-details"></a>Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| ID |string |
| Nome |string |
| displayName |string |
| path |string |
| LastModified |string |
| Tamanho |inteiro |
| MediaType |string |
| IsFolder |booleano |
| ETag |string |
| FileLocator |string |

#### <a name="extract-archive-to-folder"></a>Extrair o arquivo morto para a pasta
Essa operação extrai um arquivo para uma pasta (exemplo: .zip). 

| Nome da Propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| source* |Caminho do arquivo de origem |Caminho para o arquivo morto |
| destination* |Caminho da pasta de destino |Caminho para extrair o conteúdo do arquivo |
| substituir |Substituir? |Substitui os arquivos de destino se estiver definido como "true" |

Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
BlobMetadata

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| ID |string |
| Nome |string |
| displayName |string |
| path |string |
| LastModified |string |
| Tamanho |inteiro |
| MediaType |string |
| IsFolder |booleano |
| ETag |string |
| FileLocator |cadeia de caracteres |

## <a name="http-responses"></a>Respostas HTTP
A tabela a seguir descreve as respostas para as ações e gatilhos, e as descrições da resposta:  

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

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md). Explore os outros conectores disponíveis nos Aplicativos Lógicos em nossa [lista de APIs](apis-list.md).




<!--HONumber=Nov16_HO3-->


