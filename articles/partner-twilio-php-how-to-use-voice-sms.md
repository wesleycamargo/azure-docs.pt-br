<properties title="How to Use Twilio for Voice and SMS (PHP) - Azure" pageTitle="How to Use Twilio for Voice and SMS (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in PHP." documentationCenter="PHP" services="" authors="MicrosoftHelp@twilio.com; robmcm" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm" />

# Como usar o Twilio para a voz e os recursos de SMS no PHP

Este guia demonstra como executar tarefas comuns de programação com o serviço de API do Twilio no Azure. Os cenários abrangidos incluem fazer uma chamada telefônica e enviar uma mensagem serviço de mensagem curta (SMS). Para obter mais informações sobre o Twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [Próximas etapas][Próximas etapas].

## Sumário

-   [O que é Twilio?][O que é Twilio?]
-   [Preços do Twilio][Preços do Twilio]
-   [Conceitos][Conceitos]
-   [Criar uma conta no Twilio][Criar uma conta no Twilio]
-   [Verificar números telefônicos][Verificar números telefônicos]
-   [Criar um aplicativo PHP][Criar um aplicativo PHP]
-   [Configurar seu aplicativo para usar as bibliotecas do Twilio][Configurar seu aplicativo para usar as bibliotecas do Twilio]
-   [Como: Fazer uma chamada externa][Como: Fazer uma chamada externa]
-   [Como: Enviar uma mensagem SMS][Como: Enviar uma mensagem SMS]
-   [Como: Fornecer as respostas TwiML de seu próprio site][Como: Fornecer as respostas TwiML de seu próprio site]

## <span id="WhatIs"></span></a>O que é Twilio?

Twilio é alimentar o futuro das comunicações de negócios, que permite aos desenvolvedores incorporar voz, VoIP e mensagens em aplicativos. Eles virtualizam toda a infra-estrutura necessária em um ambiente baseado em nuvem, global, expondo-as por meio da plataforma de comunicações API Twilio. Os aplicativos são simples de criar e dimensionável. Aproveite a flexibilidade com pagamento medida que vá preços e se beneficiar da confiabilidade da nuvem.

**Twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. O **SMS da Twilio** permite que seu aplicativo envie e receba mensagens de texto. **Cliente de Twilio** permite que você faça chamadas VoIP de qualquer telefone, tablet ou navegador e oferece suporte a WebRTC.

## <span id="Pricing"></span></a>Preços e ofertas especiais do Twilio

Os clientes do Azure recebem uma [oferta especial][<http://www.twilio.com/azure>]: US$10 de cortesia de crédito da Twilio quando atualizam a conta da Twilio. Esse crédito de Twilio pode ser aplicado a qualquer uso de Twilio (equivalente a até 1.000 mensagens SMS de envio ou recebimento de até 1000 minutos de voz entrados, dependendo da localização do seu destino de chamada e mensagem ou número de telefone de crédito de US $10). Resgate esse crédito da Twilio e faça uma introdução em: [][]<http://ahoy.twilio.com/azure></a>.

Twilio é um serviço flexível. Não há nenhuma taxa de configuração e você pode fechar sua conta a qualquer momento. Você pode encontrar mais detalhes em [Preços da Twilio][Preços da Twilio].

## <span id="Concepts"></span></a>Conceitos

A API do Twilio é uma API RESTful que fornece os recursos de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas. Para obter uma lista, consulte [Bibliotecas de API da Twilio][Bibliotecas de API da Twilio].

Principais aspectos da API do Twilio são Twilio verbos e linguagem de marcação de Twilio (TwiML).

### <span id="Verbs"></span></a>Verbos de Twilio

A API usa os verbos do Twilio; por exemplo, o verbo **\<Say\>** instrui o Twilio a fornecer de forma audível uma mensagem em uma chamada.

A seguir está uma lista de verbos do Twilio. Saiba mais sobre os outros verbos e recursos na [Documentação da linguagem de marcação do Twilio][<http://www.twilio.com/docs/api/twiml>].

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

Para obter mais informações sobre os verbos da Twilio, seus atributos e a TwiML, consulte [TwiML][TwiML]. Para obter informações adicionais sobre a API da Twilio, consulte [API da Twilio][API da Twilio].

## <span id="CreateAccount"></span></a>Criar uma conta no Twilio

Quando estiver pronto para obter uma conta da Twilio, inscreva-se em [Avaliar a Twilio][Avaliar a Twilio]. Você pode começar com uma conta gratuita e atualizá-la depois.

Quando você se inscrever para uma conta de Twilio, você receberá uma ID de conta e um token de autenticação. Eles serão necessários para fazer chamadas de API do Twilio. Para evitar o acesso não autorizado em sua conta, mantenha o token da autenticação seguro. A ID da sua conta e o token de autenticação podem ser exibidos na [página da conta da Twilio][página da conta da Twilio], nos campos rotulados **ACCOUNT SID** e **AUTH TOKEN**, respectivamente.

## <span id="VerifyPhoneNumbers"></span></a>Verificar números telefônicos

Vários números de telefone devem ser verificados com Twilio para a sua conta. Por exemplo, para fazer chamadas telefônicas de saída usando seu número de telefone existente para a ID do chamador, o número do telefone deverá ser verificado pela Twilio. De maneira semelhante, até a atualização, se você desejar enviar mensagens SMS para um número de telefone, esse número deverá ser verificado pela Twilio. Após a atualização, você pode enviar mensagens SMS para qualquer número sem verificá-lo. Para obter informações sobre como verificar um número de telefone, consulte [Gerenciar números][Gerenciar números]. Parte do código abaixo se baseia em números de telefone que você precisará verificar com Twilio.

Como uma alternativa para usar um número existente para seus aplicativos, você pode adquirir um número de telefone Twilio. Para obter informações sobre como adquirir um número de telefone do Twilio, consulte [Ajuda para números de telefone do Twilio][Ajuda para números de telefone do Twilio].

## <span id="create_app"></span></a>Criar um aplicativo PHP

Um aplicativo PHP que usa o serviço da Twilio e está em execução no Azure não é diferente de qualquer outro aplicativo PHP que usa o serviço da Twilio. Embora os serviços da Twilio sejam baseados em REST e possam ser chamados no PHP de várias maneiras, este artigo se concentra em como usar os serviços da Twilio com a [Biblioteca da Twilio para PHP no Github][Biblioteca da Twilio para PHP no Github]. Para obter mais informações sobre como usar a biblioteca da Twilio para PHP, consulte [][1]<http://readthedocs.org/docs/twilio-php/en/latest/index.html></a>.

Instruções detalhadas sobre como criar e implantar um aplicativo Twilio/PHP no Azure estão disponíveis em [Como fazer uma chamada telefônica usando a Twilio em um aplicativo PHP no Azure][Como fazer uma chamada telefônica usando a Twilio em um aplicativo PHP no Azure].

## <span id="configure_app"></span></a>Configurar seu aplicativo para usar as bibliotecas do Twilio

Você pode configurar seu aplicativo para usar a biblioteca da Twilio para PHP de duas maneiras:

1.  Baixar a biblioteca da Twilio para PHP no GitHub ([][Biblioteca da Twilio para PHP no Github]<https://github.com/twilio/twilio-php></a>) e adicionar o diretório **Services** a seu aplicativo.

    -OU-

2.  Instalar a biblioteca da Twilio para PHP como um pacote PEAR. Ela pode ser instalada com os seguintes comandos:

        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Depois de instalar a biblioteca da Twilio para PHP, você pode adicionar uma instrução **require\_once** na parte superior dos arquivos PHP para fazer referência à biblioteca:

        require_once 'Services/Twilio.php';

Para obter mais informações, consulte [][2]<https://github.com/twilio/twilio-php/blob/master/README.md></a>.

## <span id="howto_make_call"></span></a>Como: Fazer uma chamada externa

Abaixo é mostrado como fazer uma chamada externa usando a classe **Services\_Twilio**. Esse código também usa um site fornecido pelo Twilio para retornar a resposta da linguagem de marcação do Twilio (TwiML). Substitua os valores dos números telefônico **De** e **Para** e certifique-se de verificar o número telefônico **De** para sua conta do Twilio antes de executar o código.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    // (Must be previously validated with Twilio.)
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Como mencionado, esse código utiliza um site fornecido pelo Twilio para retornar a resposta de TwiML. Em vez disso, você pode usar seu próprio site para fornecer a resposta TwiML. Para obter mais informações, consulte [Como fornecer respostas TwiML em seu próprio site][Como: Fornecer as respostas TwiML de seu próprio site].

-   **Observação**: Para solucionar problemas de erros de validação do certificado SSL, consulte [][3]<http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html></a>

## <span id="howto_send_sms"></span></a>Como: Enviar uma mensagem SMS

Abaixo é mostrado como enviar uma mensagem SMS usando a classe **Services\_Twilio**. O **de** número é fornecido por Twilio para contas de avaliação para enviar mensagens SMS. O número **Para** deve ser verificado para sua conta de Twilio antes de executar o código.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <span id="howto_provide_twiml_responses"></span></a>Como: Fornecer as respostas TwiML de seu próprio site

Quando o aplicativo inicia uma chamada para a API da Twilio, a Twilio enviará a solicitação a uma URL que deve retornar uma resposta TwiML. O exemplo acima usa a URL fornecida pela Twilio [][4]<http://twimlets.com/message></a>. (Embora o TwiML tenha sido criado para uso da Twilio, você pode exibi-lo em seu navegador. Por exemplo, clique em [][4]<http://twimlets.com/message></a> para ver um elemento `<Response>` vazio; como outro exemplo, clique em [][5]<http://twimlets.com/message?Message%5B0%5D=Hello%20World></a> para ver um elemento `<Response>` que contém um `<Say>`.)

Em vez de contar com a URL fornecida pela Twilio, você pode criar seu próprio site que retorne respostas HTTP. Você pode criar o site em qualquer linguagem que retorne respostas XML. Este tópico pressupõe que você estará usando PHP para criar o TwiML.

A página PHP seguinte resulta em uma resposta de TwiML que diz **Hello World** na chamada.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Como você pode ver no exemplo acima, a resposta de TwiML é simplesmente um documento XML. A biblioteca da Twilio para PHP contém classes que geram TwiML para você. O exemplo a seguir produz a resposta equivalente conforme mostrado acima, mas usa a classe **Services\_Twilio\_Twiml** na biblioteca da Twilio para PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Para obter mais informações sobre TwiML, consulte [][6]<https://www.twilio.com/docs/api/twiml></a>.

Após você definir a página JSP para fornecer respostas TwiML, use a URL da página JSP conforme a URL passada para o método `Services_Twilio->account->calls->create`. Por exemplo, se você tiver um aplicativo da Web chamado **MyTwiML** implantado em um serviço hospedado do Azure, e o nome da página PHP for **mytwiml.php**, a URL poderá ser passada para **Services\_Twilio-\>account-\>calls-\>create** conforme mostrado no exemplo a seguir:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Para obter informações adicionais sobre como usar a Twilio no Azure com PHP, consulte [Como fazer uma chamada telefônica usando a Twilio em um aplicativo PHP no Azure][Como fazer uma chamada telefônica usando a Twilio em um aplicativo PHP no Azure].

## <span id="AdditionalServices"></span></a>Como: Use os serviços adicionais do Twilio

Além dos exemplos mostrados aqui, o Twilio oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do Twilio do aplicativo Azure. Para obter detalhes completos, consulte a [Documentação da API do Twilio][API da Twilio].

## <span id="NextSteps"></span></a>Próximas etapas

Agora que você já conhece os princípios do serviço Twilio, acesse estes links para saber mais:

-   [Diretrizes da segurança do Twilio][Diretrizes da segurança do Twilio]
-   [Instruções e código de exemplo da Twilio][Instruções e código de exemplo da Twilio]
-   [Tutoriais de início rápido do Twilio][Tutoriais de início rápido do Twilio]
-   [Twilio no GitHub][Twilio no GitHub]
-   [Fale com o suporte do Twilio][Fale com o suporte do Twilio]

  [Próximas etapas]: #NextSteps
  [Preços do Twilio]: #Pricing
  [Conceitos]: #Concepts
  [Criar uma conta no Twilio]: #CreateAccount
  [Verificar números telefônicos]: #VerifyPhoneNumbers
  [Criar um aplicativo PHP]: #create_app
  [Configurar seu aplicativo para usar as bibliotecas do Twilio]: #configure_app
  [Como: Fazer uma chamada externa]: #howto_make_call
  [Como: Enviar uma mensagem SMS]: #howto_send_sms
  [Como: Fornecer as respostas TwiML de seu próprio site]: #howto_provide_twiml_responses
  []: http://ahoy.twilio.com/azure
  [Preços da Twilio]: http://www.twilio.com/pricing
  [Bibliotecas de API da Twilio]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [API da Twilio]: http://www.twilio.com/api
  [Avaliar a Twilio]: https://www.twilio.com/try-twilio
  [página da conta da Twilio]: https://www.twilio.com/user/account
  [Gerenciar números]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Ajuda para números de telefone do Twilio]: https://www.twilio.com/help/faq/phone-numbers
  [Biblioteca da Twilio para PHP no Github]: https://github.com/twilio/twilio-php
  [1]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
  [Como fazer uma chamada telefônica usando a Twilio em um aplicativo PHP no Azure]: ../partner-twilio-php-make-phone-call
  [2]: https://github.com/twilio/twilio-php/blob/master/README.md
  [3]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
  [4]: http://twimlets.com/message
  [5]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
  [6]: https://www.twilio.com/docs/api/twiml
  [Diretrizes da segurança do Twilio]: http://www.twilio.com/docs/security
  [Instruções e código de exemplo da Twilio]: http://www.twilio.com/docs/howto
  [Tutoriais de início rápido do Twilio]: http://www.twilio.com/docs/quickstart
  [Twilio no GitHub]: https://github.com/twilio
  [Fale com o suporte do Twilio]: http://www.twilio.com/help/contact
