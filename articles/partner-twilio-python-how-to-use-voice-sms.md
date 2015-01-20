<properties urlDisplayName="Twilio Voice/SMS Service" pageTitle="Como usar o Twilio para Voz e SMS (PHP) - Azure" metaKeywords ="Azure PHP Twilio, chamadas telefônicas Azure, chamadas telefônicas Azure, Azure twilio, Azure SMS, Azure SMS, chamadas de voz Azure, chamadas de voz do Azure, mensagens de texto do Azure, mensagens de texto do Azure" description="Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Exemplos de código escritos em PHP." metaCanonical="" services="" documentationCenter="" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="MicrosoftHelp@twilio.com" solutions="" manager="twilio" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="11/25/2014" ms.author="MicrosoftHelp@twilio.com" />





# Como usar o Twilio para a voz e os recursos de SMS no PHP
Este guia demonstra como executar tarefas comuns de programação com o serviço de API do Twilio no Azure. Os cenários abrangidos incluem fazer uma chamada telefônica e enviar uma mensagem serviço de mensagem curta (SMS). Para obter mais informações sobre o Twilio e o uso de voz e SMS em seus aplicativos, consulte a seção [Próximas etapas].(#NextSteps) .

## Sumário
* [O que é Twilio?](#WhatIs)
* [Preços do Twilio](#Pricing)
* [Conceitos](#Concepts)
* [Criar uma conta do Twilio](#CreateAccount)
* [Verificar números telefônicos](#VerifyPhoneNumbers)
* [Criar um aplicativo PHP](#create_app)
* [Configurar seu aplicativo para usar as bibliotecas do Twilio](#configure_app)
* [Como: Fazer uma chamada externa](#howto_make_call)
* [Como: Enviar uma mensagem SMS](#howto_send_sms)
* [Como: Fornecer as respostas TwiML de seu próprio site](#howto_provide_twiml_responses)

<h2><a id="WhatIs"></a>O que é Twilio?</h2>
Twilio é alimentar o futuro das comunicações de negócios, que permite aos desenvolvedores incorporar voz, VoIP e mensagens em aplicativos. Eles virtualizam toda a infra-estrutura necessária em um ambiente baseado em nuvem, global, expondo-as por meio da plataforma de comunicações API Twilio. Os aplicativos são simples de criar e dimensionável. Aproveite a flexibilidade com pagamento medida que vá preços e se beneficiar da confiabilidade da nuvem.

**O Twilio Voice** permite que seus aplicativos façam e recebam chamadas telefônicas. O **SMS da Twilio** permite que seu aplicativo envie e receba mensagens de texto. **O Cliente Twilio** permite que você faça chamadas VoIP de qualquer telefone, tablet ou navegador e seja compatível com WebRTC.

<h2><a id="Pricing"></a>Preços e ofertas especiais do Twilio</h2>

Os clientes do Azure recebem uma [oferta especial] de US$ 10 de crédito do Twilio quando você atualiza sua conta do Twilio. Esse crédito de Twilio pode ser aplicado a qualquer uso de Twilio (equivalente a até 1.000 mensagens SMS de envio ou recebimento de até 1000 minutos de voz entrados, dependendo da localização do seu destino de chamada e mensagem ou número de telefone de crédito de US $10). Resgate esse crédito do Twilio e comece a usar em: [ahoy.twilio.com/azure].

Twilio é um serviço flexível. Não há nenhuma taxa de configuração e você pode fechar sua conta a qualquer momento. Você pode encontrar mais detalhes em [Preços do Twilio] [twilio_pricing].

<h2><a id="Concepts"></a>Conceitos</h2>
A API do Twilio é uma API RESTful que fornece os recursos de voz e SMS para aplicativos. As bibliotecas de cliente estão disponíveis em vários idiomas. Para obter uma lista, consulte [Bibliotecas de API do Twilio] [twilio_libraries].

Principais aspectos da API do Twilio são Twilio verbos e linguagem de marcação de Twilio (TwiML).

<h3><a id="Verbs"></a>Verbos de Twilio</h3>
A API usa os verbos do Twilio. Por exemplo, o verbo **<Say>** instrui o Twilio a fornecer de forma audível uma mensagem em uma chamada.

A seguir está uma lista de verbos do Twilio. Saiba mais sobre os outros verbos e recursos através da ]Documentação da linguagem de marcação do Twilio[ [http://www.twilio.com/docs/api/twiml]].

* **<Dial>**: Conecta o chamador com outro telefone.
* **<Gather>**: Coleta os dígitos numéricos inseridos no teclado numérico de telefone.
* **<Hangup>**: Termina uma chamada.
* **<Play>**: Reproduz um arquivo de áudio.
* **<Pause>**: Espera silenciosamente por um número especificado de segundos.
* **<Record>**: grava a voz do chamador e retorna um URL de um arquivo que contém a gravação.
* **<Redirect>**: Transfere o controle de uma chamada ou SMS para TwiML em uma URL diferente.
* **<Reject>**: Descarta uma chamada recebida para o número do Twilio sem cobrança
* **<Say>**: Converte o texto em fala que é executada em uma chamada.
* **<Sms>**: Envia uma mensagem SMS.

<h3><a id="TwiML"></a>TwiML</h3>
TwiML é um conjunto de instruções em XML com base nos verbos do Twilio que informam o Twilio como processar uma chamada ou SMS.

Por exemplo, o seguinte TwiML converteria a mensagem **Olá, mundo** em fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando o aplicativo chama a API Twilio, um dos parâmetros a API é a URL que retorna a resposta TwiML. Para fins de desenvolvimento, você pode usar URLs fornecidos Twilio para fornecer as respostas de TwiML usadas por seus aplicativos. Você também pode hospedar seus próprio URLs para produzir as respostas TwiML e outra opção é usar o **TwiMLResponse** objeto.

Para obter mais informações sobre os verbos do Twilio, seus atributos e o TwiML, consulte [TwiML] [twiml]. Para obter informações adicionais sobre a API do Twilio, consulte [API do Twilio] [twilio_api].

<h2><a id="CreateAccount"></a>Criar uma conta no Twilio</h2>
Quando você estiver pronto para obter uma conta do Twilio, inscreva-se em [Experimentar o Twilio] [try_twilio]. Você pode começar com uma conta gratuita e atualizá-la depois.

Ao se inscrever em uma conta do Twilio, você receberá uma ID de conta e um token de autenticação. Eles serão necessários para fazer chamadas de API do Twilio. Para evitar o acesso não autorizado em sua conta, mantenha o token da autenticação seguro. Sua ID da conta e o token de autenticação poderão ser exibidos na ]página da conta Twilio[ [ twilio_account], nos campos rotulados **ACCOUNT SID** e **AITH TOKEN**, respectivamente.

<h2><a id="create_app"></a>Criar um aplicativo PHP</h2>
Um aplicativo PHP que usa o serviço da Twilio e está em execução no Azure não é diferente de qualquer outro aplicativo PHP que usa o serviço da Twilio. Embora os serviços da Twilio sejam baseados em REST e possam ser chamados no PHP de várias maneiras, este artigo se concentra em como usar os serviços da Twilio com a [Biblioteca da Twilio para PHP no Github][twilio_php]. Para obter mais informações sobre como usar a biblioteca da Twilio para PHP, consulte [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs].

Instruções detalhadas sobre como criar e implantar um aplicativo Twilio/PHP no Azure estão disponíveis em [Como fazer uma chamada telefônica usando a Twilio em um aplicativo PHP no Azure][howto_phonecall_php].

<h2><a id="configure_app"></a>Configurar seu aplicativo para usar as bibliotecas do Twilio</h2>
Você pode configurar seu aplicativo para usar a biblioteca da Twilio para PHP de duas maneiras:

1. Baixar a biblioteca da Twilio para PHP do GitHub ([https://github.com/twilio/twilio-php][twilio_php]) e adicionar o diretório **Services** ao seu aplicativo.

	-OR-

2. Instalar a biblioteca da Twilio para PHP como um pacote PEAR. Ela pode ser instalada com os seguintes comandos:

		$ pear channel-discover twilio.github.com/pear
		$ pear install twilio/Services_Twilio

Depois de instalar a biblioteca da Twilio para PHP, você pode adicionar uma instrução **require_once** no início dos arquivos PHP para fazer referência à biblioteca:

    	require_once 'Services/Twilio.php';

Para obter mais informações, consulte [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

<h2><a id="howto_make_call"></a>Como: Fazer uma chamada externa</h2>
A seguir é mostrado como fazer uma chamada externa usando a classe **Services_Twilio**. Esse código também usa um site fornecido pelo Twilio para retornar a resposta da linguagem de marcação do Twilio (TwiML). Substitua os valores dos números telefônico **De** e **Para** e certifique-se de verificar o número telefônico **De** para sua conta do Twilio antes de executar o código.

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

Como mencionado, esse código utiliza um site fornecido pelo Twilio para retornar a resposta de TwiML. Em vez disso, você pode usar seu próprio site para fornecer a resposta TwiML. Para obter mais informações, consulte [Como fornecer respostas TwiML em seu próprio site].(#howto_provide_twiml_responses).


- **Observação**: Para solucionar problemas de erros de validação do certificado SSL, consulte [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 


<h2><a id="howto_send_sms"></a>Como: Enviar uma mensagem SMS</h2>
A seguir é mostrado como enviar uma mensagem SMS usando a classe **Services_Twilio**. O **de** número é fornecido por Twilio para contas de avaliação para enviar mensagens SMS. O número **Para** deve ser verificado para sua conta de Twilio antes de executar o código.

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

<h2><a id="howto_provide_twiml_responses"></a>Como: Fornecer as respostas TwiML de seu próprio site</h2>
Quando o aplicativo inicia uma chamada para a API da Twilio, a Twilio enviará a solicitação a uma URL que deve retornar uma resposta TwiML. O exemplo acima usa a URL fornecida pela Twilio [http://twimlets.com/message][twimlet_message_url]. (Embora o TwiML tenha sido criado para uso da Twilio, você pode exibi-lo em seu navegador. Por exemplo, clique em [http://twimlets.com/message][twimlet_message_url] para ver um elemento "<Response>" vazio. Como outro exemplo, clique em [http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] para ver um elemento "<Response>" que contém um elemento "<Say>".)

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

Para obter mais informações sobre TwiML, consulte [https://www.twilio.com/docs/api/twiml][twiml_reference]. 

Após você configurar a página PHP para fornecer as respostas TwiML, use a URL da página PHP conforme a URL passada para o método "Services_Twilio->account->calls->create". Por exemplo, se você tiver um aplicativo da Web chamado **MyTwiML** implantado em um serviço hospedado do Azure, e o nome da página PHP for **mytwiml.php**, a URL poderá ser passada para  **Services_Twilio->account->calls->create** conforme mostrado no exemplo a seguir:

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

Para obter informações adicionais sobre como usar a Twilio no Azure com PHP, consulte [Como fazer uma chamada telefônica usando a Twilio em um aplicativo PHP no Azure][howto_phonecall_php].

<h2><a id="AdditionalServices"></a>Como: Use os serviços adicionais do Twilio</h2>
Além dos exemplos mostrados aqui, o Twilio oferece APIs baseadas na Web que podem ser usadas para aproveitar a funcionalidade adicional do Twilio do aplicativo Azure. Para obter detalhes completos, consulte a [Documentação da API do Twilio] [twilio_api_documentation].

<h2><a id="NextSteps"></a>Próximas etapas</h2>
Agora que você aprendeu as noções básicas do serviço Twilio, siga estes links para saber mais:

* [Diretrizes de segurança do Twilio][twilio_security_guidelines]
* [ Código de exemplo e guias de procedimentos do Twilio] [twilio_howtos]
* [Tutoriais de Início rápido do Twilio][twilio_quickstarts]
* [Twilio no GitHub] [twilio_on_github]
* [Fale com o suporte do Twilio] [twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html

[howto_phonecall_php]: http://windowsazure.com/pt-br/documentation/articles/partner-twilio-php-make-phone-call
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

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

<!--HONumber=35.2-->
