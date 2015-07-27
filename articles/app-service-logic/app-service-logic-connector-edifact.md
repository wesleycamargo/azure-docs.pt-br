<properties 
   pageTitle="Conector do BizTalk Edifact" 
   description="Conector do BizTalk Edifact" 
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

#Conector do BizTalk Edifact
O Serviço do Edifact do Microsoft Azure permite receber e enviar mensagens conforme o protocolo Edifact nas comunicações de empresas. Edifact é também conhecido como ASC Edifact ou Edifact do Accredited Standards Committee. Ele é amplamente usado em vários setores.

##Pré-requisitos
- Aplicativo de API do TPM: antes de criar um conector do Edifact, você precisa criar um [Conector de Gerenciamento de Parceiros Comerciais do BizTalk][1]
- Banco de dados SQL do Azure: cada um dos aplicativos de API B2B requer seu próprio Banco de Dados SQL do Azure.
- Barramento de Serviço do Azure: é opcional e usado somente no caso de envio em lote.

##Usando o conector do Edifact
Para usar o Conector do Edifact, você precisa primeiro criar uma instância do aplicativo de API para o Conector do AS2. Isso pode ser feito de forma embutida durante a criação de um aplicativo lógico ou com a seleção do aplicativo de API do Conector do AS2 do Azure Marketplace.

##Configurando o conector do Edifact
Parceiros comerciais são as entidades envolvidas em comunicações B2B (business-to-business). Quando dois parceiros estabelecem uma relação, isso é chamado de um Contrato. O acordo definido se baseia na comunicação que os dois parceiros desejam atingir e é específica com relação a transporte ou protocolo.

As etapas envolvidas na criação de um contrato de parceiro comercial são documentadas [aqui][2]

##Usando o Conector do Edifact na superfície do designer de aplicativos lógicos
O Conector do Edifact pode ser usado como um gatilho ou uma ação.

###Gatilho
- Iniciar o designer de fluxo de Aplicativos Lógicos do Azure
- Clique no conector do Edifact no painel à direita

	![Configurações do gatilho][3]
- Clique em ->

	![Opções do gatilho][4]
- O Conector do EDIFACT expõe um único gatilho. Selecione *Lote da liberação*

	![Entrada do lote de liberação][5]
- Esse gatilho não tem entradas. Clique em ->

	![Lote de liberação configurado][6]
- Como parte da saída, o conector retorna a carga útil do Edifact, o id do contrato, bem como informações sobre se a mensagem é feita em lotes ou não.

###Ação
- Clique no conector do Edifact no painel à direita

	![Configurações de ação][7]
- Clique em ->

	![Lista de ações][8]
- O conector do Edifact dá suporte a várias ações. Selecione *Codificar*

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
		<td>inteiro</td>
		<td>ID do contrato</td>
	</tr>
	<tr>
		<td>É uma mensagem em lotes</td>
		<td>boolean</td>
		<td>É uma mensagem em lotes</td>
	</tr>
	<tr>
		<td>Separador de elemento de dados</td>
		<td>string</td>
		<td>Separador de elemento de dados</td>
	</tr>
	<tr>
		<td>Separador de componente</td>
		<td>string</td>
		<td>Separador de componente</td>
	</tr>
	<tr>
		<td>Terminador de segmento</td>
		<td>string</td>
		<td>Terminador de segmento</td>
	</tr>
	<tr>
		<td>Indicador de ponto decimal</td>
		<td>string</td>
		<td>Indicador de ponto decimal</td>
	</tr>
	<tr>
		<td>Separador de repetição</td>
		<td>string</td>
		<td>Separador de repetição</td>
	</tr>
	<tr>
		<td>Caractere de escape</td>
		<td>string</td>
		<td>Caractere de escape</td>
	</tr>
	<tr>
		<td>Caractere de substituição</td>
		<td>string</td>
		<td>Caractere de substituição</td>
	</tr>
	<tr>
		<td>Sufixo do terminador de segmento</td>
		<td>string</td>
		<td>Sufixo do terminador de segmento</td>
	</tr>
</table>

A ação retorna um objeto que contém a carga útil EDIFACT mediante a conclusão bem-sucedida.

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e Monitorar aplicativos de API e conector](../app-service-api/app-service-api-manage-in-portal.md).


<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-edifact/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-edifact/ListOfTriggers.PNG
[5]: ./media/app-service-logic-connector-edifact/ReleaseBatchTriggerInput.PNG
[6]: ./media/app-service-logic-connector-edifact/ReleaseBatchTriggerConfigured.PNG
[7]: ./media/app-service-logic-connector-edifact/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-edifact/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-edifact/EncodeInput.PNG
[10]: ./media/app-service-logic-connector-edifact/EncodeConfigured.PNG

<!---HONumber=July15_HO3-->