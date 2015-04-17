<properties 
   pageTitle="Criar Aplicativo Lógico de EAI usando VETR" 
   description= "Este tópico aborda os recursos de Validar, Codificar e Transformar dos serviços XML do BizTalk." 
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
   ms.date="03/23/2015"
   ms.author="rajram"/>


# Criar Aplicativo Lógico de EAI usando VETR

A maioria dos cenários de EAI (Integração de Aplicativos Empresariais) media dados entre uma origem e um destino.  Esses cenários normalmente têm um conjunto de requisitos comum:

- Verifique se dados de sistemas diferentes estão no formato correto
- Faça pesquisas nos dados de entrada para tomar decisões
- Converta dados de um formato para outro (por exemplo, do formato de dados de um sistema CRM para o formato de dados de um sistema ERP)
- Encaminhe os dados para o aplicativo ou sistema desejado

Neste artigo, vamos examinar um padrão comum de integração:  "mediação de mensagem unidirecional" ou VETR (Validar, Enriquecer, Transformar, Encaminhar).  O padrão VETR media dados entre uma entidade de origem e uma entidade de destino.  Normalmente, a origem e o destino são fontes de dados. 

Considere um site que aceita pedidos.  Os usuários lançam pedidos no sistema usando HTTP.  Nos bastidores, o sistema valida se os dados de entrada estão corretos, os normaliza e os mantém em uma fila do Barramento de Serviço para processamento adicional.  O sistema retira os pedidos da fila, esperando por eles em um formato específico.  Assim, o fluxo de ponta a ponta é:

HTTP -> Validação -> Transformação -> Barramento de Serviço

![Basic VETR Flow][1]

Os seguintes aplicativos de API do BizTalk ajudam na criação desse padrão:

*Gatilho HTTP* - fonte que inicia o evento de mensagem
*Validar* - valida se os dados recebidos estão corretos
*Transformar* - transforma os dados de entrada no formato exigido pelo sistema downstream
*Conector do Barramento de Serviço* - entidade de destino aonde os dados são enviados


## Construindo o padrão VETR básico
### Noções básicas

No Portal de Gerenciamento do Azure, clique no botão **+Novo** na parte inferior esquerda da tela e clique em Aplicativo Lógico.  Escolha um nome, local, assinatura, grupo de recursos e localização que funcionem.  Os grupos de recursos atuam como contêineres para seus aplicativos e todos os recursos do seu aplicativo vão para o mesmo grupo de recursos.

Em seguida, vamos adicionar ações e gatilhos.


## Adicionar Gatilho HTTP

1. Selecione **Ouvinte HTTP** na galeria para criar um novo ouvinte.  Chame-o de **HTTP1**.
2. Deixe **Enviar resposta automaticamente?** definido como falso.  Configure a ação do gatilho definindo _Método HTTP_ como _POST_ e definindo a _URL Relativa_ como _/OneWayPipeline_.

![HTTP Trigger][2]


## Adicionar ação de validação

Agora, vamos inserir ações que são executadas sempre que o gatilho é acionado - ou seja, sempre que uma chamada for recebida no ponto de extremidade HTTP.

1. Adicione o **Validador XML do BizTalk** da galeria e nomeie-o _(Validate1)_ para criar uma instância.
2. Configure um esquema XSD para validar as mensagens XML de entrada.  Selecione a ação _Validar_ e _triggers('httplistener').outputs.Content_ como o valor para o parâmetro _inputXml_.

Agora, a ação de validação é a primeira ação após o ouvinte HTTP.  Da mesma forma, vamos adicionar o restante das ações.

![BizTalk XML Validator][3]


## Adicionar ação de transformação
Vamos configurar transformações para normalizar os dados de entrada.

1. Adicione **Transformar** da galeria. 
2. Para configurar uma transformação para transformar as mensagens XML de entrada, selecione a ação **Transformar** como a ação a ser executada quando essa API é chamada e selecione "`triggers('httplistener').outputs.Content`" como o valor de _inputXml_.  O mapa é um parâmetro opcional, uma vez que os dados de entrada são correspondidos com todas as transformações configuradas e apenas aquelas que correspondem ao esquema são aplicadas.
3. Por fim, a transformação é executada somente se a validação for bem-sucedida.  Para configurar essa condição, selecione o ícone de engrenagem no canto superior direito para _ "Adicionar uma condição a ser atendida"_.  Defina a condição como "`equals(actions('xmlvalidator').status,'Succeeded')`"


![BizTalk Transforms][4]


## Adicionar o conector do Barramento de Serviço
Em seguida, vamos adicionar o destino - uma fila do Barramento de Serviço - no qual gravar os dados.

1. Adicione um **Conector do Barramento de Serviço** da galeria.  Defina _Nome_ como _Servicebus1_, defina _Cadeia de Conexão_ como a cadeia de conexão da sua instância de barramento de serviço, defina _Nome da Entidade_ como _Queue_ e ignore _Nome da assinatura_. 
2. Selecione a ação **Enviar Mensagem** e defina o campo _Mensagem_ da ação como _actions('transformservice').outputs.OutputXml_

![Service Bus][5]


## Enviar resposta HTTP
Depois do processamento do pipeline, nós enviamos de volta uma resposta HTTP para êxito e falha com as seguintes etapas:

1. Adicione um **Ouvinte HTTP** da galeria e selecione a ação **Enviar Resposta HTTP**.
2. Defina _Conteúdo da Resposta_ como "Processamento do pipeline concluído", _Código de Status de Resposta_ como "200" para indicar HTTP 200 OK e condicione à expressão "`@equals(actions('servicebusconnector').status,'Succeeded')`"
	
Repita as etapas acima para enviar uma resposta HTTP em caso de falha também.  Altere a condição para "`@not(equals(actions('servicebusconnector').status,'Succeeded')).`"


## Conclusão
Sempre que alguém envia uma mensagem ao ponto de extremidade HTTP, ela aciona o gatilho e executa as ações que acabamos de criar.  Para gerenciar os aplicativos lógicos que você criar, clique **Procurar** no Portal de Gerenciamento do Azure e clique **Aplicativos Lógicos**.  Clique em seu aplicativo para obter mais informações.


<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG


<!--HONumber=49-->