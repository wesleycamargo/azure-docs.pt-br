<properties
pageTitle="Saiba como usar o conector de FTP em aplicativos lógicos | Microsoft Azure"
description="Crie aplicativos lógicos com o serviço de Aplicativo do Azure. Conecte-se ao servidor FTP para gerenciar seus arquivos. Você pode executar várias ações, como carregar, atualizar, obter e excluir arquivos no servidor FTP."
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/22/2016"
ms.author="deonhe"/>

# Introdução ao conector de FTP

Use o conector de FTP para monitorar, gerenciar e criar arquivos em um servidor FTP.

Para usar [qualquer conector](./apis-list.md), primeiro é preciso criar um aplicativo lógico. Você pode começar [criando um aplicativo lógico agora mesmo](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conectar-se ao FTP

Para que o aplicativo lógico possa acessar qualquer serviço, crie primeiro uma *conexão* com o serviço. Uma [conexão](./connectors-overview.md) fornece conectividade entre um aplicativo lógico e outro serviço.

### Criar uma conexão com o FTP

>[AZURE.INCLUDE [Etapas para criar uma conexão com o FTP](../../includes/connectors-create-api-ftp.md)]

## Usar o gatilho de FTP

Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.IMPORTANT]O conector de FTP exige um servidor FTP que possa ser acessado pela Internet e esteja configurado para operar com o modo PASSIVO. Além disso, o conector de FTP **não é compatível com FTPS implícito (FTP por SSL)**. O conector de FTP permite apenas FTPS explícito (FTP por SSL).

Neste exemplo, mostrarei como usar o gatilho **FTP – Quando um arquivo é adicionado ou modificado** para iniciar um fluxo de trabalho do aplicativo lógico quando um arquivo é adicionado, ou modificado, em um servidor FTP. Em um exemplo corporativo, você pode usar esse gatilho para monitorar uma pasta FTP em busca de novos arquivos que representam pedidos de clientes. Você pode usar uma ação de conector FTP, como **Obter conteúdo do arquivo** para obter o conteúdo do pedido para processamento posterior e armazenamento em seu banco de dados de pedidos.

1. Insira *ftp* na caixa de pesquisa no designer de aplicativos lógicos e escolha o gatilho **FTP – Quando um arquivo é adicionado ou modificado** ![Imagem 1 do gatilho de FTP](./media/connectors-create-api-ftp/ftp-trigger-1.png) O controle **Quando um arquivo é adicionado ou modificado** é aberto ![Imagem 2 do gatilho de FTP](./media/connectors-create-api-ftp/ftp-trigger-2.png)
- Escolha **...**, localizado no lado direito do controle. Isso abre o controle de seletor de pasta ![Imagem 3 do gatilho de FTP](./media/connectors-create-api-ftp/ftp-trigger-3.png)
- Escolha **>** (seta para a direita) e procure a pasta que deseja monitorar em busca de arquivos novos ou modificados. Selecione a pasta e observe que a pasta agora é exibida no controle **Pasta**. ![Imagem 4 do gatilho de FTP](./media/connectors-create-api-ftp/ftp-trigger-4.png)


Neste ponto, seu aplicativo lógico foi configurado com um gatilho que iniciará uma execução de outros gatilhos e as ações no fluxo de trabalho quando um arquivo é, ou modificado, ou criado, na pasta FTP específica.

>[AZURE.NOTE]Para que um aplicativo lógico funcione, ele deve conter pelo menos um gatilho e uma ação. Siga as etapas na próxima seção para adicionar uma ação.



## Usar uma ação de FTP

Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Agora que você adicionou um gatilho, siga estas etapas para adicionar uma ação que obterá o conteúdo do arquivo novo ou modificado encontrado pelo gatilho.

1. Escolha **+ Nova etapa** a fim de adicionar a ação para obter o conteúdo do arquivo no servidor FTP
- Escolha o link **Adicionar uma ação**. ![Imagem 1 da ação de FTP](./media/connectors-create-api-ftp/ftp-action-1.png)
- Insira *FTP* para pesquisar todas as ações relacionadas ao FTP.
- Escolha **FTP – Obter conteúdo do arquivo** como a ação a ser tomada quando um arquivo novo ou modificado é encontrado na pasta FTP. ![Imagem 2 da ação de FTP](./media/connectors-create-api-ftp/ftp-action-2.png) O controle **Obter conteúdo do arquivo** é aberto. **Observação**: será solicitado que você autorize o aplicativo lógico a acessar sua conta no servidor FTP, caso não tenha feito isso anteriormente. ![Imagem 3 da ação de FTP](./media/connectors-create-api-ftp/ftp-action-3.png)
- Escolha o controle **Arquivo** (o espaço em branco localizado abaixo de **ARQUIVO***). Aqui, você pode usar qualquer uma das várias propriedades do arquivo novo ou modificado encontrado no servidor FTP.
- Escolha a opção **Conteúdo do arquivo**. ![Imagem 4 da ação de FTP](./media/connectors-create-api-ftp/ftp-action-4.png)
-  O controle é atualizado, indicando que a ação **FTP – Obter conteúdo do arquivo** obterá o *conteúdo do arquivo* do arquivo novo ou modificado no servidor FTP. ![Imagem 5 da ação de FTP](./media/connectors-create-api-ftp/ftp-action-5.png)
- Salve seu trabalho e adicione um arquivo à pasta FTP para testar o fluxo de trabalho.

Neste ponto, o aplicativo lógico foi configurado com um gatilho para monitorar uma pasta em um servidor FTP e iniciar o fluxo de trabalho quando encontrar um arquivo novo ou modificado no servidor FTP.

O aplicativo lógico também foi configurado com uma ação para obter o conteúdo do arquivo novo ou modificado.

Agora você pode adicionar outra ação, como a ação [SQL Server – inserir linha](./connectors-create-api-sqlazure.md#insert-row), para inserir o conteúdo do arquivo novo ou modificado em uma tabela de banco de dados SQL.

## Detalhes técnicos

Veja abaixo os detalhes sobre os gatilhos, as ações e as respostas que essa conexão permite:

## Gatilhos de FTP

O FTP tem os seguintes gatilhos:

|Gatilho | Descrição|
|--- | ---|
|[Quando um arquivo é adicionado ou modificado](connectors-create-api-ftp.md#when-a-file-is-added-or-modified)|Esta operação dispara um fluxo quando um arquivo é adicionado ou modificado em uma pasta.|


## Ações de FTP

O FTP tem as seguintes ações:


|Ação|Descrição|
|--- | ---|
|[Obter metadados do arquivo](connectors-create-api-ftp.md#get-file-metadata)|Esta operação obtém os metadados de um arquivo.|
|[Atualizar arquivo](connectors-create-api-ftp.md#update-file)|Esta operação atualiza um arquivo.|
|[Excluir arquivo](connectors-create-api-ftp.md#delete-file)|Esta operação exclui um arquivo.|
|[Obter metadados do arquivo usando o caminho](connectors-create-api-ftp.md#get-file-metadata-using-path)|Esta operação obtém os metadados de um arquivo usando o caminho.|
|[Obter o conteúdo do arquivo usando o caminho](connectors-create-api-ftp.md#get-file-content-using-path)|Esta operação obtém o conteúdo de um arquivo usando o caminho.|
|[Obter conteúdo do arquivo](connectors-create-api-ftp.md#get-file-content)|Esta operação obtém o conteúdo de um arquivo.|
|[Criar arquivo](connectors-create-api-ftp.md#create-file)|Esta operação cria um arquivo.|
|[Copiar arquivo](connectors-create-api-ftp.md#copy-file)|Esta operação copia um arquivo para um servidor FTP.|
|[Lista de arquivos na pasta](connectors-create-api-ftp.md#list-files-in-folder)|Esta operação obtém a lista de arquivos e subpastas em uma pasta.|
|[Lista de arquivos na pasta-raiz](connectors-create-api-ftp.md#list-files-in-root-folder)|Esta operação obtém a lista de arquivos e subpastas na pasta-raiz.|
|[Extrair pasta](connectors-create-api-ftp.md#extract-folder)|Essa operação extrai um arquivo para uma pasta (exemplo: .zip).|
### Detalhes da ação

Veja abaixo os detalhes das ações e dos gatilhos para esse conector, com suas respostas:



### Obter metadados do arquivo
Esta operação obtém os metadados de um arquivo.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|id*|Arquivo|Selecionar um arquivo|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

BlobMetadata


| Nome da Propriedade | Tipo de Dados |
|---|---|---|
|ID|string|
|Nome|string|
|DisplayName|string|
|Caminho|string|
|LastModified|string|
|Tamanho|inteiro|
|MediaType|string|
|IsFolder|booleano|
|ETag|string|
|FileLocator|string|




### Atualizar arquivo
Esta operação atualiza um arquivo.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|id*|Arquivo|Selecionar um arquivo|
|body*|Conteúdo do arquivo|O conteúdo do arquivo|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

BlobMetadata


| Nome da Propriedade | Tipo de Dados |
|---|---|---|
|ID|string|
|Nome|string|
|DisplayName|string|
|Caminho|string|
|LastModified|string|
|Tamanho|inteiro|
|MediaType|string|
|IsFolder|booleano|
|ETag|string|
|FileLocator|string|




### Excluir arquivo
Esta operação exclui um arquivo.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|id*|Arquivo|Selecionar um arquivo|

Um * indica que uma propriedade é obrigatória




### Obter metadados do arquivo usando o caminho
Esta operação obtém os metadados de um arquivo usando o caminho.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|path*|Caminho do arquivo|Selecionar um arquivo|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

BlobMetadata


| Nome da Propriedade | Tipo de Dados |
|---|---|---|
|ID|string|
|Nome|string|
|DisplayName|string|
|Caminho|string|
|LastModified|string|
|Tamanho|inteiro|
|MediaType|string|
|IsFolder|booleano|
|ETag|string|
|FileLocator|string|




### Obter o conteúdo do arquivo usando o caminho
Esta operação obtém o conteúdo de um arquivo usando o caminho.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|path*|Caminho do arquivo|Selecionar um arquivo|

Um * indica que uma propriedade é obrigatória




### Obter conteúdo do arquivo
Esta operação obtém o conteúdo de um arquivo.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|id*|Arquivo|Selecionar um arquivo|

Um * indica que uma propriedade é obrigatória




### Criar arquivo
Esta operação cria um arquivo.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|folderPath*|Caminho da pasta|Selecionar uma pasta|
|name*|Nome do arquivo|Nome do arquivo|
|body*|Conteúdo do arquivo|O conteúdo do arquivo|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

BlobMetadata


| Nome da Propriedade | Tipo de Dados |
|---|---|---|
|ID|string|
|Nome|string|
|DisplayName|string|
|Caminho|string|
|LastModified|string|
|Tamanho|inteiro|
|MediaType|string|
|IsFolder|booleano|
|ETag|string|
|FileLocator|string|




### Copiar arquivo
Esta operação copia um arquivo para um servidor FTP.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|source*|Url da origem|URL para o arquivo de origem|
|destination*|Caminho do arquivo de destino|Caminho do arquivo de destino, incluindo o nome do arquivo de destino|
|substituir|Substituir?|Substitui o arquivo de destino se estiver definido como "true"|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

BlobMetadata


| Nome da Propriedade | Tipo de Dados |
|---|---|---|
|ID|string|
|Nome|string|
|DisplayName|string|
|Caminho|string|
|LastModified|string|
|Tamanho|inteiro|
|MediaType|string|
|IsFolder|booleano|
|ETag|string|
|FileLocator|string|




### Quando um arquivo é adicionado ou modificado
Esta operação dispara um fluxo quando um arquivo é adicionado ou modificado em uma pasta.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|folderId*|Pasta|Selecionar uma pasta|

Um * indica que uma propriedade é obrigatória




### Lista de arquivos na pasta
Esta operação obtém a lista de arquivos e subpastas em uma pasta.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|id*|Pasta|Selecionar uma pasta|

Um * indica que uma propriedade é obrigatória



#### Detalhes da Saída

BlobMetadata


| Nome da Propriedade | Tipo de Dados |
|---|---|---|
|ID|string|
|Nome|string|
|DisplayName|string|
|Caminho|string|
|LastModified|string|
|Tamanho|inteiro|
|MediaType|string|
|IsFolder|booleano|
|ETag|string|
|FileLocator|cadeia de caracteres|




### Lista de arquivos na pasta-raiz
Esta operação obtém a lista de arquivos e subpastas na pasta-raiz.


Não há parâmetros para essa chamada

#### Detalhes da Saída

BlobMetadata


| Nome da Propriedade | Tipo de Dados |
|---|---|---|
|ID|string|
|Nome|string|
|DisplayName|string|
|Caminho|string|
|LastModified|string|
|Tamanho|inteiro|
|MediaType|string|
|IsFolder|booleano|
|ETag|string|
|FileLocator|string|




### Extrair pasta
Essa operação extrai um arquivo para uma pasta (exemplo: .zip).


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|source*|Caminho do arquivo de origem|Caminho para o arquivo morto|
|destination*|Caminho da pasta de destino|Caminho para a pasta de destino|
|substituir|Substituir?|Substitui os arquivos de destino se estiver definido como "true"|

Um * indica que uma propriedade é obrigatória



#### Detalhes da Saída

BlobMetadata


| Nome da Propriedade | Tipo de Dados |
|---|---|---|
|ID|string|
|Nome|string|
|DisplayName|string|
|Caminho|string|
|LastModified|string|
|Tamanho|inteiro|
|MediaType|string|
|IsFolder|booleano|
|ETag|string|
|FileLocator|cadeia de caracteres|



## Respostas HTTP

As ações e os gatilhos acima podem retornar um ou mais dos seguintes códigos de status HTTP:

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceita|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido.|
|padrão|Falha na Operação.|







## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->