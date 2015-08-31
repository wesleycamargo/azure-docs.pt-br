<properties
   pageTitle="Usando o conector do Barramento de Serviço do Azure no Serviço de Aplicativo do Azure"
   description="Como usar o conector do Barramento de Serviço do Azure"
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
   ms.date="08/19/2015"
   ms.author="andalmia"/>


# Conector de barramento de serviço do Azure

O conector do Barramento de Serviço do Azure permite enviar mensagens de entidades do Barramento de Serviço como filas e tópicos e receber mensagens de entidades do Barramento de Serviço como filas e assinaturas.

## Gatilhos e Ações
Gatilhos são eventos que ocorrem. Por exemplo, quando um pedido é atualizado ou quando um novo cliente é adicionado. Uma ação é o resultado do gatilho. Por exemplo, quando um pedido ou uma nova mensagem entra em uma fila, enviar um alerta ou uma mensagem.

O conector do Barramento de Serviço do Azure pode ser usado como um gatilho ou uma ação em um aplicativo lógico e dá suporte a dados nos formatos JSON e XML.

O conector do Barramento de Serviço do Azure tem os seguintes gatilhos e ações disponíveis:

Gatilhos | Ações
--- | ---
Mensagem disponível | Enviar mensagem

## Criar o conector do Barramento de Serviço do Azure
Um conector pode ser criado em um aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Pesquise "Conector do Barramento de Serviço do Azure", selecione-o e selecione **Criar**.
3. Insira o Nome, o Plano do Serviço de Aplicativo e outras propriedades: 
  ![][1]

4. Insira as seguintes configurações de pacote:

	Nome | Descrição
--- | ---
Cadeia de conexão | A cadeia de conexão do Barramento de Serviço do Azure. Por exemplo, insira: *Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=[nome];SharedAccessKey=[chave]*.
Nome da entidade | Digite o nome da fila ou do tópico.
Nome da assinatura | Digite o nome da assinatura da qual receber mensagens.

5. Clique em **Criar**.

## Usando o conector do Barramento de Serviço em seu aplicativo lógico
Após criar o conector, você pode usar o conector do Barramento de Serviço do Azure como gatilho ou ação para seu aplicativo lógico. Para fazer isso:

1.	Crie um novo aplicativo lógico e escolha o mesmo grupo de recursos que tem o conector do Barramento de Serviço do Azure: 
  ![][2]

2.	Abra "Gatilhos e Ações" para abrir o designer de Aplicativos Lógicos e configurar seu fluxo de trabalho: 
  ![][3]

3. O conector do Barramento de Serviço do Azure aparece na seção "Aplicativos de API neste grupo de recursos" na galeria do lado direito: 
  ![][4]

4. Você pode soltar o conector do Barramento de Serviço do Azure no editor clicando em "Conector do Barramento de Serviço do Azure".

5.	Agora você pode usar o conector do Barramento de Serviço do Azure no fluxo de trabalho. Você pode usar a mensagem recuperada do gatilho do Barramento de Serviço do Azure ("Mensagem Disponível") em outras ações no fluxo: 
  ![][5]

  ![][6]

Você também pode usar a ação "Enviar Mensagem" do Barramento de Serviço do Azure: 
  ![][7]

  ![][8]

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-azureservicebus/img1.PNG
[2]: ./media/app-service-logic-connector-azureservicebus/img2.PNG
[3]: ./media/app-service-logic-connector-azureservicebus/img3.png
[4]: ./media/app-service-logic-connector-azureservicebus/img4.PNG
[5]: ./media/app-service-logic-connector-azureservicebus/img5.PNG
[6]: ./media/app-service-logic-connector-azureservicebus/img6.PNG
[7]: ./media/app-service-logic-connector-azureservicebus/img7.PNG
[8]: ./media/app-service-logic-connector-azureservicebus/img8.PNG

<!---HONumber=August15_HO8-->