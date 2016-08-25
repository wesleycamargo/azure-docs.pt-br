<properties
pageTitle="SMTP | Microsoft Azure"
description="Crie aplicativos lógicos com o serviço de Aplicativo do Azure. Conecte-se ao SMTP para enviar email."
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/15/2016"
ms.author="deonhe"/>

# Introdução ao conector do SMTP

Conecte-se ao SMTP para enviar email.

Para usar [qualquer conector](./apis-list.md), primeiro é preciso criar um aplicativo lógico. Você pode começar [criando um aplicativo lógico agora mesmo](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conectar-se ao SMTP

Para que o aplicativo lógico possa acessar qualquer serviço, crie primeiro uma *conexão* com o serviço. Uma [conexão](./connectors-overview.md) fornece conectividade entre um aplicativo lógico e outro serviço. Por exemplo, para se conectar ao SMTP, é preciso ter uma *conexão* do SMTP. Para criar uma conexão, é preciso fornecer as credenciais normalmente usadas para acessar o serviço ao qual você deseja se conectar. Desse modo, no exemplo de SMTP, você precisa das credenciais do seu nome de conexão, do endereço do servidor SMTP e das informações de logon do usuário para criar a conexão com o SMTP. [Saiba mais sobre conexões]()

### Criar uma conexão com o SMTP

>[AZURE.INCLUDE [Etapas para criar uma conexão com o SMTP](../../includes/connectors-create-api-smtp.md)]

## Usar um gatilho de SMTP

Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Neste exemplo, como o SMTP não tem seu próprio gatilho, usaremos o gatilho **Salesforce – quando um objeto é criado**. Esse gatilho será ativado quando um novo objeto for criado no Salesforce. Em nosso exemplo, nós o configuraremos de modo que toda vez que um novo cliente potencial for criado no Salesforce, uma ação *enviar email* ocorrerá por meio do conector de SMTP com uma notificação do novo cliente potencial que está sendo criado.

1. Digite *salesforce* na caixa de pesquisa no designer de aplicativos lógicos e escolha o gatilho **Salesforce – quando um objeto é criado**.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  

2. O controle **Quando um objeto é criado** é exibido.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  

3. Escolha o **Tipo de Objeto** e escolha *Cliente Potencial* na lista de objetos. Nessa etapa, você está indicando que está criando um gatilho que notificará seu aplicativo lógico sempre que um novo cliente potencial for criado no Salesforce.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  

4. O gatilho foi criado.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## Usar uma ação de SMTP

Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Agora que o gatilho foi adicionado, siga estas etapas para adicionar uma ação de SMTP que ocorrerá quando um novo cliente potencial for criado no Salesforce.

1. Escolha **+ Nova etapa** para adicionar a ação que deseja executar quando um novo cliente potencial é criado.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  

2. Escolha **Adicionar uma ação**. Isso abre a caixa de pesquisa, onde é possível procurar qualquer ação que você deseja realizar.  
 ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  

3. Digite *smtp* para pesquisar todas as ações relacionadas ao SMTP.

4. Escolha **SMTP – enviar email** como a ação a ser tomada quando o novo cliente potencial é criado. O bloco de controle de ação é aberto. Você terá que estabelecer a conexão smtp no bloco de designer, caso não tenha feito isso anteriormente.  
 ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)  

5. Insira suas informações de email desejadas no bloco **SMTP – enviar email**.  
 ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  

6. Salve seu trabalho para ativar o fluxo de trabalho.

## Detalhes técnicos

Veja abaixo os detalhes sobre os gatilhos, as ações e as respostas que essa conexão permite:

## Gatilhos de SMTP

O SMTP não possui gatilhos.

## Ações de SMTP

O SMTP tem a seguinte ação:


|Ação|Descrição|
|--- | ---|
|[Enviar Email](connectors-create-api-smtp.md#send-email)|Esta operação envia um email para um ou mais destinatários.|

### Detalhes da ação

Veja abaixo os detalhes da ação para esse conector, com suas respostas:


### Enviar Email
Esta operação envia um email para um ou mais destinatários.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|Para|Para|Especifique endereços de email separados por ponto-e-vírgula, como recipient1@domain.com;recipient2@domain.com|
|CC|cc|Especifique endereços de email separados por ponto-e-vírgula, como recipient1@domain.com;recipient2@domain.com|
|Assunto|Assunto|Assunto do email|
|Corpo|Corpo|Corpo do email|
|Da|Da|Endereço de email do remetente, como sender@domain.com|
|IsHtml|É Html|Envie o email como HTML (verdadeiro/falso)|
|Cco|bcc|Especifique endereços de email separados por ponto-e-vírgula, como recipient1@domain.com;recipient2@domain.com|
|Importância|Importância|Importância do email (Alta, Normal ou Baixa)|
|ContentData|Dados do Conteúdo dos Anexos|Dados de conteúdo (codificados em base64 para transmissões e no estado em que se encontram para cadeia de caracteres)|
|ContentType|Tipo de Conteúdo dos Anexos|Tipo de conteúdo|
|ContentTransferEncoding|Codificação de Transferência de Conteúdo dos Anexos|Codificação de transferência de conteúdo (base64 ou nenhuma)|
|FileName|Nome do Arquivo dos Anexos|Nome do arquivo|
|ContentId|ID do Conteúdo dos Anexos|ID do conteúdo|

Um * indica que uma propriedade é obrigatória


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
