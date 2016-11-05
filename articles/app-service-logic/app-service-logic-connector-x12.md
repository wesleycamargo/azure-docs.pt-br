---
title: Usando o Conector do BizTalk X12 em Aplicativos Lógicos | Microsoft Docs
description: Como criar e configurar o Conector do BizTalk X12 ou aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure
services: logic-apps
documentationcenter: .net,nodejs,java
author: rajeshramabathiran
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 04/20/2016
ms.author: rajram

---
# Introdução ao aplicativo de API BizTalk X12 e à adição do mesmo ao seu Aplicativo Lógico
[!INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Use o serviço BizTalk X12 para receber e enviar mensagens através do protocolo X12 nas comunicações comerciais. O X12 também é conhecido como ASC X12 ou Comitê de Normas Certificadas X12 e é amplamente usado em vários setores.

Você pode adicionar o aplicativo de API BizTalk X12 a seu fluxo de trabalho comercial e processar dados como parte de um fluxo de trabalho comercial dentro de um Aplicativo Lógico.

## Pré-requisitos
* Aplicativo de API do TPM: antes de criar um conector X12, você precisa criar um [Conector de Gerenciamento de Parceiros Comerciais do BizTalk][1]
* Banco de dados SQL do Azure: cada um dos aplicativos de API B2B requer seu próprio Banco de Dados SQL do Azure.
* Barramento de Serviço do Azure: é opcional e usado somente no caso de envio em lote.

## Usando o Conector do BizTalk X12
Para usar o Conector do BizTalk X12, você precisa primeiro criar uma instância do Aplicativo de API do Conector BizTalk X12. Isso pode ser feito de forma embutida durante a criação de um aplicativo lógico ou com a seleção do aplicativo de API do Conector do BizTalk X12 do Azure Marketplace.

## Configurando o Conector do BizTalk X12
Parceiros comerciais são as entidades envolvidas em comunicações B2B (business-to-business). Quando dois parceiros estabelecem uma relação, isso é chamado de um Contrato. O acordo definido se baseia na comunicação que os dois parceiros desejam atingir e é específica com relação a transporte ou protocolo.

Consulte as etapas para [criar um contrato de parceiro comercial][2].

## Usando o Conector X12 na superfície do designer de aplicativos lógicos
O Conector X12 pode ser usado como um gatilho ou uma ação.

### Gatilho
* Iniciar o designer de fluxo de Aplicativos Lógicos do Azure.
* Clique no Conector do X12 no painel à direita: ![Configurações do gatilho][3]
* Clique em ->: ![Opções do gatilho][4]
* O Conector do BizTalk X12 expõe um único gatilho. Selecione *Lote da liberação*: ![Entrada do lote de liberação][5]
* Esse gatilho não tem entradas. Clique em ->: ![Lote de liberação configurado][6]
* Como parte da saída, o conector retorna a carga útil do X12, a id do contrato, bem como informações sobre se a mensagem é feita em lotes ou não.

### Ação
* Clique no Conector do X12 no painel à direita: ![Configurações de ação][7]
* Clique em ->: ![Lista de ações][8]
* O conector X12 dá suporte a várias ações. Selecione *Codificar*: ![Codificar entrada][9]
* Fornecer as entradas para a ação e configurá-la: ![Codificação configurada][10]

| Parâmetro | Tipo | Descrição do parâmetro |
| --- | --- | --- |
| Conteúdo |string |Mensagem XML |
| ID do contrato |string |ID do contrato |
| É uma mensagem em lotes |string |É uma mensagem em lotes |

A ação retorna um objeto que contém a carga útil do X12.

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

> [!NOTE]
> Se você deseja começar com os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar os Aplicativos Lógicos](https://tryappservice.azure.com/?appservice=logic), em que você pode criar imediatamente um aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.
> 
> 

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

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

<!---HONumber=AcomDC_0803_2016-->