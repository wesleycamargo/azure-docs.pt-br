<properties 
   pageTitle="Conector AS2" 
   description="Conector AS2" 
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

#Conector AS2
O Conector do AS2 do Microsoft Azure permite receber e enviar mensagens usando o protocolo de transporte AS2 em comunicações entre empresas. AS2 significa Applicability Statement 2. Os dados são transportados de maneira segura e confiável pela Internet. A segurança é obtida usando criptografia e certificados digitais.

##Pré-requisitos
- Aplicativo de API do TPM: antes de criar um conector do AS2, você precisa criar um [Conector de Gerenciamento de Parceiros Comerciais do BizTalk][1]
- Banco de dados SQL do Azure: cada um dos aplicativos de API B2B requer seu próprio Banco de Dados SQL do Azure.
- Contêiner de Armazenamento de Blob do Azure: armazena as propriedades da mensagem quando o arquivamento AS2 está habilitado. Se você não precisar do arquivamento de mensagens AS2, um contêiner de armazenamento não será necessário. 

##Usando o conector do AS2
Para usar o Conector do AS2, você precisa primeiro criar uma instância do aplicativo de API para o Conector do AS2. Isso pode ser feito de forma embutida durante a criação de um aplicativo lógico ou com a seleção do aplicativo de API do Conector do AS2 do Azure Marketplace.

##Configurando o Conector do AS2
Parceiros comerciais são as entidades envolvidas em comunicações B2B (business-to-business). Quando dois parceiros estabelecem uma relação, isso é chamado de um Contrato. O acordo definido se baseia na comunicação que os dois parceiros desejam atingir e é específica com relação a transporte ou protocolo.

As etapas envolvidas na criação de um contrato de parceiro comercial são documentadas [aqui][2]

##Usando o Conector do AS2 na superfície do designer de aplicativo,s lógicos
O Conector do AS2 pode ser usado como um gatilho ou uma ação.

###Gatilho
- Iniciar o designer de fluxo de Aplicativos Lógicos do Azure
- Clique no Conector do AS2 no painel à direita

	![Configurações do gatilho][3]
- Clique em ->

	![Opções do gatilho][4]
- O Conector do AS2 expõe um único gatilho. Selecione *Receber e Decodificar*

	![Receber e decodificar dados][5]
- Esse gatilho não tem entradas. Clique em ->

	![Receber e decodificar configurados][6]
- Como parte da saída, o conector retorna a carga útil do AS2, bem como os metadados específicos do AS2.

###Ação
- Clique no Conector do AS2 no painel à direita

	![Configurações de ação][7]
- Clique em ->

	![Lista de ações][8]
- O conector do AS2 tem suporte para apenas uma ação. Selecione *Codificar e Enviar*

	![Codificar e enviar entrada][9]
- Fornecer as entradas para a ação e configurá-la

	![Codificar e enviar configurado][10]

<table>
	<tr>
		<th>Parâmetro</th>
		<th>Tipo</th>
		<th>Descrição do parâmetro</th>
	</tr>
	<tr>
		<td>Carga útil</td>
		<td>objeto</td>
		<td>Carga útil</td>
	</tr>
	<tr>
		<td>AS2 de</td>
		<td>string</td>
		<td>AS2 de</td>
	</tr>
	<tr>
		<td>AS2 para</td>
		<td>string</td>
		<td>AS2 para</td>
	</tr>
	<tr>
		<td>URL do parceiro</td>
		<td>string</td>
		<td>URL do parceiro</td>
	</tr>
	<tr>
		<td>Habilitar arquivamento</td>
		<td>boolean</td>
		<td>Habilitar arquivamento</td>
	</tr>
</table>

A ação retorna um código de resposta HTTP 200 à conclusão bem-sucedida.

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e Monitorar aplicativos de API e conector](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-as2/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-as2/TriggerOptions.PNG
[5]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeInput.PNG
[6]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeConfigured.PNG
[7]: ./media/app-service-logic-connector-as2/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-as2/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-as2/EncodeAndSendInput.PNG
[10]: ./media/app-service-logic-connector-as2/EncodeAndSendConfigured.PNG

<!---HONumber=62-->