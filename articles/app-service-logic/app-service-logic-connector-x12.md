<properties 
   pageTitle="Conector do BizTalk X12" 
   description="Conector do BizTalk X12" 
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
   ms.date="06/14/2015"
   ms.author="rajram"/>

#Conector do BizTalk X12
O Serviço Microsoft Azure X12 permite receber e enviar mensagens conforme o protocolo X12 nas comunicações de empresas. X12 também é comumente conhecido como ASC X12 ou X12 do Accredited Standards Committee. Ele é amplamente usado em vários setores.

##Pré-requisitos
- Aplicativo de API do TPM: antes de criar um conector X12, você precisa criar um [Conector de Gerenciamento de Parceiros Comerciais do BizTalk][1]
- Banco de dados SQL do Azure: cada um dos aplicativos de API B2B requer seu próprio Banco de Dados SQL do Azure.
- Barramento de Serviço do Azure: é opcional e usado somente no caso de envio em lote.

##Usando o Conector do BizTalk X12
Para usar o Conector do BizTalk X12, você precisa primeiro criar uma instância do Aplicativo de API do Conector BizTalk X12. Isso pode ser feito de forma embutida durante a criação de um aplicativo lógico ou com a seleção do aplicativo de API do Conector do BizTalk X12 do Azure Marketplace.

##Configurando o Conector do BizTalk X12
Parceiros comerciais são as entidades envolvidas em comunicações B2B (business-to-business). Quando dois parceiros estabelecem uma relação, isso é chamado de um Contrato. O acordo definido se baseia na comunicação que os dois parceiros desejam atingir e é específica com relação a transporte ou protocolo.

As etapas envolvidas na criação de um contrato de parceiro comercial são documentadas [aqui][2]

##Usando o Conector X12 na superfície do designer de aplicativos lógicos
O Conector X12 pode ser usado como um gatilho ou uma ação.

###Gatilho
- Iniciar o designer de fluxo de Aplicativos Lógicos do Azure
- Clique no Conector do X12 no painel à direita

	![Configurações do gatilho][3]
- Clique em ->

	![Opções do gatilho][4]
- O Conector do BizTalk X12 expõe um único gatilho. Selecione *Lote da liberação*

	![Entrada do lote de liberação][5]
- Esse gatilho não tem entradas. Clique em ->

	![Lote de liberação configurado][6]
- Como parte da saída, o conector retorna a carga útil do X12, a id do contrato, bem como informações sobre se a mensagem é feita em lotes ou não.

###Ação
- Clique no Conector do X12 no painel à direita

	![Configurações de ação][7]
- Clique em ->

	![Lista de ações][8]
- O conector X12 dá suporte a várias ações. Selecione *Codificar*

	![Codificar entrada][9]
- Fornecer as entradas para a ação e configurá-la

	![Codificação configurada][10]

<table>
	<tr>
		<th>Parâmetro</th>
		<th>Tipo</th>
		<th>Descrição do parâmetro</th>
	</tr>
	<tr>
		<td>Conteúdo</td>
		<td>string</td>
		<td>Mensagem XML</td>
	</tr>
	<tr>
		<td>ID do contrato</td>
		<td>string</td>
		<td>ID do contrato</td>
	</tr>
	<tr>
		<td>É uma mensagem em lotes</td>
		<td>string</td>
		<td>É uma mensagem em lotes</td>
	</tr>
</table>

A ação retorna um objeto que contém a carga útil do X12.

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e Monitorar aplicativos de API e conector](../app-service-api/app-service-api-manage-in-portal.md).


<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-x12/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-x12/ListOfTriggers.PNG
[5]: ./media/app-service-logic-connector-x12/ReleaseBatchTriggerInput.PNG
[6]: ./media/app-service-logic-connector-x12/ReleaseBatchTriggerConfigured.PNG
[7]: ./media/app-service-logic-connector-x12/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-x12/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-x12/EncodeInput.PNG
[10]: ./media/app-service-logic-connector-x12/EncodeConfigured.PNG
[11]: ./media/app-service-logic-connector-x12/TriggerSettings.PNG

<!---HONumber=July15_HO3-->