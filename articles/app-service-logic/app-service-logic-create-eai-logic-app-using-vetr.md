<properties
   pageTitle="Criar um Aplicativo Lógico EAI usando VETR em aplicativos lógicos no Serviço de Aplicativo do Azure | Microsoft Azure"
   description="Recursos Validar, Codificar e Transformar dos serviços XML do BizTalk"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/20/2016"
   ms.author="rajram"/>


# Criar aplicativo lógico de EAI usando VETR

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

A maioria dos cenários de EAI (Integração de Aplicativos Empresariais) media dados entre uma origem e um destino. Esses cenários normalmente têm um conjunto de requisitos comum:

- Verifique se dados dos sistemas diferentes estão no formato correto.
- Faça pesquisas nos dados de entrada para tomar decisões.
- Converta dados de um formato para outro. Por exemplo, converta dados do formato de dados de um sistema CRM para o formato de um sistema ERP.
- Encaminhe os dados para o aplicativo ou sistema desejado.

Este artigo mostra um padrão comum de integração: "mediação de mensagem unidirecional" ou VETR (validar, enriquecer, transformar, rotear). O padrão VETR media dados entre uma entidade de origem e uma entidade de destino. Normalmente, a origem e o destino são fontes de dados.

Considere um site que aceita pedidos. Os usuários lançam pedidos no sistema usando HTTP. Nos bastidores, o sistema valida se os dados de entrada estão corretos, normaliza-os e os mantém em uma fila do Barramento de Serviço para processamento adicional. O sistema retira os pedidos da fila, esperando por eles em um formato específico. Assim, o fluxo de ponta a ponta é:

**HTTP** → **Validar** → **Transformar** → **Barramento de Serviço**

![Fluxo VETR básico][1]

Os seguintes aplicativos de API do BizTalk ajudam na criação desse padrão:

* **Gatilho HTTP**: fonte que inicia o evento de mensagem
* **Validar**: valida se os dados recebidos estão corretos
* **Transformar**: transforma os dados de entrada no formato exigido pelo sistema downstream
* **Conector do Barramento de Serviço**: entidade de destino aonde os dados são enviados


## Construindo o padrão VETR básico
### Noções básicas

No portal do Azure, selecione **+Novo**, **Web + Celular** e **Aplicativo Lógico**. Escolha um nome, local, assinatura, grupo de recursos e localização que funcionem. Os grupos de recursos atuam como contêineres para os aplicativos. Todos os recursos de seu aplicativo vão para o mesmo grupo de recursos.

Em seguida, vamos adicionar ações e gatilhos.


## Adicionar Gatilho HTTP
1. Em **Modelos de Aplicativos Lógicos**, selecione **Criar do Zero**.
1. Selecione **Ouvinte HTTP** na galeria para criar um novo ouvinte. Chame-o de **HTTP1**.
2. Defina a configuração **Enviar resposta automaticamente?** como falsa. Configure a ação do gatilho definindo _Método HTTP_ como _POST_ e definindo _URL Relativa_ como _/OneWayPipeline_: ![Gatilho de HTTP][2]
3. Escolha a marca de seleção verde para concluir o disparador.

## Adicionar ação de validação

Agora, vamos inserir ações que são executadas sempre que o gatilho é acionado, ou seja, sempre que uma chamada é recebida no ponto de extremidade HTTP.

1. Adicione o **Validador XML do BizTalk** da galeria e nomeie-o _(Validate1)_ para criar uma instância.
2. Configure um esquema XSD para validar as mensagens XML de entrada. Selecione a ação _Validar_ e selecione _triggers(‘httplistener’).outputs.Content_ como valor do parâmetro _inputXml_.

Agora, a ação de validação é a primeira ação após o ouvinte HTTP:

![Validador de XML do BizTalk][3]

Da mesma forma, vamos adicionar o restante das ações.

## Adicionar ação Transformar
Vamos configurar transformações para normalizar os dados de entrada.

1. Adicione **Serviço de Transformação do BizTalk** na galeria.
2. Para configurar uma transformação e transformar as mensagens XML de entrada, selecione a ação **Transformar** como a ação a ser executada quando essa API for chamada. Selecione ```triggers(‘httplistener’).outputs.Content``` como valor para _inputXml_. *Mapa* é um parâmetro opcional, uma vez que os dados de entrada são comparados com todas as transformações configuradas e apenas aquelas que correspondem ao esquema são aplicadas.
3. Por fim, a transformação é executada somente se a validação for bem-sucedida. Para configurar essa condição, selecione o ícone de engrenagem no canto superior direito e selecione _Adicionar uma condição a ser atendida_. Defina a condição como ```equals(actions('xmlvalidator').status,'Succeeded')```:

![Transformações do BizTalk][4]


## Adicionar o conector do Barramento de Serviço
Em seguida, vamos adicionar o destino (uma Fila do Barramento de Serviço) no qual gravar os dados.

1. Adicione um **Conector do Barramento de Serviço** da galeria. Defina o **Nome** como _Servicebus1_, defina **Cadeia de Conexão** como a cadeia de conexão para sua instância de barramento de serviço, defina **Nome da Entidade** como _Fila_, e ignore **Nome da assinatura**.
2. Selecione a ação **Enviar Mensagem** e defina o campo **Conteúdo** da ação como _actions('transformservice').outputs.OutputXml_.
3. Definir o campo **Tipo de Conteúdo** como *application/xml*:

![Barramento de Serviço][5]


## Enviar resposta HTTP
Depois do processamento do pipeline, envie de volta uma resposta HTTP tanto para êxito quanto para falha com as seguintes etapas:

1. Adicione um **ouvinte HTTP** da galeria e selecione a ação **Enviar resposta de HTTP**.
2. Defina a **ID da Resposta** como Enviar *Mensagem*.
2. Defina **Conteúdo da Resposta** como *Processamento de pipeline concluído*.
3. **Código de Status de Resposta** como *200* para indicar HTTP 200 OK.
4. Selecione o menu suspenso no canto superior direito e selecione **Adicionar uma condição a ser atendida**. Defina a condição como a seguinte expressão: ```@equals(actions('azureservicebusconnector').status,'Succeeded')``` <br/>
5. Repita essas etapas para enviar uma resposta HTTP em caso de falha também. Altere **Condição** para a seguinte expressão: ```@not(equals(actions('azureservicebusconnector').status,'Succeeded'))``` <br/>
6. Selecione **OK** e **Criar**.



## Conclusão
Sempre que alguém envia uma mensagem ao ponto de extremidade HTTP, isso dispara o aplicativo e executa as ações que você acabou de criar. Para gerenciar esses aplicativos lógicos, selecione **Procurar** no Portal do Azure e selecione **Aplicativos Lógicos**. Selecione seu aplicativo para obter mais informações.

Alguns tópicos úteis:

[Gerenciar e monitorar os aplicativos e conectores de API](app-service-logic-monitor-your-connectors.md) <br/> [Monitorar os aplicativos lógicos](app-service-logic-monitor-your-logic-apps.md)

<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG

<!---HONumber=AcomDC_0803_2016-->