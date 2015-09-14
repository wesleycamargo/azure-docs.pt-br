<properties
   pageTitle="Usando o Conector do SharePoint em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure"
	description="Como criar e configurar o Conector do SharePoint ou o aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure"
	services="app-service\logic"
	documentationCenter=".net,nodejs,java"
	authors="anuragdalmia"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="app-service-logic"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="integration"
	ms.date="08/23/2015"
	ms.author="sameerch"/>

# Introdução ao Conector do SharePoint e à adição dele ao seu Aplicativo Lógico
Conecte-se ao SharePoint Server ou ao SharePoint Online para gerenciar documentos e itens de lista. Você pode executar várias ações, como criar, atualizar, obter e excluir documentos e itens de lista. Ao usar o servidor SharePoint local, é possível inserir a cadeia de conexão do Barramento de Serviço como parte da configuração do conector e instalar o agente de ouvinte local para se conectar ao servidor.

O aplicativo de Conector do SharePoint Online e do SharePoint Server na galeria oferece gatilho e ações como mecanismos de interação com o SharePoint.

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo. Você pode adicionar o Conector do SharePoint a seu fluxo de trabalho de negócios e processar dados como parte desse fluxo de trabalho dentro de um Aplicativo Lógico.

## Criar um Conector do SharePoint Online

Um conector pode ser criado em um aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Selecione **Aplicativos de API** e pesquise “Conector do SharePoint Online”.
3. Digite o Nome, o Plano do Serviço de Aplicativo e outras propriedades.
4. Insira as seguintes configurações de pacote:

	Nome | Obrigatório | Descrição
--- | --- | ---
URL do site | Sim | Insira a URL completa do site do SharePoint. Por exemplo, insira: **https://microsoft.sharepoint.com/teams/wabstest*.
Biblioteca de Documentos / URLs Relativas de Lista | Sim | Insira as bibliotecas de documentos/URLs de listas, com relação à URL do site do SharePoint, que podem ser modificadas pelo conector. Por exemplo, insira: *Listas/Tarefa, Documentos Compartilhados*.

5. Após a conclusão, as Configurações de Pacote serão semelhantes às seguintes: 
![][1]

Depois disso, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o Conector do SharePoint Online.

## Criando um Conector do SharePoint Server

Um conector pode ser criado em um aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Selecione **Aplicativos de API** e pesquise “Conector do SharePoint Server”.
3. Digite o Nome, o Plano do Serviço de Aplicativo e outras propriedades.
4. Insira as seguintes configurações de pacote:

	Nome | Obrigatório | Descrição
--- | --- | ---
URL do site | Sim | Insira a URL completa do site do SharePoint. Por exemplo, insira: *https://microsoft.sharepoint.com/teams/wabstest*. Modo de Autenticação | Sim | Especifique o modo de autenticação para se conectar ao site do SharePoint. As opções incluem:<ul><li>Padrão</li><li>WindowsAuthentication</li><li>FormBasedAuthentication</li></ul><br/><br/>se você optar por Padrão, as credenciais que executam o Conector do SharePoint são usadas; Nome de Usuário/Senha não são necessários. Nome de Usuário e Senha são necessários para outros tipos de autenticação.<br/><br/>**Observação** Não há suporte para a autenticação anônima.
Nome de usuário | Não | Especifique um nome de usuário válido para se conectar ao site do SharePoint, se o modo de autenticação não for Padrão.
Senha | Não | Especifique uma senha válida para se conectar ao site do SharePoint, se o modo de Autenticação não for Padrão.
Biblioteca de Documentos / URLs Relativas da Lista | Sim | Especifique as bibliotecas de documentos/URLs de listas, com relação à URL do site do SharePoint, que podem ser modificadas pelo conector. Por exemplo, insira: *Listas/Tarefa, Documentos Compartilhados*.
Cadeia de conexão do Barramento de Serviço | Não | Se você estiver estabelecendo conexão local, insira a cadeia de conexão de retransmissão do Barramento de Serviço.<br/><br/>[Usando o Gerenciador de Conexão Híbrida](app-service-logic-hybrid-connection-manager.md)<br/>[Preços do Barramento de Serviço](http://azure.microsoft.com/pricing/details/service-bus/)

5. Após a conclusão, as Configurações de Pacote são semelhantes às seguintes: 
![][2]

Depois disso, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o Conector do SharePoint Server.


## Usando o Conector do SharePoint em seu aplicativo lógico

Depois de criar seu aplicativo de API, agora você pode usar o conector do SharePoint como um gatilho ou uma ação para o seu aplicativo lógico. Para fazer isso, você precisa:

1. Criar um novo Aplicativo Lógico e escolher o mesmo grupo de recursos que contém o Conector do SharePoint.

2. Abra **Gatilhos e Ações** para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo de trabalho. O Conector do SharePoint aparece na seção “Usados Recentemente" na galeria à direita. Selecione-o.

3. Se o conector do SharePoint é selecionado no início do aplicativo lógico, ele atua como gatilho. Caso contrário, as ações poderiam ser realizadas na conta do SharePoint usando o conector.

4. Ao usar o conector do SharePoint Online, você precisa autenticar e autorizar os aplicativos lógicos a executar operações em seu nome. Para iniciar a autorização, clique em **Autorizar** no Conector do SharePoint: 
![][3]

5. Clique em Autorizar abre a caixa de diálogo de autenticação do SharePoint. Forneça os detalhes de logon da conta do SharePoint na qual você deseja executar as operações: 
![][4]

6. Conceda aos aplicativos lógicos acesso à sua conta para executar operações em seu nome: 
![][5]

7. Se o Conector do SharePoint estiver configurado como Gatilho, os gatilhos são mostrados. Caso contrário, uma lista de ações é exibida e você pode escolher a operação apropriada que deseja executar: 
![][6]
  
**URL relativa configurada para a biblioteca de documentos** 
![][7]

**URL relativa configurada para a lista de documentos**

> [AZURE.NOTE]Para os gatilhos a seguir, supõe-se que você especificou “Documentos compartilhados, Listas/Tarefa” na configurações de Pacote do Conector, em que “Documentos Compartilhados” é uma biblioteca de documentos e “Listas/Tarefa” é uma Lista.

##  Gatilhos
Use gatilhos se quiser iniciar um aplicativo lógico.

> [AZURE.NOTE]Os gatilhos excluem os arquivos depois de lê-los. Para preservar esses arquivos, forneça um valor para o local de arquivamento.

### 1\. Novo documento nos documentos compartilhados (JSON)
Este gatilho é acionado quando um novo documento está disponível em “Documentos compartilhados”.

#### Entrada

Nome | Obrigatório | Descrição
--- | --- | ---
Nome de Exibição | Não | Especifique um modo de exibição válido usado para filtrar os documentos a serem separados. Por exemplo, digite: “Ordens Aprovadas”. Para processar todos os documentos existentes, deixe esse campo vazio.
Local do Arquivo Morto | Não | Especifique uma URL de pasta válida, relativa ao site do SharePoint, na qual os documentos processados são arquivados.
Substituir no Arquivo Morto | Não | Marque esta opção para substituir um arquivo no caminho do arquivo morto se ele já existir.
Consulta Caml | Não, Avançado | Especifique uma consulta CAML válida para filtrar documentos. Por exemplo, insira: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Saída

Nome | Descrição
--- | ---
Nome | Nome do documento.
Conteúdo | Conteúdo do documento.
ContentTransferEncoding | Codificação de Transferência de Conteúdo da Mensagem. (“nenhuma” ou ”base64”)

**Observação**: todas as colunas do item de documento são mostradas nas propriedades de saída em “Avançado”.


### 2\. Novo Item de Tarefas (JSON)
O gatilho é acionado quando um novo item é adicionado à lista “Tarefas”.

#### Entrada

Nome | Obrigatório | Descrição
--- | --- | ---
Nome de Exibição | Não | Especifique um modo de exibição válido usado para filtrar itens na lista. Por exemplo, digite: “Ordens Aprovadas”. Para processar todos os novos itens, deixe esse campo vazio.
Local do Arquivo Morto | Não | Especifique uma URL de pasta válido, relativo ao site do SharePoint, na qual os itens de lista processados são arquivados.
Consulta CAML | Não, Avançado | Especifique uma consulta CAML válida para filtrar documentos. Por exemplo, insira: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Saída

Nome | Descrição
--- | ---
As colunas da lista são preenchidas dinamicamente e mostradas nos parâmetros de saída. | &nbsp;


### 3\. Novo documento em documentos compartilhados (XML)

Este gatilho é acionado quando um novo documento está disponível em “Documentos compartilhados”. O novo documento é retornado como uma mensagem XML.

#### Entrada

Nome | Obrigatório | Descrição
--- | --- | ---
Nome de Exibição | Não | Especifique um modo de exibição válido usado para filtrar os documentos a serem separados. Por exemplo, digite: “Ordens Aprovadas”. Para processar todos os documentos existentes, deixe esse campo vazio.
Local do Arquivo Morto | Não | Especifique uma URL de pasta válida, relativa ao site do SharePoint, na qual os documentos de lista processados são arquivados.
Substituir no Arquivo Morto | Não | Marque esta opção para substituir um arquivo no caminho do arquivo morto se ele já existir.
Consulta Caml | Não, Avançado | Especifique uma consulta CAML válida para filtrar documentos. Por exemplo, insira: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Saída

Nome | Descrição
--- | ---
Conteúdo | Conteúdo do documento.
ContentTransferEncoding | Codificação de Transferência de Conteúdo da Mensagem. (“nenhuma” ou ”base64”)


### 4\. Novo item em Tarefas (XML)

O gatilho é acionado quando um novo item é adicionado à lista “Tarefas”. O novo item de lista é retornado como uma mensagem XML.

#### Entrada

Nome | Obrigatório | Descrição
--- | --- | ---
Nome de Exibição | Não | Especifique um modo de exibição válido usado para filtrar itens na lista. Exemplo: “Pedidos aprovados”. Para processar todos os novos itens, deixe esse campo vazio.
Local do Arquivo Morto | Não | Especifique uma URL de pasta válido, relativo ao site do SharePoint, na qual os itens de lista processados são arquivados.
Consulta CAML | Não, Avançado | Especifique uma consulta Caml válida para filtrar itens de lista. Por exemplo, insira: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Saída

Nome | Descrição
--- | ---
Conteúdo | Conteúdo do documento.
ContentTransferEncoding | Codificação de Transferência de Conteúdo da Mensagem. (“nenhuma” ou ”base64”)


##  Ações
Para as ações a seguir, supõe-se que você especificou “Documentos compartilhados, Listas/Tarefa” nas configurações de Pacote do Conector, em que “Documentos Compartilhados” é uma biblioteca de documentos e “Listas/Tarefa” é uma Lista.

### 1\. Carregar em Documentos Compartilhados (JSON)

Esta ação carrega o novo documento em “Documentos Compartilhados”. A entrada é um objeto JSON fortemente tipado com todos os campos de coluna da biblioteca de documentos.

#### Entrada

Nome | Obrigatório | Descrição
--- | --- | ---
Nome | Sim | Nome do documento.
Conteúdo | Sim | Conteúdo do documento.
ContentTransferEncoding | Sim | Codificação de Transferência de Conteúdo da Mensagem. (“nenhuma” ou ”base64”)
Forçar Substituição | Sim | Se for definido como verdadeiro e houver um documento com o nome especificado, ele será substituído.
ReqParam1* | Sim | Este é um dos parâmetros necessários para adicionar um documento à biblioteca de documentos.
ReqParam2* | Sim | Este é um dos parâmetros necessários para adicionar um documento à biblioteca de documentos.
OptionalParam1* | Não. Avançado | Este é um dos parâmetros opcionais para adicionar um documento à biblioteca de documentos.
OptionalParam2* | Não. Avançado | Este é um dos parâmetros opcionais para adicionar um documento à biblioteca de documentos.

**Observação**: todos os parâmetros da biblioteca de documentos são preenchidos dinamicamente. Os parâmetros obrigatórios estão visíveis, onde os parâmetros opcionais estão na seção avançada.

#### Saída

Nome | Descrição
--- | ---
ItemId | ItemId do documento adicionado à biblioteca de documentos.
Status | O carregamento bem-sucedido do documento retornará o código de status 200 (OK).


 

### 2\. Obter dos Documentos Compartilhados (JSON)
Esta ação obtém o documento da biblioteca de documentos com base na URL relativa (estrutura de pastas) do documento.

#### Entrada

Nome | Obrigatório | Descrição
--- | --- | ---
URI Relativo do Documento | Não | Especifique a URL do documento, relativa a “Documentos compartilhados”. Por exemplo, insira: *myspec1,myfolder/orders*.

#### Saída

Nome | Descrição
--- | ---
Conteúdo | Conteúdo do documento
ContentTransferEncoding | Codificação de transferência de conteúdo da mensagem. (“nenhuma”|”base64”)
Status | Uma execução bem-sucedida da ação retorna o código de status 200 (OK).
Param1* | Este é um dos parâmetros de um documento na biblioteca de documentos.
Param2* | Este é um dos parâmetros de um documento na biblioteca de documentos.

**Observação**: todos os parâmetros da biblioteca de documentos são preenchidos dinamicamente. E Eles estão na seção Avançado.

 

### 3\. Excluir dos Documentos Compartilhados

Esta ação exclui o documento da biblioteca de documentos com base na URL relativa (estrutura de pastas) do documento.

#### Entrada

Nome | Obrigatório | Descrição
--- | --- | ---
URI Relativo do Documento | Não | Especifique a URL do documento, relativa a “Documentos compartilhados”. Por exemplo, insira: *myspec1,myfolder/orders*.

#### Saída

Nome | Descrição
--- | ---
Status | Uma execução bem-sucedida da ação retorna o código de status 200 (OK).


### 4\. Inserir em Tarefas (JSON)

Esta ação adiciona um item à lista de itens.

#### Entrada

Nome | Obrigatório | Descrição
--- | --- | ---
ReqParam1* | Sim | Este é um dos parâmetros necessários para adicionar um item à Lista.
ReqParam2* | Sim | Este é um dos parâmetros necessários para adicionar um item à Lista.
OptionalParam1* | Não. Avançado | Este é um dos parâmetros necessários para adicionar um item à Lista.
OptionalParam2* | Não. Avançado | Este é um dos parâmetros necessários para adicionar um item à Lista.

**Observação**: todos os parâmetros da “Lista” são preenchidos dinamicamente. Os parâmetros obrigatórios estão visíveis, enquanto os parâmetros opcionais estão na seção avançado.

#### Saída

Nome | Descrição
--- | ---
ItemId | O ItemId do item de lista adicionado.
Status | Uma inserção bem-sucedida de item de lista retorna o código de status 200 (OK).


### 5\. Atualizar Tarefas (JSON)

Esta ação atualiza um item na lista de itens.

#### Entrada

Nome | Obrigatório | Descrição
--- | --- | ---
ItemId | Sim | ItemId do item de lista.
ReqParam1* | Não | Este é um dos parâmetros necessários para adicionar um item à Lista.
ReqParam2* | Não | Este é um dos parâmetros necessários para adicionar um item à Lista.
OptionalParam1* | Não. Avançado | Este é um dos parâmetros necessários para adicionar um item à Lista.
OptionalParam2* | Não. Avançado | Este é um dos parâmetros necessários para adicionar um item à Lista.

**Observação**: todos os parâmetros da “Lista” são preenchidos dinamicamente. Os parâmetros obrigatórios estão visíveis, enquanto os parâmetros opcionais estão na seção avançado.

#### Saída

Nome | Descrição
--- | ---
Status | Uma atualização bem-sucedida do item de lista retornará o código de status 200 (OK).


### 6\. Obter Item das Tarefas (JSON)

Esta ação obtém um item da lista de itens.

#### Entrada

Nome | Obrigatório | Descrição
--- | --- | ---
ItemId | Sim | O ItemId do item da lista.

#### Saída

Nome | Descrição
--- | ---
Column1* | Este é um dos parâmetros na lista.
Column2* | Este é um dos parâmetros na lista.
Status | Uma execução bem-sucedida da ação retorna o código de status 200 (OK).

**Observação**: As colunas da lista são preenchidas dinamicamente e mostradas nos parâmetros de saída.


### 7\. Excluir Item das Tarefas

Esta ação exclui um item da lista de itens.

#### Entrada

Nome | Obrigatório | Descrição
--- | --- | ---
ItemId | Sim | O ItemId do item da lista.

#### Saída

Nome | Descrição
--- | ---
Status | Uma exclusão bem-sucedida do item de lista retornará o código de status 200 (OK).


### 8\. Listar Documentos Compartilhados (JSON)

Esta ação lista todos os documentos de uma biblioteca de documentos. Você pode usar uma Exibição ou uma consulta CAML para filtrar os documentos.

#### Entrada

Nome | Obrigatório | Descrição
--- | --- | ---
Nome de Exibição | Não | Especifique um modo de exibição válido usado para filtrar os documentos a serem separados. Por exemplo, digite: “Ordens Aprovadas”. Para processar todos os documentos existentes, deixe esse campo vazio.
Consulta Caml | Não | Especifique uma consulta CAML válida para filtrar documentos. Por exemplo, insira: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Saída

Nome | Descrição
--- | ---
Documentos | Matriz de todos os documentos. Cada documento tem os seguintes campos: <ul><li>Documentos </li><li>Nome</li><li>ID do Item</li><li>URL Completa do Item</li><li>Avançado</li><li>URL de Edição de Item</li><li>Nome da Lista</li><li>URL Completa da Lista</li></ul>
Status | Uma inserção bem-sucedida de item de lista retorna o código de status 200 (OK).


### 9\. Carregar em Documentos Compartilhados (XML)

Esta ação carrega o novo documento em “Documentos Compartilhados”. O documento de entrada deve ser uma carga XML. A resposta da ação será uma carga XML.
 
#### Entrada

Nome | Obrigatório | Descrição
--- | --- | ---
Nome | Sim | Nome do documento.
Conteúdo | Sim | Conteúdo do documento.
ContentTransferEncoding | Sim | Codificação de Transferência de Conteúdo da Mensagem. (“nenhuma”|”base64”)
Forçar Substituição | Sim | Se for definido como TRUE e houver um documento com o nome especificado, ele será substituído.
 
#### Saída

Nome | Descrição
--- | ---
XML de Saída | Resposta da ação de carregamento em formato XML.
Status | O carregamento bem-sucedido do documento retornará o código de status 200 (OK).

### 10\. Obter dos Documentos Compartilhados (XML)

Esta ação obtém o documento da biblioteca de documentos com base na URL relativa (estrutura de pastas) do documento.

#### Entrada

Nome | Obrigatório | Descrição
--- | --- | ---
URI Relativo do Documento | Não | Especifique a URL do documento, relativa a “Documentos compartilhados”. Por exemplo, insira: *myspec1,myfolder/orders*.
Tipo de arquivo | Sim | Especifique se o arquivo é um arquivo binário ou um arquivo de texto.

#### Saída

Nome | Descrição
--- | ---
XML de Saída | Conteúdo do documento
ContentTransferEncoding | Codificação de transferência de conteúdo da mensagem. (“nenhuma”|”base64”)
Status | Uma execução bem-sucedida da ação retorna o código de status 200 (OK).

### 11\. Inserir nas Tarefas (XML)

Esta ação adiciona um item à lista de itens. A entrada deve ser uma carga XML.

#### Entrada

Nome | Obrigatório | Descrição
--- | --- | ---
XML de entrada | Sim | A mensagem XML que contém os valores dos campos do item de lista a ser inserido. Você pode usar o Aplicativo de API de Transformação para gerar a mensagem XML.

**Observação**: todos os parâmetros da “Lista” são preenchidos dinamicamente. Os parâmetros obrigatórios estão visíveis, enquanto os parâmetros opcionais estão na seção avançado.

#### Saída

Nome | Descrição
--- | ---
ItemId | O ItemId do item de lista adicionado.
Status | Uma inserção bem-sucedida de item de lista retorna o código de status 200 (OK).


### 12\. Atualizar Tarefas (XML)

Esta ação atualiza um item na lista de itens. A entrada deve ser uma carga XML.

#### Entrada

Nome | Obrigatório | Descrição
--- | --- | ---
ItemID | Sim | ItemId do item de lista.
Inserir XML | Sim | A mensagem XML que contém os valores dos campos do item de lista a ser inserido. Você pode usar o Aplicativo de API de Transformação para gerar a mensagem XML.

**Observação**: todos os parâmetros da “Lista” são preenchidos dinamicamente. Os parâmetros obrigatórios estão visíveis, enquanto os parâmetros opcionais estão na seção avançado.

#### Saída

Nome | Descrição
--- | ---
Status | Uma atualização bem-sucedida do item de lista retornará o código de status 200 (OK).


### 13\. Obter Item das Tarefas (XML)

Esta ação obtém um item da lista de itens.

#### Entrada

Nome | Obrigatório | Descrição
--- | --- | ---
ItemID | Sim | O ItemId do item da lista.

#### Saída

Nome | Descrição
--- | ---
XML de Saída | A mensagem XML que contém os valores dos campos do item de lista selecionado.
Status | Uma execução bem-sucedida da ação retorna o código de status 200 (OK).


## Configuração Híbrida (opcional)

> [AZURE.NOTE]Essa etapa será necessária apenas se você estiver usando o SharePoint local por trás do firewall.

O Serviço de Aplicativo usa o Gerenciador de Configuração Híbrida para se conectar com segurança ao sistema local. Se seu conector usar um SharePoint Server local, o Gerenciador de Conexão Híbrida será necessário.

Consulte [Usando o Gerenciador de Conexão Híbrida](app-service-logic-hybrid-connection-manager.md).

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e Monitorar aplicativos de API e conector](app-service-api-manage-in-portal.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-sharepoint/image_0.png
[2]: ./media/app-service-logic-connector-sharepoint/image_1.png
[3]: ./media/app-service-logic-connector-sharepoint/image_2.png
[4]: ./media/app-service-logic-connector-sharepoint/image_3.png
[5]: ./media/app-service-logic-connector-sharepoint/image_4.jpg
[6]: ./media/app-service-logic-connector-sharepoint/image_5.png
[7]: ./media/app-service-logic-connector-sharepoint/image_6.png

<!----HONumber=August15_HO9-->