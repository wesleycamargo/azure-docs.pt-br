<properties pageTitle="Usar o Twilio para funcionalidades de voz e SMS | Centro de desenvolvimento dos Serviços Móveis" metaKeywords="" description="Learn how to perform common tasks using the Twilio API with Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to use Twilio for voice and SMS capabilities from Mobile Services" authors="MicrosoftHelp@twilio.com" solutions="" manager="twilio" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/25/2014" ms.author="MicrosoftHelp@twilio.com" />


<h1>Como usar o Twilio para funcionalidades de voz e SMS dos Serviços Móveis</h1>

Este tópico mostra como executar tarefas comuns usando a API do Twilio com os Serviços Móveis do Azure. Neste tutorial, você aprenderá como criar scripts de API personalizados que usam a API do Twilio para iniciar uma chamada telefônica e enviar uma mensagem SMS. 

<h2><a id="WhatIs"></a>O que é Twilio?</h2>
O Twilio está alimentando o futuro das comunicações de negócios, permitindo que os desenvolvedores integrem voz, VoIP e mensagens em aplicativos. Ele virtualiza toda a infraestrutura necessária em um ambiente baseado em nuvem, global, expondo-a pela plataforma de comunicações de API do Twilio. Os aplicativos são simples de criar e dimensionáveis. Aproveite a flexibilidade com preços pré-pagos, e beneficie-se da confiabilidade da nuvem.

**Twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. **Twilio SMS** permite que seus aplicativos enviem e recebam mensagens SMS. **Twilio Client** permite que você faça chamadas VoIP de qualquer telefone, tablet ou navegador e dá suporte a WebRTC.

<h2><a id="Pricing"></a>Preços e ofertas especiais do Twilio</h2>
Os clientes do Azure recebem uma [oferta especial][special_offer]: US$ 10 de cortesia de crédito da Twilio quando atualizam a conta do Twilio. Esse crédito Twilio pode ser aplicado a qualquer uso do Twilio (equivalente a até 1.000 mensagens SMS de envio ou recebimento de até 1.000 minutos de voz recebidos, dependendo da localização do seu destino de chamada e mensagem ou número de telefone de crédito de US $10).Resgate esse crédito da Twilio. Entre em [ahoy.twilio.com/azure][special_offer].

Twilio é um serviço pré-pago. Não há nenhuma taxa de configuração e você pode fechar sua conta a qualquer momento. Você pode encontrar mais detalhes em [Preços do Twilio][twilio_pricing].  

<h2><a id="Concepts"></a>Conceitos</h2>
A API do Twilio é uma API RESTful que fornece os recursos de voz e SMS para aplicativos. As bibliotecas do cliente estão disponíveis em vários idiomas; para obter uma lista, consulte[Bibliotecas de API do Twilio.] [twilio_libraries].  Tutoriais adicionais estão disponíveis para uso do Twilio em qualquer aplicativo do Azure escrito em [.NET][azure_twilio_howto_dotnet], [node.js][azure_twilio_howto_node], [Java][azure_twilio_howto_java], [PHP][azure_twilio_howto_php], [Python][azure_twilio_howto_python] ou [Ruby][azure_twilio_howto_ruby].

Principais aspectos da API do Twilio são os verbos do Twilio e a linguagem de marcação de Twilio (TwiML).

<h3><a id="Verbs"></a>Verbos do Twilio</h3>
A API usa os verbos do Twilio; por exemplo, o verbo **<Say>** instrui o Twilio a fornecer de forma audível uma mensagem em uma chamada. 

A seguir está uma lista de verbos do Twilio.  Saiba mais sobre os outros verbos e recursos na [Documentação da linguagem de marcação da Twilio](http://www.twilio.com/docs/api/twiml).

* **<Dial>**:Conecta o chamador com outro telefone.
* **<Gather>**:Coleta os dígitos numéricos inseridos no teclado numérico de telefone.
* **<Hangup>**:Termina uma chamada.
* **<Play>**:Reproduz um arquivo de áudio.
* **<Pause>**:Espera silenciosamente por um número especificado de segundos.
* **<Record>**:Grava a voz do chamador e retorna uma URL de um arquivo que contém a gravação.
* **<Redirect>**:Transfere o controle de uma chamada ou SMS para TwiML em uma URL diferente.
* **<Reject>**:Descarta uma chamada recebida para o número do Twilio sem cobrança
* **<Say>**:Converte o texto em fala que é executada em uma chamada.
* **<Sms>**:Envia uma mensagem SMS.

<h3> <a id="TwiML"></a>TwiML</h3>
TwiML é um conjunto de instruções em XML com base nos verbos do Twilio que informam o Twilio como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML converte o texto **Hello World** em fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando o aplicativo chama a API Twilio, um dos parâmetros a API é a URL que retorna a resposta TwiML. Para fins de desenvolvimento, você pode usar URLs fornecidos Twilio para fornecer as respostas de TwiML usadas por seus aplicativos. Você também pode hospedar seus próprio URLs para produzir as respostas TwiML e outra opção é usar o objeto **TwiMLResponse**.

Para obter mais informações sobre os verbos Twilio, seus atributos e a TwiML, consulte[TwiML][twiml]. Para obter informações adicionais sobre a API da Twilio, consulte [API do Twilio][twilio_api].

<h2><a id="CreateAccount"></a>Criar uma conta do Twilio</h2>
Quando estiver pronto para obter uma conta do Twilio, inscreva-se em [Experimentar o Twilio][try_twilio].Você pode começar com uma conta gratuita e atualizá-la depois.

Quando você se inscrever para uma conta de Twilio, você receberá uma ID de conta e um token de autenticação. Eles serão necessários para fazer chamadas de API do Twilio. Para evitar o acesso não autorizado em sua conta, mantenha o token da autenticação seguro. A ID da sua conta e o token de autenticação podem ser exibidos na[página da conta da Twilio][twilio_account], nos campos rotulados**ACCOUNT SID** e **AUTH TOKEN**, respectivamente.

<h2><a id="create_app"></a>Criar um Serviço Móvel</h2>
Um Serviço Móvel que hospeda um aplicativo habilitado para Twilio não é diferente de qualquer outro Serviço Móvel. Basta adicionar a biblioteca do node.js do Twilio para referenciá-lo em seus scripts de APIs personalizadas do Serviço Móvel. Para obter informações sobre como criar um serviço móvel inicial, consulte [Introdução aos Serviços Móveis](/pt-br/documentation/articles/mobile-services-ios-get-started/).

<h2><a id="ConfigureMobileService"></a>Configurar seu Serviço Móvel para usar a biblioteca do Node.js da Twilio</h2>
A Twilio fornece uma biblioteca do Node.js que encapsula vários aspectos da Twilio para fornecer maneiras simples e fáceis de interagir com a API REST da Twilio e o Cliente da Twilio para gerar respostas TwiML.

Para usar a biblioteca do node.js da Twilio em seu Serviço Móvel, você precisa utilizar o suporte ao módulo npm dos Serviços Móveis, o que pode ser feito armazenando seus scripts no controle do código-fonte. 

1. Complete o tutorial [Scripts de armazenamento no controle do código-fonte](/pt-br/documentation/articles/mobile-services-store-scripts-source-control/). Isso o leva através da configuração do controle do código-fonte para seus serviços móveis e armazena os scripts de servidor em um repositório Git.

2. Depois de configurar o controle do código-fonte para o seu Serviço Móvel, abra o repositório em seu computador local, navegue até o subpasta `\services`, abra o arquivo package.json em um editor de texto, e adicione o campo a seguir ao objeto **dependências**:

		"twilio": "~1.7.0"
 
3. Depois de adicionar a referência do pacote Twilio ao objeto **dependências**, o arquivo package.json deve ter a seguinte aparência:

		{
		  "name": "todolist",
		  "version": "1.0.0",
		  "description": "todolist - hosted on Windows Azure Mobile Services",
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

	>[WACOM.NOTE]A dependência para o Twilio deve ser adicionada como`"twilio": "~1.7.0"`, with a (~). Uma referência com um acento circunflexo (^) não tem suporte. 

4. Confirme esta atualização de arquivos e envie a atualização de volta para o serviço móvel.

	Esta atualização ao arquivo package.json reiniciará o serviço móvel.
	
O serviço móvel agora instala e carrega o pacote Twilio para que você possa consultar e usar a biblioteca Twilio em seus scripts de API e tabela personalizados.

<h2><a id="howto_make_call"></a>Como: Fazer uma chamada externa</h2>
O script a seguir mostra como iniciar uma chamada de saída do seu Serviço Móvel usando a função **makeCall**. Esse código também usa um site fornecido pela Twilio para retornar a resposta da linguagem de marcação do Twilio (TwiML).Substitua os valores dos números telefônico **De** e **Para** e certifique-se de verificar o número telefônico **De** para sua conta do Twilio antes de executar o código.

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

Para obter mais informações sobre os parâmetros passados para a função**client.makeCall**, consulte[http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Como mencionado, esse código utiliza um site fornecido pelo Twilio para retornar a resposta de TwiML. Em vez disso, você pode usar seu próprio site para fornecer a resposta TwiML. Para obter mais informações, consulte [Como: Fornecer as respostas TwiML de seu próprio site](#howto_provide_twiml_responses).

<h2><a id="howto_send_sms"></a>Como: Enviar uma mensagem SMS</h2>
O código a seguir mostra como enviar uma mensagem SMS usando a função **sendSms**. O número **de** é fornecido pelo Twilio para contas de avaliação enviarem mensagens SMS. O número**para** número deve ser verificado para sua conta do Twilio antes de executar o código.

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


<h2><a id="howto_provide_twiml_responses"></a>Como: Fornecer as respostas TwiML de seu próprio site</h2>

Quando o aplicativo inicia uma chamada para a API da Twilio por exemplo, por meio do método client.InitiateOutboundCall - a Twilio envia a solicitação para uma URL que deve retornar uma resposta TwiML. O exemplo em Como: Fazer uma chamada externa usando a URL fornecida pelo Twilio http://twimlets.com/message para retornar a resposta.

<div class="dev-callout">
<b>Observação</b>
<p>Enquanto TwiML destina-se ao uso pelos serviços Web, é possível exibir o TwiML no seu navegador. Por exemplo, clique em <a href="http://twimlets.com/message">twimlet_message_url</a> para ver um elemento <Response> vazio; como outro exemplo, clique em <a href="http://twimlets.com/message?Message%5B0%5D=Hello%20World">twimlet_message_url_hello_world</a> para ver um element <Response> que contém um elemento <Say>.</p>
</div>

Em vez de confiar na URL fornecida pela Twilio, você pode criar seu próprio site na URL que retorna respostas HTTP. Você pode criar o site em qualquer linguagem que retorna respostas HTTP. Este tópico pressupõe que você estará hospedando a URL de um manipulador genérico do ASP.NET.

O script a seguir resulta em uma resposta TwiML que diz Hello World na chamada.

    var twilio = require('twilio');

    exports.post = function(request, response) {
        var resp = new twilio.TwimlResponse();
        resp.say({voice:'woman'}, 'ahoy hoy! Testing Twilio and node.js');
        response.set('Content-Type', 'text/xml');
        response.send(200, resp.toString());
    };

Para obter mais informações sobre TwiML, consulte [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

Depois de ter configurado uma maneira de fornecer respostas TwiML, você pode passar essa URL para o método **client.makeCall** conforme mostrado no exemplo de código a seguir:
    
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

[WACOM.INCLUDE [twilio_additional_services_and_next_steps](../includes/twilio_additional_services_and_next_steps.md)]


[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#


[azure_twilio_howto_dotnet]: /pt-br/develop/net/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_java]: /pt-br/develop/java/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_node]: /pt-br/develop/nodejs/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_ruby]: /pt-br/develop/ruby/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_python]: /pt-br/develop/python/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_php]: /pt-br/develop/php/how-to-guides/twilio-voice-and-sms-service/

<!--HONumber=35.1-->
