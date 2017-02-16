---
title: "Usar o Conector do SharePoint Online em seus Aplicativos Lógicos | Microsoft Docs"
description: "Introdução ao uso do Conector do SharePoint Online em seus Aplicativos lógicos"
services: 
documentationcenter: 
author: msftman
manager: anneta
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: c462c98e2ecef1afe62ef921265b8842b6ae717d


---
# <a name="get-started-with-the-sharepoint-online-connector"></a>Introdução ao conector do SharePoint Online
O Conector do SharePoint fornece uma maneira de trabalhar com listas do SharePoint.

> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.
> 
> 

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="lets-talk-about-triggers-and-actions"></a>Vamos conversar sobre gatilhos e ações
O Conector do SharePoint pode ser usado como uma ação; ele tem gatilho(s). Todos os conectores dão suporte a dados nos formatos JSON e XML. 

O Conector do SharePoint tem a(s) seguinte(s) ação(ões) e/ou gatilho(s) disponível(is):

### <a name="sharepoint-actions"></a>Ações do SharePoint
Você pode executar as seguintes ações:

| Ação | Descrição |
| --- | --- |
| GetFileMetadata |Usada para obter metadados de um arquivo da Biblioteca de Documentos |
| UpdateFile |Usada para atualizar um arquivo na Biblioteca de Documentos |
| DeleteFile |Usada para excluir um arquivo na Biblioteca de Documentos |
| GetFileMetadataByPath |Usada para obter metadados de um arquivo da Biblioteca de Documentos |
| GetFileContentByPath |Usada para obter um arquivo na Biblioteca de Documentos |
| GetFileContent |Usada para obter um arquivo na Biblioteca de Documentos |
| CreateFile |Usada para carregar um arquivo na biblioteca de documentos |
| CopyFile |Usada para copiar um arquivo na Biblioteca de Documentos |
| ExtractFolderV2 |Usada para extrair uma pasta na Biblioteca de Documentos |
| PostItem |Cria um novo item em uma lista do SharePoint |
| GetItem |Recupera um único item de uma lista do SharePoint |
| DeleteItem |Exclui um item de uma lista do SharePoint |
| PatchItem |Atualiza um item em uma lista do SharePoint |

### <a name="sharepoint-triggers"></a>Gatilhos do SharePoint
Você pode escutar estes eventos:

| Gatilho | Descrição |
| --- | --- |
| OnNewFile |Dispara um fluxo quando um novo arquivo é criado em uma pasta do SharePoint |
| OnUpdatedFile |Dispara um fluxo quando um arquivo é modificado em uma pasta do SharePoint |
| GetOnNewItems |Quando um novo item é criado em uma lista do SharePoint |
| GetOnUpdatedItems |Quando um item existente é modificado em uma lista do SharePoint |

## <a name="create-a-connection-to-sharepoint"></a>Criar uma conexão com o SharePoint
Para usar o Conector do SharePoint, crie primeiro uma **conexão** e, então, forneça os detalhes dessas propriedades: 

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| A criptografia do token |Sim |Fornecer credenciais do SharePoint |

Para se conectar ao **SharePoint Online**, você precisa fornecer sua identidade (nome de usuário e senha, credenciais de cartão inteligente, etc.) para o SharePoint Online. Depois de se autenticar, você poderá continuar e usar o Conector do SharePoint Online em seu aplicativo lógico. 

No designer do aplicativo lógico, siga estas etapas para entrar no SharePoint e criar a conexão **conexão** para uso em seu aplicativo lógico:

1. Insira SharePoint na caixa de pesquisa e aguarde até que a pesquisa retorne todas as entradas com SharePoint no nome:    
   ![Configurar o SharePoint][1]  
2. Selecione **SharePoint Online, Quando um arquivo é criado**   
3. Selecione **Entrar no SharePoint Online**:   
   ![Configurar o SharePoint][2]    
4. Forneça suas credenciais do SharePoint para entrar e se autenticar com o SharePoint    
   ![Configurar o SharePoint][3]     
5. Após a conclusão da autenticação, você será redirecionado ao seu aplicativo lógico para concluí-lo por meio da configuração da caixa de diálogo **Quando um arquivo é criado** do SharePoint.          
   ![Configurar o SharePoint][4]  
6. Em seguida, é possível adicionar outros gatilhos e outras ações necessárias para concluir seu aplicativo lógico.   
7. Salve seu trabalho selecionando **Salvar** na barra de menus acima.  

## <a name="sharepoint-rest-api-reference"></a>Referência da API REST do SharePoint
#### <a name="this-documentation-is-for-version-10"></a>Esta documentação destina-se à versão: 1.0
### <a name="used-for-getting-a-file-metadata-on-document-library"></a>Usada para obter metadados de um arquivo da Biblioteca de Documentos
**```GET: /datasets/{dataset}/files/{id}```** 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |nenhum |URL do site do SharePoint. Por exemplo http://contoso.sharepoint.com/sites/mysite |
| ID |string |Sim |path |nenhum |Identificador exclusivo do arquivo |

### <a name="here-are-the-possible-responses"></a>Estas são as respostas possíveis:
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

- - -
### <a name="used-for-updating-a-file-on-document-library"></a>Usada para atualizar um arquivo na Biblioteca de Documentos
**```PUT: /datasets/{dataset}/files/{id}```** 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |nenhum |URL do site do SharePoint. Por exemplo http://contoso.sharepoint.com/sites/mysite |
| ID |string |Sim |path |nenhum |Identificador exclusivo do arquivo |
| corpo | |Sim |corpo |nenhum |O conteúdo do arquivo |

### <a name="here-are-the-possible-responses"></a>Estas são as respostas possíveis:
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

- - -
### <a name="used-for-deleting-a-file-on-document-library"></a>Usada para excluir um arquivo na Biblioteca de Documentos
**```DELETE: /datasets/{dataset}/files/{id}```** 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |nenhum |URL do site do SharePoint. Por exemplo http://contoso.sharepoint.com/sites/mysite |
| ID |string |Sim |path |nenhum |Identificador exclusivo do arquivo |

### <a name="here-are-the-possible-responses"></a>Estas são as respostas possíveis:
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

- - -
### <a name="used-for-getting-a-file-metadata-on-document-library"></a>Usada para obter metadados de um arquivo da Biblioteca de Documentos
**```GET: /datasets/{dataset}/GetFileByPath```** 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |nenhum |URL do site do SharePoint. Por exemplo http://contoso.sharepoint.com/sites/mysite |
| path |string |sim |query |nenhum |Caminho do arquivo |

### <a name="here-are-the-possible-responses"></a>Estas são as respostas possíveis:
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

- - -
### <a name="used-for-getting-a-file-on-document-library"></a>Usada para obter um arquivo na Biblioteca de Documentos
**```GET: /datasets/{dataset}/GetFileContentByPath```** 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |nenhum |URL do site do SharePoint. Por exemplo http://contoso.sharepoint.com/sites/mysite |
| path |string |sim |query |nenhum |Caminho do arquivo |

### <a name="here-are-the-possible-responses"></a>Estas são as respostas possíveis:
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

- - -
### <a name="used-for-getting-a-file-on-document-library"></a>Usada para obter um arquivo na Biblioteca de Documentos
**```GET: /datasets/{dataset}/files/{id}/content```** 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |nenhum |URL do site do SharePoint. Por exemplo http://contoso.sharepoint.com/sites/mysite |
| ID |string |Sim |path |nenhum |Identificador exclusivo do arquivo |

### <a name="here-are-the-possible-responses"></a>Estas são as respostas possíveis:
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

- - -
### <a name="used-for-uploading-a-file-on-document-library"></a>Usada para carregar um arquivo na biblioteca de documentos
**```POST: /datasets/{dataset}/files```** 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |nenhum |URL do site do SharePoint. Por exemplo http://contoso.sharepoint.com/sites/mysite |
| folderPath |string |sim |query |nenhum |O caminho para a pasta |
| Nome |string |sim |query |nenhum |Nome do arquivo |
| corpo | |Sim |corpo |nenhum |O conteúdo do arquivo |

### <a name="here-are-the-possible-responses"></a>Estas são as respostas possíveis:
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

- - -
### <a name="used-for-copying-a-file-on-document-library"></a>Usada para copiar um arquivo na Biblioteca de Documentos
**```POST: /datasets/{dataset}/copyFile```** 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |nenhum |URL do site do SharePoint. Por exemplo http://contoso.sharepoint.com/sites/mysite |
| fonte |string |sim |query |nenhum |Caminho para o arquivo de origem |
| destino |string |sim |query |nenhum |Caminho para o arquivo de destino |
| substituir |booleano |não |query |false |Se deseja ou não substituir um arquivo existente |

### <a name="here-are-the-possible-responses"></a>Estas são as respostas possíveis:
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

- - -
### <a name="triggers-a-flow-when-a-new-file-is-created-in-a-sharepoint-folder"></a>Dispara um fluxo quando um novo arquivo é criado em uma pasta do SharePoint
**```GET: /datasets/{dataset}/triggers/onnewfile```** 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |nenhum |URL do site do SharePoint |
| folderId |string |sim |query |nenhum |Identificador exclusivo da pasta no SharePoint |

### <a name="here-are-the-possible-responses"></a>Estas são as respostas possíveis:
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

- - -
### <a name="triggers-a-flow-when-a-file-is-modified-in-a-sharepoint-folder"></a>Dispara um fluxo quando um arquivo é modificado em uma pasta do SharePoint
**```GET: /datasets/{dataset}/triggers/onupdatedfile```** 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |nenhum |URL do site do SharePoint |
| folderId |string |sim |query |nenhum |Identificador exclusivo da pasta no SharePoint |

### <a name="here-are-the-possible-responses"></a>Estas são as respostas possíveis:
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

- - -
### <a name="used-for-extracting-a-folder-on-document-library"></a>Usada para extrair uma pasta na Biblioteca de Documentos
**```POST: /datasets/{dataset}/extractFolderV2```** 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |nenhum |URL do site do SharePoint. Por exemplo http://contoso.sharepoint.com/sites/mysite |
| fonte |string |sim |query |nenhum |Caminho para o arquivo de origem |
| destino |string |sim |query |nenhum |Caminho para a pasta de destino |
| substituir |booleano |não |query |false |Se deseja ou não substituir um arquivo existente |

### <a name="here-are-the-possible-responses"></a>Estas são as respostas possíveis:
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

- - -
### <a name="when-a-new-item-is-created-in-a-sharepoint-list"></a>Quando um novo item é criado em uma lista do SharePoint
**```GET: /datasets/{dataset}/tables/{table}/onnewitems```** 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |nenhum |URL do Site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite) |
| tabela |string |Sim |path |nenhum |Nome de lista do SharePoint |
| $skip |inteiro |não |query |Nenhum |Número de entradas a serem ignoradas (padrão = 0) |
| $top |inteiro |não |query |Nenhum |Número máximo de entradas a serem recuperadas (padrão = 256) |
| $filter |string |não |query |Nenhum |Uma consulta de filtro ODATA para restringir o número de entradas |
| $orderby |string |não |query |Nenhum |Uma consulta orderBy do ODATA para especificar a ordem das entradas |

### <a name="here-are-the-possible-responses"></a>Estas são as respostas possíveis:
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

- - -
### <a name="when-an-existing-item-is-modified-in-a-sharepoint-list"></a>Quando um item existente é modificado em uma lista do SharePoint
**```GET: /datasets/{dataset}/tables/{table}/onupdateditems```** 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |nenhum |URL do Site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite) |
| tabela |string |Sim |path |nenhum |Nome de lista do SharePoint |
| $skip |inteiro |não |query |Nenhum |Número de entradas a serem ignoradas (padrão = 0) |
| $top |inteiro |não |query |Nenhum |Número máximo de entradas a serem recuperadas (padrão = 256) |
| $filter |string |não |query |Nenhum |Uma consulta de filtro ODATA para restringir o número de entradas |
| $orderby |string |não |query |Nenhum |Uma consulta orderBy do ODATA para especificar a ordem das entradas |

### <a name="here-are-the-possible-responses"></a>Estas são as respostas possíveis:
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

- - -
### <a name="creates-a-new-item-in-a-sharepoint-list"></a>Cria um novo item em uma lista do SharePoint
**```POST: /datasets/{dataset}/tables/{table}/items```** 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |nenhum |URL do Site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite) |
| tabela |string |Sim |path |nenhum |Nome de lista do SharePoint |
| item | |Sim |corpo |nenhum |Item a ser criado |

### <a name="here-are-the-possible-responses"></a>Estas são as respostas possíveis:
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

- - -
### <a name="retrieves-a-single-item-from-a-sharepoint-list"></a>Recupera um único item de uma lista do SharePoint
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```** 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |nenhum |URL do Site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite) |
| tabela |string |Sim |path |nenhum |Nome de lista do SharePoint |
| ID |inteiro |Sim |path |nenhum |Identificador exclusivo do item a ser recuperado |

### <a name="here-are-the-possible-responses"></a>Estas são as respostas possíveis:
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

- - -
### <a name="deletes-an-item-from-a-sharepoint-list"></a>Exclui um item de uma lista do SharePoint
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```** 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |nenhum |URL do Site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite) |
| tabela |string |Sim |path |nenhum |Nome de lista do SharePoint |
| ID |inteiro |Sim |path |nenhum |Identificador exclusivo do item a ser excluído |

### <a name="here-are-the-possible-responses"></a>Estas são as respostas possíveis:
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

- - -
### <a name="updates-an-item-in-a-sharepoint-list"></a>Atualiza um item em uma lista do SharePoint
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```** 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sim |path |nenhum |URL do Site do SharePoint (exemplo: http://contoso.sharepoint.com/sites/mysite) |
| tabela |string |Sim |path |nenhum |Nome de lista do SharePoint |
| ID |inteiro |Sim |path |nenhum |Identificador exclusivo do item a ser atualizado |
| item | |Sim |corpo |nenhum |Item com propriedades alteradas |

### <a name="here-are-the-possible-responses"></a>Estas são as respostas possíveis:
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

- - -
## <a name="object-definitions"></a>Definições de objeto:
 **DataSetsMetadata**:

Propriedades obrigatórias para DataSetsMetadata:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| tabular |não definido |
| blob |não definido |

 **TabularDataSetsMetadata**:

Propriedades obrigatórias para TabularDataSetsMetadata:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| fonte |string |
| displayName |string |
| urlEncoding |string |
| tableDisplayName |string |
| tablePluralName |string |

 **BlobDataSetsMetadata**:

Propriedades obrigatórias para BlobDataSetsMetadata:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| fonte |string |
| displayName |string |
| urlEncoding |string |

 **BlobMetadata**:

Propriedades obrigatórias para BlobMetadata:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
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

 **Objeto**:

Propriedades obrigatórias para o objeto:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
|  | |

 **TableMetadata**:

Propriedades obrigatórias para TableMetadata:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| Nome |string |
| título |string |
| x-ms-permission |string |
| schema |não definido |

 **DataSetsList**:

Propriedades obrigatórias para DataSetsList:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| value |array |

 **DataSet**:

Propriedades obrigatórias para DataSet:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| Nome |string |
| displayName |string |

 **Tabela**:

Propriedades necessárias para Table:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| Nome |string |
| displayName |string |

 **Item**:

Propriedades obrigatórias para Item:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| ItemInternalId |string |

 **ItemsList**:

Propriedades obrigatórias para ItemsList:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| value |array |

 **TablesList**:

Propriedades obrigatórias para TablesList:

Nenhuma das propriedades é obrigatória. 

**Todas as propriedades**: 

| Nome | Tipo de Dados |
| --- | --- |
| value |array |

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md)  

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png



<!--HONumber=Jan17_HO3-->


