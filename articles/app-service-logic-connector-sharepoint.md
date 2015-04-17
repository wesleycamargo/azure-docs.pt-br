<properties 
   pageTitle="Usando o Conector do SharePoint em seu aplicativo lógico" 
   description="Usando o Conector do SharePoint em seu aplicativo lógico" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="vagarw"/>

# Usando o Conector do SharePoint em seu aplicativo lógico

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo.  O Conector do Microsoft SharePoint permite que você se conecte ao Microsoft SharePoint Server/SharePoint Online e gerencie documentos e itens de lista.  Você pode executar várias ações, como criar, atualizar, obter e excluir documentos e itens de lista.  No caso do SharePoint Server local, é possível fornecer a cadeia de conexão do Barramento de Serviço como parte da configuração do conector e instalar o agente de ouvinte local para se conectar ao servidor.

O aplicativo de Conector do SharePoint Online e do SharePoint Server na galeria oferece gatilho e ações como mecanismos de interação com o SharePoint.

## Criando um Conector do SharePoint Online para seu aplicativo lógico

Para usar o Conector do SharePoint Online, você precisa primeiro criar uma instância do aplicativo de API do Conector do SharePoint Online.  Isso pode ser feito da seguinte maneira:

1. Abra o Azure Marketplace usando a opção + NOVO na parte inferior direita do Portal do Azure.

2. Navegue até "Web e Dispositivos Móveis > Aplicativos de API" e pesquise "Conector do SharePoint Online".

3. Configure o Conector do SharePoint Online e clique em Criar.  Estes são os parâmetros que você precisa fornecer para criar o conector:

	<table>
	  <tr>
	    <td><b>Nome</b></td>
	    <td><b>Obrigatório</b></td>
	    <td><b>Descrição</b></td>
	  </tr>
	  <tr>
	    <td>URL do Site</td>
	    <td>Sim</td>
	    <td>Especifique a URL completa do site do SharePoint.  Ex: https://microsoft.sharepoint.com/teams/wabstest </td>
	  </tr>
	  <tr>
	    <td>Biblioteca de Documentos / URLs Relativas da Lista</td>
	    <td>Sim</td>
	    <td>Especifique as bibliotecas de documentos/URLs de listas, com relação à URL do site do SharePoint, que podem ser modificadas pelo conector.  Ex:  Listas/Tarefa, Documentos Compartilhados.</td>
	  </tr>
	</table>
	![][1]


4. Depois disso, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o Conector do SharePoint Online.

## Criando um Conector do SharePoint Server para seu aplicativo lógico

Para usar o Conector do SharePoint Server, você precisa primeiro criar uma instância do Aplicativo de API de Conector do SharePoint Server.  Isso pode ser feito da seguinte maneira:

1. Abra o Azure Marketplace usando a opção + NOVO na parte inferior direita do Portal do Azure.

2. Navegue até "Web e Dispositivos Móveis > Aplicativos de API" e pesquise "Conector do SharePoint Server".

3. Configure o Conector do SharePoint Server e clique em Criar.  Estes são os parâmetros que você precisa fornecer para criar o conector:

	<table>
	  <tr>
	    <td><b>Nome</b></td>
	    <td><b>Obrigatório</b></td>
	    <td><b>Descrição</b></td>
	  </tr>
	  <tr>
	    <td>URL do Site</td>
	    <td>Sim</td>
	    <td>Especifique a URL completa do site do SharePoint.  Ex: https://microsoft.sharepoint.com/teams/wabstest </td>
	  </tr>
	  <tr>
	    <td>Modo de Autenticação</td>
	    <td>Sim</td>
	    <td>Especifique o modo de autenticação para se conectar ao site do SharePoint.  Os valores permitidos são:<br><br>
			Padrão<br>
			OAuth2<br>
			WindowsAuthentication<br>
			FormBasedAuthentication.<br><br>
	
	Caso você escolha as credenciais Padrão, serão usadas as credenciais padrão segundo as quais o microsserviço do SharePoint está sendo executado e não será necessário fornecer nome de usuário e senha.  Os campos de nome de usuário e senha são obrigatórias para outros tipos de autenticação. <br><br>Observação: não há suporte para autenticação anônima.</td>
	  </tr>
	  <tr>
	    <td>Nome de usuário</td>
	    <td>Não</td>
	    <td>Especifique um nome de usuário válido para se conectar ao site do SharePoint se o modo de autenticação não for Padrão/OAuth2.</td>
	  </tr>
	  <tr>
	    <td>Senha</td>
	    <td>Não</td>
	    <td>Especifique uma senha válida para se conectar ao site do SharePoint se o modo de autenticação não for Padrão/OAuth2.</td>
	  </tr>
	  <tr>
	    <td>Biblioteca de Documentos / URLs Relativas da Lista</td>
	    <td>Sim</td>
	    <td>Especifique as bibliotecas de documentos/URLs de listas, com relação à URL do site do SharePoint, que podem ser modificadas pelo conector.  Ex:  Listas/Tarefa, Documentos Compartilhados.</td>
	  </tr>
	  <tr>
	    <td>Cadeia de conexão do Barramento de Serviço</td>
	    <td>Não</td>
	    <td>Deve ser uma cadeia de conexão válida do Namespace do Barramento de Serviço.<br><br>
	
	Você precisa instalar um agente de escuta em um servidor que possa acessar seu SharePoint Server. <br>Você pode ir para a página de resumo do aplicativo de API e clicar em 'Hybrid Connection' para instalar o agente.</td>
	  </tr>
	</table>


	![][2]

4. Depois disso, você pode criar um aplicativo lógico no mesmo grupo de recursos para usar o Conector do SharePoint Server.
5. Você precisa instalar um agente de escuta em um servidor que possa acessar seu SharePoint Server.  Você pode ir para a página de resumo do aplicativo de API e clicar em 'Hybrid Connection' para instalar o agente

## Usando o Conector do SharePoint em seu aplicativo lógico

Depois de criar seu aplicativo de API, você pode usar o Conector do SharePoint como um gatilho ou ação para seu aplicativo lógico.  Para fazer isso, você precisa:

1. Criar um novo aplicativo lógico e escolher o mesmo grupo de recursos que contém o Conector do SharePoint.

2. Abra "Gatilhos e Ações" para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo.  O Conector do SharePoint apareceria na seção "Usados Recentemente" na galeria à direita.  Selecione-o.

3. Se o Conector do SharePoint tiver sido selecionado no início do aplicativo lógico, ele age como gatilho. Caso contrário, ações poderiam ser executadas na conta do SharePoint usando o conector. 

4. Você precisa autenticar e autorizar aplicativos lógicos a executarem operações em seu nome se o Conector do SharePoint Online for usado ou se a autenticação for OAuth2 no Conector do SharePoint Server.  Para iniciar a autorização, clique em Autorizar no Conector do SharePoint. 

	![][3]

5. Clique em Autorizar abre a caixa de diálogo de autenticação do SharePoint.  Forneça os detalhes de logon da conta do SharePoint na qual você deseja executar as operações. 

	![][4]
6. Conceda aos aplicativos lógicos acesso à sua conta para executar operações em seu nome. 

	![][5]

7. Se o Conector do SharePoint estiver configurado como Gatilho, os gatilhos são mostrados. Caso contrário, uma lista de ações é exibida e você pode escolher a operação que deseja executar.  

	![][6]

	<b>URL relativa configurada para a biblioteca de documentos</b><br><br>

	![][7]

	<b>URL relativa configurada para a lista de documentos</b>

	<b>Observação:</b> para os gatilhos abaixo, supõe-se que o usuário especificou 'Shared Documents, Lists/Task' nas configurações do pacote do conector, em que 'Shared Documents' é uma biblioteca de documentos e 'Lists/Task' é uma lista. 

##  Gatilhos
Use gatilhos se quiser iniciar um aplicativo lógico 

### 1.		Novo documento nos documentos compartilhados (JSON)
Este gatilho é acionado quando um novo documento está disponível em 'Shared Documents'. 

**Entradas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obrigatório</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>Nome de Exibição</td>
    <td>Não</td>
    <td>Especifique um modo de exibição válido usado para filtrar os documentos a serem separados.  Exemplo: 'Approved Orders'. Para processar todos os documentos existentes, deixe esse campo vazio. </td>
  </tr>
  <tr>
    <td>Local do Arquivo Morto</td>
    <td>Não</td>
    <td>Especifique uma URL de pasta válido, relativo ao site do SharePoint, onde os documentos processados são arquivados. </td>
  </tr>
  <tr>
    <td>Substituir no Arquivo Morto</td>
    <td>Não</td>
    <td>Marque esta opção para substituir um arquivo no caminho do arquivo morto se ele já existir. </td>
  </tr>
  <tr>
    <td>Consulta CAML</td>
    <td>Não, Avançado</td>
    <td>Especifique uma consulta CAML válida para filtrar documentos.  Exemplo:  <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>

**Saídas:**
<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>Nome  </td>
    <td>Nome do documento.</td>
  </tr>
  <tr>
    <td>Conteúdo</td>
    <td>Conteúdo do documento.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Codificação de transferência de conteúdo da mensagem.  ("none"|"base64")</
  </tr>
</table>


Observação:  Todas as colunas do item de documento são exibidas nas propriedades de saída 'Advanced'.


###2.  Novo Item de Tarefas (JSON)
O gatilho é acionado quando um novo item é adicionado à lista 'Tasks'.

**Entradas:**
<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obrigatório</b></td>
    <td><b>Descrição</b></td>
 </tr>
  <tr>
    <td>Nome de Exibição</td>
    <td>Não</td>
    <td>Especifique um modo de exibição válido usado para filtrar itens na lista.  Exemplo: 'Approved Orders'. Para processar todos os novos itens, deixe esse campo vazio. </td>
  </tr>
  <tr>
    <td>Local do Arquivo Morto</td>
    <td>Não</td>
    <td>Especifique uma URL de pasta válido, relativo ao site do SharePoint, onde os itens de lista processados são arquivados. </td>
  </tr>
  <tr>
    <td>Consulta CAML</td>
    <td>Não, Avançado</td>
    <td>Especifique uma consulta CAML válida para filtrar itens de lista.  Exemplo:  <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**Saídas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>As colunas da lista são preenchidas dinamicamente e mostradas nos parâmetros de saída.</td>
    <td> </td>
  </tr>

</table>


###3.  Novo documento nos documentos compartilhados (XML)

Este gatilho é acionado quando um novo documento está disponível em 'Shared Documents'.  O novo documento é retornado como uma mensagem XML.

**Entradas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obrigatório</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>Nome de Exibição</td>
    <td>Não</td>
    <td>Especifique um modo de exibição válido usado para filtrar os documentos a serem separados.  Exemplo: 'Approved Orders'. Para processar todos os documentos existentes, deixe esse campo vazio. </td>
  </tr>
  <tr>
    <td>Local do Arquivo Morto</td>
    <td>Não</td>
    <td>Especifique uma URL de pasta válido, relativo ao site do SharePoint, onde os documentos processados são arquivados. </td>
  </tr>
  <tr>
    <td>Substituir no Arquivo Morto</td>
    <td>Não</td>
    <td>Marque esta opção para substituir um arquivo no caminho do arquivo morto se ele já existir. </td>
  </tr>
  <tr>
    <td>Consulta CAML</td>
    <td>Não, Avançado</td>
    <td>Especifique uma consulta CAML válida para filtrar documentos.  Exemplo:  <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>

**Saídas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>Conteúdo</td>
    <td>Conteúdo do documento.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Codificação de transferência de conteúdo da mensagem.  ("none"|"base64")</td>
  </tr>
</table>


###4.  Novo Item de Tarefas (XML)

O gatilho é acionado quando um novo item é adicionado à lista 'Tasks'.  O novo item de lista é retornado como uma mensagem XML.

**Entradas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obrigatório</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>Nome de Exibição</td>
    <td>Não</td>
    <td>Especifique um modo de exibição válido usado para filtrar itens na lista.  Exemplo: 'Approved Orders'. Para processar todos os novos itens, deixe esse campo vazio. </td>
  </tr>
  <tr>
    <td>Local do Arquivo Morto</td>
    <td>Não</td>
    <td>Especifique uma URL de pasta válido, relativo ao site do SharePoint, onde os itens de lista processados são arquivados. </td>
  </tr>
  <tr>
    <td>Consulta CAML</td>
    <td>Não, Avançado</td>
    <td>Especifique uma consulta CAML válida para filtrar itens de lista.  Exemplo:  <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**Saídas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>Conteúdo</td>
    <td>Conteúdo do documento.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Codificação de transferência de conteúdo da mensagem.  ("none"|"base64")</td>
  </tr>
</table>


##  Ações
Para as ações abaixo, supõe-se que o usuário especificou 'Shared Documents, Lists/Task' nas configurações do pacote do conector, em que 'Shared Documents' é uma biblioteca de documentos e 'Lists/Task' é uma lista. 

###1.  Carregar para Documentos Compartilhados (JSON)

Esta ação carrega o novo documento para 'Shared Documents'.  A entrada é um objeto JSON fortemente tipado com todos os campos de coluna da biblioteca de documentos.

**Entradas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obrigatório</b></td>
    <td><b>Descrição</b></td>
 </tr>
  <tr>
    <td>Nome</td>
    <td>Sim</td>
    <td>Nome do documento.</td>
  </tr>
  <tr>
    <td>Conteúdo</td>
    <td>Sim</td>
    <td>Conteúdo do documento.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Sim</td>
    <td>Codificação de transferência de conteúdo da mensagem.  ("none"|"base64")</td>
  </tr>
  <tr>
    <td>Forçar Substituição</td>
    <td>Sim</td>
    <td>Se for definido como verdadeiro e houver um documento com o nome especificado, ele será substituído.</td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>Sim</td>
    <td>Este é um dos parâmetros necessários para adicionar um documento à biblioteca de documentos.</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>Sim</td>
    <td>Este é um dos parâmetros necessários para adicionar um documento à biblioteca de documentos.</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>Não, Avançado</td>
    <td>Este é um dos parâmetros opcionais para adicionar um documento à biblioteca de documentos.</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>Não, Avançado</td>
    <td>Este é um dos parâmetros opcionais para adicionar um documento à biblioteca de documentos.</td>
  </tr>
</table>

<b>Observação:</b> todos os parâmetros da biblioteca de documentos são preenchidos dinamicamente. Os parâmetros obrigatórios ficam visíveis, enquanto os parâmetros opcionais ficam na seção avançada.


**Saídas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>ItemId do documento adicionado à biblioteca de documentos.</td>
  </tr>
  <tr>
    <td>Status</td>
    <td>O carregamento bem-sucedido do documento retornará o código de status 200 (OK).</td>
  </tr>
</table>


 

###2.  Obter dos Documentos Compartilhados (JSON)
Esta ação obtém o documento da biblioteca de documentos com base na URL relativa (estrutura de pastas) do documento.


**Entradas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obrigatório</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>URI Relativo do Documento</td>
    <td>Não</td>
    <td>Especifique a URL do documento, relativa a 'Shared Documents'.  Exemplo: myspec1, minha_pasta/pedidos</td>
  </tr>
</table>


**Saídas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>Conteúdo</td>
    <td>Conteúdo do documento</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Codificação de transferência de conteúdo da mensagem.  ("none"|"base64")</td>
  </tr>
  <tr>
    <td>Status</td>
    <td>Uma execução bem-sucedida da ação retorna o código de status 200 (OK).</td>
  </tr>
  <tr>
    <td>Param1*</td>
    <td>Este é um dos parâmetros de um documento na biblioteca de documentos.</td>
  </tr>
  <tr>
    <td>Param2*</td>
    <td>Este é um dos parâmetros de um documento na biblioteca de documentos.</td>
  </tr>
</table>

<b>Observação:</b> todos os parâmetros da biblioteca de documentos são preenchidos dinamicamente. Eles estão na seção avançada.

 

###3.  Excluir dos Documentos Compartilhados

Esta ação exclui o documento da biblioteca de documentos com base na URL relativa (estrutura de pastas) do documento.

**Entradas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obrigatório</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>URI Relativo do Documento</td>
    <td>Não</td>
    <td>Especifique a URL do documento, relativa a 'Shared Documents'.  Exemplo: myspec1, minha_pasta/pedidos</td>
  </tr>
</table>


**Saídas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>Status</td>
    <td>Uma execução bem-sucedida da ação retorna o código de status 200 (OK).</td>
  </tr>
</table>


###4.  Inserir em Tarefas (JSON)

Esta ação adiciona um item à Lista de itens.

**Entradas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obrigatório</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>Sim</td>
    <td>Este é um dos parâmetros necessários para adicionar um item à Lista.</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>Sim</td>
    <td>Este é um dos parâmetros necessários para adicionar um item à Lista.</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>Não, Avançado</td>
    <td>Este é um dos parâmetros necessários para adicionar um item à Lista.</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>Não, Avançado</td>
    <td>Este é um dos parâmetros necessários para adicionar um item à Lista.</td>
  </tr>
</table>


<b>Observação:</b> todos os parâmetros da 'List' são preenchidos dinamicamente. Os parâmetros obrigatórios ficam visíveis, enquanto os parâmetros opcionais ficam na seção avançada.

 
**Saídas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>O ItemId do item da lista é adicionado.</td>
  </tr>
  <tr>
    <td>Status</td>
    <td>Uma inserção bem-sucedida de item de lista retorna o código de status 200 (OK).</td>
  </tr>
</table>


###5.  Atualizar Tarefas (JSON)

Esta ação atualiza um item na lista de itens.

**Entradas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obrigatório</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Sim</td>
    <td>O ItemId do item da lista.</td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>Não</td>
    <td>Este é um dos parâmetros necessários para adicionar um item à Lista.</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>Não</td>
    <td>Este é um dos parâmetros necessários para adicionar um item à Lista.</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>Não, Avançado</td>
    <td>Este é um dos parâmetros necessários para adicionar um item à Lista.</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>Não, Avançado</td>
    <td>Este é um dos parâmetros necessários para adicionar um item à Lista.</td>
  </tr>
</table>

<b>Observação:</b> todos os parâmetros da 'List' são preenchidos dinamicamente. Os parâmetros obrigatórios ficam visíveis, enquanto os parâmetros opcionais ficam na seção avançada.


**Saídas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Uma atualização bem-sucedida do item de lista retornará o código de status 200 (OK).</td>
  </tr>
</table>


###6.  Obter Item das Tarefas (JSON)

Esta ação obtém um item da lista de itens.


**Entradas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obrigatório</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Sim</td>
    <td>O ItemId do item da lista.</td>
  </tr>
</table>


**Saídas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>Column1*</td>
    <td>Este é um dos parâmetros na lista.</td>
  </tr>
  <tr>
    <td>Column2*</td>
    <td>Este é um dos parâmetros na lista.</td>
  </tr>
  <tr>
    <td>Status</td>
    <td>Uma execução bem-sucedida da ação retorna o código de status 200 (OK).</td>
  </tr>
</table>

<b>Observação:</b> As colunas da lista são preenchidas dinamicamente e mostradas nos parâmetros de saída.


###7.  Excluir Item das Tarefas

Esta ação exclui um item da lista de itens.

 
**Entradas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obrigatório</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Sim</td>
    <td>O ItemId do item da lista.</td>
  </tr>
</table>


**Saídas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Uma exclusão bem-sucedida do item da lista retornará o código de status 200 (OK).</td>
  </tr>
</table>


###8.  Listar Documentos Compartilhados (JSON)

Esta ação lista todos os documentos de uma biblioteca de documentos.  Você pode usar uma Exibição ou uma consulta CAML para filtrar os documentos.  

 
**Entradas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obrigatório</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>Nome de Exibição</td>
    <td>Não</td>
    <td>Especifique um modo de exibição válido usado para filtrar os documentos a serem separados.  Exemplo: 'Approved Orders'. Para processar todos os documentos existentes, deixe esse campo vazio. </td>
  </tr>
  <tr>
    <td>Consulta CAML</td>
    <td>Não</td>
    <td>Especifique uma consulta CAML válida para filtrar documentos.  Exemplo:  <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**Saídas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>Documentos</td>
    <td>Matriz de todos os documentos.  Cada documento tem os campos abaixo. <br><br>
	Documentos[]<br>
	Nome<br>
	Id do Item<br>
	URL Completa do Item<br>
	Avançado<br>
	URL de Edição do Item<br>
	Nome da Lista<br>
	URL Completa da Lista<br>
	</td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Uma inserção bem-sucedida de item de lista retorna o código de status 200 (OK).</td>
  </tr>
</table>


###9.  Carregar para Documentos Compartilhados (XML)

Esta ação carrega o novo documento para 'Shared Documents'.  O documento de entrada deve ser uma carga XML.  A resposta da ação será uma carga XML.
 

**Entradas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obrigatório</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>Nome</td>
    <td>Sim</td>
    <td>Nome do documento.</td>
  </tr>
  <tr>
    <td>Conteúdo</td>
    <td>Sim</td>
    <td>Conteúdo do documento.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Sim</td>
    <td>Codificação de transferência de conteúdo da mensagem.  ("none"|"base64")</td>
  </tr>
  <tr>
    <td>Forçar Substituição</td>
    <td>Sim</td>
    <td>Se for definido como verdadeiro e houver um documento com o nome especificado, ele será substituído.</td>
  </tr>
</table>
 

**Saídas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>XML de Saída</td>
    <td>Resposta da ação de carregamento em formato XML.</td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>O carregamento bem-sucedido do documento retornará o código de status 200 (OK).</td>
  </tr>
</table>

###10.  Obter nos Documentos Compartilhados (XML)

Esta ação obtém o documento da biblioteca de documentos com base na URL relativa (estrutura de pastas) do documento.

 
**Entradas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obrigatório</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>URI Relativo do Documento</td>
    <td>Não</td>
    <td>Especifique a URL do documento, relativa a 'Shared Documents'.  Exemplo: myspec1, minha_pasta/pedidos</td>
  </tr>
  <tr>
    <td>Tipo de Arquivo</td>
    <td>Sim</td>
    <td>Especifique se o arquivo é um arquivo binário ou um arquivo de texto.</td>
  </tr>
</table>


**Saídas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>XML de Saída</td>
    <td>Conteúdo do documento</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Codificação de transferência de conteúdo da mensagem.  ("none"|"base64")</td>
  </tr>
  <tr>
    <td>Status</td>
    <td>Uma execução bem-sucedida da ação retorna o código de status 200 (OK).</td>
  </tr>
</table>

###11.  Inserir nas Tarefas (XML)

Esta ação adiciona um item à lista de itens.  A entrada deve ser uma carga XML.

** Entradas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obrigatório</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>Inserir XML</td>
    <td>Sim</td>
    <td>A mensagem XML que contém os valores dos campos do item de lista a ser inserido.  Você pode usar o Aplicativo de API de Transformação para gerar a mensagem XML.</td>
  </tr>
</table>
 
<b>Observação:</b> todos os parâmetros da 'List' são preenchidos dinamicamente. Os parâmetros obrigatórios ficam visíveis, enquanto os parâmetros opcionais ficam na seção avançada.

 
**Saídas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>O ItemId do item da lista é adicionado.</td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Uma inserção bem-sucedida de item de lista retorna o código de status 200 (OK).</td>
  </tr>
</table>


###12.  Atualizar Tarefas (XML)

Esta ação atualiza um item na lista de itens.  A entrada deve ser uma carga XML.


**Entradas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obrigatório</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Sim</td>
    <td>O ItemId do item da lista.</td>
  </tr>
  <tr>
    <td>Inserir XML</td>
    <td>Sim</td>
    <td>A mensagem XML que contém os valores dos campos do item de lista a ser inserido.  Você pode usar o Aplicativo de API de Transformação para gerar a mensagem XML.</td>
  </tr>
</table>

<b>Observação:</b> todos os parâmetros da 'List' são preenchidos dinamicamente. Os parâmetros obrigatórios ficam visíveis, enquanto os parâmetros opcionais ficam na seção avançada.

 
**Saídas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Uma atualização bem-sucedida do item de lista retornará o código de status 200 (OK).</td>
  </tr>
</table>


###13.  Obter Item das Tarefas (XML)

Esta ação obtém um item da lista de itens.


**Entradas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Obrigatório</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Sim</td>
    <td>O ItemId do item da lista.</td>
  </tr>
</table>

**Saídas:**

<table>
  <tr>
    <td><b>Nome</b></td>
    <td><b>Descrição</b></td>
  </tr>
  <tr>
    <td>XML de Saída</td>
    <td>A mensagem XML que contém os valores dos campos do item de lista selecionado. </td>
  </tr>
  <tr>
    <td>Status  </td>
    <td>Uma execução bem-sucedida da ação retorna o código de status 200 (OK).</td>
  </tr>
</table>


<!--Image references-->
[1]: ./media/app-service-logic-connector-sharepoint/image_0.png
[2]: ./media/app-service-logic-connector-sharepoint/image_1.png
[3]: ./media/app-service-logic-connector-sharepoint/image_2.png
[4]: ./media/app-service-logic-connector-sharepoint/image_3.png
[5]: ./media/app-service-logic-connector-sharepoint/image_4.jpg
[6]: ./media/app-service-logic-connector-sharepoint/image_5.png
[7]: ./media/app-service-logic-connector-sharepoint/image_6.png

<!--HONumber=49-->