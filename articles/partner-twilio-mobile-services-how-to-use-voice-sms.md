<properties linkid="develop-mobile-tutorials-twilio-for-voice-and-sms" pageTitle="Usar a Twilio para obter recursos de voz e SMS | Mobile Dev Center" metaKeywords="" description="Saiba como executar tarefas comuns usando a API da Twilio com os Serviços Móveis do Azure." metaCanonical="" services="" documentationCenter="Mobile" title="Como usar a Twilio para obter recursos de voz e SMS dos Serviços Móveis" authors="twilio" solutions="" manager="" editor="" />


<h1>Como usar a Twilio para funcionalidades de voz e SMS dos Serviços Móveis</h1>

Este tópico mostra como executar tarefas comuns usando a API da Twilio com os Serviços Móveis do Azure. Neste tutorial, você aprenderá como criar scripts de API personalizados que usam a API da Twilio para iniciar uma chamada telefônica e enviar uma mensagem SMS. 

<h2><a id="WhatIs"></a>O que é o Twilio?</h2>
Twilio é alimentar o futuro das comunicações de negócios, que permite aos desenvolvedores incorporar voz, VoIP e mensagens em aplicativos. Eles virtualizam toda a infra-estrutura necessária em um ambiente baseado em nuvem, global, expondo-as por meio da plataforma de comunicações API Twilio. Os aplicativos são simples de criar e dimensionável. Aproveite a flexibilidade com pagamento medida que vá preços e se beneficiar da confiabilidade da nuvem.

**Twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. **Twilio SMS** permite que seus aplicativos enviem e recebam mensagens SMS. **Cliente de Twilio** permite que você faça chamadas VoIP de qualquer telefone, tablet ou navegador e oferece suporte a WebRTC.

<h2><a id="Pricing"></a>Preços e ofertas especiais do Twilio</h2>
Os clientes do Azure recebem uma [oferta especial][special_offer]: cortesia de US$ 10 de crédito da Twilio quando atualizam a conta da Twilio. Esse crédito de Twilio pode ser aplicado a qualquer uso de Twilio (equivalente a até 1.000 mensagens SMS de envio ou recebimento de até 1000 minutos de voz entrados, dependendo da localização do seu destino de chamada e mensagem ou número de telefone de crédito de US $10). Resgate esse crédito da Twilio e faça uma introdução em [ahoy.twilio.com/azure][special_offer].

Twilio é um serviço flexível. Não há nenhuma taxa de configuração e você pode fechar sua conta a qualquer momento. Você pode encontrar mais detalhes em [Preços da Twilio][twilio_pricing].  

<h2><a id="Concepts"></a>Conceitos</h2>
A API do Twilio é uma API RESTful que fornece os recursos de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas. Para obter uma lista, consulte [Bibliotecas de API da Twilio] [twilio_libraries].  Tutoriais adicionais estão disponíveis para uso da Twilio em qualquer aplicativo do Azure escrito em [.NET][azure_twilio_howto_dotnet], [node.js][azure_twilio_howto_node], [Java][azure_twilio_howto_java], [PHP][azure_twilio_howto_php], [Python][azure_twilio_howto_python] ou [Ruby][azure_twilio_howto_ruby].

Principais aspectos da API do Twilio são Twilio verbos e linguagem de marcação de Twilio (TwiML).

<h3><a id="Verbs"></a>Verbos de Twilio</h3>
A API usa os verbos do Twilio; por exemplo, o verbo **&lt;Say&gt;** instrui o Twilio a fornecer de forma audível uma mensagem em uma chamada. 

A seguir está uma lista de verbos do Twilio.  Saiba mais sobre os outros verbos e recursos na [Documentação da linguagem de marcação da Twilio (a página pode estar em inglês)](http://www.twilio.com/docs/api/twiml).

* **&lt;Dial&gt;**: Conecta o chamador com outro telefone.
* **&lt;Gather&gt;**: Coleta os dígitos numéricos inseridos no teclado numérico de telefone.
* **&lt;Hangup&gt;**: Termina uma chamada.
* **&lt;Play&gt;**: Reproduz um arquivo de áudio.
* **&lt;Pause&gt;**: Espera silenciosamente por um número especificado de segundos.
* **&lt;Record&gt;**: Grava a voz do chamador e retorna um URL de um arquivo que contém a gravação.
* **&lt;Redirect&gt;**: Transfere o controle de uma chamada ou SMS para TwiML em um URL diferente.
* **&lt;Reject&gt;**: Descarta uma chamada recebida para o número do Twilio sem cobrar você
* **&lt;Say&gt;**: Converte o texto em fala que é executada em uma chamada.
* **&lt;Sms&gt;**: Envia uma mensagem SMS.

<h3> <a id="TwiML"></a>TwiML</h3>
TwiML é um conjunto de instruções em XML com base nos verbos do Twilio que informam o Twilio como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML converteria a mensagem **Olá, mundo** em fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Olá mundo</Say>
    </Response>

Quando o aplicativo chama a API Twilio, um dos parâmetros a API é a URL que retorna a resposta TwiML. Para fins de desenvolvimento, você pode usar URLs fornecidos Twilio para fornecer as respostas de TwiML usadas por seus aplicativos. Você também pode hospedar seus próprio URLs para produzir as respostas TwiML e outra opção é usar o **TwiMLResponse** objeto.

Para obter mais informações sobre os verbos da Twilio, seus atributos e a TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API da Twilio, consulte [API da Twilio][twilio_api].

<h2><a id="CreateAccount"></a>Criar uma conta no Twilio</h2>
Quando estiver pronto para obter uma conta da Twilio, inscreva-se em [Avaliar a Twilio][try_twilio]. Você pode começar com uma conta gratuita e atualizá-la depois.

Quando você se inscrever para uma conta de Twilio, você receberá uma ID de conta e um token de autenticação. Eles serão necessários para fazer chamadas de API do Twilio. Para evitar o acesso não autorizado em sua conta, mantenha o token da autenticação seguro. A ID da sua conta e o token de autenticação podem ser exibidos na [página da conta da Twilio][twilio_account], nos campos rotulados **ACCOUNT SID** e **AUTH TOKEN**, respectivamente.

<h2><a id="VerifyPhoneNumbers"></a>Verificar números telefônicos</h2>
Vários números de telefone devem ser verificados com Twilio para a sua conta. Por exemplo, se você quiser fazer chamadas telefônicas, o número de telefone deve ser verificado como uma identificação de chamador saída com Twilio. Da mesma forma, se você desejar um número de telefone para receber mensagens SMS, o número de telefone de recebimento deve ser verificado com Twilio. Para obter informações sobre como verificar um número de telefone, consulte [Gerenciar números] [verify_phone]. Parte do código abaixo se baseia em números de telefone que você precisará verificar com Twilio.

Como uma alternativa para usar um número existente para seus aplicativos, você pode adquirir um número de telefone Twilio. Para obter informações sobre como adquirir um número de telefone de Twilio, consulte [ajuda de números de telefone de Twilio](https://www.twilio.com/help/faq/phone-numbers)

<h2><a id="create_app"></a>Criar um Serviço Móvel</h2>
Um Serviço Móvel que hospeda um aplicativo habilitado para a Twilio não é diferente de qualquer outro Serviço Móvel. Basta adicionar a biblioteca do node.js da Twilio para referenciá-la em seus scripts de APIs personalizadas do Serviço Móvel. Para obter informações sobre como criar um serviço móvel inicial, consulte [Introdução aos Serviços Móveis](http://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started/).

<h2><a id="VerifyPhoneNumbers"></a>Configurar seu Serviço Móvel para usar a biblioteca do Node.js da Twilio</h2>
A Twilio fornece uma biblioteca do Node.js que encapsula vários aspectos da Twilio para fornecer maneiras simples e fáceis de interagir com a API REST da Twilio e o Cliente da Twilio para gerar respostas TwiML.

Para usar a biblioteca do node.js da Twilio em seu Serviço Móvel, você precisa utilizar o suporte ao módulo npm dos Serviços Móveis, o que pode ser feito armazenando seus scripts no controle de código-fonte. O tutorial [Armazenar scripts no controle de código-fonte (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/develop/mobile/tutorials/store-scripts-in-source-control/) explica como configurar o controle de código-fonte pela primeira vez em seus Serviços Móveis e como armazenar seus scripts de servidor em um repositório Git.

Depois que você tiver configurado o controle de código-fonte para seu Serviço Móvel, abra a guia Configurar no painel Serviço Móvel e localize e copie a URL do Git.

Cole essa URL em um navegador e substitua o nome do repositório por */DebugConsole/index.html*

Por exemplo, altere:

    https://twilioSample.scm.azure-mobile.net/twilioSample.git

para:

    https://twilioSample.scm.azure-mobile.net/DebugConsole/index.html

Quando solicitado, digite as credenciais usadas durante a configuração do controle de código-fonte do serviço.  Depois de conectado, você verá o Console do Serviço Móvel do Azure.  

![Console do Serviço Móvel](./media/partner-twilio-mobile-services-how-to-use-voice-sms/twilio-kuduconsole.png)

No console, altere o diretório para a pasta de scripts:

    cd site\wwwroot\App_Data\config\scripts

Na pasta da api, você pode instalar o módulo de nó da Twilio executando o seguinte comando:

    npm install twilio

Agora você pode fazer referência e usar a biblioteca da Twilio em sua API personalizada e em scripts de tabela.

<h2><a id="howto_make_call"></a>Como: fazer uma chamada externa</h2>
O script a seguir mostra como iniciar uma chamada de saída do seu Serviço Móvel usando a função **makeCall**. Esse código também utiliza um site desde Twilio para retornar a resposta de linguagem de marcação de Twilio (TwiML). Substitua os valores dos números telefônico **De** e **Para** e certifique-se de verificar o número telefônico **De** para sua conta do Twilio antes de executar o código.

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

Como mencionado, esse código utiliza um site fornecido pelo Twilio para retornar a resposta de TwiML. Em vez disso, você pode usar seu próprio site para fornecer a resposta TwiML. Para mais informações, consulte [Como: fornecer respostas TwiML de seu próprio site](#howto_provide_twiml_responses).

<h2><a id="howto_send_sms"></a>Como: enviar uma mensagem de SMS</h2>
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


<h2><a id="howto_provide_twiml_responses"></a>Como: fornecer respostas TwiML de seu próprio site</h2>

Quando o aplicativo inicia uma chamada para a API da Twilio - por exemplo, por meio do método client.InitiateOutboundCall - a Twilio envia a solicitação para uma URL que deve retornar uma resposta TwiML. O exemplo em Como fazer uma chamada de saída usa a URL fornecida pela Twilio http://twimlets.com/message para retornar a resposta.

<div class="dev-callout">
<b>Observação</b>
<p>Enquanto TwiML destina-se ao uso pelos serviços da web, você pode exibir o TwiML no seu navegador. Por exemplo, clique em <a href="http://twimlets.com/message">twimlet_message_url</a> para ver um elemento &lt;Response&gt; vazio. Como outro exemplo, clique em <a href="http://twimlets.com/message?Message%5B0%5D=Hello%20World">twimlet_message_url_hello_world</a> para ver um elemento &lt;Response&gt; que contém um elemento &lt;Say&gt;.</p>
</div>

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

