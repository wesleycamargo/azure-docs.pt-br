<properties
pageTitle="Adicionar a API do Yammer aos seus Aplicativos Lógicos | Microsoft Azure"
description="Visão geral da API do Yammer com os parâmetros da API REST"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="02/23/2016"
ms.author="deonhe"/>

# Introdução à API do Yammer

Conecte-se ao Yammer para acessar conversas em sua rede corporativa.

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. Para a versão do esquema 2014-12-01-preview, clique em [Yammer](../app-service-logic/app-service-logic-connector-yammer.md).

Com o Yammer, você pode:

- Crie seu fluxo de negócios baseado nos dados obtidos do Yammer. 
- Use gatilhos para quando há uma nova mensagem em um grupo ou um feed a seguir.
- Use as ações para postar uma mensagem, obter todas as mensagens e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando uma nova mensagem for exibida, você poderá enviar um email usando o Office 365.

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações
O Yammer inclui os gatilhos e as ações a seguir.

Gatilho | Ações
--- | ---
<ul><li>Quando há uma nova mensagem em um grupo</li><li>Quando há uma nova mensagem no meu feed Seguindo</li></ul>| <ul><li>Obter todas as mensagens</li><li>Obtém mensagens em um grupo</li><li>Obtém as mensagens no meu feed Seguindo</li><li>Postar mensagem</li><li>Quando há uma nova mensagem em um grupo</li><li>Quando há uma nova mensagem no meu feed Seguindo</li></ul>

Todas as APIs dão suporte a dados nos formatos JSON e XML.

## Criar uma conexão com o Yammer
Para usar a API do Yammer, crie primeiro uma **conexão**, em seguida, forneça os detalhes dessas propriedades:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|A criptografia do token|Sim|Fornecer suas credenciais do Yammer|

Siga estas etapas para entrar no Yammer e concluir a configuração da **conexão** do Yammer em seu aplicativo lógico:

1. Selecione **Recorrência**
2. Selecione uma **Frequência** e insira um **Intervalo**
3. Selecione **Adicionar uma ação** ![Configurar o Yammer][1]
4. Insira Yammer na caixa de pesquisa e aguarde até que a pesquisa retorne todas as entradas com Yammer no nome
5. Selecione **Yammer – Obter todas as mensagens**
6. Selecione **Entrar no Yammer**: ![Configurar o Yammer][2]
7. Forneça suas credenciais do Yammer para entrar e autorizar o aplicativo ![Configurar o Yammer][3]  
8. Você será redirecionado à página de logon de sua organização. **Permita** que o Yammer interaja com seu aplicativo lógico: ![Configurar o Yammer][4] 
9. Depois de entrar, retorne ao aplicativo lógico para concluí-lo configurando a seção **Yammer – Obter todas as mensagens** e adicionando outros gatilhos e outras ações necessárias.![Configurar o Yammer][5]  
10. Salve seu trabalho selecionando **Salvar** na barra de menus acima.


>[AZURE.TIP] Você pode usar essa conexão em outros aplicativos lógicos.

## Referência da API REST do Yammer
Esta documentação destina-se à versão: 1.0


### Obter todas as mensagens públicas na rede do Yammer do usuário conectado
Corresponde a “Todas” as conversas na interface da Web do Yammer.```GET: /messages.json```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|older\_then|inteiro|não|query|nenhum|Retorna mensagens mais antigas que a ID de mensagem especificada como cadeia de caracteres numéricos. Isso é útil para paginação de mensagens. Por exemplo, se você está vendo 20 mensagens e a mais antiga tem o número 2912, você pode acrescentar "?older\_than=2912" à sua solicitação para obter as 20 mensagens anteriores às que você está vendo.|
|newer\_then|inteiro|não|query|nenhum|Retorna mensagens mais novas que a ID de mensagem especificada como cadeia de caracteres numéricos. Deve ser usado ao pesquisar novas mensagens. Se você estiver examinando mensagens e a última mensagem retornada for 3516, poderá fazer uma solicitação com o parâmetro "?newer\_than=3516" para garantir que não obtenha cópias duplicadas de mensagens já em sua página.|
|limite|inteiro|não|query|nenhum|Retorna somente o número especificado de mensagens.|
|página|inteiro|não|query|nenhum|Obtém a página especificada. Se os dados retornados forem maiores que o limite, você poderá usar esse campo para acessar as páginas subsequentes|


### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|408|Tempo Limite da Solicitação|
|429|Número Excessivo de Solicitações|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|503|Serviço do Yammer Indisponível|
|504|Tempo Limite do Gateway|
|padrão|Falha na Operação.|


### Postar uma mensagem em um grupo ou no Feed de Toda a Empresa
Se a ID do grupo for fornecida, a mensagem será postada no grupo especificado; caso contrário, ela será postada no Feed de Toda a Empresa.```POST: /messages.json```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|input| |sim|corpo|nenhum|Postar solicitação de mensagem|


### Resposta

|Nome|Descrição|
|---|---|
|201|Criado|



## Definições de objeto

#### Mensagem: mensagem do Yammer

| Nome | Tipo de Dados | Obrigatório |
|---|---| --- | 
|ID|inteiro|não|
|content\_excerpt|string|não|
|sender\_id|inteiro|não|
|replied\_to\_id|inteiro|não|
|created\_at|string|não|
|network\_id|inteiro|não|
|message\_type|string|não|
|sender\_type|string|não|
|url|string|não|
|web\_url|string|não|
|group\_id|inteiro|não|
|corpo|não definido|não|
|thread\_id|inteiro|não|
|direct\_message|booleano|não|
|client\_type|string|não|
|client\_url|string|não|
|idioma|string|não|
|notified\_user\_ids|array|não|
|privacidade|string|não|
|liked\_by|não definido|não|
|system\_message|booleano|não|

#### PostOperationRequest: representa uma solicitação POST para o conector do Yammer postar no Yammer

| Nome | Tipo de Dados | Obrigatório |
|---|---| --- | 
|corpo|string|sim|
|group\_id|inteiro|não|
|replied\_to\_id|inteiro|não|
|direct\_to\_id|inteiro|não|
|difusão|booleano|não|
|topic1|string|não|
|topic2|string|não|
|topic3|string|não|
|topic4|string|não|
|topic5|string|não|
|topic6|string|não|
|topic7|string|não|
|topic8|string|não|
|topic9|string|não|
|topic10|string|não|
|topic11|string|não|
|topic12|string|não|
|topic13|string|não|
|topic14|string|não|
|topic15|string|não|
|topic16|string|não|
|topic17|string|não|
|topic18|string|não|
|topic19|string|não|
|topic20|string|não|

#### MessageList: lista de mensagens

| Nome | Tipo de Dados | Obrigatório |
|---|---| --- | 
|mensagens|array|não|


#### MessageBody: corpo da mensagem

| Nome | Tipo de Dados | Obrigatório |
|---|---| --- | 
|analisado|string|não|
|sem formatação|string|não|
|avançado|string|não|

#### LikedBy: curtido por

| Nome | Tipo de Dados | Obrigatório |
|---|---| --- | 
|count|inteiro|não|
|nomes|array|não|

#### YammmerEntity: curtido por

| Nome | Tipo de Dados | Obrigatório |
|---|---| --- | 
|type|string|não|
|ID|inteiro|não|
|full\_name|string|não|


## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

[1]: ./media/create-api-yammer/connectionconfig1.png
[2]: ./media/create-api-yammer/connectionconfig2.png
[3]: ./media/create-api-yammer/connectionconfig3.png
[4]: ./media/create-api-yammer/connectionconfig4.png
[5]: ./media/create-api-yammer/connectionconfig5.png

<!---HONumber=AcomDC_0302_2016-->