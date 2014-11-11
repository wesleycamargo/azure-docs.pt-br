<properties linkid="develop-mobile-tutorials-twilio-for-voice-and-sms" pageTitle="Use Twilio for Voice and SMS Capabilities | Mobile Dev Center" metaKeywords="" description="Learn how to perform common tasks using the Twilio API with Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use Twilio for voice and SMS capabilities from Mobile Services" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />

# Como usar a Twilio para funcionalidades de voz e SMS dos Serviços Móveis

Este tópico mostra como executar tarefas comuns usando a API da Twilio com os Serviços Móveis do Azure. Neste tutorial, você aprenderá como criar scripts de API personalizados que usam a API da Twilio para iniciar uma chamada telefônica e enviar uma mensagem SMS.

## <span id="WhatIs"></span></a>O que é Twilio?

Twilio é alimentar o futuro das comunicações de negócios, que permite aos desenvolvedores incorporar voz, VoIP e mensagens em aplicativos. Eles virtualizam toda a infra-estrutura necessária em um ambiente baseado em nuvem, global, expondo-as por meio da plataforma de comunicações API Twilio. Os aplicativos são simples de criar e dimensionável. Aproveite a flexibilidade com pagamento medida que vá preços e se beneficiar da confiabilidade da nuvem.

**Twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. **Twilio SMS** permite que seus aplicativos enviem e recebam mensagens SMS. **Cliente de Twilio** permite que você faça chamadas VoIP de qualquer telefone, tablet ou navegador e oferece suporte a WebRTC.

## <span id="Pricing"></span></a>Preços e ofertas especiais do Twilio

Os clientes do Azure recebem uma [oferta especial][oferta especial]: US$10 de cortesia de crédito da Twilio quando atualizam a conta da Twilio. Esse crédito de Twilio pode ser aplicado a qualquer uso de Twilio (equivalente a até 1.000 mensagens SMS de envio ou recebimento de até 1000 minutos de voz entrados, dependendo da localização do seu destino de chamada e mensagem ou número de telefone de crédito de US $10). Resgate esse crédito da Twilio e faça uma introdução em [ahoy.twilio.com/azure][oferta especial].

Twilio é um serviço flexível. Não há nenhuma taxa de configuração e você pode fechar sua conta a qualquer momento. Você pode encontrar mais detalhes em [Preços da Twilio][Preços da Twilio].

## <span id="Concepts"></span></a>Conceitos

A API do Twilio é uma API RESTful que fornece os recursos de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas. Para obter uma lista, consulte [Bibliotecas de API da Twilio][Bibliotecas de API da Twilio]. Tutoriais adicionais estão disponíveis para uso da Twilio em qualquer aplicativo do Azure escrito em [.NET][.NET], [node.js][node.js], [Java][Java], [PHP][PHP], [Python][Python] ou [Ruby][Ruby].

Principais aspectos da API do Twilio são Twilio verbos e linguagem de marcação de Twilio (TwiML).

### <span id="Verbs"></span></a>Verbos do Twilio

A API usa os verbos do Twilio; por exemplo, o verbo **\<Say\>** instrui o Twilio a fornecer de forma audível uma mensagem em uma chamada.

A seguir está uma lista de verbos do Twilio. Saiba mais sobre os outros verbos e recursos na [Documentação da linguagem de marcação da Twilio][Documentação da linguagem de marcação da Twilio].

-   **\<Dial\>**: Conecta o chamador com outro telefone.
-   **\<Gather\>**: Coleta os dígitos numéricos inseridos no teclado numérico de telefone.
-   **\<Hangup\>**: Termina uma chamada.
-   **\<Play\>**: Reproduz um arquivo de áudio.
-   **\<Pause\>**: Espera silenciosamente por um número especificado de segundos.
-   **\<Record\>**: Grava a voz do chamador e retorna uma URL de um arquivo que contém a gravação.
-   **\<Redirect\>**: Transfere o controle de uma chamada ou SMS para TwiML em uma URL diferente.
-   **\<Reject\>**: Descarta uma chamada recebida para o número do Twilio sem cobrança
-   **\<Say\>**: Converte o texto em fala que é executada em uma chamada.
-   **\<Sms\>**: Envia uma mensagem SMS.

### <span id="TwiML"></span></a>TwiML

TwiML é um conjunto de instruções em XML com base nos verbos do Twilio que informam o Twilio como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML converteria a mensagem **Olá, mundo** em fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando o aplicativo chama a API Twilio, um dos parâmetros a API é a URL que retorna a resposta TwiML. Para fins de desenvolvimento, você pode usar URLs fornecidos Twilio para fornecer as respostas de TwiML usadas por seus aplicativos. Você também pode hospedar seus próprio URLs para produzir as respostas TwiML e outra opção é usar o **TwiMLResponse** objeto.

Para obter mais informações sobre os verbos da Twilio, seus atributos e a TwiML, consulte [TwiML][Documentação da linguagem de marcação da Twilio]. Para obter informações adicionais sobre a API da Twilio, consulte [API da Twilio][API da Twilio].

## <span id="CreateAccount"></span></a>Criar uma conta no Twilio

Quando estiver pronto para obter uma conta da Twilio, inscreva-se em [Avaliar a Twilio][Avaliar a Twilio]. Você pode começar com uma conta gratuita e atualizá-la depois.

Quando você se inscrever para uma conta de Twilio, você receberá uma ID de conta e um token de autenticação. Eles serão necessários para fazer chamadas de API do Twilio. Para evitar o acesso não autorizado em sua conta, mantenha o token da autenticação seguro. A ID da sua conta e o token de autenticação podem ser exibidos na [página da conta da Twilio][página da conta da Twilio], nos campos rotulados **ACCOUNT SID** e **AUTH TOKEN**, respectivamente.

## <span id="VerifyPhoneNumbers"></span></a>Verificar números telefônicos

Vários números de telefone devem ser verificados com Twilio para a sua conta. Por exemplo, se você quiser fazer chamadas telefônicas, o número de telefone deve ser verificado como uma identificação de chamador saída com Twilio. Da mesma forma, se você desejar um número de telefone para receber mensagens SMS, o número de telefone de recebimento deve ser verificado com Twilio. Para obter informações sobre como verificar um número telefônico, consulte [Gerenciar números][Gerenciar números]. Parte do código abaixo se baseia em números de telefone que você precisará verificar com Twilio.

Como uma alternativa para usar um número existente para seus aplicativos, você pode adquirir um número de telefone Twilio. Para obter informações sobre como adquirir um número de telefone do Twilio, consulte [Ajuda para números de telefone do Twilio][Ajuda para números de telefone do Twilio].

## <span id="create_app"></span></a>Criar um Serviço Móvel

Um Serviço Móvel que hospeda um aplicativo habilitado para a Twilio não é diferente de qualquer outro Serviço Móvel. Basta adicionar a biblioteca do node.js da Twilio para referenciá-la em seus scripts de APIs personalizadas do Serviço Móvel. Para obter informações sobre como criar um serviço móvel inicial, consulte [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

## <span id="VerifyPhoneNumbers"></span></a>Configurar seu Serviço Móvel para usar a biblioteca do Node.js da Twilio

A Twilio fornece uma biblioteca do Node.js que encapsula vários aspectos da Twilio para fornecer maneiras simples e fáceis de interagir com a API REST da Twilio e o Cliente da Twilio para gerar respostas TwiML.

Para usar a biblioteca do node.js da Twilio em seu Serviço Móvel, você precisa utilizar o suporte ao módulo npm dos Serviços Móveis, o que pode ser feito armazenando seus scripts no controle de código-fonte. O tutorial [Armazenar scripts no controle de código-fonte (a página pode estar em inglês)][Armazenar scripts no controle de código-fonte (a página pode estar em inglês)] explica como configurar o controle de código-fonte pela primeira vez em seus Serviços Móveis e como armazenar seus scripts de servidor em um repositório Git.

Depois que você tiver configurado o controle de código-fonte para seu Serviço Móvel, abra a guia Configurar no painel Serviço Móvel e localize e copie a URL do Git.

Cole essa URL em um navegador e substitua o nome do repositório por */DebugConsole/index.html*

Por exemplo, altere:

    https://twilioSample.scm.azure-mobile.net/twilioSample.git

para:

    https://twilioSample.scm.azure-mobile.net/DebugConsole

Quando solicitado, digite as credenciais usadas durante a configuração do controle de código-fonte do serviço. Depois de conectado, você verá o Console do Serviço Móvel do Azure.

![Console do Serviço Móvel][Console do Serviço Móvel]

No console, altere o diretório para a pasta de scripts:

    cd site\wwwroot\App_Data\config\scripts

Na pasta da api, você pode instalar o módulo de nó da Twilio executando o seguinte comando:

    npm install twilio

Agora você pode fazer referência e usar a biblioteca da Twilio em sua API personalizada e em scripts de tabela.

## <span id="howto_make_call"></span></a>Como: Fazer uma chamada externa

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

Para obter mais informações sobre os parâmetros passados para o método **client.makeCall**, consulte [][]<http://www.twilio.com/docs/api/rest/making-calls></a>.

Como mencionado, esse código utiliza um site fornecido pelo Twilio para retornar a resposta de TwiML. Em vez disso, você pode usar seu próprio site para fornecer a resposta TwiML. Para obter mais informações, consulte [Como Fornecer as respostas TwiML de seu próprio site][Como Fornecer as respostas TwiML de seu próprio site].

## <span id="howto_send_sms"></span></a>Como: Enviar uma mensagem SMS

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

## <span id="howto_provide_twiml_responses"></span></a>Como: Fornecer as respostas TwiML de seu próprio site

Quando o aplicativo inicia uma chamada para a API da Twilio por exemplo, por meio do método client.InitiateOutboundCall - a Twilio envia a solicitação para uma URL que deve retornar uma resposta TwiML. O exemplo em Como: Fazer uma chamada externa usa a URL fornecida pelo Twilio <http://twimlets.com/message> para retornar a resposta.

<div class="dev-callout">
<b>Observa&ccedil;&atilde;o</b>
<p>Enquanto TwiML destina-se ao uso pelos servi&ccedil;os Web, &eacute; poss&iacute;vel exibir o TwiML no seu navegador. Por exemplo, clique em <a href="http://twimlets.com/message">twimlet_message_url</a> para ver um elemento &lt;Response&gt; vazio; como outro exemplo, clique em <a href="http://twimlets.com/message?Message%5B0%5D=Hello%20World">twimlet_message_url_hello_world</a> para ver um elemento &lt;Response&gt; que cont&eacute;m um elemento &lt;Say&gt;.</p>
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

Para obter mais informações sobre TwiML, consulte [][1]<https://www.twilio.com/docs/api/twiml></a>.

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

  [oferta especial]: http://ahoy.twilio.com/azure
  [Preços da Twilio]: http://www.twilio.com/pricing
  [Bibliotecas de API da Twilio]: https://www.twilio.com/docs/libraries
  [.NET]: /pt-br/develop/net/how-to-guides/twilio-voice-and-sms-service/
  [node.js]: /pt-br/develop/nodejs/how-to-guides/twilio-voice-and-sms-service/
  [Java]: /pt-br/develop/java/how-to-guides/twilio-voice-and-sms-service/
  [PHP]: /pt-br/develop/php/how-to-guides/twilio-voice-and-sms-service/
  [Python]: /pt-br/develop/python/how-to-guides/twilio-voice-and-sms-service/
  [Ruby]: /pt-br/develop/ruby/how-to-guides/twilio-voice-and-sms-service/
  [Documentação da linguagem de marcação da Twilio]: http://www.twilio.com/docs/api/twiml
  [API da Twilio]: http://www.twilio.com/api
  [Avaliar a Twilio]: https://www.twilio.com/try-twilio
  [página da conta da Twilio]: https://www.twilio.com/user/account
  [Gerenciar números]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Ajuda para números de telefone do Twilio]: https://www.twilio.com/help/faq/phone-numbers
  [Introdução aos Serviços Móveis]: http://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started/
  [Console do Serviço Móvel]: ./media/partner-twilio-mobile-services-how-to-use-voice-sms/twilio-kuduconsole.png
  []: http://www.twilio.com/docs/api/rest/making-calls
  [Como Fornecer as respostas TwiML de seu próprio site]: #howto_provide_twiml_responses
  [1]: https://www.twilio.com/docs/api/twiml
  [twilio_additional_services_and_next_steps]: ../includes/twilio_additional_services_and_next_steps.md
