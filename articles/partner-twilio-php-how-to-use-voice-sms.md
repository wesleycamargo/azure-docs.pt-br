<properties 
	pageTitle="Como usar o Twilio para voz e SMS (PHP) - Azure" 
	description="Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Exemplos de código escritos em PHP." 
	documentationCenter="php" 
	services="" 
	authors="devinrader" 
	manager="twilio" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="microsofthelp@twilio.com"/>

# Como usar o Twilio para obter recursos de voz e SMS no PHP
Este guia demonstra como executar tarefas comuns de programação com o serviço de API do Twilio no Azure. Os cenários abrangidos incluem fazer uma chamada telefônica e enviar um SMS (Serviço de Mensagem Curta). Para obter mais informações sobre o Twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [Próximas etapas](#NextSteps) .

## Sumário
* [O que é o Twilio?](#WhatIs)
* [Preços do Twilio](#Pricing)
* [Conceitos](#Concepts)
* [Criar uma conta do Twilio](#CreateAccount)
* [Verificar números telefônicos](#VerifyPhoneNumbers)
* [Criar um aplicativo PHP](#create_app)
* [Configurar seu aplicativo para usar as bibliotecas do Twilio](#configure_app)
* [Como: Fazer uma chamada externa](#howto_make_call)
* [Como: Enviar uma mensagem SMS](#howto_send_sms)
* [Como: Fornecer as respostas TwiML de seu próprio site](#howto_provide_twiml_responses)

<h2><a id="WhatIs"></a>O que é o Twilio?</h2>
O Twilio está alimentando o futuro das comunicações de negócios, permitindo que os desenvolvedores insiram voz, VoIP e mensagens em aplicativos. Ele virtualiza toda a infraestrutura necessária em um ambiente baseado em nuvem, global, expondo-a pela plataforma de comunicações de API do Twilio. Os aplicativos são simples de criar e dimensionáveis. Aproveite a flexibilidade com preços pré-pagos, e beneficie-se da confiabilidade da nuvem.

**Twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. **Twilio SMS** permite que seu aplicativo envie e receba mensagens de texto. **Twilio Client** permite que você faça chamadas VoIP de qualquer telefone, tablet ou navegador e seja compatível com WebRTC.

<h2><a id="Pricing"></a>Preços e ofertas especiais do Twilio</h2>

Clientes do Azure recebem um [oferta especial][http://www.twilio.com/azure]: US$10 de cortesia de crédito do Twilio quando atualizam a conta do Twilio. Esse crédito Twilio pode ser aplicado a qualquer uso do Twilio (US$10 de crédito equivalente a até 1.000 mensagens SMS de envio ou recebimento de até 1.000 minutos de voz de entrada, dependendo da localização do número do telefone e do destino da mensagem ou chamada). Resgate esse crédito do Twilio e comece a usar em: [http://ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio é um serviço pré-pago. Não há taxas de configuração e você pode fechar sua conta a qualquer momento. Você pode encontrar mais detalhes em [Preços do Twilio][twilio_pricing].

<h2><a id="Concepts"></a>Conceitos</h2>
A API do Twilio é uma API RESTful que fornece recursos de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas; para obter uma lista, consulte [Bibliotecas de API do Twilio][twilio_libraries].

Os principais aspectos da API do Twilio são os verbos Twilio e a TwiML (Linguagem de Marcação do Twilio).

<h3><a id="Verbs"></a>Verbos de Twilio</h3>
A API usa os verbos do Twilio; por exemplo, o verbo **&lt;Say&gt;** instrui o Twilio a fornecer de forma audível uma mensagem em uma chamada.

Veja, a seguir, uma lista de verbos do Twilio. Saiba mais sobre os outros verbos e recursos na [Documentação da linguagem de marcação do Twilio](http://www.twilio.com/docs/api/twiml).

* **&lt;Dial&gt;**: Conecta o chamador com outro telefone.
* **&lt;Gather&gt;**: Coleta os dígitos numéricos inseridos no teclado numérico de telefone.
* **&lt;Hangup&gt;**: Termina uma chamada.
* **&lt;Play&gt;**: Reproduz um arquivo de áudio.
* **&lt;Pause&gt;**: Espera silenciosamente por um número especificado de segundos.
* **&lt;Record&gt;**: Grava a voz do chamador e retorna uma URL de um arquivo que contém a gravação.
* **&lt;Redirect&gt;**: Transfere o controle de uma chamada ou SMS para TwiML em uma URL diferente.
* **&lt;Reject&gt;**: Descarta uma chamada recebida para o número do Twilio sem cobrança
* **&lt;Say&gt;**: Converte o texto em fala que é executada em uma chamada.
* **&lt;Sms&gt;**: Envia uma mensagem SMS.

<h3><a id="TwiML"></a>TwiML</h3>
TwiML é um conjunto de instruções em XML com base nos verbos do Twilio que informam o Twilio como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML converteria o texto **Hello World** em fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando o aplicativo chama a API Twilio, um dos parâmetros da API é a URL que retorna a resposta TwiML. Para fins de desenvolvimento, você pode usar URLs fornecidas pelo Twilio para fornecer as respostas TwiML usadas por seus aplicativos. Você também pode hospedar suas próprias URLs para produzir as respostas TwiML; outra opção é usar o objeto **TwiMLResponse**.

Para obter mais informações sobre os verbos Twilio, seus atributos e TwiML, consulte [TwiML][twiml]. Para obter informações adicionais sobre a API do Twilio, consulte [API do Twilio][twilio_api].

<h2><a id="CreateAccount"></a>Criar uma conta do Twilio</h2>
Quando você estiver pronto para obter uma conta de Twilio, inscreva-se em [Experimentar o Twilio][try_twilio]. Você pode começar com uma conta gratuita e atualizá-la depois.

Quando você se inscrever para uma conta de Twilio, receberá uma ID de conta e um token de autenticação. Eles serão necessários para fazer chamadas de API do Twilio. Para evitar o acesso não autorizado em sua conta, mantenha o token da autenticação seguro. A ID da conta e o token de autenticação ficam visíveis na [Página da conta do Twilio][twilio_account], nos campos rotulados **SID DE CONTA** e **TOKEN DE AUTENTICAÇÃO**, respectivamente.


<h2><a id="create_app"></a>Criar um aplicativo PHP</h2>
Um aplicativo PHP que usa o serviço do Twilio e está em execução no Azure não é diferente de qualquer outro aplicativo PHP que usa o serviço do Twilio. Enquanto os serviços do Twilio são baseados em REST e podem ser chamados do PHP de várias maneiras, este artigo concentra-se em como usar esses serviços com a [Biblioteca do Twilio para PHP no Github][twilio_php]. Para obter mais informações sobre como usar a biblioteca do Twilio para PHP, consulte [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs].

Instruções detalhadas para criar e implantar um aplicativo Twilio/PHP no Azure estão disponíveis em [Como fazer uma chamada telefônica usando o Twilio em um aplicativo PHP no Azure][howto_phonecall_php].

<h2><a id="configure_app"></a>Configurar seu aplicativo para usar as bibliotecas do Twilio</h2>
Você pode configurar seu aplicativo para usar a biblioteca do Twilio para PHP de duas maneiras:

1. Baixe a biblioteca do Twilio para PHP no Github ([https://github.com/twilio/twilio-php][twilio_php]) e adicione o diretório **Serviços** ao seu aplicativo.

	-OR-

2. Instale a biblioteca do Twilio para PHP como um pacote PEAR. Ela pode ser instalada com os seguintes comandos:

		$ pear channel-discover twilio.github.com/pear
		$ pear install twilio/Services_Twilio

Depois de instalar a biblioteca do Twilio para PHP, você pode adicionar uma instrução **require_once** na parte superior dos arquivos PHP para fazer referência à biblioteca:

    	require_once 'Services/Twilio.php';

Para obter mais informações, consulte [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

<h2><a id="howto_make_call"></a>Como: Fazer uma chamada externa</h2>
Abaixo é mostrado como fazer uma chamada externa usando a classe **Services_Twilio**. Esse código também usa um site fornecido pelo Twilio para retornar a resposta TwiML (Linguagem de Marcação do Twilio). Substitua os valores dos números telefônico **De** e **Para** e verifique o número telefônico **De** de sua conta do Twilio antes de executar o código.

	// Include the Twilio PHP library.
	require_once 'Services/Twilio.php';

	// Library version.
	$version = "2010-04-01";

	// Set your account ID and authentication token.
	$sid = "your_twilio_account_sid";
	$token = "your_twilio_authentication_token";

	// The number of the phone initiating the the call.
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

Como mencionado, esse código utiliza um site fornecido pelo Twilio para retornar a resposta TwiML. Em vez disso, você pode usar seu próprio site para fornecer a resposta TwiML. Para obter mais informações, consulte [Como fornecer respostas TwiML em seu próprio site](#howto_provide_twiml_responses).


- **Observação**: Para solucionar erros de validação de certificado SSL, consulte [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 


<h2><a id="howto_send_sms"></a>Como: Enviar uma mensagem SMS</h2>
Abaixo é mostrado como enviar uma mensagem SMS usando a classe **Services_Twilio**. O número em **De** é fornecido pelo Twilio para contas de avaliação, para enviar mensagens SMS. O número em **Para** deve ser verificado para sua conta do Twilio antes de executar o código.

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
		$client->$client->account->messages->sendMessage($from_number, $to_number, $message);
	}
	catch (Exception $e) 
	{
		echo 'Error: ' . $e->getMessage();
	}

<h2><a id="howto_provide_twiml_responses"></a>Como: Fornecer respostas TwiML de seu próprio site</h2>
Quando o aplicativo inicia uma chamada para a API do Twilio, o Twilio enviará a solicitação a uma URL que deve retornar uma resposta TwiML. O exemplo acima usa a URL fornecida pelo Twilio [http://twimlets.com/message][twimlet_message_url]. (Embora o TwiML tenha sido criado para uso do Twilio, você pode exibi-lo em seu navegador. Por exemplo, clique em [http://twimlets.com/message][twimlet_message_url] para ver um elemento `<Response>`; como outro exemplo, clique em [http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] para ver um elemento `<Response>` que contém um elemento `<Say>`.)

Em vez de contar com a URL fornecida pelo Twilio, você pode criar seu próprio site que retorne respostas HTTP. Você pode criar o site em qualquer linguagem que retorne respostas XML. Este tópico pressupõe que você estará usando PHP para criar o TwiML.

A página PHP seguinte resulta em uma resposta de TwiML que diz **Hello World** na chamada.

    <?php    
		header("content-type: text/xml");    
		echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
	?>
	<Response>    
		<Say>Hello world.</Say>
	</Response>

Como você pode ver no exemplo acima, a resposta de TwiML é simplesmente um documento XML. A biblioteca do Twilio para PHP contém classes que geram TwiML para você. O exemplo a seguir produz a resposta equivalente conforme mostrado acima, mas usa a classe **Services\_Twilio\_Twiml** na biblioteca do Twilio para PHP:

	require_once('Services/Twilio.php');
	
	$response = new Services_Twilio_Twiml();
	$response->say("Hello world.");
	print $response;

Para obter mais informações sobre TwiML, consulte [https://www.twilio.com/docs/api/twiml][twiml_reference]. 

Uma vez que a página PHP estiver configurada para fornecer respostas TwiML, use a URL da página PHP como a URL passada para o método `Services_Twilio->account->calls->create`. Por exemplo, se você tiver um aplicativo Web chamado **MyTwiML** implantado em um serviço hospedado do Azure e o nome da página PHP for **mytwiml. PHP**, a URL poderá ser passada para  **Services_Twilio->account->calls->create** conforme mostrado no exemplo a seguir:

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

Para obter informações adicionais sobre como usar Twilio no Azure com PHP, consulte [Como fazer uma chamada telefônica usando o Twilio em um aplicativo PHP no Azure][howto_phonecall_php].

<h2><a id="AdditionalServices"></a>Como: Usar os serviços adicionais do Twilio</h2>
Além dos exemplos mostrados aqui, o Twilio oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do Twilio do aplicativo Azure. Para obter detalhes completos, consulte a [Documentação da API do Twilio][twilio_api_documentation].

<h2><a id="NextSteps"></a>Próximas etapas</h2>
Agora que você já conhece os princípios do serviço Twilio, acesse estes links para aprender mais:

* [Diretrizes de segurança do Twilio][twilio_security_guidelines]
* [Código de exemplo e Procedimentos do Twilio][twilio_howtos]
* [Tutoriais de início rápido do Twilio][twilio_quickstarts] 
* [Twilio no GitHub][twilio_on_github]
* [Fale com o suporte do Twilio][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: ../partner-twilio-php-make-phone-call
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart

<!--HONumber=45--> 
