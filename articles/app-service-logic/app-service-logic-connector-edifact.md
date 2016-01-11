<properties 
   pageTitle="Usando o Conector do BizTalk Edifact em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure" 
   description="Como criar e configurar o Conector do BizTalk Edifact ou aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure" 
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
   ms.date="12/17/2015"
   ms.author="rajram"/>

# Comece a usar o Conector do BizTalk Edifact e adicione-o a seu Aplicativo Lógico  
Use o serviço do Edifact para receber e enviar mensagens através do protocolo Edifact nas comunicações comerciais. O Edifact também é conhecido como ASC Edifact ou Accredited Standards Committee Edifact e é amplamente usado em vários setores.

Você pode adicionar o conector do BizTalk Edifact a seu fluxo de trabalho de negócios e processar dados como parte desse fluxo de trabalho dentro de um Aplicativo Lógico.

## Pré-requisitos
- Aplicativo de API do TPM: antes de criar um conector do Edifact, você precisa criar um [Conector de Gerenciamento de Parceiros Comerciais do BizTalk][1].
- Banco de dados SQL do Azure: cada um dos aplicativos de API B2B requer seu próprio Banco de Dados SQL do Azure.
- Barramento de Serviço do Azure: é opcional e usado somente no caso de envio em lote.

## Usando o conector do Edifact
Para usar o Conector do Edifact, você precisa primeiro criar uma instância do aplicativo de API para o Conector do AS2. Isso pode ser feito de forma embutida durante a criação de um aplicativo lógico ou com a seleção do aplicativo de API do Conector do AS2 do Azure Marketplace.

## Configurando o conector do Edifact
Parceiros comerciais são as entidades envolvidas em comunicações B2B (business-to-business). Quando dois parceiros estabelecem uma relação, isso é chamado de um Contrato. O acordo definido se baseia na comunicação que os dois parceiros desejam atingir e é específica com relação a transporte ou protocolo.

Consulte as etapas para [criar um contrato de parceiro comercial][2].

## Usando o Conector do Edifact na superfície do designer de aplicativos lógicos
O Conector do Edifact pode ser usado como um gatilho ou uma ação.

### Gatilho
- Iniciar o designer de fluxo de Aplicativos Lógicos do Azure
- Clique no Conector do Edifact no painel à direita: ![Configurações do gatilho][3]
- Clique em ->: ![Opções do gatilho][4]
- O Conector do EDIFACT expõe um único gatilho. Selecione *Lote da liberação*: ![Entrada do lote de liberação][5]
- Esse gatilho não tem entradas. Clique em ->: ![Lote de liberação configurado][6]
- Como parte da saída, o conector retorna a carga útil do Edifact, o id do contrato, bem como informações sobre se a mensagem é feita em lotes ou não.

### Ação
- Clique no Conector do Edifact no painel à direita: ![Configurações de ação][7]
- Clique em ->: ![Lista de ações][8]
- O conector do Edifact dá suporte a várias ações. Selecione *Codificar*: ![Codificar entrada][9]
- Forneça as entradas para a ação e configure-a: ![Codificação configurada][10]

	Parâmetro|Tipo|Descrição do parâmetro
---|---|---
Conteúdo|string|Mensagem XML
ID do contrato|inteiro|ID do contrato
É uma mensagem em lotes|boolean|É uma mensagem em lotes
Separador de elemento de dados|string|Separador de elemento de dados
Separador de componente|string|Separador de componente
Terminador de segmento|string|Terminador de segmento
Indicador de ponto decimal|string|Indicador de ponto decimal
Separador de repetição|string|Separador de repetição
Caractere de escape|string|Caractere de escape
Caractere de substituição|string|Caractere de substituição
Sufixo do terminador de segmento|string|Sufixo do terminador de segmento

A ação retorna um objeto que contém a carga útil EDIFACT mediante a conclusão bem-sucedida.

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Se você deseja começar com os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar os Aplicativos Lógicos](https://tryappservice.azure.com/?appservice=logic), em que você pode criar imediatamente um aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

Exibir a referência da API REST do Swagger em [Conectores e referência dos Aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

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

<!---HONumber=AcomDC_1223_2015-->