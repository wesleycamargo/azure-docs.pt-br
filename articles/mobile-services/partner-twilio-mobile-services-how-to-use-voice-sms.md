<properties 
	pageTitle="Usar a Twilio para obter recursos de voz e SMS | Microsoft Azure" 
	description="Saiba como executar tarefas comuns usando a API do Twilio com serviços móveis do Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="devinrader" 
	manager="twilio" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="devinrader"/>


# Como usar a Twilio para funcionalidades de voz e SMS dos Serviços Móveis

Este tópico mostra como executar tarefas comuns usando a API da Twilio com os Serviços Móveis do Azure. Neste tutorial, você aprenderá como criar scripts de API personalizados que usam a API da Twilio para iniciar uma chamada telefônica e enviar uma mensagem SMS.

## <a id="WhatIs"></a>O que é Twilio?
Twilio é alimentar o futuro das comunicações de negócios, que permite aos desenvolvedores incorporar voz, VoIP e mensagens em aplicativos. Eles virtualizam toda a infra-estrutura necessária em um ambiente baseado em nuvem, global, expondo-as por meio da plataforma de comunicações API Twilio. Os aplicativos são simples de criar e dimensionável. Aproveite a flexibilidade com pagamento medida que vá preços e se beneficiar da confiabilidade da nuvem.

**Twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. **Twilio SMS** permite que seus aplicativos enviem e recebam mensagens SMS. **Cliente de Twilio** permite que você faça chamadas VoIP de qualquer telefone, tablet ou navegador e oferece suporte a WebRTC.

## <a id="Pricing"></a>Preços e ofertas especiais da Twilio
Os clientes do Azure recebem uma [oferta especial][special_offer]\: US$ 10 de cortesia de crédito da Twilio quando atualizam a sua conta da Twilio. Esse crédito de Twilio pode ser aplicado a qualquer uso de Twilio (equivalente a até 1.000 mensagens SMS de envio ou recebimento de até 1000 minutos de voz entrados, dependendo da localização do seu destino de chamada e mensagem ou número de telefone de crédito de US $10). Resgate esse crédito da Twilio e faça uma introdução em [ahoy.twilio.com/azure][special_offer].

Twilio é um serviço flexível. Não há nenhuma taxa de configuração e você pode fechar sua conta a qualquer momento. Você pode encontrar mais detalhes em [Preços da Twilio][twilio_pricing].

## <a id="Concepts"></a>Conceitos
A API do Twilio é uma API RESTful que fornece os recursos de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas. Para obter uma lista, consulte [Bibliotecas de API da Twilio][twilio_libraries]. Tutoriais adicionais estão disponíveis para uso da Twilio em qualquer aplicativo do Azure escrito em [.NET][azure_twilio_howto_dotnet], [node.js][azure_twilio_howto_node], [Java][azure_twilio_howto_java], [PHP][azure_twilio_howto_php], [Python][azure_twilio_howto_python] ou [Ruby][azure_twilio_howto_ruby].

Principais aspectos da API do Twilio são Twilio verbos e linguagem de marcação de Twilio (TwiML).

### <a id="Verbs"></a>Verbos da Twilio
A API usa os verbos da Twilio; por exemplo, o verbo **&lt;Say&gt;** instrui a Twilio a fornecer de forma audível uma mensagem em uma chamada.

A seguir está uma lista de verbos do Twilio. Saiba mais sobre os outros verbos e recursos na [Documentação da linguagem de marcação da Twilio ](http://www.twilio.com/docs/api/twiml).

* **&lt;Dial&gt;**: Conecta o chamador a outro telefone.
* **&lt;Gather&gt;**: Coleta os dígitos numéricos inseridos no teclado numérico do telefone.
* **&lt;Hangup&gt;**: Termina uma chamada.
* **&lt;Play&gt;**: Reproduz um arquivo de áudio.
* **&lt;Pause&gt;**: Espera silenciosamente por um número especificado de segundos.
* **&lt;Record&gt;**: Grava a voz do chamador e retorna uma URL de um arquivo que contém a gravação.
* **&lt;Redirect&gt;**: Transfere o controle de uma chamada ou SMS para TwiML em uma URL diferente.
* **&lt;Reject&gt;**: Descarta uma chamada recebida para o número da Twilio sem cobrar você
* **&lt;Say&gt;**: Converte o texto em fala que é executada em uma chamada.
* **&lt;Sms&gt;**: Envia uma mensagem SMS.

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções em XML com base nos verbos do Twilio que informam o Twilio como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML converteria a mensagem **Olá, mundo** em fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando o aplicativo chama a API Twilio, um dos parâmetros a API é a URL que retorna a resposta TwiML. Para fins de desenvolvimento, você pode usar URLs fornecidos Twilio para fornecer as respostas de TwiML usadas por seus aplicativos. Você também pode hospedar seus próprio URLs para produzir as respostas TwiML e outra opção é usar o **TwiMLResponse** objeto.

Para obter mais informações sobre os verbos da Twilio, seus atributos e a TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API da Twilio, consulte [API da Twilio][twilio_api].

## <a id="CreateAccount"></a>Criar uma conta na Twilio
Quando estiver pronto para obter uma conta da Twilio, inscreva-se em [Avaliar a Twilio][try_twilio]. Você pode começar com uma conta gratuita e atualizá-la depois.

Quando você se inscrever para uma conta de Twilio, você receberá uma ID de conta e um token de autenticação. Eles serão necessários para fazer chamadas de API do Twilio. Para evitar o acesso não autorizado em sua conta, mantenha o token da autenticação seguro. A ID da sua conta e o token de autenticação podem ser exibidos na [página da conta da Twilio][twilio_account], nos campos rotulados **ACCOUNT SID** e **AUTH TOKEN**, respectivamente.

## <a id="create_app"></a>Criar um Serviço Móvel
Um Serviço Móvel que hospeda um aplicativo habilitado para a Twilio não é diferente de qualquer outro Serviço Móvel. Basta adicionar a biblioteca do node.js da Twilio para referenciá-la em seus scripts de APIs personalizadas do Serviço Móvel. Para obter informações sobre como criar um serviço móvel inicial, consulte [Introdução aos Serviços Móveis](mobile-services-ios-get-started.md).

## <a id="ConfigureMobileService"></a>Configurar seu Serviço Móvel para usar a biblioteca do Node.js da Twilio
A Twilio fornece uma biblioteca do Node.js que encapsula vários aspectos da Twilio para fornecer maneiras simples e fáceis de interagir com a API REST da Twilio e o Cliente da Twilio para gerar respostas TwiML.

Para usar a biblioteca do node.js da Twilio em seu Serviço Móvel, você precisa utilizar o suporte ao módulo npm dos Serviços Móveis, o que pode ser feito armazenando seus scripts no controle de código-fonte.

1. Concluir o tutorial [Scripts de armazenamento no controle do código-fonte](mobile-services-store-scripts-source-control.md). Isso o leva através da configuração do controle do código-fonte para seus serviços móveis e armazena os scripts de servidor em um repositório Git.

2. Depois de configurar o controle do código-fonte para o seu Serviço Móvel, abra o repositório no computador local, navegue até a subpasta `\services`, abra o arquivo package.json em um editor de texto e adicione o seguinte campo ao objeto **dependências**:

		"twilio": "~1.7.0"
 
3. Depois de adicionar a referência do pacote Twilio ao objeto **dependências**, o arquivo package.json deve ter a seguinte aparência:

		{
		  "name": "todolist",
		  "version": "1.0.0",
		  "description": "todolist - hosted on Azure Mobile Services",
		  "main": "server.js",
		  "engines": {
		    "node": ">= 0.8.19"
		  },
		  "dependencies": {
			"twilio": "~1.7.0" 
		  },
		  "devDependencies": {},
		  "scripts": {},
		  "author": "unknown",
		  "licenses": [],
		  "keywords":[]
		}

	>[AZURE.NOTE]A dependência para a Twilio deve ser adicionada `"twilio": "~1.7.0"`, com um (\~). Não há suporte para uma referência com um acento circunflexo (^).

4. Confirme essa atualização de arquivo e envie a atualização de volta por push para o serviço móvel.

	Essa atualização para o arquivo package.json reiniciará o serviço móvel.
	
O serviço móvel agora instala e carrega o pacote da Twilio, então você pode referenciar e usar a biblioteca da Twilio em sua API personalizada e scripts de tabela.

## <a id="howto_make_call"></a>Como fazer uma chamada externa
O script a seguir mostra como iniciar uma chamada de saída do seu Serviço Móvel usando a função **makeCall**. Esse código também usa um site fornecido pelo Twilio para retornar a resposta da linguagem de marcação do Twilio (TwiML). Substitua os valores dos números telefônico **De** e **Para** e certifique-se de verificar o número telefônico **De** para sua conta do Twilio antes de executar o código.

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://www.example.com/twiml.php' 

        }, function(err, responseData) {
            console.log(responseData.from); 
            response.send(200, '');
        });
    };

Para obter mais informações sobre os parâmetros passados para a função **client.makeCall**, consulte [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Como mencionado, esse código utiliza um site fornecido pelo Twilio para retornar a resposta de TwiML. Em vez disso, você pode usar seu próprio site para fornecer a resposta TwiML. Para mais informações, consulte [Como fornecer respostas TwiML de seu próprio site](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Como enviar uma mensagem de SMS
O código a seguir mostra como enviar uma mensagem SMS usando a função **sendSms**. O **de** número é fornecido por Twilio para contas de avaliação para enviar mensagens SMS. O **para** número deve ser verificado para sua conta de Twilio antes de executar o código.

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');
 
        client.sendSms({
            to:'[]',
            from:'[]',
            body:'ahoy hoy! Testing Twilio and node.js'
        }, function(error, message) {
    
            // The "error" variable will contain error information, if any.
            // If the request was successful, this value will be "false"
            if (!error) {
                console.log('Success! The SID for this SMS message is: ' + message.sid);
                console.log('Message sent on: ' + message.dateCreated);
            }
            else {
                console.log('Oops! There was an error.');
            }
            response.send(200, { error : error } );
        });
    };


## <a id="howto_provide_twiml_responses"></a>Como fornecer respostas TwiML de seu próprio site

Quando o aplicativo inicia uma chamada para a API da Twilio por exemplo, por meio do método client.InitiateOutboundCall - a Twilio envia a solicitação para uma URL que deve retornar uma resposta TwiML. O exemplo em Como fazer uma chamada externa usa a URL http://twimlets.com/message fornecida pela Twilio para retornar a resposta.

> [AZURE.NOTE]Enquanto TwiML destina-se ao uso pelos serviços Web, é possível exibir o TwiML no seu navegador. Por exemplo, clique em [twimlet\_message\_url](http://twimlets.com/message) para ver um elemento &lt;Response&gt; vazio; como outro exemplo, clique em [twimlet\_message\_url\_hello\_world](http://twimlets.com/message?Message%5B0%5D=Hello%20World) para ver um elemento &lt;Response&gt; que contém um elemento &lt;Say&gt;.

Em vez de confiar na URL fornecida pela Twilio, você pode criar seu próprio site na URL que retorna respostas HTTP. Você pode criar o site em qualquer linguagem que retorna respostas HTTP. Este tópico pressupõe que você irá hospedando a URL de um manipulador genérico do ASP.NET.

O script a seguir resulta em uma resposta TwiML que diz Hello World na chamada.

    var twilio = require('twilio');

    exports.post = function(request, response) {
        var resp = new twilio.TwimlResponse();
        resp.say({voice:'woman'}, 'ahoy hoy! Testing Twilio and node.js');
        response.set('Content-Type', 'text/xml');
        response.send(200, resp.toString());
    };

Para obter mais informações sobre TwiML, consulte [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

Depois de ter configurado uma maneira de fornecer respostas TwiML, você pode passar essa URL para o método **client.makeCall**, conforme mostrado no exemplo de código a seguir:
    
    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://<your_mobile_service>.azure-mobile.net/api/makeCall' 

        }, function(err, responseData) {

            console.log(responseData.from);
            response.send(200, '');
        });
    };

[AZURE.INCLUDE [twilio\_additional\_services\_and\_next\_steps](../../includes/twilio_additional_services_and_next_steps.md)]


[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]: https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#


[azure_twilio_howto_dotnet]: /develop/net/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_java]: /develop/java/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_node]: /develop/nodejs/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_ruby]: /develop/ruby/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_python]: /develop/python/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_php]: /develop/php/how-to-guides/twilio-voice-and-sms-service/
 

<!---HONumber=August15_HO7-->