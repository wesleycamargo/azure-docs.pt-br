<properties
	pageTitle="Adicionar a API do SMTP aos seus Aplicativos Lógicos | Microsoft Azure"
	description="Visão geral da API do SMTP com os parâmetros da API REST"
	services=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="05/16/2016"
   ms.author="mandia"/>

# Introdução à API do SMTP
Conecte-se a um servidor SMTP para enviar email. A API do SMTP pode ser usada em:

- Aplicativos lógicos (discutidos neste tópico)
- PowerApps (consulte a [lista de conexões de PowerApps](https://powerapps.microsoft.com/tutorials/connections-list/) para obter uma lista completa)

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.

Com o SMTP, você pode:

- Compile seu fluxo de negócios incluindo o envio de emails usando SMTP. 
- Use uma ação para enviar email. Essa ação obtêm uma resposta e disponibiliza a saída para outras ações. Por exemplo, quando há um novo arquivo no servidor FTP, você pode obtê-lo e enviá-lo anexado em um email usando o SMTP. 

Para adicionar uma operação nos aplicativos lógicos, veja [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações
A API de SMTP tem a seguinte ação disponível. Não há nenhum gatilho.

|Gatilhos | Ações|
|--- | ---|
|Nenhum | Enviar email|

Todas as APIs dão suporte a dados nos formatos JSON e XML.

## Criar uma conexão com o SMTP
Ao adicionar essa API aos seus aplicativos lógicos, insira os seguintes valores:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
| Nome do Servidor SMTP | Sim | Insira o FQDN (domínio totalmente qualificado) ou o endereço IP do servidor SMTP.|
| Nome de usuário |Sim |Insira o nome de usuário para se conectar ao Servidor SMTP. |
| Senha | Sim|Insira a senha do nome de usuário. |

Depois de criar a conexão, insira as propriedades do SMTP, como os valores de Destinatário e Cópia Oculta. A **Referência da API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] Você pode usar essa mesma conexão do SMTP em outros aplicativos lógicos.

## Referência da API REST do Swagger
Aplica-se à versão: 1.0.

### Enviar Email
Envia um email para um ou mais destinatários.```POST: /SendEmail```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|emailMessage| muitos|sim|corpo|nenhum |Mensagem de email|

## Definições de objeto

#### Email: email SMTP

| Nome | Tipo de Dados | Obrigatório|
|---|---|---|
|Para|string|não|
|CC|string|não|
|Assunto|string|não|
|Corpo|string|não|
|Da|string|não|
|IsHtml|booleano|não|
|Cco|string|não|
|Importância|string|não|
|Anexos|array|não|


#### Anexo: anexo de email

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|FileName|string|não|
|ContentId|string|não|
|ContentData|string|sim|
|ContentType|string|sim|
|ContentTransferEncoding|string|sim|


## Próximas etapas
[Crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0518_2016-->